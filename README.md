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

This test should be complete across metrics as we test every metric for each font. It would be
incomplete if we aren't able to rely on library calls to provide the necessary metrics for all
fonts included in the text editor. In such a situation, this test should be abandoned. It may be
possible to print a sample and derive the metrics from the output. However, this would be tedious
and likely lack the precision necessary to be worthwhile.

This test criteria is also disjoint as the value of an individual test can not be both valid 
and invalid.

#### Font Validation Tools

Third-party tooling, such as 
[FontForge](http://designwithfontforge.com/en-US/Making_Sure_Your_Font_Works_Validation.html),
provide a mechanism for independent verification.

For a given font f_i, we want the third-party tool to be in agreement with the text editor as to the
font name.

| Text Editor and Tool Agree | b1   | b2    |
|----------------------------|------|-------|
| f_i                        | true | false |

This test is complete, assuming the tool in question is capable of validating all fonts offered by
the text editor. It is also disjoint as the text editor and tool will either agree or disagree as to
the font's identification.

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

This test is complete across fonts as a sample of each font is generated. It would be incomplete if
the source of a font no longer existed or could not be uniquely identified. In these cases, the most
reputable source for a font could be used. The test is also disjoint as the printed samples can not 
be both equivalent and not equivalent.

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
As outlined in lecture, every test has its pitfalls.

Some tests could fail while the text editor was demonstrating valid behavior. Reproducing a sample 
of the font from its source could yield incongruent output, but the source may have altered their
spec over time. They may now offer narrow, wide, and regular versions of their font with no clear
equivalent to the text editor. The problem becomes worse when the tests require the comparison of
printouts. This introduces variations beyond the scope of the text editor. Likewise, color and
layout options can differ across operating systems and introduce unwanted sources of error.

The font validation tool may have chosen to implement deep learning, where inadequate training or
large tolerances could result in a font being misidentified.

Other tests could pass while the text editor was demonstrating invalid behavior. Our known good
examples could become outdated if a text editor font is updated, but new samples aren't produced.

However, the test suite as a whole would produce a very high level of confidence. While each test
adds another layer of validation, the whole is greater than the parts. There are caveats to each
test, but the likelihood of the tests passing and the application behavior being invalid seems
incredibly remote.