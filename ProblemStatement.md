# Problem Statement: M-Ary Tree Locking and Upgrade

## Description

You are given a complete, balanced M-ary tree consisting of `N` nodes. Each node has a unique name. You must support `Q` queries of three types: **Lock, Unlock, and Upgrade**.

Each node can be locked by a user identified by an integer ID. Your task is to determine whether each query succeeds and print `true` or `false` accordingly.

## Operations

### 1. Lock(v, id)

Lock node `v` for user `id`.

The operation succeeds only if:
- Node `v` is currently unlocked.
- No ancestor of `v` is locked.
- No descendant of `v` is locked.

If any condition fails, return `false`. Otherwise, lock the node and return `true`.

### 2. Unlock(v, id)

Unlock node `v` for user `id`.

The operation succeeds only if:
- Node `v` is currently locked.
- Node `v` is locked by the same user `id`.

If either condition fails, return `false`. Otherwise, unlock the node and return `true`.

### 3. Upgrade(v, id)

Upgrade node `v` for user `id`.

The operation succeeds only if:
- Node `v` is currently unlocked.
- Node `v` has at least one locked descendant.
- Every locked descendant of `v` is locked by user `id`.
- No ancestor of `v` is locked.

If all conditions are satisfied, unlock all locked descendants of `v`, lock node `v` for user `id`, and return `true`. Otherwise, return `false`.

## Input Format

- The first line contains three integers `N`, `M`, and `Q`, representing the number of nodes, the maximum number of children per node, and the number of queries, respectively.
- The second line contains `N` space-separated strings representing the unique node names in level-order traversal.
- Each of the next `Q` lines contains three values: `operationType`, `nodeName`, and `userId`.

Operation types:
- `1` — Lock
- `2` — Unlock
- `3` — Upgrade

## Output Format

For each query, print `true` if the operation succeeds; otherwise, print `false`. Print each result on a separate line.

## Constraints and Rules

1. A node cannot be locked if any of its ancestors or descendants are locked, regardless of user ID.
2. Multiple sibling nodes can be locked simultaneously, provided their locking rules are satisfied.
3. Only the user who locked a node can unlock it.
4. An upgrade automatically unlocks all locked descendants and locks the specified node.
5. An upgrade cannot be performed on an already locked node or a node with a locked ancestor.
6. An upgrade requires at least one locked descendant, and all locked descendants must belong to the specified user.

## Example

### Input

```text
7 2 5
A B C D E F G
1 D 1
1 E 1
3 B 1
1 D 2
2 B 1
```

### Output
```text
    true
    true
    true
    false
    true
```

### Explanation

1. `Lock(D, 1)` succeeds because `D` has no locked ancestors or descendants.
2. `Lock(E, 1)` succeeds because `E` is a sibling of `D`.
3. `Upgrade(B, 1)` succeeds because `D` and `E` are both locked by user 1. They are unlocked, and `B` becomes locked.
4. `Lock(D, 2)` fails because `B`, an ancestor of `D`, is locked.
5. `Unlock(B, 1)` succeeds because `B` is locked by user 1.
