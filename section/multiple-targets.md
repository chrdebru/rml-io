## Combining multiple Targets {#multiple-targets}

Multiple Targets MAY be specified for the same triple 
in the same or multiple Term Maps.
If multiple Targets are defined in the same Term Map, 
all triples containing the generated Term are exported 
to all the specified Targets. 
In case multiple Term Maps of the same RDF triple contain more than one Target, 
the generated triples are exported to all the specified Targets.
If multiple Term Maps of an RDF triple refer multiple times to the same Target,
the RDF triple is written only once to the target to avoid duplicates.

### Multiple Targets in the same Term Map {#multiple-targets-same-term-map}

All triples are exported to all Targets, if the Term Map contains multiple 
Targets. In the example a Subject Map has three specified Targets, 
all triples with the subject `http://example.org/{@id}` are exported 
to the three specified Targets:

- Target 1: `/data/dump.nt.zip`, N-Triples serialization, Zip compression
- Target 2: `/data/dump.jsonld.tar.xz`, JSON-LD serialization, TarXz compression
- Target 3: `/data/dump.rdf.tar.gzip`, RDF/XML serialization, TarGZip compression

<pre class="ex-input">
&lt;Supergirl&gt;
  &lt;Character id="0"&gt;
    &lt;name&gt;Kara Danvers&lt;/name&gt;
    &lt;nickname&gt;Supergirl&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="1"&gt;
    &lt;name&gt;Alex Danvers&lt;/name&gt;
    &lt;nickname&gt;Sentinel&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="2"&gt;
    &lt;name&gt;J'onn J'onzz&lt;/name&gt;
    &lt;nickname&gt;Martian Manhunter&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="3"&gt;
    &lt;name&gt;Nia Nal&lt;/name&gt;
    &lt;nickname&gt;Dreamer&lt;/nickname&gt;
  &lt;/Character&gt; 
&lt;/Supergirl&gt;
</pre>

<pre class="ex-access">
&lt;#DCATSourceAccess&gt; a dcat:Dataset;
  dcat:distribution [ a dcat:Distribution;
    dcat:downloadURL "https://rml.io/specs/rml-target/Supergirl.xml";
  ];
.
</pre>

<pre class="ex-mapping">
&lt;#TriplesMap&gt; a rr:TriplesMap;
  rml:logicalSource [ a rml:LogicalSource;
    rml:source &lt;#DCATSourceAccess&gt;;
    rml:referenceFormulation ql:JSONPath;
    rml:iterator "$.[*]";
  ];
  rr:subjectMap [ a rr:SubjectMap;
    rr:template "http://example.org/{@id}";
    rml:logicalTarget <#TargetDump1>;
    rml:logicalTarget <#TargetDump2>;
    rml:logicalTarget <#TargetDump3>;
  ];
  rr:predicateObjectMap [ a rr:PredicateObjectMap;
    rr:predicateMap [ a rr:PredicateMap;
      rr:constant foaf:name;
    ];
    rr:objectMap [ a rr:ObjectMap;
      rml:reference "name/text()";
    ];
  ];
.
</pre>

<pre class="ex-target">
&lt;#TargetDump1&gt; a rmlt:LogicalTarget;
  rmlt:target &lt;#VoIDDump1&gt;;
  rmlt:serialization formats:N-Triples;
  rmlt:compression comp:zip;
.
&lt;#TargetDump2&gt; a rmlt:LogicalTarget;
  rmlt:target &lt;#VoIDDump1&gt;;
  rmlt:serialization formats:JSON-LD;
  rmlt:compression comp:tarxz;
.
&lt;#TargetDump3&gt; a rmlt:LogicalTarget;
  rmlt:target &lt;#VoIDDump1&gt;;
  rmlt:serialization formats:RDF_XML;
  rmlt:compression comp:targzip;
.
</pre>

<pre class="ex-access">
&lt;#VoIDDump1&gt; a void:Dataset ;
  void:dataDump &lt;file:///data/dump1.nt.zip&gt;;
.
&lt;#VoIDDump2&gt; a void:Dataset ;
  void:dataDump &lt;file:///data/dump2.jsonld.tar.xz&gt;;
.
&lt;#VoIDDump3&gt; a void:Dataset ;
  void:dataDump &lt;file:///data/dump3.rdf.tar.gz&gt;;
.
</pre>

<pre class="ex-output">
# file:///data/dump1.nt.zip
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Kara Danvers" .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Alex Danvers" .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "J'onn J'onzz" .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Nia Nal" .

# file:///data/dump2.jsonld.tar.xz
[
  {
    "@id": "http://example.org/0",
    "http://xmlns.com/foaf/0.1/name": [
      {
        "@value": "Kara Danvers"
      }
    ]
  },
  {
    "@id": "http://example.org/1",
    "http://xmlns.com/foaf/0.1/name": [
      {
        "@value": "Alex Danvers"
      }
    ]
  },
  {
    "@id": "http://example.org/2",
    "http://xmlns.com/foaf/0.1/name": [
      {
        "@value": "J'onn J'onzz"
      }
    ]
  },
  {
    "@id": "http://example.org/3",
    "http://xmlns.com/foaf/0.1/name": [
      {
        "@value": "Nia Nal"
      }
    ]
  }
]

# file:///data/dump3.rdf.tar.gz
&lt;?xml version="1.0" encoding="utf-8" ?&gt;
&lt;rdf:RDF xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
    xmlns:foaf="http://xmlns.com/foaf/0.1/"&gt;
  &lt;rdf:Description rdf:about="http://example.org/0"&gt;
    &lt;foaf:name>Kara Danvers&lt;/foaf:name&gt;
  &lt;/rdf:Description&gt;
  &lt;rdf:Description rdf:about="http://example.org/1"&gt;
    &lt;foaf:name>Alex Danvers&lt;/foaf:name&gt;
  &lt;/rdf:Description&gt;
  &lt;rdf:Description rdf:about="http://example.org/2"&gt;
    &lt;foaf:name>J'onn J'onzz&lt;/foaf:name&gt;
  &lt;/rdf:Description&gt;
  &lt;rdf:Description rdf:about="http://example.org/3"&gt;
    &lt;foaf:name>Nia Nal&lt;/foaf:name&gt;
  &lt;/rdf:Description&gt;
