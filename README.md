Bulk Edit (module for Omeka S)
==============================

> __New versions of this module and support for Omeka S version 3.0 and above
> are available on [GitLab], which seems to respect users and privacy better
> than the previous repository.__

[Bulk Edit] is a module for [Omeka S] that adds tools to bulk edit resources in
order to modify or to clean them.

Current processes are:

- Modify language codes
- Remove duplicate values
- Remove all trailing white spaces
- Replace value of a property (directly or via regex)
- Remove the literal value of a property
- Prepend or append a string to a value of a property
- Set or remove language of a property
- Order values by language (in particular for the title)
- Set visibility of a property public or private
- Displace values from a property to another one
- Explode a value into multiple ones
- Merge two values into one
- Convert a value to another data type
- Update or remove the owner
- Update value suggest labels
- apply visibility for values
- fill label for Value Suggest values
- remove specified values
- remove specified media
- add or remove a thumbnail
- update order of media
- explode item into multiple items by media
- explode pdf into multiple images (mainly for quick display via iiif)
- Update the media html via item or media
- update media source
- Update the media type (mime-type) of a media
- update media visibility

Furthermore, values are automatically trimmed and deduplicated when a resource
is saved.


Installation
------------

### Module

See general end user documentation for [installing a module].

This module requires the module [Common], that should be installed first.

* From the zip

Download the last release [BulkEdit.zip] from the list of releases, and
uncompress it in the `modules` directory.

* From the source and for development

If the module was installed from the source, rename the name of the folder of
the module to `BulkEdit`.

Then install it like any other Omeka module and follow the config instructions.

### Libraries

If you want to explode a pdf, you need either the command `pdftoppm` (poppler)
(recommended) or `gs` (ghostscript).


Usage
-----

The tool is available via the standard bulk process in Admin > Items, Admin > Item Sets,
and Admin > Media. Simply select specific resources or all, then click Go, then
select and config the process to do.

To improve search of resources, you can use module [Advanced Search], that
allows to search by creation date, modification date, or by visibility.

The job is launched directly when specific resources are selected, and in the
background when all resources are selected.

### Cleaning metadata

#### Trim property values

