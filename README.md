# Extracting Common Data Elements
pulling common data elements from CAP reports

And for my next trick, I will attempt to identify all the Common Data Elements (CDEs) identified by the College of American Pathologists.

The task is simple, but a bit annoying.  Take the PDFs <a href="http://www.cap.org/web/oracle/webcenter/portalapp/pagehierarchy/cancer_protocol_templates.jspx?_adf.ctrl-state=64odvofsf_43&_afrLoop=732070598957850#!">they provide</a> that outline the CDEs and turn them into JSON structures.  These JSON structures will be used to build templates later.  Here is a brief example from a PDF in the data/ folder

```
Histologic Type (Note A)
___ Adenocarcinoma (acinar, not otherwise specified) ___ Prostatic duct adenocarcinoma
___ Mucinous (colloid) adenocarcinoma
___ Signet-ring cell carcinoma
___ Adenosquamous carcinoma
___ Small cell carcinoma
___ Sarcomatoid carcinoma
___ Undifferentiated carcinoma, not otherwise specified ___ Other (specify): __________________
```

Should be turned into something like this, but I don't have any religion on the data model... yet

```
{ name: "Histologic Type"
  multiple_choice: False
  valid_choices: 
	[ 
		{name: "Adenocarcinoma (acinar, not otherwise specified)", type: Boolean}
		, {name: "Prostatic duct adenocarcinoma", type: Boolean}
		, {name: "Mucinous (colloid) adenocarcinoma", type: Boolean}
		, {name: "Signet-ring cell carcinoma", type: Boolean}
		, {name: "Adenosquamous carcinoma", type: Boolean}
		, {name: "Small cell carcinoma", type: Boolean}
		, {name: "Sarcomatoid carcinoma", type: Boolean}
		, {name: "Undifferentiated carcinoma, not otherwise specified", type: Boolean}
		, {name: "Other", type: Boolean, input: Text}
	]
}
```

There are literally hundreds of PDFs with the CDEs in them.  You can collect them all from the CAP website.  Some of the CDEs will occur across multiple forms.  This, of course, leads us to having to create a "form" object that has these as CDEs.

For instance, the sample form is "PROSTATE GLAND: Radical Prostatectomy".

So ultimately, we'll need to large JSON files, one with the CDEs and one with the report temmplates.

## Reports

I added the report summary XLS file in the data directory, it has all the mandatory headings for the reports.

## VirtualEnv

If you've never used Virtual Env before, it's not hard.  From the top level dir of the project, do

```
source env/bin/activate
pip install -r requirements.txt
```

if you add the the needed python libraries, do 

```
pip freeze > requirements.txt
```

and check in the new requirements file.

## Running Scripts

I usually set them up to be run from the project root. So you should be able to do

```
python scripts/python/extract_cde_test.py
```

and get a load of output.

## PDFMiner

For the moment, I'm using PDFMiner.  It has a [basic API](http://www.unixuser.org/~euske/python/pdfminer/programming.html)

## FHIR Options

So, the CAP formats are under license by the College of American Pathologists.  This is a pain.  HOWEVER, it turns out that HL7 also maintains the fields we need for the reports, so all we have to do is translate the pathology reports into FHIR messages.  Sounds simple...  [This page](http://hl7.org/fhir/2016May/nehta/nehta-prostate-radical.html) has some details on the prostate pathology.  Yes!