&lt;/rdf:RDF&gt;
</pre>

### Subject Map and Predicate Map {#subject-and-predicate-map}

All triples containing the subject `http://example.org/{id}`
are exported to `TargetDump1` 
and all triples containing the predicate `foaf:name`
are exported to `TargetDump2`.

<pre class="ex-input">
&lt;Supergirl&gt;
  &lt;Character id="0"&gt;
    &lt;name&gt;Kara Danvers&lt;/name&gt;
    &lt;nickname&gt;Supergirl&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="1"&gt;
    &lt;name&gt;Alex Danvers&lt;/name&gt;
    &lt;nickname&gt;Sentinel&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="2"&gt;
    &lt;name&gt;J'onn J'onzz&lt;/name&gt;
    &lt;nickname&gt;Martian Manhunter&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="3"&gt;
    &lt;name&gt;Nia Nal&lt;/name&gt;
    &lt;nickname&gt;Dreamer&lt;/nickname&gt;
  &lt;/Character&gt; 
&lt;/Supergirl&gt;
</pre>

<pre class="ex-access">
&lt;#DCATSourceAccess&gt; a dcat:Dataset;
  dcat:distribution [ a dcat:Distribution;
    dcat:downloadURL "https://rml.io/specs/rml-target/Supergirl.xml";
  ];
.
</pre>

<pre class="ex-mapping">
&lt;#TriplesMap&gt; a rr:TriplesMap;
  rml:logicalSource [ a rml:LogicalSource;
    rml:source &lt;#DCATSourceAccess&gt;;
    rml:referenceFormulation ql:JSONPath;
    rml:iterator "$.[*]";
  ];
  rr:subjectMap [ a rr:SubjectMap;
    rr:template "http://example.org/{@id}";
    rml:logicalTarget <#TargetDump1>;
  ];
  rr:predicateObjectMap [ a rr:PredicateObjectMap;
    rr:predicateMap [ a rr:PredicateMap;
      rr:constant foaf:name;
      rml:logicalTarget <#TargetDump2>;
    ];
    rr:objectMap [ a rr:ObjectMap;
      rml:reference "name/text()";
    ];
  ];
  rr:predicateObjectMap [ a rr:PredicateObjectMap;
    rr:predicateMap [ a rr:PredicateMap;
      rr:constant foaf:nickname;
    ];
    rr:objectMap [ a rr:ObjectMap;
      rml:reference "nickname/text()";
    ];
  ];
.
</pre>

<pre class="ex-target">
&lt;#TargetDump1&gt; a rmlt:LogicalTarget;
  rmlt:target &lt;#VoIDDump1&gt;;
  rmlt:serialization formats:N-Triples;
.
&lt;#TargetDump2&gt; a rmlt:LogicalTarget;
  rmlt:target &lt;#VoIDDump1&gt;;
  rmlt:serialization formats:N-Triples;
.
</pre>

<pre class="ex-access">
&lt;#VoIDDump1&gt; a void:Dataset ;
  void:dataDump &lt;file:///data/dump1.nt&gt;;
.
&lt;#VoIDDump2&gt; a void:Dataset ;
  void:dataDump &lt;file:///data/dump2.nt&gt;;
.
</pre>

<pre class="ex-output">
# file:///data/dump1.nt
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Kara Danvers" .
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Supergirl" .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Alex Danvers" .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Sentinel" .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "J'onn J'onzz" .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Martian Manhunter" .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Nia Nal" .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Dreamer" .

# file:///data/dump2.nt
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Kara Danvers" .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Alex Danvers" .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "J'onn J'onzz" .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Nia Nal" .
</pre>

### Subject Map and Object Map {#subject-and-object-map}

All triples containing the subject `http://example.org/{id}`
are exported to `TargetDump1` 
and all triples containing the object reference `name/text()`
are exported to `TargetDump2`.

<pre class="ex-input">
&lt;Supergirl&gt;
  &lt;Character id="0"&gt;
    &lt;name&gt;Kara Danvers&lt;/name&gt;
    &lt;nickname&gt;Supergirl&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="1"&gt;
    &lt;name&gt;Alex Danvers&lt;/name&gt;
    &lt;nickname&gt;Sentinel&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="2"&gt;
    &lt;name&gt;J'onn J'onzz&lt;/name&gt;
    &lt;nickname&gt;Martian Manhunter&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="3"&gt;
    &lt;name&gt;Nia Nal&lt;/name&gt;
    &lt;nickname&gt;Dreamer&lt;/nickname&gt;
  &lt;/Character&gt; 
&lt;/Supergirl&gt;
</pre>

<pre class="ex-access">
&lt;#DCATSourceAccess&gt; a dcat:Dataset;
  dcat:distribution [ a dcat:Distribution;
    dcat:downloadURL "https://rml.io/specs/rml-target/Supergirl.xml";
  ];
.
</pre>

<pre class="ex-mapping">
&lt;#TriplesMap&gt; a rr:TriplesMap;
  rml:logicalSource [ a rml:LogicalSource;
    rml:source &lt;#DCATSourceAccess&gt;;
    rml:referenceFormulation ql:JSONPath;
    rml:iterator "$.[*]";
  ];
  rr:subjectMap [ a rr:SubjectMap;
    rr:template "http://example.org/{@id}";
    rml:logicalTarget <#TargetDump1>;
  ];
  rr:predicateObjectMap [ a rr:PredicateObjectMap;
    rr:predicateMap [ a rr:PredicateMap;
      rr:constant foaf:name;
    ];
    rr:objectMap [ a rr:ObjectMap;
      rml:reference "name/text()";
      rml:logicalTarget <#TargetDump2>;
    ];
  ];
  rr:predicateObjectMap [ a rr:PredicateObjectMap;
    rr:predicateMap [ a rr:PredicateMap;
      rr:constant foaf:nickname;
    ];
    rr:objectMap [ a rr:ObjectMap;
      rml:reference "nickname/text()";
    ];
  ];
