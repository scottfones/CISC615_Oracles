# CISC615: Assignment 5 - Test Oracles

## Test Criteria

To help identify test criteria, we proceeded through the four general methods introduced in lecture:

### Direct Verification
#### Glyph Metrics
![](https://learnopengl.com/img/in-practice/glyph.png)

The combination of a typeface and size should provide verifiable metrics. The image above, from a
Nvidia developer
[document](https://developer.download.nvidia.com/assets/gamedev/files/NV_path_rendering_FAQ.pdf),
demonstrates the glyph metrics provided by call to NV_path_rendering. This call is relayed to the
FreeType2 library, which may provide a testing constraint; this method might only apply to fonts
supported by the FreeType2 library.

Given a font f_i and metric m_i we can query for an expected value.

| Metric is Valid |  b1  |   b2  |
|-----------------|------|-------|
| m_i of f_i      | true | false |

#### Font Validation Tools

Third-party tooling, such as
[FontForge](http://designwithfontforge.com/en-US/Making_Sure_Your_Font_Works_Validation.html),
provide a mechanism for independent verification.

For a given font f_i, we want the third-party tool to be in agreement with the text editor as to the
font name.

| Text Editor and Tool Agree | b1   | b2    |
|----------------------------|------|-------|
| f_i                        | true | false |

### Redundant Computation

#### Source Replication

We can validate our fonts by replicating output from the text editor with the typeface's source
(a GitHub repo, website, etc.).

For a given font f_i, we create a sample in the text editor that expresses all possible symbols. We
then replicate the sample using the font's source. Both samples are then printed and compared to
check for equivalency.

| Samples are Equivalent | b1   | b2    |
|------------------------|------|-------|
| f_i                    | True | False |

#### Cross Validation

Identical characters, strings, and paragraphs should be created in another editor
(let's say gedit for example), and compared to the editor we are testing to make sure that all
inputs produce identical output. It is important to note that all fonts are not rendered exactly
the same on all operating systems (OS), so it would be good to have a testing environment for each
major OS, with consistent environments in each.

### Consistency Checks
#### OCR (Optical Character Recognition)
We can use and OCR tool in the following way:
 1. Create or gather a database of images of each character for each available font
 2. Create meaningful keys for the dataset; example: `times-new-roman-12pt-italic`
 3. Within the editor, display each character from each font
 4. Overlay the known good image from the database
 5. Check for outlying pixels, flag the rendered character if any exist

This oracle will not be operating system agnostic; different operating systems render fonts
using different methods. Additionally, it would be a good idea to keep environments the same,
i.e. display settings and other customization properties the same for a given test machine.

#### Font Size Changes

Starting from the smallest size and proceeding step-wise to the largest should result in monotonic
increases in the displayed font. The increases can be measured across all the characteristics listed
above in the [glyph metrics](#glyph-metrics) section.

#### Serifs

Serif fonts should produce serif output, and sans-serif fonts should produce sans-serif output. This
is particularly important for certain characters such as `a`, `g`, and `y`. We should verify that
these characters especially are rendered differently than their counterparts.

#### Case Consistency

Lower-case characters should always render as lower-case, and upper-case characters as upper-case.
We can use the OCR verification above to overlay both cases for each character, to make sure that
they do not match.

#### Monospace Character Spacing

All the monospaced fonts should render each character with uniform spacing. This means that any
paragraph consisting of the same number of characters should be the same size. We can generate
random paragraphs of characters and measure the screen size consumed by each, and verify that
they are equal.

#### Consistent Baseline

![](https://lh3.googleusercontent.com/03pPQIfJ0NxIaeH_CoX780hdTmqus7SlYy7cTKoZksZz6AXpw1xRLYzWdv_mYDer2FfW3VvqsAfvQXOBydAEda5-Eq6nRoGNZDPG=w1064-v0)

From
[Understanding typography](https://material.io/design/typography/understanding-typography.html#type-properties)

> The baseline is the invisible line upon which a line of text rests. In Material Design, the
> baseline is an important specification in measuring the vertical distance between text and an
> element.

The baseline of a font should not change across sizes. We can use OCR to verify that the bottom of
characters such as `a`, `c`, and `x` are in line, and that characters like `y`, `d`, and `b` have
the appropriate dip below the baseline given by the previous set of characters.

#### Cap Height

![](https://lh3.googleusercontent.com/GV5FQ8hady9sQU0eHxUw_6O3TqPBxd1hezBNMSyw8WfdibMPZIMqt3x4gXVJWN7exKc-MT6teHqKNGnrbXPvLYq01weNCr2NVhVb5Q=w1064-v0)

From
[Understanding typography](https://material.io/design/typography/understanding-typography.html#type-properties)

> Cap height refers to the height of a typeface’s flat capital letters (such as M or I) measured
> from the baseline. Round and pointed capital letters, such as S and A, are optically adjusted by
> being drawn with a slight overshoot above the cap height to achieve the effect of being the same
> size. Every typeface has a unique cap height.

A bit more nuanced than the baseline metric is cap height; we would have to have a data set
comprised of the _relationships_ between characters such as those listed above for each typeface.
Any character that is rendered in a way that violates these relationships would be a flag.

### Data Redundancy

We have not found a solution for generating a test oracle for data redundancy; it seems that
output checking mechanism doesn't apply to displaying fonts.

## Conclusion
Developing an oracle introduces questions of intent. An [article](https://www.developsense.com/articles/2005-01-TestingWithoutAMap.pdf) by the singer-songwriter
Michael Bolton (maybe not the same guy...), offers the mnemonic _HICCUPPS_ for oracle heuristics:
 - History
 - Image
 - Comparable products
 - Claims
 - Users' expectations
 - _The_ Product itself
 - Purpose
 - Statutes

 These heuristics suggest oracles based on expectations and consistency rather than verification
 of truth. In context, this means developing oracles that ensure correctness relative to the program
 are more desireable than chasing external verification. A user expects consistent behavior from a
 text; I know the name of the font I like, but I've never checked to make sure it's a true
 reproduction of the original.

 We do still attempt to verify the font. External validation tools, source replication, and cross
 validation all attempt to establish whether the font has been labeled and reproduced correctly.
 Individually, these tests are vulnerable to conditions outside our control. External validation
 tools might not work well, the source of a font may not exist, and cross validation is unhelpful
 when there's disagreements. It's Nietzsche all the way down, but should provide sufficient
 confidence in combination.

 The remainder of our tests are designed to ensure expected behavior from the application with a
 high degree of precision. Library calls that yield glyph metrics, optical character recognition,
 and intra-font relationships provide quantitative mechanisms to identify regressions and
 consistency. With each applying to specific properties of a font, they provide a higher degree of
 confidence as a whole.

 We believe the proposed test oracles would provide a great deal of confidence that the text editor's
 fonts are displayed correctly.