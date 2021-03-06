=============================
 pyes - Python ElasticSearch
=============================

:Web: http://pypi.python.org/pypi/pyes/
:Download: http://pypi.python.org/pypi/pyes/
:Source: http://github.com/aparo/pyes/
:Keywords: search, elastisearch, distribute search

--

pyes is a connector to use elasticsearch from python.

This version requires elasticsearch 0.15 or above.

Features
========

- Thrift/HTTP protocols
- Bulk insert/delete
- Index management
- Every search query types
- Facet Support
- Geolocalization support
- Highlighting
- Percolator
- River support

Connecting
==========

These function are taken from pycassa.

Import the module:

    >>> import pyes

pyes is able to use standard http or thrift protocol. If your port starts with "92" http protocol is used, otherwise thrift.


For a single connection (which is _not_ thread-safe), pass a list of servers.

For thrift:

    >>> conn = pyes.ES() # Defaults to connecting to the server at '127.0.0.1:9500'
    >>> conn = pyes.ES(['127.0.0.1:9500'])

For http:

    >>> conn = pyes.ES(['127.0.0.1:9200'])

Connections are robust to server failures. Upon a disconnection, it will attempt to connect to each server in the list in turn. If no server is available, it will raise a NoServerAvailable exception.

Timeouts are also supported and should be used in production to prevent a thread from freezing while waiting for the server to return.

    >>> conn = pyes.ES(timeout=3.5) # 3.5 second timeout
    (Make some pyes calls and the connection to the server suddenly becomes unresponsive.)

    Traceback (most recent call last):
    ...
    pyes.connection.NoServerAvailable

Note that this only handles socket timeouts. 


Usage
=====

Creating a connection:

    >>> from pyes import *
    >>> conn = ES('127.0.0.1:9500')

Deleting an index:

    >>> try:
    >>>     conn.delete_index("test-index")
    >>> except:
    >>>     pass

(an exception is fored if the index is not present)

Create an index:

    >>> conn.create_index("test-index")

Creating a mapping:

    >>> mapping = { u'parsedtext': {'boost': 1.0,
    >>>                  'index': 'analyzed',
    >>>                  'store': 'yes',
    >>>                  'type': u'string',
    >>>                  "term_vector" : "with_positions_offsets"},
    >>>          u'name': {'boost': 1.0,
    >>>                     'index': 'analyzed',
    >>>                     'store': 'yes',
    >>>                     'type': u'string',
    >>>                     "term_vector" : "with_positions_offsets"},
    >>>          u'title': {'boost': 1.0,
    >>>                     'index': 'analyzed',
    >>>                     'store': 'yes',
    >>>                     'type': u'string',
    >>>                     "term_vector" : "with_positions_offsets"},
    >>>          u'pos': {'store': 'yes',
    >>>                     'type': u'integer'},
    >>>          u'uuid': {'boost': 1.0,
    >>>                    'index': 'not_analyzed',
    >>>                    'store': 'yes',
    >>>                    'type': u'string'}}
    >>> conn.put_mapping("test-type", {'properties':mapping}, ["test-index"])

Index some documents:

    >>> conn.index({"name":"Joe Tester", "parsedtext":"Joe Testere nice guy", "uuid":"11111", "position":1}, "test-index", "test-type", 1)
    >>> conn.index({"name":"Bill Baloney", "parsedtext":"Joe Testere nice guy", "uuid":"22222", "position":2}, "test-index", "test-type", 2)

Refresh an index:

    >>> conn.refresh(["test-index"])

Execute a query

    >>> q = TermQuery("name", "joe")
    >>> results = self.conn.search(query = q)

Iterate on results:

    >>> for r in results:
    >>>    print r

For more examples looks at the tests.


Changelog
=========

v. 0.17.0:

           API BREAKING: Added new searcher iterator API. (To use the old code rename ".search" in ".search_raw")

           Tests refactory.

TODO
----

- add coverage
- add jython native client protocol

License
=======

This software is licensed under the ``New BSD License``. See the ``LICENSE``
file in the top distribution directory for the full license text.

.. # vim: syntax=rst expandtab tabstop=4 shiftwidth=4 shiftround
