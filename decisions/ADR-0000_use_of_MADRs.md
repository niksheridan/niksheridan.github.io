# Control and Logging of Decisions Using MADR

* Status: **accepted** <!-- optional -->
* Deciders: Nik Sheridan <!-- optional -->
* Date: 2019-03-13 <!-- optional -->

Technical Story: Decisions need to be auditable and documented to prevent circular discussion to encourage progression in an accesible way to all<!-- optional -->

## Context and Problem Statement

* Do 'document behemoths' act as an intimidating barrier to those who need to refer to them, making them ineffective as a means of governance?
* Can 'document behemoths' contain so much information they become brittle in their content and disguise linkage of multiple artefacts contained within them?
* Can 'complicated templates' become so time comsuming to write that they are only '*done correctly and properly*' by the chosen few?
* Should documentation in general be dissolved into easily digestable parts with familair structures where information can be found the places they are expected to be?
* Should documentation be 'easy' to create and should automation be used in it's creation?
* Should existing methods and tools be reused to create documentation, to prevent tool sprawl?
* Can specialist software (e.g. document processors or digram tool software) prevent review of important information and prohibit it's update?
* Can inconsistent documentation create confusion when they should be creating clarity?
* How critical is it that any decision is version controlled?
* Can input to documentation be liberal, but the release of it to 'production state' be conservative?
* If documentations purpose is to describe ideas, then does it by implication tolerate an *'aspect of chaos'* that fosters creativity and view itself more as a process of tempering this into something constructive and useful?

## Decision Drivers <!-- optional -->

* Monolithic documents often manifest, in practice, a lack of williness to change and a protective attitude to a singular line of throught
* Encylopedic documents are often referred by their author, but more often than not never read by their reader
* Often sections of large documents (such as chapters in printed books) are referred to as the *only bit you need to read*, which increases scope for misinterpretation of context
* Terse linked documentation (especially diagrams) tend to be used more than comprehensive text
* Text based documentation is far more manageable to version control than graphical representation
* Changes should be auditable, and releasing these to 'production' could be viewed as more important than the actions they justify and underpin
* Simplicity is strong driver for broader adoption and ongoing maintenance
* [driver 2, e.g., a force, facing concern, …]
* … <!-- numbers of drivers can vary -->

## Considered Options

* Use graphical representations that are generated using text based definitions and commit these to version control
* Use existing minimalist familiar methods of creating documents and other means of beautifying them
* Consideration shoud be given to *how small is small enough*; the minumum should be the goal as anything more is surplus to requirements, or arguably 'wastage'
* Quality is not directly proportionate to word count; arguments can be made to the opposite being true
* … <!-- numbers of options can vary -->

## Decision Outcome

Chosen option: "[option 1]", because [justification. e.g., only option, which meets k.o. criterion decision driver | which resolves force force | … | comes out best (see below)].
Markdown Architectural Decision Records within a git based respsity should be used, where possible to create decision records.
If decisions required changing, branches can be created, and approved through merge requests with a more formalised review.
Identity of contributors can be authenticated and audited through standard git controls

### Positive Consequences <!-- optional -->

* [e.g., improvement of quality attribute satisfaction, follow-up decisions required, …]
* …

### Negative Consequences <!-- optional -->

* [e.g., compromising quality attribute, follow-up decisions required, …]
* …

## Pros and Cons of the Options <!-- optional -->

### [option 1]

[example | description | pointer to more information | …] <!-- optional -->

* Good, because [argument a]
* Good, because [argument b]
* Bad, because [argument c]
* … <!-- numbers of pros and cons can vary -->

### [option 2]

[example | description | pointer to more information | …] <!-- optional -->

* Good, because [argument a]
* Good, because [argument b]
* Bad, because [argument c]
* … <!-- numbers of pros and cons can vary -->

### [option 3]

[example | description | pointer to more information | …] <!-- optional -->

* Good, because [argument a]
* Good, because [argument b]
* Bad, because [argument c]
* … <!-- numbers of pros and cons can vary -->

## Links <!-- optional -->

* [Link type] [Link to ADR] <!-- example: Refined by [ADR-0005](0005-example.md) -->
* … <!-- numbers of links can vary -->