# Sphinx Docscripts

[Sphinx](https://www.sphinx-doc.org/) documentation scripts used by our projects:

- The [`.readthedocs.yaml`](./.readthedocs.yaml) file is a configuration file used by [readthedocs.org](https://readthedocs.org/).
- The [`autodoc_typehints.py`](./autodoc_typehints.py) script is a custom Sphinx plugin to automatically document types for attributes and function signatures, based on Python's type hints.
- The [`docs`](./docs/) folder contains all documentation-building scripts.

Additionally, the [`docs/intersphinx`](./docs/intersphinx/) folder contains object inventories for our most commonly used libraries, for use by the [intersphinx](https://www.sphinx-doc.org/en/master/usage/extensions/intersphinx.html) extension.

## Project Information

In [`docs/conf.py`](./docs/conf.py):

- Set `project` to the project name (e.g. `example-project`).
- Update `copyright` and `author` if required (e.g. in collabs).
- Set `release` and `version` to the desired version numbers.

In [`docs/make-api.json`](./docs/make-api.json):

- Set `pkg_name` to the package name (e.g. `"example_project"`).

In [`docs/index.rst`](./docs/index.rst):

- Set the title to the project name.
- Set the correct GitHub repository link.
- Write a brief description of the project (e.g. the description at the start of the GitHub README file).

In [`docs/api-toc.rst`](./docs/api-toc.rst):

- Set the package name in the `api` folder.
- Add any other module names (e.g. `api/PACKAGE_NAME.SCRIPT_MODULE_NAME` for a script package).

In [`docs/getting-started.rst`](./docs/getting-started.rst):

- Set the correct PyPI release link.
- Set the correct project name in the `pip install` instructions.
- Set the correct GitHub repository link.
- Write an introductory description of the project, with basic usage instructions/examples.

In [`docs/requirements.txt`](./docs/requirements.txt), add package dependencies.


## Usage

To make the documentation, run the following commands from the [`docs`](./docs) folder:

```
make api
make clean
make html
```

The [make-api-clean-html.bat](./docs/make-api-clean-html.bat) can be run from the [`docs`](./docs) folder to this effect.


## Documentation Settings

The following keys can be set in [`docs/make-api.json`](./docs/make-api.json):

```python
    "type_aliases": Dict[str, List[str]],
    "include_members": Dict[str, List[str]],
    "exclude_members": Dict[str, List[str]],
    "include_modules": List[str],
    "exclude_modules": List[str],
    "member_fullnames": Dict[str, Dict[str, str]],
    "special_class_members": Dict[str, List[str]],
```

### Type Aliases

The `type_aliases` key can be set to a dictionary, where an entry maps a module name to a list of module-level type alias names which should be explicitly included in the documentation, because Autodoc doesn't otherwise recognise them.
This setting impacts documentation more broadly, making the type aliases listed available to docstrings for other modules.
The following usage example is adapted from [`tensorsat`](https://github.com/hashberg-io/tensorsat):

```json
"type_aliases": {
    "tensorsat.diagrams": [
        "Box", "Shape", "Slot", "Block", "Port", "Wire",
        "Slots", "Ports", "Wires", "BoxClass"
    ],
    "tensorsat.lang.fin_rel": [
        "Size", "El", "Point", "NumpyUInt8Array", "FinSetShape"
    ],
    "tensorsat.lib.sat": ["Clause", "CNFDiagramMode"],
    "tensorsat.viz.diagrams": ["DiagramGraphNodeKind", "DiagramGraphNode"],
    "tensorsat.contractions": ["Contraction"],
    "tensorsat.contractions.simple": [
        "ContractionPath", "Contract2Args", "OptEinsumOptimize"
    ],
    "tensorsat.contractions.cotengra": [
        "CotengraOptimizer", "ReusableCotengraOptimizer"
    ]
}
```

### Include Members

The `include_members` key can be set to a dictionary, where an entry maps a module name to a list of module-level names which should be explicitly included in the documentation, because Autodoc wouldn't otherwise recognise them.
Typically, this is used to force Sphinx to document exported module-level variables/constants, or to include protected/private members that should nonetheless be publicly documented.
The following usage example is adapted from [`tensorsat`](https://github.com/hashberg-io/tensorsat):

```json
"include_members": {
    "tensorsat.lib.bincirc": [
        "bit",
        "not_",
        "and_",
        "or_",
        "xor_",
        "bit_0",
        "bit_1",
        "bit_unk",
        "binop_labels"
    ]
}
```

### Exclude Members

The `exclude_members` key can be set to a dictionary, where an entry maps a module name to a list of module-level type alias names which should be explicitly excluded from the documentation.
Typically, these are members which are public but shouldn't be publicly documented, e.g. because they are not user-facing.
The following usage example is adapted from [`multiformats`](https://github.com/hashberg-io/multiformats):

```json
"exclude_members": {
    "multiformats.multicodec": ["build_multicodec_tables"],
    "multiformats.multibase": ["build_multibase_tables"],
    "multiformats.multibase.raw": ["RawEncoder", "RawDecoder"],
    "multiformats.cid": ["CIDVersionNumbers", "byteslike"],
    "multiformats.multiaddr.raw": [
        "ip4_encoder", "ip4_decoder", "ip6_encoder",
        "ip6_decoder", "tcp_udp_encoder", "tcp_udp_decoder"
    ]
},
```

### Include Modules

The `include_modules` key can be set to a list of module names to be explicitly included in the documentation, because because Autodoc wouldn't otherwise recognise them.


### Exclude Modules

The `exclude_modules` key can be set to a list of module names to be explicitly excluded from the documentation.
Typically, these are utility modules which shouldn't be documented in the public API.
The following usage example is adapted from [`dag-cbor`](https://github.com/hashberg-io/dag-cbor):

```json
"exclude_modules": [
    "dag_cbor.decoding._err",
    "dag_cbor.decoding._err_utils",
    "dag_cbor.decoding._stream"
],
```

### Member Fullnames

The `member_fullnames` key can be set to a dictionary, where an entry maps a module name to a dictionary of member fullnames.
Each entry in a dictionary of member fullnames maps the local name exported by the module to the corresponding full name, qualified to indicate where the exported member was originally defined.
Typically, this is used to document constants exported by modules but defined in sub-modules.
The following usage example is adapted from [`bases`](https://github.com/hashberg-io/bases):


```json
"member_fullnames": {
    "bases": {
        "base2": "bases.encoding.base2",
        "base8": "bases.encoding.base8",
        "base10": "bases.encoding.base10",
        "base16": "bases.encoding.base16",
        "base32": "bases.encoding.base32",
        "base32hex": "bases.encoding.base32hex",
        "base32z": "bases.encoding.base32z",
        "base36": "bases.encoding.base36",
        "base64": "bases.encoding.base64",
        "base64url": "bases.encoding.base64url",
        "base10": "bases.encoding.base10",
        "base36": "bases.encoding.base36",
        "base58btc": "bases.encoding.base58btc",
        "base58flickr": "bases.encoding.base58flickr",
        "base58ripple": "bases.encoding.base58ripple",
        "base45": "bases.encoding.base45"
    }
}
```


### Special Class Members

The `special_class_members` key can be set to a dictionary, where an entry maps a class name to a list of special member names to be explicitly included in the documentation.
The following usage example is adapted from [`dag-cbor`](https://github.com/hashberg-io/dag-cbor):

```json
"special_class_members": {
    "dag_cbor.ipld.IPLDObjPath": ["__new__", "__truediv__", "__rtruediv__", "__le__", "__lt__", "__repr__", "__rshift__"]
}
```

**Note.** This example is outdated, and recent versions of the Sphinx docscripts automatically include all the special class names listed above. The full list of special class names which are automatically included is given below, and `special_class_members` is only necessary to document special names not listed there.
Special class names documented:

```python
# Comparison:
__eq__
__gt__
__ge__
__lt__
__le__
__ne__
# Unary operators:
__abs__
__not__
__inv__
__invert__
__neg__
__pos__
# Binary operators:
__add__
__and__
__concat__
__floordiv__
__lshift__
__mod__
__mul__
__matmul__
__or__
__pow__
__rshift__
__sub__
__truediv__
__xor__
# Inplace binary operators:
__iadd__
__iand__
__iconcat__
__ifloordiv__
__ilshift__
__imod__
__imul__
__imatmul__
__ior__
__ipow__
__irshift__
__isub__
__itruediv__
__ixor__
# RHS binary operators:
__radd__
__rand__
__rconcat__
__rfloordiv__
__rlshift__
__rmod__
__rmul__
__rmatmul__
__ror__
__rpow__
__rrshift__
__rsub__
__rtruediv__
__rxor__
# Conversion methods:
__bool__
__int__
__float__
__complex__
__bytes__
__str__
# Other methods:
__init__
__new__
__call__
__repr__
__index__
__contains__
__delitem__
__getitem__
__setitem__
__getattr__
__setattr__
__delattr__
__set_name__
__set__
__get__
```