Remove leading and trailing whitespaces preventively on any resource creation or
update, or curatively via the batch edit, so values will be easier to find and
to compare exactly (see [omeka/omeka-s#1258]). Note that the curative trimming
uses a regex when possible (with mysql ≥ 8.0.4 or mariadb ≥ 10.0.5). There is no
difference in most of the cases, except when there are multiple whitespace mixed
(space, tabulation, new line, end of line, etc.).

#### Specify data type of linked resources

In some cases, in particular when using resource templates with data type "resource",
linked resources are saved in the database with the generic data type "resource",
not with the specific "resourc:item", "resource:media, etc.
This process is needed to clarify output of the facets with module [Advanced Search],
lists from the module [Reference], and in some other places.

#### Clean languages

Sometime, an empty language for a value is an empty string. This option makes it
null.

#### Modify language codes

This options allows to replace all "fr" or "fre" by "fra", or any other language
code, or into an empty code. It can be limited to specific properties.

#### Deduplicate property values

Remove exact duplicated values on any new or updated resource preventively.
Note: preventive deduplication is case sensitive, but curative deduplication is
case insensitive (it uses a direct query and the Omeka database is case
insensitive by default).

### Replace values

#### Replace value of a property directly or via regex

Fill fields "Replace" and "By", specify the type of replacement (simple, html or
regex), then select the properties to update.

The mode "html" means that the original string will be checked as raw string and
as html encoded string too. For example, string `café` will be checked with
`café` and `caf&eacute;`). Be careful when simple characters are mixed with
entities, it may be difficult to replace all strings. The replacement string is
used unchanged, so it is recommended to use entities for it too.

For Unicode, use the mode "Convert badly formatted characters from Microsoft to Unicode",
without option from/to.

For regex, use standard patterns. Examples:
- Convert spaces into "-" for identifiers: pattern = `~\s+~`, replacement = `-`.
- Convert `D:20220908101423` into a normalized date: pattern = `~^D:(\d{4})(\d{2})(\d{2})~`,
  replacement = `$1-$2-$3`.

#### Remove the literal value of a property

Simply check the box "Remove string", then select the properties to update.
The string will be removed, but it can be prepended or appended with another
string. In that case, the value is kept, else it is removed.

#### Prepend or append a string

Fill fields "Prepend" and/or "Append" and select the properties to update.

#### Set or remove language of a property

Select the properties to set or remove language.
Note: all values of the selected properties are updated, so be aware of existing
languages when they are multiple.

### Remove specified media

You can remove medias of items by media-types or extensions.

### Add or remove a thumbnail

Add or remove a thumbnail from resources, with or without existing thumbnail.

### Order values

#### Order values by language

Sometime, we need that the title and the description to be displayed to be in
one language, but the value in this language is not always the first in the
metadata. This is important for the title and the description, that are
displayed in many places.

So just write the language in the order you want for the properties you want.
Values with other languages or without language will be kept after.

### Set or unset visibility of a property

Select the properties to set or unset visibility.

### Displace values from a property to another one

Select the source properties and the destination property, then process edit.
Some filters (datatype, language, string, visibility) allows to move only
selected values.

### Explode a value into multiple values

This tool is useful for an import of a csv file, where the checkbox for "multivalue"
was missed. Select the properties and the separator, that may have multiple
character.

### Merge two values as uri

Select the properties and their values will be merged two by two. This tool can
only be used when the number of values is even. When a value has already the
datatype "uri" with a label, it is not changed and all values of the property
are skipped to avoid merge issues. When two uris follow each other, the property
is skipped too. At least one value should be an url. When the label and the
values are different urls, the property is skipped. It’s recommended to check
order of values first.

### Convert datatype

Select the source datatype and the new datatype. Only some datatype are managed
currently .

When converting from an url to geographic coordinates (module [Data Type Geometry])
with a uri to [Geonames], the real coordinates are fetched.

### Fill data

#### Update or remove owner

Simply set the user to use or set "Remove user" in the select.

### Fill labels

Select the source datatype and the new datatype. Only some datatype are managed
currently .

### Explode pdf into images, mainly for iiif

To explode a pdf, you need either the command `pdftoppm` (poppler) (recommended)
or `gs` (ghostscript).

### Update media html from item or media

Select the items or medias and update media html, then update it like an item
value.

### Update media source

Update or clear the media source, for example remove the directory path for
files imported with the full path. The source may be processed via regex or a
string may be prepended or appended too. The settings are the same than the ones
used to replace values above.

### Update media types (mime-types)

Select the items or medias and set the existing and the new media type. They
should be standard ones and usually the more precise possible, like `application/tei+xml`
instead of `application/xml`.

### Update media visibility

Update media visibility according to some metadata.


TODO
----

- [x] Add conversion for custom vocab.
- [ ] Move hard-coded xml xpath into form.


Warning
-------

Use it at your own risk.

It’s always recommended to backup your files and your databases and to check
your archives regularly so you can roll back if needed.


Troubleshooting
---------------

See online issues on the [module issues] page.


License
-------

This module is published under the [CeCILL v2.1] license, compatible with
[GNU/GPL] and approved by [FSF] and [OSI].

This software is governed by the CeCILL license under French law and abiding by
the rules of distribution of free software. You can use, modify and/ or
redistribute the software under the terms of the CeCILL license as circulated by
CEA, CNRS and INRIA at the following URL "http://www.cecill.info".

As a counterpart to the access to the source code and rights to copy, modify and
redistribute granted by the license, users are provided only with a limited
warranty and the software’s author, the holder of the economic rights, and the
successive licensors have only limited liability.

In this respect, the user’s attention is drawn to the risks associated with
loading, using, modifying and/or developing or reproducing the software by the
user in light of its specific status of free software, that may mean that it is
complicated to manipulate, and that also therefore means that it is reserved for
developers and experienced professionals having in-depth computer knowledge.
Users are therefore encouraged to load and test the software’s suitability as
regards their requirements in conditions enabling the security of their systems
and/or data to be ensured and, more generally, to use and operate it in the same
conditions as regards security.

The fact that you are presently reading this means that you have had knowledge
of the CeCILL license and that you accept its terms.


Copyright
---------

* Copyright Daniel Berthereau, 2018-2025 (see [Daniel-KM])

First developed for the [Archives Henri Poincaré] of [Université de Lorraine],
then improved for various projects.


[Bulk Edit]: https://gitlab.com/Daniel-KM/Omeka-S-module-BulkEdit
[Omeka S]: https://omeka.org/s
[installing a module]: https://omeka.org/s/docs/user-manual/modules/#installing-modules
[Advanced Search]: https://gitlab.com/Daniel-KM/Omeka-S-module-AdvancedSearch
[Common]: https://gitlab.com/Daniel-KM/Omeka-S-module-Common
[omeka/omeka-s#1258]: https://github.com/omeka/omeka-s/issues/1258
[Reference]: https://gitlab.com/Daniel-KM/Omeka-S-module-Reference
[Data Type Geometry]: https://gitlab.com/Daniel-KM/Omeka-S-module-DataTypeGeometry
[Geonames]: https://www.geonames.org/
[module issues]: https://gitlab.com/Daniel-KM/Omeka-S-module-BulkEdit/-/issues
[CeCILL v2.1]: https://www.cecill.info/licences/Licence_CeCILL_V2.1-en.html
[GNU/GPL]: https://www.gnu.org/licenses/gpl-3.0.html
[FSF]: https://www.fsf.org
[OSI]: http://opensource.org
[Archives Henri Poincaré]: https://poincare.univ-lorraine.fr
[Université de Lorraine]: https://www.univ-lorraine.fr
[GitLab]: https://gitlab.com/Daniel-KM
[Daniel-KM]: https://gitlab.com/Daniel-KM "Daniel Berthereau"
