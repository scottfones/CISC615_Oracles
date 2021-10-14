# CISC615: Assignment 5 - Test Oracles

## Test Criteria

To help identify test criteria, we proceeded through the four general methods introduced in lecture:

### Direct Verification
#### Glyph Metrics
![](https://learnopengl.com/img/in-practice/glyph.png)

The combination of a typeface and size should provide verifiable metrics. The image above, from an Nvidia developer [document](https://developer.download.nvidia.com/assets/gamedev/files/NV_path_rendering_FAQ.pdf), demonstrates the glyph metrics provided by call to NV_path_rendering. This call is is relayed to the FreeType2 library, which may provide a testing constraint; this method might only applies to fonts supported by the FreeType2 library.

Given a font f_i and metric m_i we can query for an expected value.

| Metric is Valid |  b1  |   b2  |
|-----------------|------|-------|
| m_i of f_i      | true | false |

This test should be complete across metrics as we test every metric for each font. It would be incomplete if we aren't able to rely on library calls to provide the necessary metrics for all fonts included in the text editor. In such a situation, this test should be abandoned. It may be possible to print a sample and derive the metrics from the output. However, this would tedious and likely lack the precision necessary to be worthwhile.

This test criteria is also disjoint as the value of an individual test can not be both valid and invalid.

### Redundant Computation

There are several opportunities for redundant computation.

#### Font Validation Tools

Third-party tooling. such as [FontForge](http://designwithfontforge.com/en-US/Making_Sure_Your_Font_Works_Validation.html), provide a mechanism for independent verification.

For a given font f_i, we want the third-party tool to be in agreement with the text editor as to the font name.

| Text Editor and Tool Agree | b1   | b2    |
|----------------------------|------|-------|
| f_i                        | true | false |

This test is complete, assuming the tool in question is capable of validating all fonts offered by the text editor. It is also disjoint as the text editor and tool will either agree or disagree as to the font's identification.

#### Source Replication

We can validate our fonts by replicating output from the text editor with the typeface's source (a GitHub repo, website, etc.).

For a given font f_i, we create a sample in the text editor that expresses all possible symbols. We then replicate the sample using the font's source. Both samples are then printed and compared to check for equivalency.

| Samples are Equivalent | b1   | b2    |
|------------------------|------|-------|
| f_i                    | True | False |

This test is complete across fonts as a sample of each font is generated. It would be incomplete if the source of a font no longer existed or could not be uniquely identified. In these cases, the most reputable source for a font could be used. The test is also disjoint as the printed samples can not be both equivalent and not equivalent.

#### Cross Validation

The text editor should be agnostic toward its environment; documents should look and print identically across operating systems.

### Consistency Checks
#### OCR (Optical Character Recognition)
??? Right place? check meaning of model.
We can validate data as presented in the editor window against a given model.

#### Font Size Changes

Starting from the smallest size and proceeding step-wise to the largest should result in monotonic increases in the displayed font. The result should be mirrored when reversed.

#### Overlay a Known Good Example
??? Redundant Computation?
A "library" of printed examples should be kept. The output of the text editor can be verified by printing a sample and overlaying the corresponding example from the library.

#### Serifs

Serif fonts should produce serif output, and sans-serif fonts should produce sans-serif output.

#### Case Consistency

Lower-case characters should always render as lower-case, and upper-case characters as upper-case.

#### Monospace Character Spacing

All the monospaced fonts should render each character with uniform spacing.

#### Consistent Baseline

The baseline of a font should no change across sizes.

#### Cap Height

### Data Redundancy

#### Permutation Validation

#### Font Ligatures (Sin Addition)
???


## Conclusion
As outlined in lecture, every test has its pitfalls.

Some tests could fail while the text editor was demonstrating valid behavior. Reproducing a sample of the font from its source could yield incongruent output, but the source may have altered their spec over time. They may now offer narrow, wide, and regular versions of their font with no clear equivalent to the text editor. The problem becomes worse when the tests require the comparison of printouts. This introduces variations beyond the scope of the text editor. Likewise, color and layout options can differ across operating systems and introduce unwanted sources of error. The font validation tool may have chosen to implement deep learning to instigate venture capital funding. Inadequate training or large tolerances could result in a font being misidentified.

Other tests could pass while the text editor was demonstrating invalid behavior. Our known good examples could become outdated if a text editor font is updated, but new samples aren't produced.

However, the test suite as a whole would produce a very high level of confidence. While each test adds another layer of validation, the whole is greater than the parts. There are caveats to each test, but the likelihood of the tests passing and the application behavior being invalid seems incredibly remote. 