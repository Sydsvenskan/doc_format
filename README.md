# Document format

The key parts of a NYP (NY Plattform / New Platform) document are:

* Basic attributes like `uri`, `uuid`, `path`, `title` and `products`.
* Links that provide information about the document's relationship to other entities: the author, subjects et.c.
* Content blocks that describes the content that should be rendered: headlines, text, preambles, images, embeds et.c.
* Metadata blocks that describes document data that isn't a part of the rendered content: open graph info, teasers, newsvalue information et.c. The metadata blocks use the same schema as content blocks.
* Properties, a list of key-value pairs where information that doesn't have a clear place in the document format can be stored when migrating from other content formats or translating between formats.

The document format is defined in a [JSON Schema](document.schema.json). There's also a [sample document](doc.json).

Two prefixes will be recurring throughout the example document for mime-style types and URI protocols, and that's `x-im/`/`im://` and `x-cmbr/`/`cmbr://`. The `im` prefix is used for types that we and InfoMaker have agreed on as shared standards; the `cmbr` prefix is used for our own types. `cmbr` is a case of vestigal naming as the NYP project had (at one point) the working title Cambrian.

## Basic attributes

```json
   "uuid" : "8606660e-06d2-4ebe-bc3a-6c17cbfb6179",
   "uri" : "im://article/8606660e-06d2-4ebe-bc3a-6c17cbfb6179",
   "path" : "2017-02-22/malmorestaurang-far-tva-stjarnor-i-michelinguiden",
   "title" : "Malmörestaurang får två stjärnor i Michelinguiden",
   "type" : "x-im/article",
   "status" : "usable",
   "source" : "writer",
   "products" : [
      "sydsvenskan"
   ],
   "created" : "2017-02-22T08:12:40Z",
   "modified" : "2017-02-22T10:37:23Z",
   "published" : "2017-02-22T09:22:07+01:00",
```

## Links

Links are pretty self-explanatory. In our taxonomy `rel:channel` is the main section/channel for an article and `rel:subject` are additional subjects. The only required attribute for a link is `rel`, and they can have data objects and child links (see the image content block for an example).

If the UUID is present and non-nil we interpret that as a promise that there is a document on the other side of the relationship. This is used in our GraphQL layer to traverse our object graph.

```json
   "links" : [
      {
         "uuid" : "be5b467a-c2d3-58d0-be1c-cadccd525db6",
         "rel" : "channel",
         "type" : "x-im/channel",
         "title" : "Dygnet runt"
      },
      {
         "title" : "Malmö",
         "type" : "x-im/channel",
         "uuid" : "b322c84f-7e97-4af6-a23a-62f61504a910",
         "rel" : "subject"
      },
      {
         "title" : "Jonas Gillberg",
         "type" : "x-im/author",
         "uuid" : "77fe8f0c-4aaf-4b5d-8369-08dac4a67878",
         "rel" : "author"
      },
      {
         "uuid" : "5c7bae59-0fce-4dbf-83d5-b5b63927bfaf",
         "rel" : "subject",
         "type" : "x-im/story",
         "title" : "Guide Michelin 2017"
      },
```

## Content

The content blocks are discrete, typed blocks of content that is rendered by our frontends. All content blocks have an ID and a type.

The most complex block here is the one for the image. Our convention for blocks that represent a document is to put the ID of that document in the `uuid` attribute of the block and add a `rel:self` link that points to and describes the other document. The data in the self-link is what's used for presentation purposes, not whatever data is on the image doc. Even though we can load documents easily in the GraphQL layer a guiding principle has been to duplicate enough data locally to present a basic and understandable version of the document, it also helps with the inevitable need to use different descriptions, crops, et.c. for the same image in different places.

Attributes in the `data` object should be reusable and non-specific. The data object schema is shared globally (between doctypes, links, and blocks) and the value types must be consistent. Internally the document is translated to statically typed data structures, and the value for an attribute cannot be `"Hello world"` in one document and then `1234` in another. Use links to express rich data structures rather than complicating or adding the data object. It is not a free-for-all JSON blob store. Changing the document structure should be always be avoided, if possible. The `data` object is the least problematic place to add stuff, but it still shouldn't be done too lightly.

The text blocks here specify `html` as the format, by this we mean simple inline styles (`<strong>`, `<em>`, et.c.) and anchor links `<a>`. At the moment this is not enforced by our editor-layer or schema, but the principle is that presentation-wise there are no guarantees fo anything except the basic stuff, and if you start putting crap there you get a talking-to.

