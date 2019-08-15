# Working with Managed Operators

`nsc` can be used to administrate multiple operators. Operators can be thought of as the owners of nats-servers, and fall into two categories: local and managed. The key difference, pardon the pun, is that managed operators are ones which you don't have the nkey for. An example of a managed operator is the Synadia service called NGS. Synadia has the keys.

Accounts, as represented by their JWTs, are signed by the operator. Some operators may use local copies of JWTs, others may use the [nats-account-server](../nas/README.md) to manage their JWTS. Synadia uses a custom server for their JWTs that works similarly to the open-sourced account server.

There are a few special commands when dealing with server based operators:

* Account JWTs can be pushed to the server using `nsc push`
* Account JWTs can be pulled from a server using `nsc pull`

For managed operators this push/pull behavior is built into `nsc`. Each time you edit your account JWT `nsc` will push the change to a managed operator's server and pull the signed response. If this fails the JWT on disk may not match the value on the server. You can always push or pull the account again without editing it.

The managed operator will not only sign your account JWT with its key, but may also edit the JWT to include limits to constrain your access to their NATS servers. Some operators may also add demonstration or standard imports. Generally you can remove these, although the operator gets the final call on all Account edits. As with any deployment, the managed operator doesn't track user JWTs.

To start using a managed operator you need to tell `nsc` about it. There are a couple ways to do this. First you can manually tell `nsc` to download the operator JWT using the `add operator` command:

```bash
% nsc add operator -u http://localhost:6060/jwt/v1/operator
```

The URL you pass in should be provided to you by the operator. The second way to add a managed operator is with the `init` command:

```bash
% nsc init -u http://localhost:6060/jwt/v1/operator -n alpha
```

or

```bash
% nsc init -o synadia -n alpha
```

In the second case you can use the name of an existing operator, or a well known one (currently only "synadia").

Once you add a managed operator you can add accounts to it normally, with the caveat that new accounts are pushed and pulled as described above.
