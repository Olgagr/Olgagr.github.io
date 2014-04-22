---
layout: post
title:  "How to implement autocomplete with Solr and Ruby on Rails"
date:   2014-02-02 09:30:12
categories: ruby solr
---

If you use Solr in your Ruby on Rails project, there are a lot of chances that in some point you’ll need to implement autocomplete for searches. In this article I’ll show you how you can implement it. In fact, it is not so complicated. The trick is to change the Solr’s schema.xml correctly. I use ruby 1.9.3, rails 3.2.15 and sunspot_solr 2.0.0 (Solr 3).

## Add fieldType to schema.xml

First of all we have to define new field types in schema.xml. Copy this code and paste it into <types> section of schema.xml.

{% highlight xml %}
<fieldType name="autocomplete" class="solr.TextField" positionIncrementGap="100">
    <analyzer type="index">
      <tokenizer class="solr.KeywordTokenizerFactory"/>
      <filter class="solr.LowerCaseFilterFactory"/>
      <filter class="solr.EdgeNGramFilterFactory" minGramSize="1" maxGramSize="25" />
    </analyzer>
    <analyzer type="query">
      <tokenizer class="solr.KeywordTokenizerFactory"/>
      <filter class="solr.LowerCaseFilterFactory"/>
    </analyzer>
 </fieldType>	
{% endhighlight %}

## Set field class

Let’s analyze it. We add new fieldType, with name ‘autocomplete’. Every fieldType has to have unique name, it’s kind of identifier. Next we set the class for our field. For autocomplete we need text field, so this general, Solr class will meet our needs.

## positionIncrementGap setting

Other setting is positionIncrementGap. This one is used to prevent phrase queries from matching the end of one value and the beginnig of the next value in multivalued fields. Let me give you an example to clarify why we want this setting. Suppose we have multivalued field “user”:

user John Smith
user Ann Maybe

With a positionIncrementGap of 0, a phrase query of “smith ann” would be a match. Well, we don’t want a match across many value fields. So to prevent that we set positionIncrementGap which sets virtual space between the last token of one field instance and the first token of the next instance.

## Index analyzer

Analyzers tell Solr how field should be analyze during indexing and quering phrase. For indexing we need as a tokenizer KeywordTokenizerFactory (tokenizers describe how the text will be divided into separated tokens). This one treats the entire field as a single token, regardless of its content. For example: ‘John Doe’ will be treat as ‘John Doe’ value during matching, not ‘John’ and ‘Doe’ separately.

Next we set two filters:

* LowerCaseFilterFactory – it lowercases the letters in each token
* EdgeNGramFilterFactory – by default, create n-grams from the beginning edge of a input token. This is an description from official doc. Let me give you an example. We have field with value ‘developer’. With setting:

{% highlight xml %}
<filter class="solr.EdgeNGramFilterFactory" minGramSize="3" maxGramSize="25" />	
{% endhighlight %}

the string ‘developer’ will be broken into terms: ‘dev’, ‘deve’, ‘devel’, ‘develo’, ‘develop’, ‘develope’, ‘developer’. This is exactly what we want for autocomplete purpose.

## Query analyzer

This analyzer tells Solr how to parse user query. So we treat the query as single value (tokenizer KeywordTokenizerFactory) and we lowercase it before matching (filter LowerCaseFilterFactory).

{% highlight xml %}
<dynamicField name="*_ac" type="autocomplete" indexed="true"  stored="true"/>	
{% endhighlight %}

This is dynamic field as we want to have more than one field of this type. Any field with postfix ‘_ac’ will be autocomplete. We also want to set this kind of field as indexed (it means user can use this field to search) and stored (we want to show this field in search results).

## Set up model

Now we can set up our model. Let’s say we have model Post and title field will be autocomplete type:

{% highlight ruby %}
class Post < ActiveRecord::Base
  searchable do
    text :title_autocomplete, as: title_ac { title }
  end
end	
{% endhighlight %}

Now run rake sunspot:solr:reindex to reindex Solr.

Then in model Post:

{% highlight ruby %}
Post.search do
  fulltext params[:q] do
     fields(:title_autocomplete)
  end
end	
{% endhighlight %}

where params[:q] is user query.

	
	
	


	

