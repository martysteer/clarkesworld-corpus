wget -nc -P ./backissues -i clarksworld-issues-urls.txt
grep '<p class="story' ./backissues/* | cut -d '"' -f4 >> clarksworld-issues-urls.txt 
wget -nc --limit-rate=30k -P ./backissues -i clarksworld-issues-urls.txt 

trafilatura --precision --hash-as-name --inputdir ./backissues --outputdir ./data -out txt 
trafilatura --precision --hash-as-name --inputdir ./backissues --outputdir ./data --json
trafilatura --precision --hash-as-name --inputdir ./backissues --outputdir ./data --xml

There is slightly more reliable metadata in the XML headers:

```
<doc 
	sitename="Clarkesworld Magazine" 
	title="Clarkesworld Magazine - Science Fiction &amp; Fantasy" 
	author="Mark Cole" 
	date="2022-01-01" 
	source="https://clarkesworldmagazine.com/cole_10_11/" 
	hostname="clarkesworldmagazine.com" 
	excerpt="Clarkesworld Science Fiction and Fantasy Magazine and Podcast. This page: Spaceships, Time Paradoxes and Duct Tape: The Joys Of Independent SF Film by Mark Cole" 
	categories="" 
	tags="" 
	fingerprint="QzXyqIcF3UrxT8HGlI1Fitdqdc8=">



"title": "Clarkesworld Magazine - Science Fiction & Fantasy", 
"author": "Mark Cole", 
"hostname": "clarkesworldmagazine.com", 
"date": "2022-01-01", 
"categories": "", 
"tags": "", 
"fingerprint": "QzXyqIcF3UrxT8HGlI1Fitdqdc8=", 
"id": null, 
"license": null, 
"comments": "", 
"raw_text": cleaned text
"text": text with newlines
```

And the author name and story title are in the original HTML <title> in a sentence format:

```<title>{title} by {author} : Clarkesworld Magazine &#8211; Science Fiction &amp; Fantasy</title>```


The XML gets the `<excerpt>`, and from that we can get the story title.
Combine and split `*.xml` and `*.json` into CSV metadata - meta-out.ipynb

TODO:
Cleanup `*.txt` to use friendly filenames, and associate this filepath with the CSV metadata.
Extract metadata from ISFDB and merge into this datapackage.



e.g.
<doc sitename="Clarkesworld Magazine" title="Clarkesworld Magazine - Science Fiction &amp; Fantasy" author="Jeff VanderMeer" date="2022-01-01" source="https://clarkesworldmagazine.com/lanagan_interview/" hostname="clarkesworldmagazine.com" excerpt="Clarkesworld Science Fiction and Fantasy Magazine and Podcast. This page: Margo Lanagan and Tender Morsels by Jeff Vandermeer" categories="" tags="" fingerprint="nv5Crq8npfSXW28p6gIg8UmVB38=">


Story metadata: http://www.isfdb.org/cgi-bin/title.cgi?989235
Interview Title: Margo Lanagan and Tender MorselsTitle Record # 989235
Interviewee: Margo Lanagan
Interviewer: Jeff VanderMeer
Date: 2008-10-00
Type: INTERVIEW
Language: English
User Rating: This title has no votes. VOTE
Current Tags: None Add Tags
Publications
Title	Date	Author/Editor	Publisher/Pub. Series	ISBN/Catalog ID	Price	Pages	Format	Type	Cover Artist	Verif
Clarkesworld Magazine, October 2008	2008-10-00	ed. Neil Clarke, Sean Wallace	Wyrm Publishing	25	$0.00?	 	webzine?	mag	Joebert Zaide	Checkmark


Author metadata: http://www.isfdb.org/cgi-bin/ea.cgi?2934
Author: Jeff VanderMeerAuthor Record # 2934
Legal Name: VanderMeer, Jeffrey Scott
Birthplace: Bellefonte, Pennsylvania, USA
Birthdate: 7 July 1968
Language: English
Webpages: csfdb.scifi-wiki.com, IMDB, jeffvandermeer.com, sf-encyclopedia.com, Twitter, Wikipedia-EN
Used These Alternate Names: Džef Vandermer
Author Tags: fantasy (7), steampunk (6), science fiction (6), new weird (4), horror (3), time travel (2), dystopia (2), fungi (1), Libraries (1), Fairy Tales (1), original anthology (1), Judaism (1), into-movie (1), Towers (1), feminist (1), Florida (1), female protagonists (1), twins (1), Disney World (1), manatees (1) and 13 additional tags.


This is well cool. After trafilatura has done it's stuff, run meta-out.ipynb like this:

```
jupyter-nbconvert --to notebook --inplace --execute meta-out.ipynb

```

This will create a CSV file called traffy-meta.csv. Open this, edit it, add columns, change values.

When you are done, export to CSV again. To get the updated metadata embedded into the corpus,
run this notebooks:

```
jupyter-nbconvert --to notebook --inplace --execute meta-in.ipynb
```
And a `./corpus` directory will become full of the corpus JSON files with the latest metadata!

Of course, you can run other scripts to build out the CSV file with whatever values you want. Just make sure to keep the fingerprint column, so the meta-in notebook knows which of the trafilatura source files to use when it merges the text/raw-text values with the metadata.
