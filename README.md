# alburnum-maas-client

Python client API library made especially for [MAAS][1].

This was created by a core MAAS developer (Gavin Panella) so ought to
just about work. It was written in his own time, and it's licensed under
the GNU Affero GPLv3 too, the same as MAAS itself.

[![Build Status](https://travis-ci.org/alburnum/alburnum-maas-client.svg?branch=master)](https://travis-ci.org/alburnum/alburnum-maas-client)
[![codecov.io](https://codecov.io/github/alburnum/alburnum-maas-client/coverage.svg?branch=master)](https://codecov.io/github/alburnum/alburnum-maas-client?branch=master)

This package was begun by Alburnum Ltd, and some of it has come from
[MAAS itself](https://code.launchpad.net/~maas-committers/maas/trunk)
upon which Canonical Ltd have the copyright. However, Alburnum Ltd
licenses its parts under the AGPLv3, and MAAS is also under the AGPLv3,
so everything should be good.


## Dependencies

You'll need the `maascli` package. It's not on PyPI unfortunately. On
Ubuntu, install the `maas-cli` system package:

    $ sudo apt-get install maas-cli

All the other dependencies are declared already in `setup.py`.


## Example code

First you need to create a *profile* with the `maas` command-line tool.
There is [documentation for that][2] on the MAAS website.

Here's a snippet that creates a tag and associates it with every node in
the MAAS:

```python
import httplib
from pprint import pprint

from alburnum.maas import bones

# Load a MAAS CLI profile. The name below (here "madagascar") should be the
# name of a profile created when using `maas login` at the command-line.
session = bones.SessionAPI.fromProfileName(options.profile)

# Create a tag if it doesn't exist.
tag_name = "foo"
try:
    tag = session.Tag.read(name=tag_name)
except bones.CallError as error:
    if error.status == HTTPStatus.NOT_FOUND:
        tag = session.Tags.new(
            name=tag_name, comment="%s's Stuff" % tag_name.capitalize())
    else:
        raise

# List all the MAAS's tags.
print(" Tags.list() ".center(50, "="))
pprint(session.Tags.list())

# Associate the tag with all nodes.
print(" Tag.update_nodes() ".center(50, "="))
pprint(session.Tag.update_nodes(
    name=tag["name"], add=[
        node["system_id"] for node in session.Nodes.list()
    ]))
```


[1]: https://maas.ubuntu.com/
[2]: https://maas.ubuntu.com/docs1.8/maascli.html#logging-in