.
</pre>

<pre class="ex-target">
&lt;#TargetDump1&gt; a rmlt:LogicalTarget;
  rmlt:target &lt;#VoIDDump1&gt;;
  rmlt:serialization formats:N-Triples;
.
&lt;#TargetDump2&gt; a rmlt:LogicalTarget;
  rmlt:target &lt;#VoIDDump1&gt;;
  rmlt:serialization formats:N-Triples;
.
</pre>

<pre class="ex-access">
&lt;#VoIDDump1&gt; a void:Dataset ;
  void:dataDump &lt;file:///data/dump1.nt&gt;;
.
&lt;#VoIDDump2&gt; a void:Dataset ;
  void:dataDump &lt;file:///data/dump2.nt&gt;;
.
</pre>

<pre class="ex-output">
# file:///data/dump1.nt
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Kara Danvers" .
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Supergirl" .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Alex Danvers" .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Sentinel" .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "J'onn J'onzz" .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Martian Manhunter" .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Nia Nal" .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Dreamer" .

# file:///data/dump2.nt
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Kara Danvers" .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Alex Danvers" .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "J'onn J'onzz" .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Nia Nal" .
</pre>

### Predicate Map and Object Map {#predicate-and-object-map}

All triples containing the predicate `foaf:name`
are exported to `TargetDump1` 
and all triples containing the object reference `nickname/text()`
are exported to `TargetDump2`.

<pre class="ex-input">
&lt;Supergirl&gt;
  &lt;Character id="0"&gt;
    &lt;name&gt;Kara Danvers&lt;/name&gt;
    &lt;nickname&gt;Supergirl&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="1"&gt;
    &lt;name&gt;Alex Danvers&lt;/name&gt;
    &lt;nickname&gt;Sentinel&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="2"&gt;
    &lt;name&gt;J'onn J'onzz&lt;/name&gt;
    &lt;nickname&gt;Martian Manhunter&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="3"&gt;
    &lt;name&gt;Nia Nal&lt;/name&gt;
    &lt;nickname&gt;Dreamer&lt;/nickname&gt;
  &lt;/Character&gt; 
&lt;/Supergirl&gt;
</pre>

<pre class="ex-access">
&lt;#DCATSourceAccess&gt; a dcat:Dataset;
  dcat:distribution [ a dcat:Distribution;
    dcat:downloadURL "https://rml.io/specs/rml-target/Supergirl.xml";
  ];
.
</pre>

<pre class="ex-mapping">
&lt;#TriplesMap&gt; a rr:TriplesMap;
  rml:logicalSource [ a rml:LogicalSource;
    rml:source &lt;#DCATSourceAccess&gt;;
    rml:referenceFormulation ql:JSONPath;
    rml:iterator "$.[*]";
  ];
  rr:subjectMap [ a rr:SubjectMap;
    rr:template "http://example.org/{@id}";
  ];
  rr:predicateObjectMap [ a rr:PredicateObjectMap;
    rr:predicateMap [ a rr:PredicateMap;
      rr:constant foaf:name;
      rml:logicalTarget <#TargetDump1>;
    ];
    rr:objectMap [ a rr:ObjectMap;
      rml:reference "name/text()";
    ];
  ];
  rr:predicateObjectMap [ a rr:PredicateObjectMap;
    rr:predicateMap [ a rr:PredicateMap;
      rr:constant foaf:nickname;
    ];
    rr:objectMap [ a rr:ObjectMap;
      rml:reference "nickname/text()";
      rml:logicalTarget <#TargetDump2>;
    ];
  ];
.
</pre>

<pre class="ex-target">
&lt;#TargetDump1&gt; a rmlt:LogicalTarget;
  rmlt:target &lt;#VoIDDump1&gt;;
  rmlt:serialization formats:N-Triples;
.
&lt;#TargetDump2&gt; a rmlt:LogicalTarget;
  rmlt:target &lt;#VoIDDump1&gt;;
  rmlt:serialization formats:N-Triples;
.
</pre>

<pre class="ex-access">
&lt;#VoIDDump1&gt; a void:Dataset ;
  void:dataDump &lt;file:///data/dump1.nt&gt;;
.
&lt;#VoIDDump2&gt; a void:Dataset ;
  void:dataDump &lt;file:///data/dump2.nt&gt;;
.
</pre>

<pre class="ex-output">
# file:///data/dump1.nt
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Kara Danvers" .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Alex Danvers" .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "J'onn J'onzz" .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Nia Nal" .

# file:///data/dump2.nt
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Kara Danvers" .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Alex Danvers" .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "J'onn J'onzz" .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Nia Nal" .
</pre>

### Subject Map, Predicate Map and Object Map {#subject-predicate-and-object-map}

All triples containing the subject `http://example.org/{@id}`
are exported to `TargetDump1` 
and all triples containing the object reference `nickname/text()` or
predicate `foaf:nickname` are exported to `TargetDump2`.

<pre class="ex-input">
&lt;Supergirl&gt;
  &lt;Character id="0"&gt;
    &lt;name&gt;Kara Danvers&lt;/name&gt;
    &lt;nickname&gt;Supergirl&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="1"&gt;
    &lt;name&gt;Alex Danvers&lt;/name&gt;
    &lt;nickname&gt;Sentinel&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="2"&gt;
    &lt;name&gt;J'onn J'onzz&lt;/name&gt;
    &lt;nickname&gt;Martian Manhunter&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="3"&gt;
    &lt;name&gt;Nia Nal&lt;/name&gt;
    &lt;nickname&gt;Dreamer&lt;/nickname&gt;
  &lt;/Character&gt; 
