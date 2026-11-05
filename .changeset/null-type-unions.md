---
"@apical-ts/core-utils": patch
"@apical-ts/craft": patch
---

Generate `z.null()` for schemas declaring `type: "null"`

OpenAPI 3.1 schemas with an explicit `type: "null"` were converted to
`z.unknown()`. This broke the common nullable-reference pattern:

```yaml
oneOf:
  - $ref: "#/components/schemas/Qux"
  - type: "null"
```

which generated `exclusiveUnion([Qux, z.unknown()])`. Because every value
matches `z.unknown()`, any valid non-null value matched both branches and was
rejected by the exclusive `oneOf` check. The converter now emits `z.null()`, so
the union becomes `exclusiveUnion([Qux, z.null()])` and its inferred type is
`Qux | null`.
