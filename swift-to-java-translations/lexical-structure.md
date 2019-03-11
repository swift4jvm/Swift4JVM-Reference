---
description: 'https://docs.swift.org/swift-book/ReferenceManual/LexicalStructure.html'
---

# Lexical Structure

### Whitespace and Comments

Whitespace and comments behave exactly as in Swift.

### Identifiers

Identifiers follow the same rules as in Swift.

### Keywords and Punctuation

The list of keywords and reserved punctuation symbols is the same as in Swift.

### Literals

Literals follow the same syntax as in Swift. Unless an explicit type is specified for use with `ExpressibleBy*Literal`, they are mapped to the corresponding library types, which themselves are mapped to native Java types. The following table shows the default types for the four types of literals:

<table>
  <thead>
    <tr>
      <th style="text-align:left">Literal</th>
      <th style="text-align:left">Swift Type</th>
      <th style="text-align:left">Java Type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><code>42</code>, <code>0x1AF</code>, <code>0b1101</code>, <code>0o735</code>
      </td>
      <td style="text-align:left"><code>Int</code>
      </td>
      <td style="text-align:left"><code>int</code>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>3.14159</code>, <code>1.375e-3</code>, <code>0xEAp+1</code>
      </td>
      <td style="text-align:left"><code>Double</code>
      </td>
      <td style="text-align:left"><code>double</code>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>&quot;Example&quot;</code>, <code>&quot;Hello, \(world)&quot;</code>, <code>&quot;&quot;&quot;</code>
        </p>
        <p><code>multi-line</code>
        </p>
        <p><code>&quot;&quot;&quot;</code>
        </p>
      </td>
      <td style="text-align:left"><code>String</code>
      </td>
      <td style="text-align:left"><code>java.lang.String</code>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>true</code>, <code>false</code>
      </td>
      <td style="text-align:left"><code>Bool</code>
      </td>
      <td style="text-align:left"><code>boolean</code>
      </td>
    </tr>
  </tbody>
</table>### Operators

Operators follow the same rules as in Swift.