&lt;/Supergirl&gt;
</pre>

<pre class="ex-access">
&lt;#DCATSourceAccess&gt; a dcat:Dataset;
  dcat:distribution [ a dcat:Distribution;
    dcat:downloadURL "https://rml.io/specs/rml-target/Supergirl.xml";
  ];
.
</pre>

<pre class="ex-mapping">
&lt;#TriplesMap&gt; a rr:TriplesMap;
  rml:logicalSource [ a rml:LogicalSource;
    rml:source &lt;#DCATSourceAccess&gt;;
    rml:referenceFormulation ql:JSONPath;
    rml:iterator "$.[*]";
  ];
  rr:subjectMap [ a rr:SubjectMap;
    rr:template "http://example.org/{@id}";
    rml:logicalTarget <#TargetDump1>;
  ];
  rr:predicateObjectMap [ a rr:PredicateObjectMap;
    rr:predicateMap [ a rr:PredicateMap;
      rr:constant foaf:name;
    ];
    rr:objectMap [ a rr:ObjectMap;
      rml:reference "name/text()";
    ];
  ];
  rr:predicateObjectMap [ a rr:PredicateObjectMap;
    rr:predicateMap [ a rr:PredicateMap;
      rr:constant foaf:nickname;
      rml:logicalTarget <#TargetDump2>;
    ];
    rr:objectMap [ a rr:ObjectMap;
      rml:reference "nickname/text()";
      rml:logicalTarget <#TargetDump2>;
    ];
  ];
.
</pre>

<pre class="ex-target">
&lt;#TargetDump1&gt; a rmlt:LogicalTarget;
  rmlt:target &lt;#VoIDDump1&gt;;
  rmlt:serialization formats:N-Triples;
.
&lt;#TargetDump2&gt; a rmlt:LogicalTarget;
  rmlt:target &lt;#VoIDDump1&gt;;
  rmlt:serialization formats:N-Triples;
.
</pre>

<pre class="ex-access">
&lt;#VoIDDump1&gt; a void:Dataset ;
  void:dataDump &lt;file:///data/dump1.nt&gt;;
.
&lt;#VoIDDump2&gt; a void:Dataset ;
  void:dataDump &lt;file:///data/dump2.nt&gt;;
.
</pre>

<pre class="ex-output">
# file:///data/dump1.nt
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Kara Danvers" .
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Supergirl" .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Alex Danvers" .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Sentinel" .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "J'onn J'onzz" .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Martian Manhunter" .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Nia Nal" .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Dreamer" .

# file:///data/dump2.nt
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Kara Danvers" .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Alex Danvers" .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "J'onn J'onzz" .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Nia Nal" .
</pre>

### Subject Map and Graph Map {#subject-and-graph-map}

All triples containing the subject `http://example.org/{@id}`
are exported to `TargetDump1` 
and all triples within the named graph `ex:Characters`
are exported to `TargetDump2`. 

<pre class="ex-input">
&lt;Supergirl&gt;
  &lt;Character id="0"&gt;
    &lt;name&gt;Kara Danvers&lt;/name&gt;
    &lt;nickname&gt;Supergirl&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="1"&gt;
    &lt;name&gt;Alex Danvers&lt;/name&gt;
    &lt;nickname&gt;Sentinel&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="2"&gt;
    &lt;name&gt;J'onn J'onzz&lt;/name&gt;
    &lt;nickname&gt;Martian Manhunter&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="3"&gt;
    &lt;name&gt;Nia Nal&lt;/name&gt;
    &lt;nickname&gt;Dreamer&lt;/nickname&gt;
  &lt;/Character&gt; 
&lt;/Supergirl&gt;
</pre>

<pre class="ex-access">
&lt;#DCATSourceAccess&gt; a dcat:Dataset;
  dcat:distribution [ a dcat:Distribution;
    dcat:downloadURL "https://rml.io/specs/rml-target/Supergirl.xml";
  ];
.
</pre>

<pre class="ex-mapping">
&lt;#TriplesMap&gt; a rr:TriplesMap;
  rml:logicalSource [ a rml:LogicalSource;
    rml:source &lt;#DCATSourceAccess&gt;;
    rml:referenceFormulation ql:JSONPath;
    rml:iterator "$.[*]";
  ];
  rr:subjectMap [ a rr:SubjectMap;
    rr:template "http://example.org/{@id}";
    rml:logicalTarget <#TargetDump1>;
  ];
  rr:predicateObjectMap [ a rr:PredicateObjectMap;
    rr:graphMap [ a rr:GraphMap;
      rml:logicalTarget <#TargetDump2>;
      rr:constant ex:Characters;
    ];
    rr:predicateMap [ a rr:PredicateMap;
      rr:constant foaf:name;
    ];
    rr:objectMap [ a rr:ObjectMap;
      rml:reference "name/text()";
    ];
  ];
  rr:predicateObjectMap [ a rr:PredicateObjectMap;
    rr:predicateMap [ a rr:PredicateMap;
      rr:constant foaf:nickname;
    ];
    rr:objectMap [ a rr:ObjectMap;
      rml:reference "nickname/text()";
    ];
  ];
.
</pre>

<pre class="ex-target">
&lt;#TargetDump1&gt; a rmlt:LogicalTarget;
  rmlt:target &lt;#VoIDDump1&gt;;
  rmlt:serialization formats:N-Triples;
.
&lt;#TargetDump2&gt; a rmlt:LogicalTarget;
  rmlt:target &lt;#VoIDDump1&gt;;
  rmlt:serialization formats:N-Triples;
.
</pre>

<pre class="ex-access">
&lt;#VoIDDump1&gt; a void:Dataset ;
  void:dataDump &lt;file:///data/dump1.nq&gt;;
