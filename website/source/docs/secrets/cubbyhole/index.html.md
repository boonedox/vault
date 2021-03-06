---
layout: "docs"
page_title: "Secret Backend: Cubbyhole"
sidebar_current: "docs-secrets-cubbyhole"
description: |-
  The cubbyhole secret backend can store arbitrary secrets scoped to a single token.
---

# Cubbyhole Secret Backend

Name: `cubbyhole`

The `cubbyhole` secret backend is used to store arbitrary secrets within
the configured physical storage for Vault. It is mounted at the `cubbyhole/`
prefix by default and cannot be mounted elsewhere or removed.

This backend differs from the `generic` backend in that the `generic` backend's
values are accessible to any token with read privileges on that path. In this
backend, paths are scoped per token; no token can read secrets placed in
another token's cubbyhole. When the token expires, its cubbyhole is destroyed.

Also unlike the `generic` backend, because the cubbyhole's lifetime is linked
to an authentication token, there is no concept of a lease or lease TTL for
values contained in the token's cubbyhole.

Writing to a key in the `cubbyhole/` backend will replace the old value,
the sub-fields are not merged together.

## Quick Start

The `cubbyhole` backend allows for writing keys with arbitrary values.

As an example, we can write a new key "foo" to the `cubbyhole` backend, which
is mounted at `cubbyhole/`:

```
$ vault write cubbyhole/foo \
    zip=zap
Success! Data written to: cubbyhole/foo
```

This writes the key with the "zip" field set to "zap". We can test this by doing
a read:

```
$ vault read cubbyhole/foo
Key           	Value
zip           	zap
```

As expected, the value previously set is returned to us.

## API

### /cubbyhole
#### GET

<dl class="api">
  <dt>Description</dt>
  <dd>
    Retrieves the secret at the specified location.
  </dd>

  <dt>Method</dt>
  <dd>GET</dd>

  <dt>URL</dt>
  <dd>`/cubbyhole/<path>`</dd>

  <dt>Parameters</dt>
  <dd>
     None
  </dd>

  <dt>Returns</dt>
  <dd>

    ```javascript
    {
      "auth": null,
      "data": {
        "foo": "bar"
      },
      "lease_duration": 0,
      "lease_id": "",
      "renewable": false
    }
    ```

  </dd>
</dl>

#### POST/PUT

<dl class="api">
  <dt>Description</dt>
  <dd>
    Stores a secret at the specified location.
  </dd>

  <dt>Method</dt>
  <dd>POST/PUT</dd>

  <dt>URL</dt>
  <dd>`/cubbyhole/<path>`</dd>

  <dt>Parameters</dt>
  <dd>
    <ul>
      <li>
        <span class="param">(key)</span>
        <span class="param-flags">optional</span>
        A key, paired with an associated value, to be held at the
        given location. Multiple key/value pairs can be specified,
        and all will be returned on a read operation.
      </li>
    </ul>
  </dd>

  <dt>Returns</dt>
  <dd>
  A `204` response code.
  </dd>
</dl>