```json
   "content" : [
      {
         "id" : "d9a0e68e272d",
         "type" : "x-cmbr/preamble",
         "data" : {
            "format" : "html",
            "text" : "Det kan vara det största som hänt Skånes krogvärld. På onsdagen blev det klart att Malmörestaurangen Vollmers tilldelas två stjärnor i den kommande Michelinguiden och blir den sjätte restaurangen i landet med den äran. Dessutom får Malmörestaurangen Sture en stjärna i guiden."
         }
      },
      {
         "id" : "MjUyLDkzLDIyMCw4",
         "uuid" : "beeea8ca-ec26-59b4-bd2d-e0b6f44c960d",
         "links" : [
            {
               "links" : [
                  {
                     "uuid" : "00000000-0000-0000-0000-000000000000",
                     "rel" : "author",
                     "title" : "Jessica Gow/TT"
                  }
               ],
               "type" : "x-im/image",
               "data" : {
                  "text" : "Karim Khouani från restaurang Sture som tilldelades en stjärna tillsammans med bröderna Mats och Ebbe Vollmer från restaurang Vollmers som fick två.",
                  "width" : 2560,
                  "height" : 1536
               },
               "uri" : "im://image/MsV6vIznJIJD1ShQ-7rFiu4y0dI.jpeg",
               "uuid" : "beeea8ca-ec26-59b4-bd2d-e0b6f44c960d",
               "rel" : "self"
            }
         ],
         "type" : "x-im/image"
      },
      {
         "id" : "paragraph-80075258d6d34c932a3a4375da8e1d7e",
         "data" : {
            "format" : "html",
            "text" : "På onsdagsförmiddagen presenterade Michelinguiden vilka skandinaviska restauranger som kommer att finnas med i deras guide för 2017. På en presskonferens på Vasateatern i Stockholm förklarade Michael Ellis från Guide Michelin att restaurangen Vollmers på Tegelgårdsgatan i Malmö kommer att ha två stjärnor i årets Michelinguide."
         },
         "type" : "x-cmbr/text"
      },
```

Block with links are used for embedding external content like f.ex. tweets and facebook posts:

```json
{
  "id": "OTUsMjE4LDUyLDIwMg",
  "links": [
    {
      "type": "x-im/tweet",
      "rel": "self",
      "uri": "im://tweet/833079315893981184",
      "url": "https://twitter.com/InvaderXan/status/833079315893981184"
    }
  ],
  "type": "x-im/socialembed"
}
```

```json
{
  "links": [
    {
      "rel": "self",
      "type": "x-im/facebook-post",
      "url": "https://www.facebook.com/BobMarley/photos/a.312248430756.183051.117533210756/10155182262050757/?type=3"
    }
  ],
  "id": "MTI3LDQwLDcwLDIyMQ",
  "type": "x-im/socialembed"
}
```

...and youtube videos:

```json
{
  "url": "https://www.youtube.com/watch?v=zw47_q9wbBE",
  "type": "x-im/youtube",
  "uri": "https://www.youtube.com/watch?v=zw47_q9wbBE",
  "id": "MjQyLDM3LDIyMSw0",
  "data": {
    "start": 0
  }
}
```

This allows us to handle embeds intelligently and not rely on pasted HTML-embed-blobs. A fontend can choose how to present a embedded resource, it could just show a link, or use some custom/native handling of youtube. Upgrading embed-code in response to third party changes is also a presentation-layer change, and doesn't require changes to data. 

## Metadata

Metadata blocks follow the exact same rules and schema as content blocks.

The newsvalue block (`x-im/newsvalue`) feeds into our algorithm-controlled frontpage & sections. In this case it specifies a lifetime (`duration`) of 86400 seconds (24h) and a editorial newsvalue scoring of `4`.

The content profile (`x-cmbr/content-profile`) that follows is our internal profiling of a piece of content where we profile articles as, among onter things, news, longread, social, and gender bias. This gets fed into our analytics and frontpage layout system.

The feature block (`x-cmbr/features`) contains per document feature flags, in this case there's a link that enables comments.

The teaser block (`x-im/teaser`) describes the teaser that should be shown for the document on f.ex. sections and the frontpage. We have plans to expand the teaser block with links to products and channels to allow for separate teasers on different sites or sections. The teaser block (with the `id` "auto-teaser") also illustrates that block IDs don't have to be random gibberish, they just have to be locally unique. 

```json
   "meta" : [
      {
         "id" : "c90f8bff8a48",
         "type" : "x-im/newsvalue",
         "data" : {
            "duration" : 86400,
            "description" : "1D",
            "score" : 4
         }
      },
      {
         "id" : "OTksMjI2LDg4LDExMw",
         "links" : [
            {
               "uri" : "cmbr://content-profile/type/news",
               "rel" : "type",
               "type" : "x-cmbr/content-profile+type"
            },
            {
               "uri" : "cmbr://content-profile/gender/neutral",
               "rel" : "gender",
               "type" : "x-cmbr/content-profile+gender"
            }
         ],
         "type" : "x-cmbr/content-profile"
      },
      {
         "id" : "MTg5LDExMSwxNDYsMTAz",
         "type" : "x-cmbr/features",
         "links" : [
            {
               "uri" : "x-cmbr://enable-comments",
               "rel" : "feature",
               "type" : "x-cmbr/feature"
            }
         ]
      },
      {
         "data" : {
            "text" : "Historiskt ögonblick i Malmös krogvärld – jubel på Vollmers",
            "width" : 2560,
            "height" : 1536
         },
         "type" : "x-im/teaser",
         "links" : [
            {
               "type" : "x-im/image",
               "rel" : "image",
               "uuid" : "beeea8ca-ec26-59b4-bd2d-e0b6f44c960d",
               "uri" : "im://image/MsV6vIznJIJD1ShQ-7rFiu4y0dI.jpeg"
            }
         ],
         "title" : "Malmörestaurang får två stjärnor i Michelinguiden ",
         "id" : "auto-teaser"
      }
   ]
```