.
&lt;#VoIDDump2&gt; a void:Dataset ;
  void:dataDump &lt;file:///data/dump2.nq&gt;;
.
</pre>

<pre class="ex-output">
# file:///data/dump1.nq
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Kara Danvers" &lt;http://example.org/Characters&gt; .
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Supergirl" _b0 .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Alex Danvers" &lt;http://example.org/Characters&gt; .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Sentinel" _b0 .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "J'onn J'onzz" &lt;http://example.org/Characters&gt; .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Martian Manhunter" _b0 .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Nia Nal" &lt;http://example.org/Characters&gt; .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Dreamer" _b0 .

# file:///data/dump2.nq
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Kara Danvers" &lt;http://example.org/Characters&gt; .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Alex Danvers" &lt;http://example.org/Characters&gt; .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "J'onn J'onzz" &lt;http://example.org/Characters&gt; .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Nia Nal" &lt;http://example.org/Characters&gt; .
</pre>

### Predicate Object Map and Graph Map {#predicate-object-and-graph-map}

All triples containing the predicate `foaf:nickname`
are exported to `TargetDump1` 
and all triples within the named graph `ex:Characters`
are exported to `TargetDump2`. 

<pre class="ex-input">
&lt;Supergirl&gt;
  &lt;Character id="0"&gt;
    &lt;name&gt;Kara Danvers&lt;/name&gt;
    &lt;nickname&gt;Supergirl&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="1"&gt;
    &lt;name&gt;Alex Danvers&lt;/name&gt;
    &lt;nickname&gt;Sentinel&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="2"&gt;
    &lt;name&gt;J'onn J'onzz&lt;/name&gt;
    &lt;nickname&gt;Martian Manhunter&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="3"&gt;
    &lt;name&gt;Nia Nal&lt;/name&gt;
    &lt;nickname&gt;Dreamer&lt;/nickname&gt;
  &lt;/Character&gt; 
&lt;/Supergirl&gt;
</pre>

<pre class="ex-access">
&lt;#DCATSourceAccess&gt; a dcat:Dataset;
  dcat:distribution [ a dcat:Distribution;
    dcat:downloadURL "https://rml.io/specs/rml-target/Supergirl.xml";
  ];
.
</pre>

<pre class="ex-mapping">
&lt;#TriplesMap&gt; a rr:TriplesMap;
  rml:logicalSource [ a rml:LogicalSource;
    rml:source &lt;#DCATSourceAccess&gt;;
    rml:referenceFormulation ql:JSONPath;
    rml:iterator "$.[*]";
  ];
  rr:subjectMap [ a rr:SubjectMap;
    rr:template "http://example.org/{@id}";
  ];
  rr:predicateObjectMap [ a rr:PredicateObjectMap;
    rr:graphMap [ a rr:GraphMap;
      rml:logicalTarget <#TargetDump2>;
      rr:constant ex:Characters;
    ];
    rr:predicateMap [ a rr:PredicateMap;
      rr:constant foaf:name;
    ];
    rr:objectMap [ a rr:ObjectMap;
      rml:reference "name/text()";
    ];
  ];
  rr:predicateObjectMap [ a rr:PredicateObjectMap;
    rr:predicateMap [ a rr:PredicateMap;
      rr:constant foaf:nickname;
      rml:logicalTarget <#TargetDump1>;
    ];
    rr:objectMap [ a rr:ObjectMap;
      rml:reference "nickname/text()";
    ];
  ];
.
</pre>

<pre class="ex-target">
&lt;#TargetDump1&gt; a rmlt:LogicalTarget;
  rmlt:target &lt;#VoIDDump1&gt;;
  rmlt:serialization formats:N-Triples;
.
&lt;#TargetDump2&gt; a rmlt:LogicalTarget;
  rmlt:target &lt;#VoIDDump1&gt;;
  rmlt:serialization formats:N-Triples;
.
</pre>

<pre class="ex-access">
&lt;#VoIDDump1&gt; a void:Dataset ;
  void:dataDump &lt;file:///data/dump1.nq&gt;;
.
&lt;#VoIDDump2&gt; a void:Dataset ;
  void:dataDump &lt;file:///data/dump2.nq&gt;;
.
</pre>

<pre class="ex-output">
# file:///data/dump1.nq
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Supergirl" _b0 .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Sentinel" _b0 .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Martian Manhunter" _b0 .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Dreamer" _b0 .

# file:///data/dump2.nq
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Kara Danvers" &lt;http://example.org/Characters&gt; .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Alex Danvers" &lt;http://example.org/Characters&gt; .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "J'onn J'onzz" &lt;http://example.org/Characters&gt; .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Nia Nal" &lt;http://example.org/Characters&gt; .
</pre>

### Language Map and Graph Map {#language-and-graph-map}

All triples containing the language tag `en` 
are exported to `TargetDump1` 
and all triples within the named graph `ex:Characters`
are exported to `TargetDump2`. 
The other triples are exported to the default target of the processor
as there is no dedicated Target assigned to triples containing 
`foaf:nickname` as predicate.

<pre class="ex-input">
&lt;Supergirl&gt;
  &lt;Character id="0"&gt;
    &lt;name&gt;Kara Danvers&lt;/name&gt;
    &lt;nickname&gt;Supergirl&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="1"&gt;
    &lt;name&gt;Alex Danvers&lt;/name&gt;
    &lt;nickname&gt;Sentinel&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="2"&gt;
    &lt;name&gt;J'onn J'onzz&lt;/name&gt;
    &lt;nickname&gt;Martian Manhunter&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="3"&gt;
    &lt;name&gt;Nia Nal&lt;/name&gt;
    &lt;nickname&gt;Dreamer&lt;/nickname&gt;
  &lt;/Character&gt; 
&lt;/Supergirl&gt;
</pre>

