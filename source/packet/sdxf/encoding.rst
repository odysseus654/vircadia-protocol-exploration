Encoding
========

An SDXF element is a field described as either a single value of a limited number of datatypes or a struct containing additional elements within.

Attributes:
    - **Structured:** The contents of the packet can be nested to arbitrary depths
    - **Optionally Processed:** Any fields not processed by a client can be trivially skipped over by adding the size of the field *(which may not be used in Vircadia)*
    - **Selected Fields:** Each field is identified by a unique ID, so fields can be added, removed, or reordered
    - **Self-describing:** The datatype of the contents is part of the description, so the server can change the datatype without breaking the protocol

Layout
------

An element is described as:
    - **Field-ID** *(unsigned varnum)*, identifying the name of the field
    - **Flags** *(1 byte)*, identifying the type and properties
    - **Length** *(unsigned varnum)*, length of the remainder of the element (unless excluded in the flags)
    - **Content** *(variable)*, content of the element

The flags are described as:

.. role:: bk

.. raw:: html

   <script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.7.1/jquery.min.js"></script>
   <script>
     $(document).ready(function() {
       $('.bk').closest('TD').addClass('bk-parent');
     });
   </script>
   <style>
      TD.bk-parent { background-color:#000000 !important;}
   </style>

+-----+-----+-----+-----+-----+-----+-----+-----+
+  0  |  1  |  2  |  3  |  4  |  5  |  6  |  7  |
+=====+=====+=====+=====+=====+=====+=====+=====+
+       Type      |    n/a    |  S  |  A  | n/a |
+-----+-----+-----+-----+-----+-----+-----+-----+

Type (3 bits):
    - **1:** embedded elements (subtree)
    - **2:** binary (not interpreted)
    - **3:** signed integer - implemented to be between 1 and 9 bytes (storing up to an uint64) in network byte order (big endian)
    - **5:** floating point - implemented as either a single-precision (4 bytes) or double-precision (8 bytes) in network byte order (big endian)
    - **6:** string - encoded as UTF-8 (*not* nul-terminated)

**S (Short):** If this flag is set then there is no "length" for this element and the contents are a single byte following the flags

**A (Array):** If this flag is set then the element is an "array":
    - *This may not be implemented as it appears to have minimal value*
    - All the values in the array must be of identical length and type
    - After the length field an additional *varnum* is added with the number of elements.  The remaining length is divided by the number of elements to get the element size

Description
-----------

Typically one of the field ID's (ID=0 perhaps) is reserved to define the "dictionary" for the protocol, where every element within the ID=0 chunk is given its name as a string value.

This doesn't really work as well for this UDT specification as we will need a packet type for both the request for the data dictionary and the response containing the information.
This is about what I expect the exchange to look like:

    - Interface (or assignment client?) connects to the domain server
    - Domain server returns the protocol hash
    - The client does not recognize the protocol hash and requests the dictionary describing it
    - The server responds with the data dictionary containing:
        - Every packet type the domain server can read or write (defining their names and ID values)
        - Every field within each packet type (numbered independently within the packet type), possibly in the structure the data would be expected in
        - A value for ID=0 within the packet type can be used as a required version number, mismatch would cause the connection to fail with a protocol version error
    - The client stores the response to disk so it can recognize the hash in the next connection without re-requesting the dictionary

During the data phase it is expected that
    - fields containing their default values would not be included in the packet, and
    - data is stored (within reasonable processing limits) in the most compact form available (especially for integers, which should have high 00 or FF bytes stripped unless doing so changes the meaning of the number)

There are other options to explore, for instance
    - Opportunistically storing floating point numbers as integers
    - If a field value is defined outside of the struct it normally appears in, its value would apply to all the structs defined after it (until a different value is specified)