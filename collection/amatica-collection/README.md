The amatica collection is a location for testing various archive methodologies,
including; bagit files, ISOs, and amatica output data.  In addition to the data,
we are looking at some different organization setups.

Three examples are shown below, a standalone bagit archive, an archive of a CD,
and a generic archive from amatica.

It's important that we understand how data gets imported into fedora, when we
are using archives, so each of these examples also includes the `fin` commands
to add data.

In this directory, the collection itself is nearly empty with a thumbnail, a
container for the `archvies` which includes the `hasPart` requirements for
individual archives.  The import initially only adds these components.

``` bash
# Test as a dry run
fin io import amatica . --dry-run
# Then run it.
fin io import amatica . --dry-run
```


# Standalone Bagit (Pets)

Among other locations, the amatica docs suggest that bagit files are a good way
to create your archives. We can also look at how we might look at bagit files
alone as a method of archiving. For the pets example, we'll try that. I am using
[[https://github.com/little9/gladstone][gladstone]] to create the bagit files.

``` bash
    bag='bagit-pets'
    gladstone --bagName ${bag} --originDirectory ex1-pets --cryptoMethod sha256 \
    --sourceOrganization "University of California, Davis Library - Special Collections" \
    --organizationAddress: '1 Shields Ave, Davis California 95616' \
    --contactName 'Special Collection' \
    --contactEmail 'library@ucdavis.edu' \
    --externalDescription 'Digital Management Collaborator Pets'
```

Note, that later when we discuss the Amatica created documentation, we will
revisit this notion of general metadata. Note here that this data is represented
in a xmlData section of the METS data, in the `dmdSec_4` id.  This seems to be
standard practice.

``` xml
<?xml version='1.0' encoding='UTF-8'?>
<mets:mets xmlns:mets="http://www.loc.gov/METS/" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://www.loc.gov/METS/ http://www.loc.gov/standards/mets/version111/mets.xsd">
  <mets:metsHdr CREATEDATE="2019-03-08T01:18:46"/>
  <mets:dmdSec ID="dmdSec_4">
    <mets:mdWrap MDTYPE="DC">
      <mets:xmlData>
        <dcterms:dublincore xmlns:dc="http://purl.org/dc/elements/1.1/" xmlns:dcterms="http://purl.org/dc/terms/" xsi:schemaLocation="http://purl.org/dc/terms/ http://dublincore.org/schemas/xmls/qdc/2008/02/11/dcterms.xsd">
          <dc:title>Collaborator Pets Metadata</dc:title>
          <dc:creator>Quinn Metadata Hart</dc:creator>
          <dc:subject>Metadata about Cats; Dogs</dc:subject>
          <dc:description>This is more metadata in description format</dc:description>
          <dc:publisher>UCDavis </dc:publisher>
          <dc:contributor>Quinn Hart</dc:contributor>
          <dc:date>2019-01-01</dc:date>
          <dc:type>Archival Information Package</dc:type>
          <dc:language>United States</dc:language>
        </dcterms:dublincore>
      </mets:xmlData>
    </mets:mdWrap>
  </mets:dmdSec>

```

Now we also want to add some  submission documentation.  There are amaticia
documentation that sez submission documentation should not be part of the scope
of the archived metadata format,and is not in the bag.

``` bash
bag=bagit-pets
doc=${bag}/metadata/submissionDocumentation
# Amatica sez put in the metadata after the bag creation.
mkdir -p ${doc}
cp bagit-pets/thumbnail.png ${doc}

```

At a later date, we may be interested in creating metadata for more files than
just one, so the example below shows how we might do this based on our ttl
files. It's a bit painful the first time through, but otherwise works pretty
well.  This example imagines that we have csv files of metadata that are used.
In the case below, we use the first row from the csv file.

``` bash
bag=bagit-pets
doc=${bag}/metadata/submissionDocumentation
sparql=~/apache-jena-3.9.0/bin/sparql

query="prefix : <http://schema.org/>
select ?filename ?dc_title ?dc_creator ?dc_date ?dc_publisher ?dc_subject ?dc_subject_2 ?dc_subject_3
WHERE {
 bind ('/objects' as ?filename) .
 ?c :name ?dc_title .
 ?c :creator ?dc_creator .
 ?c :keywords ?dc_subject .
 OPTIONAL { ?c :publisher ?dc_publisher . }
 OPTIONAL { ?c :datePublished ?dc_date}
 OPTIONAL { ?c :keywords ?dc_subject_2 . filter(?dc_subject != ?dc_subject_2) . }
 OPTIONAL { ?c :keywords ?dc_subject_3 .
            filter(?dc_subject != ?dc_subject_3) .
            filter(?dc_subject_2 != ?dc_subject_3)
  }
}
LIMIT 1"
${sparql} --data=bagit-pets.ttl --results=csv --query=- <<<$query |
 sed -e 's/dc_/dc./g' -e 's/_[0-9]//g' > ${bag}/metadata/metadata.csv
cat ${bag}/metadata/metadata.csv

```

## Importing to fedora

Inside the `bagit-pets` archive, we are handcrafting a simple `media` directory
to hold our two different data representations, and a separate thumbnail.png
file for the metadata.

The particular archive is the `bagit-pets` directory, stored in
as one of the `individual_archives` to be added.  In this case we want to
install it to the collection `./archives` path, hence the `-p archives`
command-line, and in the indvidual_archives directory, we only want to include
one archive, hence the `-s bagit-pets` command to limit the sub-path.  The
import command is then:

``` bash
fin io import -p archives -s bagit-pets amatica individual_archives
```

## Archive Only Example (bagit-pets-archive-only)

A nearly exact example is also included.  This example only includes the bagit
file, and does *not* allow for picking individual files.  The import is very
similar to the previous example.

``` bash
fin io import -p archives -s bagit-pets-archive-only amatica individual_archives
```

# An CD-ROM example (snep)

The SNEP example shows how a CD-ROM might be included.  In this example, we
created both an ISO of the original data, and a bagit file of the contents. The
bag-it example here came from amatica, but we are not including any of the
metadata data from that, only the archived data.  Like the bagit-pets example,
we are handcrafting a `media` container to hold the different representations of
the data, this includes both the *bagit* format the the original *iso*.

There is quite a bit of redundancy here, in that the bagit file, also includes
the original *iso* file from the CD-ROM.  If this was known, and we were sure we
wouldn't remove browseable files, we could point to that ISO within the unpacked
bag directly.

## Importing to fedora

This example is almost equivalent to the *bagit-pets* example

``` bash
fin io import -p archives -s snep amatica individual_archives
```

# Generic Amatica Metadata output

The final example is from a generic amatica bagit file.  This example shows the
steps to take if we start from a specific amatica AIP file.  This is actually
pretty similar to the `bagit-pets` example, save we did not create the AIP file.

Basically, the idea would be to create a new directory, drop in the 7z file.
Unzip if if we want to include per file browsing, and then add some basic
metadata and import the work.  The main difference between this and the above
examples, is that we don't worry about creating any special associatedMedia
links. This is somewhat more simple, and actually closer to the idea of the way
the `ucdlib:BagOfFiles` is expected to work.

There are a few places where we could get the metadata.  Similar to the
`bagit-ets` example, the dmdSec_4 should have the bagit metadata, though it may
be missing (as below)

``` xml
  <mets:dmdSec ID="dmdSec_4">
    <mets:mdWrap MDTYPE="PREMIS:OBJECT">
      <mets:xmlData>
        <premis:object xmlns:premis="http://www.loc.gov/premis/v3" xsi:type="premis:intellectualEntity" xsi:schemaLocation="http://www.loc.gov/premis/v3 http://www.loc.gov/standards/premis/v3/premis.xsd" version="3.0">
          <premis:objectIdentifier>
            <premis:objectIdentifierType>UUID</premis:objectIdentifierType>
            <premis:objectIdentifierValue>5e886546-34f9-4ba4-a9c7-964f20fe8ed6</premis:objectIdentifierValue>
          </premis:objectIdentifier>
          <premis:originalName>%transferDirectory%objects/the_ridge/</premis:originalName>
        </premis:object>
      </mets:xmlData>
    </mets:mdWrap>
  </mets:dmdSec>

```

In addition, where we added transfer metadata (like the archive thumbnail) after
the bagit creation above, in this instance, we need to discover that in the
unzipped file.  This the exact path for this is not clear, it should be similar
to
'archive_name/archive_identifier_name/data/objects/submissionDocumentation/.../image_file.jpg',
in this particular case it's
`D-124/D-124-abde9aa8-038d-4ee0-8e55-510507b7f8a4/data/objects/submissionDocumentation/transfer-D-124-583906ac-9d10-4938-893c-ccb3911dac4a/the_ridge_assetFront.jpg`.

## Importing to fedora

``` bash
fin io import -p archives -s D-124 amatica individual_archives
```