<pre class="ex-access">
&lt;#DCATSourceAccess&gt; a dcat:Dataset;
  dcat:distribution [ a dcat:Distribution;
    dcat:downloadURL "https://rml.io/specs/rml-target/Supergirl.xml";
  ];
.
</pre>

<pre class="ex-mapping">
&lt;#TriplesMap&gt; a rr:TriplesMap;
  rml:logicalSource [ a rml:LogicalSource;
    rml:source &lt;#DCATSourceAccess&gt;;
    rml:referenceFormulation ql:JSONPath;
    rml:iterator "$.[*]";
  ];
  rr:subjectMap [ a rr:SubjectMap;
    rr:template "http://example.org/{@id}";
  ];
  rr:predicateObjectMap [ a rr:PredicateObjectMap;
    rr:graphMap [ a rr:GraphMap;
      rml:logicalTarget <#TargetDump2>;
      rr:constant ex:Characters;
    ];
    rr:predicateMap [ a rr:PredicateMap;
      rr:constant foaf:name;
    ];
    rr:objectMap [ a rr:ObjectMap;
      rml:reference "name/text()";
      rml:languageMap [
        rml:logicalTarget <#TargetDump1>;
        rr:constant "en";
      ];
    ];
  ];
  rr:predicateObjectMap [ a rr:PredicateObjectMap;
    rr:predicateMap [ a rr:PredicateMap;
      rr:constant foaf:nickname;
    ];
    rr:objectMap [ a rr:ObjectMap;
      rml:reference "nickname/text()";
      rml:logicalTarget <#TargetDump2>;
    ];
  ];
.
</pre>

<pre class="ex-target">
&lt;#TargetDump1&gt; a rmlt:LogicalTarget;
  rmlt:target &lt;#VoIDDump1&gt;;
  rmlt:serialization formats:N-Triples;
.
&lt;#TargetDump2&gt; a rmlt:LogicalTarget;
  rmlt:target &lt;#VoIDDump1&gt;;
  rmlt:serialization formats:N-Triples;
.
</pre>

<pre class="ex-access">
&lt;#VoIDDump1&gt; a void:Dataset ;
  void:dataDump &lt;file:///data/dump1.nt&gt;;
.
&lt;#VoIDDump2&gt; a void:Dataset ;
  void:dataDump &lt;file:///data/dump2.nt&gt;;
.
</pre>

<pre class="ex-output">
# file:///data/dump1.nt
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Kara Danvers"@en &lt;http://example.org/Characters&gt; .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Alex Danvers"@en &lt;http://example.org/Characters&gt; .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "J'onn J'onzz"@en &lt;http://example.org/Characters&gt; .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Nia Nal"@en &lt;http://example.org/Characters&gt; .

# file:///data/dump2.nt
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Kara Danvers"@en &lt;http://example.org/Characters&gt; .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Alex Danvers"@en &lt;http://example.org/Characters&gt; .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "J'onn J'onzz"@en &lt;http://example.org/Characters&gt; .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Nia Nal"@en &lt;http://example.org/Characters&gt; .

# default target of processor
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Supergirl" _b0 .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Sentinel" _b0 .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Martian Manhunter" _b0 .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Dreamer" _b0 .
</pre>

### Language Map and Object Map {#language-and-object-map}

All triples containing the language tag `en` 
are exported to `TargetDump1` 
and all triples containing the object reference `nickname/text()` 
are exported to `TargetDump2`.

<pre class="ex-input">
&lt;Supergirl&gt;
  &lt;Character id="0"&gt;
    &lt;name&gt;Kara Danvers&lt;/name&gt;
    &lt;nickname&gt;Supergirl&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="1"&gt;
    &lt;name&gt;Alex Danvers&lt;/name&gt;
    &lt;nickname&gt;Sentinel&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="2"&gt;
    &lt;name&gt;J'onn J'onzz&lt;/name&gt;
    &lt;nickname&gt;Martian Manhunter&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="3"&gt;
    &lt;name&gt;Nia Nal&lt;/name&gt;
    &lt;nickname&gt;Dreamer&lt;/nickname&gt;
  &lt;/Character&gt; 
&lt;/Supergirl&gt;
</pre>

<pre class="ex-access">
&lt;#DCATSourceAccess&gt; a dcat:Dataset;
  dcat:distribution [ a dcat:Distribution;
    dcat:downloadURL "https://rml.io/specs/rml-target/Supergirl.xml";
  ];
.
</pre>

<pre class="ex-mapping">
&lt;#TriplesMap&gt; a rr:TriplesMap;
  rml:logicalSource [ a rml:LogicalSource;
    rml:source &lt;#DCATSourceAccess&gt;;
    rml:referenceFormulation ql:JSONPath;
    rml:iterator "$.[*]";
  ];
  rr:subjectMap [ a rr:SubjectMap;
    rr:template "http://example.org/{@id}";
  ];
  rr:predicateObjectMap [ a rr:PredicateObjectMap;
    rr:predicateMap [ a rr:PredicateMap;
      rr:constant foaf:name;
    ];
    rr:objectMap [ a rr:ObjectMap;
      rml:reference "name/text()";
      rml:languageMap [
        rml:logicalTarget <#TargetDump1>;
        rr:constant "en";
      ];
    ];
  ];
  rr:predicateObjectMap [ a rr:PredicateObjectMap;
    rr:predicateMap [ a rr:PredicateMap;
      rr:constant foaf:nickname;
    ];
    rr:objectMap [ a rr:ObjectMap;
      rml:reference "nickname/text()";
      rml:logicalTarget <#TargetDump2>;
    ];
  ];
.
</pre>

<pre class="ex-target">
&lt;#TargetDump1&gt; a rmlt:LogicalTarget;
  rmlt:target &lt;#VoIDDump1&gt;;
  rmlt:serialization formats:N-Triples;
.
&lt;#TargetDump2&gt; a rmlt:LogicalTarget;
  rmlt:target &lt;#VoIDDump1&gt;;
  rmlt:serialization formats:N-Triples;
