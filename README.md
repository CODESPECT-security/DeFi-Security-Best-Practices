# DeFi Internal Security Checklist

- [ ] Treat security as a first-class engineering concern from day one, not an afterthought before launch. This means: maintaining full test coverage at all times, keeping a written invariant list, writing fuzz tests, and following every significant development milestone with a round of internal security review. Paying for audits alone is not a security strategy.

- [ ] Design role separation into the architecture before writing business logic. No single role should be a superset of all others. Example: a `GUARDIAN` role that can only pause, an `OPERATOR` for routine parameter changes, and an `ADMIN` (multisig) for upgrades and critical changes.

- [ ] No single key should be able to cause irreversible damage to the protocol on its own. Any action that could drain funds, brick the protocol, or permanently alter its behaviour must require multiple independent signers.

- [ ] Contracts must be pausable. A single key should be sufficient to pause - speed is critical during an active exploit. Unpausing should require 2-3 keys to prevent accidental or malicious unpause before the issue is resolved.

- [ ] All sensitive privileged operations (ownership transfers, upgrades, parameter changes) must be 2-step: one key proposes the change, a separate key confirms it. This prevents mistakes and limits damage from a single compromised key.

- [ ] Maintain up-to-date NatSpec on all contracts, an architecture diagram showing contract interactions and value flows, and a written invariant list that lives outside of test files.

- [ ] Every non-obvious implementation decision must have an inline comment explaining the reasoning - these are the exact spots auditors and incident responders will stop and question.

- [ ] All privileged keys must be on hardware wallets with no exceptions - including keys used for what seem like temporary or non-critical deployments.

- [ ] Every deployment script must go through the same review process as production contract code. Scripts that interact with live contracts carry the same risk as the contracts themselves.

- [ ] Write a formal key rotation procedure before mainnet launch. Needing to figure out key rotation under pressure, during or after an incident, is a serious operational risk.

- [ ] Write an incident response playbook before mainnet launch. It must define: who gets contacted first and through which channel, who leads fund rescue operations, and a ready-made list of white hats and security organisations to notify.

- [ ] Have a one-click script ready that pauses all protocol operations at once before going live. Test it. Every second spent figuring out the pause process during an incident is a second the attacker has.

- [ ] Run a monitoring tool that continuously tracks protocol inflows and outflows and alerts on anomalies - large unexpected withdrawals, sudden TVL drops, unusual single-asset movements, oracle price deviations, and similar signals.

- [ ] Host a bug bounty program, with payouts proportional to TVL and a defined triage process.
