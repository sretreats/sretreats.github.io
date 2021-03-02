---
title: Markdown Syntax Guide is the templated version
date: 2019-03-11
hero: "/images/hero-2.jpg"
excerpt: Sample article showcasing basic Markdown syntax and formatting for HTML elements.
authors:
  - Hemant Negi

---

This article offers a sample of basic Markdown syntax that can be used in Hugo 
content files, also it shows whether basic HTML elements are decorated with CSS 
in a Hugo theme.
[This is a link](http://google.com)

<!--more-->

## Headings

The following HTML `<h1>`—`<h6>` elements represent six levels of section headings. `<h1>` is the highest section level while `<h6>` is the lowest.

# H1
## H2
### H3
#### H4
##### H5
###### H6

## Paragraph

Xerum, quo qui aut unt expliquam qui dolut labo. Aque venitatiusda cum, voluptionse latur sitiae dolessi aut parist aut dollo enim qui voluptate ma dolestendit peritin re plis aut quas inctum laceat est volestemque commosa as cus endigna tectur, offic to cor sequas etum rerum idem sintibus eiur? Quianimin porecus evelectur, cum que nis nust voloribus ratem aut omnimi, sitatur? Quiatem. Nam, omnis sum am facea corem alique molestrunt et eos evelece arcillit ut aut eos eos nus, sin conecerem erum fuga. Ri oditatquam, ad quibus unda veliamenimin cusam et facea ipsamus es exerum sitate dolores editium rerore eost, temped molorro ratiae volorro te reribus dolorer sperchicium faceata tiustia prat.

Itatur? Quiatae cullecum rem ent aut odis in re eossequodi nonsequ idebis ne sapicia is sinveli squiatum, core et que aut hariosam ex eat.

## Blockquotes

The blockquote element represents content that is quoted from another source, optionally with a citation which must be within a `footer` or `cite` element, and optionally with in-line changes such as annotations and abbreviations.

#### Blockquote without attribution

> Tiam, ad mint andaepu dandae nostion secatur sequo quae.
> **Note** that you can use *Markdown syntax* within a blockquote.

#### Blockquote with attribution

> Don't communicate by sharing memory, share memory by communicating.
> — <cite>Rob Pike[^1]</cite>

>[^1]: The above quote is excerpted from Rob Pike's [talk](https://www.youtube.com/watch?v=PAAkCSZUG1c) during Gopherfest, November 18, 2015.

## Tables

Tables aren't part of the core Markdown spec, but Hugo supports supports them out-of-the-box.

   Name | Age
--------|------
    Bob | 27
  Alice | 23
  Hemant| 30
  Rahuk | 22

#### Inline Markdown within tables

| Inline&nbsp;&nbsp;&nbsp;     | Markdown&nbsp;&nbsp;&nbsp;  | In&nbsp;&nbsp;&nbsp;                | Table      |
| ---------- | --------- | ----------------- | ---------- |
| *italics*  | **bold**  | ~~strikethrough~~&nbsp;&nbsp;&nbsp; | `code`     |

## Code Blocks

#### Code block with backticks

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Example HTML5 Document</title>
</head>
<body>
  <p>Test</p>
</body>
</html>
```

```yaml
apiVersion: projectcontour.io/v1
kind: HTTPProxy
metadata:
  creationTimestamp: "2020-04-13T19:44:57Z"
  generation: 2
  name: blobstore-http-proxy
  namespace: tmc-canary
  ownerReferences:
  - apiVersion: infra.tmc.eng.vmware.com/v1alpha1
    blockOwnerDeletion: true
    controller: true
    kind: S3AccessPolicy
    name: inspection-inspection-s3
    uid: 7bf8c8ec-fd93-4758-bb7e-7690f38bff4b
  resourceVersion: "661362723"
  selfLink: /apis/projectcontour.io/v1/namespaces/tmc-canary/httpproxies/blobstore-http-proxy
  uid: b5d1ed35-e29c-4014-8c16-b2db3f7fd752
spec:
  routes:
  - conditions:
    - prefix: /blobstore
    pathRewritePolicy:
      replacePrefix:
      - replacement: /
    requestHeadersPolicy:
      set:
      - name: Host
        value: cell-whitesand-aws-usw2-core-47acfezm.s3.us-west-2.amazonaws.com
    services:
    - name: s3-proxy
      port: 80
  virtualhost:
    fqdn: blob.canary.stacks.whitesand.tmc.cloud.vmware.com
    tls:
      secretName: blobstore-tls
status:
  currentStatus: valid
  description: valid HTTPProxy
```

```bash
cat asd.py
```

#### Code block with Hugo's internal highlight shortcode
{{< highlight html >}}
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Example HTML5 Document</title>
</head>
<body>
  <p>Test</p>
</body>
</html>
{{< /highlight >}}

## List Types

#### Ordered List

1. First item
2. Second item
3. Third item

#### Unordered List

* List item
* Another item
* And another item

#### Nested list

* Item
    1. First Sub-item
    2. Second Sub-item
* Second row of items.

- Item is the types of operations supported by the operator in context Item is 
  the types of operations supported by the operator in context. the types of 
  operations supported by the operator in context.
  
  - First Sub-item
  - Second Sub-item
- Second row of items.

### This is a new dummy.

1. Item
    - First Sub-item
        - Second Sub-item
        - Second Sub-item
    - Second Sub-item
3. Second row of items.

## Other Elements — abbr, sub, sup, kbd, mark

<abbr title="Graphics Interchange Format">GIF</abbr> is a bitmap image format.

H<sub>2</sub>O

X<sup>n</sup> + Y<sup>n</sup> = Z<sup>n</sup>

Press <kbd><kbd>CTRL</kbd>+<kbd>ALT</kbd>+<kbd>Delete</kbd></kbd> to end the session.

Most <mark>salamanders</mark> are nocturnal, and hunt for insects, worms, and other small creatures.