.
</pre>

<pre class="ex-access">
&lt;#VoIDDump1&gt; a void:Dataset ;
  void:dataDump &lt;file:///data/dump1.nt&gt;;
.
&lt;#VoIDDump2&gt; a void:Dataset ;
  void:dataDump &lt;file:///data/dump2.nt&gt;;
.
</pre>

<pre class="ex-output">
# file:///data/dump1.nt
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Kara Danvers"@en .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Alex Danvers"@en .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "J'onn J'onzz"@en .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Nia Nal"@en .

# file:///data/dump2.nt
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Supergirl" .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Sentinel" .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Martian Manhunter" .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Dreamer" .
</pre>

### Multiple Predicate Maps {#multiple-predicate-map}

All triples containing the predicate `foaf:name`
are exported to `TargetDump1` 
and all triples containing the predicate `foaf:nickname`
are exported to `TargetDump2`.

<pre class="ex-input">
&lt;Supergirl&gt;
  &lt;Character id="0"&gt;
    &lt;name&gt;Kara Danvers&lt;/name&gt;
    &lt;nickname&gt;Supergirl&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="1"&gt;
    &lt;name&gt;Alex Danvers&lt;/name&gt;
    &lt;nickname&gt;Sentinel&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="2"&gt;
    &lt;name&gt;J'onn J'onzz&lt;/name&gt;
    &lt;nickname&gt;Martian Manhunter&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="3"&gt;
    &lt;name&gt;Nia Nal&lt;/name&gt;
    &lt;nickname&gt;Dreamer&lt;/nickname&gt;
  &lt;/Character&gt; 
&lt;/Supergirl&gt;
</pre>

<pre class="ex-access">
&lt;#DCATSourceAccess&gt; a dcat:Dataset;
  dcat:distribution [ a dcat:Distribution;
    dcat:downloadURL "https://rml.io/specs/rml-target/Supergirl.xml";
  ];
.
</pre>

<pre class="ex-mapping">
&lt;#TriplesMap&gt; a rr:TriplesMap;
  rml:logicalSource [ a rml:LogicalSource;
    rml:source &lt;#DCATSourceAccess&gt;;
    rml:referenceFormulation ql:JSONPath;
    rml:iterator "$.[*]";
  ];
  rr:subjectMap [ a rr:SubjectMap;
    rr:template "http://example.org/{@id}";
  ];
  rr:predicateObjectMap [ a rr:PredicateObjectMap;
    rr:predicateMap [ a rr:PredicateMap;
      rr:constant foaf:name;
      rml:logicalTarget <#TargetDump1>;
    ];
    rr:objectMap [ a rr:ObjectMap;
      rml:reference "name/text()";
    ];
  ];
  rr:predicateObjectMap [ a rr:PredicateObjectMap;
    rr:predicateMap [ a rr:PredicateMap;
      rr:constant foaf:nickname;
      rml:logicalTarget <#TargetDump2>;
    ];
    rr:objectMap [ a rr:ObjectMap;
      rml:reference "nickname/text()";
    ];
  ];
.
</pre>

<pre class="ex-target">
&lt;#TargetDump1&gt; a rmlt:LogicalTarget;
  rmlt:target &lt;#VoIDDump1&gt;;
  rmlt:serialization formats:N-Triples;
.
&lt;#TargetDump2&gt; a rmlt:LogicalTarget;
  rmlt:target &lt;#VoIDDump1&gt;;
  rmlt:serialization formats:N-Triples;
.
</pre>

<pre class="ex-access">
&lt;#VoIDDump1&gt; a void:Dataset ;
  void:dataDump &lt;file:///data/dump1.nt&gt;;
.
&lt;#VoIDDump2&gt; a void:Dataset ;
  void:dataDump &lt;file:///data/dump2.nt&gt;;
.
</pre>

<pre class="ex-output">
# file:///data/dump1.nt
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Kara Danvers" .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Alex Danvers" .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "J'onn J'onzz" .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Nia Nal" .

# file:///data/dump2.nt
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Supergirl" .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Sentinel" .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Martian Manhunter" .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Dreamer" .
</pre>

### Multiple Object Maps {#multiple-object-map}

All triples containing the object reference `name/text()`
are exported to `TargetDump1` 
and all triples containing the object reference `nickname/text()`
are exported to `TargetDump2`.

<pre class="ex-input">
&lt;Supergirl&gt;
  &lt;Character id="0"&gt;
    &lt;name&gt;Kara Danvers&lt;/name&gt;
    &lt;nickname&gt;Supergirl&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="1"&gt;
    &lt;name&gt;Alex Danvers&lt;/name&gt;
    &lt;nickname&gt;Sentinel&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="2"&gt;
    &lt;name&gt;J'onn J'onzz&lt;/name&gt;
    &lt;nickname&gt;Martian Manhunter&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="3"&gt;
    &lt;name&gt;Nia Nal&lt;/name&gt;
    &lt;nickname&gt;Dreamer&lt;/nickname&gt;
  &lt;/Character&gt; 
&lt;/Supergirl&gt;
</pre>

<pre class="ex-access">
&lt;#DCATSourceAccess&gt; a dcat:Dataset;
  dcat:distribution [ a dcat:Distribution;
    dcat:downloadURL "https://rml.io/specs/rml-target/Supergirl.xml";
  ];
.
</pre>

<pre class="ex-mapping">
&lt;#TriplesMap&gt; a rr:TriplesMap;
  rml:logicalSource [ a rml:LogicalSource;
    rml:source &lt;#DCATSourceAccess&gt;;
    rml:referenceFormulation ql:JSONPath;
    rml:iterator "$.[*]";
  ];
  rr:subjectMap [ a rr:SubjectMap;
    rr:template "http://example.org/{@id}";
  ];
  rr:predicateObjectMap [ a rr:PredicateObjectMap;
    rr:predicateMap [ a rr:PredicateMap;
      rr:constant foaf:name;
    ];
    rr:objectMap [ a rr:ObjectMap;
      rml:reference "name/text()";
      rml:logicalTarget <#TargetDump1>;
    ];
  ];
  rr:predicateObjectMap [ a rr:PredicateObjectMap;
    rr:predicateMap [ a rr:PredicateMap;
      rr:constant foaf:nickname;
    ];
    rr:objectMap [ a rr:ObjectMap;
      rml:reference "nickname/text()";
      rml:logicalTarget <#TargetDump2>;
    ];
  ];
