<!---
Copyright © 2020 Interplanetary Database Association e.V.,
BigchainDB and IPDB software contributors.
SPDX-License-Identifier: (Apache-2.0 AND CC-BY-4.0)
Code is Apache-2.0 and docs are CC-BY-4.0
--->

# Terminology

There is some specialized terminology associated with BigchainDB. To get started, you should at least know the following:

### TEST Node

A **BigchainDB node** is a machine (or logical machine) running [BigchainDB Server](https://docs.bigchaindb.com/projects/server/en/latest/introduction.html) and related software. Each node is controlled by one person or organization.

### BigchainDB Network

A set of BigchainDB nodes can connect to each other to form a **BigchainDB network**. Each node in the network runs the same software. A BigchainDB network may have additional machines to do things such as  monitoring.

### BigchainDB Consortium

The people and organizations that run the nodes in a BigchainDB network belong to a **BigchainDB consortium** (i.e. another organization). A consortium must have some sort of governance structure to make decisions. If a BigchainDB network is run by a single company, then the "consortium" is just that company.

**What's the Difference Between a BigchainDB Network and a Consortium?**

A BigchaindB network is just a bunch of connected nodes. A consortium is an organization which has a BigchainDB network, and where each node in that network has a different operator.

### Transactions

Are described in detail in `BigchainDB Transactions Spec <https://github.com/bigchaindb/BEPs/tree/master/tx-specs/>`_ .

## Permissions in BigchainDB

BigchainDB lets users control what other users can do, to some extent. That ability resembles "permissions" in the \*nix world, "privileges" in the SQL world, and "access control" in the security world.


### Permission to Spend/Transfer an Output

In BigchainDB, every output has an associated condition (crypto-condition).

To spend/transfer an unspent output, a user (or group of users) must fulfill the condition. Another way to say that is that only certain users have permission to spend the output. The simplest condition is of the form, "Only someone with the private key corresponding to this public key can spend this output." Much more elaborate conditions are possible, e.g. "To spend this output, …"

- "…anyone in the Accounting Group can sign."
- "…three of these four people must sign."
- "…either Bob must sign, or both Tom and Sylvia must sign."

Once an output has been spent, it can't be spent again: *nobody* has permission to do that. That is, BigchainDB doesn't permit anyone to "double spend" an output.


### Write Permissions

When someone builds a TRANSFER transaction, they can put an arbitrary JSON object in the ``metadata`` field (within reason; real BigchainDB networks put a limit on the size of transactions). That is, they can write just about anything they want in a TRANSFER transaction.

Does that mean there are no "write permissions" in BigchainDB? Not at all!

A TRANSFER transaction will only be valid (allowed) if its inputs fulfill some previous outputs. The conditions on those outputs will control who can build valid TRANSFER transactions. In other words, one can interpret the condition on an output as giving "write permissions" to certain users to write something into the history of the associated asset.

As a concrete example, you could use BigchainDB to write a public journal where only you have write permissions. Here's how: First you'd build a CREATE transaction with the ``asset.data`` being something like ``{"title": "The Journal of John Doe"}``, with one output. That output would have an amount 1 and a condition that only you (who has your private key) can spend that output.
Each time you want to append something to your journal, you'd build a new TRANSFER transaction with your latest entry in the ``metadata`` field, e.g.

.. code-block:: json

   {"timestamp": "1508319582",
    "entry": "I visited Marmot Lake with Jane."}

The TRANSFER transaction would have one output. That output would have an amount 1 and a condition that only you (who has your private key) can spend that output. And so on. Only you would be able to append to the history of that asset (your journal).

The same technique could be used for scientific notebooks, supply-chain records, government meeting minutes, and so on.

You could do more elaborate things too. As one example, each time someone writes a TRANSFER transaction, they give *someone else* permission to spend it, setting up a sort of writers-relay or chain letter.

.. note::

   Anyone can write any JSON (again, within reason) in the ``asset.data`` field of a CREATE transaction. They don't need permission.


### Role-Based Access Control (RBAC)

In September 2017, we published a [blog post about how one can define an RBAC sub-system on top of BigchainDB](https://blog.bigchaindb.com/role-based-access-control-for-bigchaindb-assets-b7cada491997).
At the time of writing (January 2018), doing so required the use of a plugin, so it's not possible using standard BigchainDB (which is what's available on the [IPDB Testnet](https://test.ipdb.io/>). That may change in the future.
If you're interested, `contact IPDB <contact@ipdb.global>`_.
