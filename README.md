draft-icnrg-harmonization
=========================

Design Choices and Differences for NDN and CCNx 1.0 Implementations of Information-Centric Networking

## Generating RFC in txt and html formats

### Prerequisites

To generate RFC, `pandoc`, `xmlproc`, and `xml2rfc` tools needs to be installed.

- On macOS with HomeBrew:

        brew install pandoc
        sudo pip install xml2rfc

- On Ubuntu Linux

        sudo apt-get install pandoc xsltproc python-pip libxslt1-dev libxml2-dev
        sudo pip install xml2rfc
