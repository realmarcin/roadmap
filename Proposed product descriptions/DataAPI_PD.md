Note: To convert this to Markdown, Choose File -&gt; Download as -&gt;
Microsoft Word, then run Pandoc. Microsoft Word is apparently better
supported than OpenOffice or RTF (?).

pandoc -f docx -t markdown \\
DataAPIproductdescriptionforcurrentwork.docx -o DataAPI\_PD.md

Product description template
============================

[*https://github.com/kbase/roadmap/blob/master/Product%20proposal%20template.md*](https://github.com/kbase/roadmap/blob/master/Product%20proposal%20template.md)

Examples of proposals that have been stated as being acceptable
===============================================================

[*https://github.com/kbase/roadmap/blob/master/Proposed%20product%20descriptions/SDK%20Development.md*](https://github.com/kbase/roadmap/blob/master/Proposed%20product%20descriptions/SDK%20Development.md)

[*https://github.com/kbase/roadmap/blob/master/Proposed%20product%20descriptions/Metabolic%20modeling%20production%20description.md*](https://github.com/kbase/roadmap/blob/master/Proposed%20product%20descriptions/Metabolic%20modeling%20production%20description.md)

[*https://github.com/kbase/roadmap/blob/master/Proposed%20product%20descriptions/Narrative%20update%20to%20Jupyter.md*](https://github.com/kbase/roadmap/blob/master/Proposed%20product%20descriptions/Narrative%20update%20to%20Jupyter.md)

<span id="h.lgij257ys4y" class="anchor"></span>

<span id="h.ed0x817zu7rp" class="anchor"></span>KBase Data API for
Genome data

Summary
=======

Currently any software written to use KBase data must be written
explicitly against a set of data schemas using the KBase IDL schema
language. The current data schemas, which in some cases have been
adopted project-wide, are tailored to the specific needs of the services
that initially created them.As a result they are not an unbiased,
general representation of the underlying biological concepts, are not
optimal with respect to data storage and retrieval, and are not written
to respect a global set of simple rules and assumptions (e.g. using
standard external and internal identifiers, lacking reference to key
supporting KBase objects). Furthermore,ur current Workspace and IDL
interfaces expose the service code from these implementation details,
and make them "brittle" in the sense that any attempted change in the
schemas will require manually coordinated changes, presumably
simultaneously, in all system components that use that data. This
problem will be exacerbated by the potential wave of new "SDK" services
from third party developers which will have to introduce new data types
into the system and likely create unnecessary variations of existing
KBase type specs.

The KBase Data API will address this critical weakness in our system by
providing storage and schema-agnostic accessors for the data, which will
enable the platform to evolve over time by providing a layer of
abstraction over our current (and future) data stores and data schemas.
For example, API methods for accessing data for Genome related types
eliminate the need to rely directly on specific data schemas for
development. In addition, high level API methods will provide key data
operations to serve the needs of the KBase UI. Too date, KBase UI work
has been expected to create much additional code to perform basic and
other data operations, thus increasing development complexity and
slowing performance. The API is deeply tested including automated tests,
and it is versioned, and documented to provide a clear contract with
developers and users accessing data within KBase, and will address an
endemic difficulty in the current as well as all other
existingplatforms, with updating and comparing analyses through time.

Description
===========

A layer of abstraction is required to effectively hide direct data store
and schema knowledge from the average developer/user to avoid code
rewrites and redeployments for every component accessing data when
changes inevitably occur. Changes occur for a variety of reasons, e.g.
the volume of data increasing past a threshold, edits in external
formats, new requirements within KBase. The KBase Data API will provide
a single layer where data access occurs, relieving a significant burden
on developers to track, test, and optimize for type changes in the
system, and simplifying retrieval of data through API methods. The API
will allow for services, narratives, analyses etc. to become more stable
over time even when significant data or technological changes occur in
the system. The goal is that reading the API documentation should be
enough information for developers to perform tasks without needing to
have intimate knowledge of how data is stored.

Requirements
------------

The basic requirements of the product are:

-   Data type refactoring, normalization, augmenting

-   Unified set of accessors to both new (refactored) and old (legacy)
    types, where possible

-   Capability to create/upload data for the new types

-   Client/server deployment

-   Support for languages: Python, JavaScript, Perl, Java?

-   Integrated automated testing using TravisCI

-   Comprehensive documentation and examples accessible for 3rd party
    developers

-   Versioning and release management

Data type refactoring and normalization
---------------------------------------

Because the Genome concept is so central to our system and changes are
needed, it is the first candidate for developing a Data API.

In our current system we have the following concepts: a ContigSet and a
Genome. The ContigSet type represents assembled sequences, while the
Genome type represents the taxonomy and structural and functional
annotations of the assembled sequences.

We have refactored this representation into a more logical set of
fundamental abstractions: Taxon, Genome Annotation, and Assembly. It is
necessary to think about taxonomy separately from a single set of
annotations, and instead represent *Taxon* as a type, with a *Genome
Annotation* type representing the structural and functional annotation
information, and an *Assembly* type representing assembled sequences. In
addition, the Genome Annotation and Assembly types are designed to
address performance issues, can represent large Eukaryotic organism
data, and provide a more accurate and complete representation of
Eukaryotic genome data. This fundamental shift in data types would be a
significant and possibly insurmountable overhaul to our existing system
without such an API.

Unified accessors
-----------------

Provide read access to both old or new Genome types using the same API
method calls.

Data upload for new Types
-------------------------

Provide capability to create or upload data using the new types.

Client/server deployment
------------------------

Should be deployable in a distributed client/server mode, allowing the
API (server) to be made faster and better without requiring
re-deployment of UI components and services (clients) that use it.

Language support
----------------

Data API should be available from the Narrative and accessible via
Javascript for all UI components. The API scope will initially be
limited to Genome types.

Integrated Automated Testing using TravisCI
-------------------------------------------

To ensure stability and correctness, automated testing in TravisCI
(independent of a KBase installation) must be included. Because of the
many components required for a KBase installation and the time and
complexity of mocking up all the components before any tests could be
employed. A different server implementation, preferably a widely
accepted community standard implementation should be chosen that could
more easily be installed and run in TravisCI. This could serve as a path
forward for the KBase system overall to migrate to such an
implementation.

Documentation
-------------

To develop the API against the new data types, data must be loaded in
the new formats with uploaders available.

Versioning and Release Management
---------------------------------

Before a production release of the new data types, proper UI support
should be in place.

Changes in both the API and underlying object models should be
transparent and discoverable; where these changes cannot be made
backwards-compatible, errors need to be clear.

Developer Stories
=================

-   Ease of development

    -   Developer Alice wants to write a service that analyzes
        Eukaryotic genomes in KBase. Using the Workspace data store and
        existing Genome and ContigSet types, the collective data is too
        large to easily manipulate in memory; and accessing the raw JSON
        documents requires understanding how the data was stored,
        testing for available fields, and manually assembling the
        relevant information. Instead user Alice chooses the Data API or
        APIs that match the scientific mode of her inquiry. To access
        taxonomy, she uses the Taxon API; to access assembly quality and
        sequence information she uses the Assembly API; and to access
        functional and structural annotations she uses the
        GenomeAnnotation API. Starting with an object in one API, she
        can easily navigate to another API to perform more work. The
        methods are faster to develop against than the equivalent
        methods would be on the Genome and ContigSet object in the
        Workspace, they take less memory, and they are closer to the
        scientific operations actually being performed. Documentation
        for the Data API methods are available online and describe in
        plain language what is being retrieved and what kind of result
        to expect. Overall, this saves Alice time and effort in
        developing her code and allows her to concentrate on the
        scientific analysis she wants to perform. As an important bonus
        Alice’s work will have stronger guarantees of being correct and
        rerunnable in the future as the underlying KBase system and
        data evolve.

    -   Developer John wants to find all Annotations in KBase that he
        has access to that belong to a particular parent Taxon. Using
        the Workspace, he can list workspaces that are visible to him
        and then drill down into each individual workspace, filtering
        for Genome objects. Inside the Genome object are fields
        containing the scientific name and scientific lineage of
        the Genome. He needs to scan all Genomes in the system, fetch
        either the entire document or just the fields he cares about
        (which could have changed between data schema versions) and
        filter out any names or lineage that don’t match the Taxons he
        is looking for. On the other hand, using the Taxonomy API, John
        can find all child Taxa for his Taxon of interest with a single
        API call. Then for each of those he can make a single API call
        to find the Genome Annotations associated with that Taxon.

-   Representing Large Eukaryotes

    -   Developer Ann wants to perform some comparisons between several
        large eukaryotic organisms using KBase. She tries to upload the
        data using a Genbank file to the Genome type. This upload fails
        with an error that her data is too large. She then finds that
        there is a new data type that can accept large Eukaryotes called
        GenomeAnnotation and also accepts a Genbank file. She tries her
        upload again, this time selecting the GenomeAnnotation type.
        This upload is successful and creates objects in the system
        derived from the Genbank file. Ann reviews the Data API
        documentation online and determines that the same API methods
        will work for both Genome and GenomeAnnotation types, so she can
        write her code knowing that her comparison will work on her
        uploaded data and the KBase reference data. Without the new data
        types, Ann could not represent her large Eukaryotic data
        in KBase. Furthermore, without considerable extra effort, for
        the subset of data which is also available in the legacy
        objects, she could not perform an analysis against new and
        legacy data types which in essence represent the same biological
        data type.

-   Stable interfaces and System stability

    -   Developer Bob has written a service that uses the Data API. In
        the meantime, the Data API team does a minor refactor of some of
        the internals of the underlying data types for optimization. The
        result of this refactoring does \*not\* disrupt the existing
        service, because the Data API methods hide these details from
        the developer. This makes for a better experience for Bob and an
        overall more stable system.

-   Scripting power of KBase Narratives using the API

    -   User Hassan is able to use the "code cell" feature of the KBase
        Narrative to access and use the Data API in a complex analysis.
        Performing this analysis outside of KBase would be time
        consuming and involve a lot of data cleaning and manipulation
        before getting to the analysis phase. For example, KBase is able
        to store genomic data from multiple external data sources,
        including data from user uploads, and accessible through the
        same API.

-   Consistent user experience because of the API

    -   User Jamal signs into KBase, uploads his Genome data, and is
        then able to view the data in the Narrative, view it on a
        Landing page, and run methods using the data as inputs. However,
        things don’t seem to work the same everywhere. The Narrative
        viewer shows different things than the Landing Page, fields may
        be missing in one but not the other, and there have been errors
        about missing fields in the objects. Jamal doesn’t know why this
        is the case, but finds these issues troubling and decides to go
        back to just using his laptop and his standard set of
        non-KBase tools. In a few months, Jamal returns to KBase to see
        what progress has been made. While Jamal was away from KBase,
        the Data API was deployed and UI and services were modified to
        use the Data API exclusively for Genome data access. He finds
        that the Narrative viewer and Landing Page information now seem
        consistent, and methods don’t fail with missing field errors.
        Things are still not perfect for Jamal, but he is more satisfied
        and will spend more time with KBase.

Timeline
========

-   Sprint 1 7/27-15 - 8/7/15

    -   Define types that represent Taxon, Assembly, Genome Annotation
        concepts for both Microbial and Eukaryotic organisms

    -   Load sample data for the new types

    -   Return summary information about a Genome or Genome Annotation
        using the Data API

    -   Demonstrate visualization of data using the Data API inside a
        Jupyter Notebook.

    -   Present API docs using github pages

-   Sprint 2 8/17/15 - 8/28/15

    -   Additional methods for Taxon, Assembly, Genome Annotation APIs.

    -   Prototype API service for Taxon API using Apache Thrift IDL

    -   TravisCI automated testing integration

    -   100+ tests covering 3 APIs

-   Sprint 3 9/14/15 - 9/25/15

    -   Data API installed as Narrative dependency

    -   Data API library runnable from inside Narrative code cells in CI

    -   API python client tested with local Taxon API service

    -   API doc improvements

-   Sprint 4 10/2/15 - 10/16/15

    -   Data API python client accessible from development host running
        Narrative

    -   Taxon API javascript client

    -   Github pages documentation for Taxon, Assembly, Genome
        Annotation APIs

    -   Data caching using Redis

    -   Local files mock as stand-in for Workspace data store for
        testing

-   Sprint 5 10/26/15 - 11/6/15

    -   Taxon API service deployed to CI

    -   Taxon API python client talking to CI service in Narrative CI
        code cells

    -   Single deployment procedure for any Data API service

    -   Travis CI testing for API services, clients, and library

    -   Assembly API service queued for deployment to CI

    -   Data caching enabled for CI services

    -   Uploader for Assembly and GenomeAnnotation types deployed to CI

-   Sprint 6 11/16/15 - 11/25/15

    -   Genome Annotation API service queued for deployment to CI

    -   Data API Perl clients

    -   Assembly API Javascript client

-   Sprint 7 12/7/15 - 12/18/15 (paternity leave for Matt)

    -   Perl Data API clients

    -   Bugfixing

    -   Documentation updates

    -   Additional Narrative examples

-   Sprint 8 1/4/16 - 1/15/16

    -   Add methods to Genome Annotation API for retrieving UTRs and
        Exons

    -   Narrative index that links to API documentation and all
        Narrative examples

    -   More documentation updates

    -   Genome Annotation API Javascript client

-   Sprint 9 1/25/16 - 2/5/16

    -   Basic Data Landing Pages using javascript API clients for Taxon,
        Assembly, Genome Annotation

    -   Load Ensembl Plant data requested by RNA-Seq team

    -   Data API deployed to Next environment

-   Sprint 10 2/15/16 - 2/26/16

    -   Refactoring of Landing Page JS clients (repos, test code,
        import logic) to accommodate changes from UI/Narrative team

    -   Development of Genome Comparison Narrative exercising multiple
        API functions on a sample Eukaryote (A. thaliana)

-   Sprint 11 3/14/16 - 3/25/16

    -   Additional API methods based on usage feedback

    -   Additional performance testing and optimizations

    -   Bugfixes

    -   Documentation updates

    -   API versioning

    -   Release to Production environment

Test Plan
=========

A foundation of extensive, automated unit testing

-   TravisCI tests integrated with all repos

-   Code coverage for at least Python core

-   Code coverage calculated automatically and tracked with CodeCov

    -   &gt;= 70% test coverage for core Python library, including
        services

    -   &gt;= 50% test coverage for other APIs

All code is reviewed using the standard Github Pull Request (PR)
workflow

-   Code which does not pass tests is automatically rejected until it
    does

-   The status of the tests is always visible as a single colored button
    in the PR

-   Code that reduces test coverage significantly must have a good
    reason for not adding appropriate tests ("I didn't have time" is not
    a good reason)

Manual integration testing of deployed services through multiple
interfaces

-   A list of reference Eukaryotes are manually viewed through the
    Landing Pages.

-   Sample Narratives exercise the Data API to perform more complex
    analyses and validate the correctness of filtering
    and sub-selection.

Citations
=========

n/a