.
</pre>

<pre class="ex-target">
&lt;#TargetDump1&gt; a rmlt:LogicalTarget;
  rmlt:target &lt;#VoIDDump1&gt;;
  rmlt:serialization formats:N-Triples;
.
&lt;#TargetDump2&gt; a rmlt:LogicalTarget;
  rmlt:target &lt;#VoIDDump1&gt;;
  rmlt:serialization formats:N-Triples;
.
</pre>

<pre class="ex-access">
&lt;#VoIDDump1&gt; a void:Dataset ;
  void:dataDump &lt;file:///data/dump1.nt&gt;;
.
&lt;#VoIDDump2&gt; a void:Dataset ;
  void:dataDump &lt;file:///data/dump2.nt&gt;;
.
</pre>

<pre class="ex-output">
# file:///data/dump1.nt
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Kara Danvers" .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Alex Danvers" .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "J'onn J'onzz" .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Nia Nal" .

# file:///data/dump2.nt
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Supergirl" .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Sentinel" .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Martian Manhunter" .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Dreamer" .
</pre>

### Multiple Graph Maps {#multiple-graph-map}

All triples within the named graph `ex:Characters`
are exported to `TargetDump1` 
and all triples within the named graph `ex:NickNames`
are exported to `TargetDump2`. 

<pre class="ex-input">
&lt;Supergirl&gt;
  &lt;Character id="0"&gt;
    &lt;name&gt;Kara Danvers&lt;/name&gt;
    &lt;nickname&gt;Supergirl&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="1"&gt;
    &lt;name&gt;Alex Danvers&lt;/name&gt;
    &lt;nickname&gt;Sentinel&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="2"&gt;
    &lt;name&gt;J'onn J'onzz&lt;/name&gt;
    &lt;nickname&gt;Martian Manhunter&lt;/nickname&gt;
  &lt;/Character&gt;
  &lt;Character id="3"&gt;
    &lt;name&gt;Nia Nal&lt;/name&gt;
    &lt;nickname&gt;Dreamer&lt;/nickname&gt;
  &lt;/Character&gt; 
&lt;/Supergirl&gt;
</pre>

<pre class="ex-access">
&lt;#DCATSourceAccess&gt; a dcat:Dataset;
  dcat:distribution [ a dcat:Distribution;
    dcat:downloadURL "https://rml.io/specs/rml-target/Supergirl.xml";
  ];
.
</pre>

<pre class="ex-mapping">
&lt;#TriplesMap&gt; a rr:TriplesMap;
  rml:logicalSource [ a rml:LogicalSource;
    rml:source &lt;#DCATSourceAccess&gt;;
    rml:referenceFormulation ql:JSONPath;
    rml:iterator "$.[*]";
  ];
  rr:subjectMap [ a rr:SubjectMap;
    rr:template "http://example.org/{@id}";
  ];
  rr:predicateObjectMap [ a rr:PredicateObjectMap;
    rr:graphMap [ a rr:GraphMap;
      rml:logicalTarget <#TargetDump1>;
      rr:constant ex:Characters;
    ];
    rr:predicateMap [ a rr:PredicateMap;
      rr:constant foaf:name;
    ];
    rr:objectMap [ a rr:ObjectMap;
      rml:reference "name/text()";
    ];
  ];
  rr:predicateObjectMap [ a rr:PredicateObjectMap;
    rr:graphMap [ a rr:GraphMap;
      rml:logicalTarget <#TargetDump2>;
      rr:constant ex:NickNames;
    ];
    rr:predicateMap [ a rr:PredicateMap;
      rr:constant foaf:nickname;
    ];
    rr:objectMap [ a rr:ObjectMap;
      rml:reference "nickname/text()";
    ];
  ];
.
</pre>

<pre class="ex-target">
&lt;#TargetDump1&gt; a rmlt:LogicalTarget;
  rmlt:target &lt;#VoIDDump1&gt;;
  rmlt:serialization formats:N-Triples;
.
&lt;#TargetDump2&gt; a rmlt:LogicalTarget;
  rmlt:target &lt;#VoIDDump1&gt;;
  rmlt:serialization formats:N-Triples;
.
</pre>

<pre class="ex-access">
&lt;#VoIDDump1&gt; a void:Dataset ;
  void:dataDump &lt;file:///data/dump1.nq&gt;;
.
&lt;#VoIDDump2&gt; a void:Dataset ;
  void:dataDump &lt;file:///data/dump2.nq&gt;;
.
</pre>

<pre class="ex-output">
# file:///data/dump1.nq
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Kara Danvers" &lt;http://example.org/Characters&gt; .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Alex Danvers" &lt;http://example.org/Characters&gt; .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "J'onn J'onzz" &lt;http://example.org/Characters&gt; .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/name&gt; "Nia Nal" &lt;http://example.org/Characters&gt; .

# file:///data/dump2.nq
&lt;http://example.org/0&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Supergirl" &lt;http://example.org/Nicknames&gt; .
&lt;http://example.org/1&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Sentinel" &lt;http://example.org/Nicknames&gt; .
&lt;http://example.org/2&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Martian Manhunter" &lt;http://example.org/Nicknames&gt; .
&lt;http://example.org/3&gt; &lt;http://xmlns.com/foaf/0.1/nickname&gt; "Dreamer" &lt;http://example.org/Nicknames&gt; .
</pre>
