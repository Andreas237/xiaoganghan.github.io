title: Parsing Evernote export file (.enex) using Python
description: ""
category:
tags: []


### Introduction

My wife keeps all the stories written about our baby in Evernote and
then publishes them on her blog every few days. Recently, as the baby
boy is going to celebrate his first birthday, my wife plans to publish
the stories as a book. This post is about how I exported the notes into
xml, parsed & cleaned up the xml file into plain text before converting
it to TeX format.

### Step1 - Export notes

![image](http://hanxiaogang.com/site_media/static/images/posts/evernote1.png)
![image](http://hanxiaogang.com/site_media/static/images/posts/evernote2.png)

### Step2 - Parsing

The scripts used to parse the enex file exported from Evernote:

{% highlight python %}
from lxml import etree
from StringIO import StringIO

p = etree.XMLParser(remove_blank_text=True, resolve_entities=False)
def parseNoteXML(xmlFile):
    context = etree.iterparse(xmlFile, encoding='utf-8', strip_cdata=False)
    note_dict = {}
    notes = []
    for ind, (action, elem) in enumerate(context):
        text = elem.text
        if elem.tag == 'content':
            text = []
            r = etree.parse(StringIO(elem.text.encode('utf-8')), p)
            for e in r.iter():
                try:
                    text.append(e.text)
                except:
                    print 'cannot print'
        note_dict[elem.tag] = text
        if elem.tag == "note":
            notes.append(note_dict)
            note_dict = {}
    return notes

if __name__ == '__main__':
    notes = parseNoteXML('mynote.enex')
{% endhighlight %}

A typical enex file:

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE en-export SYSTEM "http://xml.evernote.com/pub/evernote-export2.dtd">
  <en-export export-date="20120727T073610Z" application="Evernote" version="Evernote Mac 3.0.5 (209942)">
    <note>
      <title>Vim Tips</title>
      <content>
      <![CDATA[<?xml version="1.0" encoding="UTF-8" standalone="no"?>
        <!DOCTYPE en-note SYSTEM "http://xml.evernote.com/pub/enml2.dtd">
          <en-note style="word-wrap: break-word; -webkit-nbsp-mode: space; -webkit-line-break: after-white-space;">
    yank for copy, delete for cut, put for parse
    <div>
            <br/>
          </div>
          <div>Move in context, not position</div>
          <div>/ search forward</div>
          <div>? search backward</div>
          <div>n repeat last search</div>
          <div>N repeat last search but in the opposite direction</div>
          <div>tx move to 'x'</div>
          <div>fx find 'x'</div>
        </en-note>
    ]]>
      </content>
      <created>20101229T161500Z</created>
      <updated>20101231T161039Z</updated>
      <note-attributes/>
    </note>
  </en-export>
{% endhighlight %}



The parsing result for the enex file:

{% highlight python %}
[{'content': ['\nyank for copy, delete for cut, put for parse\n',
              None,
              None,
              'Move in context, not position',
              '/ search forward',
              '? search backward',
              'n repeat last search',
              'N repeat last search but in the opposite direction',
              "tx move to 'x'",
              "fx find 'x'"],
  'created': '20101229T161500Z',
  'note': None,
  'note-attributes': None,
  'title': 'Vim Tips',
  'updated': '20101231T161039Z'}]
{% endhighlight %}


### Summary

The source is avaiable as a [gist](https://gist.github.com/3186646).
Hope it helpful if you face the similar situations.
