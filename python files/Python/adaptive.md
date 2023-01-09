#



For example, `LOAD_GLOBAL_MODULE` is specialized for `globals()`
dictionaries that have a keys with the expected version.

This can be tested quickly:
* `globals->keys->dk_version == expected_version`

and the operation can be performed quickly:
* `value = entries[cache->index].me_value;`.

Because it is impossible to measure the performance of an instruction without
also measuring unrelated factors, the assessment of the quality of a
specialization will require some judgement.

As a general rule, specialized instructions should be much faster than the
base instruction.

### Implementation of specialized instructions

In general, specialized instructions should be implemented in two parts:
1. A sequence of guards, each of the form
  `DEOPT_IF(guard-condition-is-false, BASE_NAME)`.
2. The operation, which should ideally have no branches and
  a minimum number of dependent memory accesses.

In practice, the parts may overlap, as data required for guards
can be re-used in the operation.

If there are branches in the operation, then consider further specialization
to eliminate the branches.

### Maintaining stats

Finally, take care that stats are gather correctly.
After the last `DEOPT_IF` has passed, a hit should be recorded with
`STAT_INC(BASE_INSTRUCTION, hit)`.
After an optimization has been deferred in the adaptive instruction,
that should be recorded with `STAT_INC(BASE_INSTRUCTION, deferred)`.
