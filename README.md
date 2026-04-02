# DeFi Internal Security Checklist

- [ ] Treat security as a first-class engineering concern from day one, not an afterthought before launch. This means: maintaining full test coverage at all times, keeping a written invariant list, writing fuzz tests, and following every significant development milestone with a round of internal security review. Paying for audits alone is not a security strategy.

- [ ] Design role separation into the architecture before writing business logic. No single role should be a superset of all others. Example: a `GUARDIAN` role that can only pause, an `OPERATOR` for routine parameter changes, and an `ADMIN` (multisig) for upgrades and critical changes.

- [ ] No single key should be able to cause irreversible damage to the protocol on its own. Any action that could drain funds or permanently alter its behaviour must require multiple independent signers, at minimum 3-of-5. Signer thresholds must scale with TVL: the larger the protocol grows, the higher the confirmation threshold should be. Revisit and adjust your multisig configuration on a defined schedule as TVL grows. A governance setup appropriate for a small protocol is not appropriate for a large one.

- [ ] Contracts must be pausable. A single key should be sufficient to pause - speed is critical during an active exploit. Unpausing should require 2-3 keys to prevent accidental or malicious unpause before the issue is resolved.

- [ ] All sensitive privileged operations (ownership transfers, upgrades, parameter changes) must go through a timelock in addition to multisig approval, with a minimum delay of 24 hours. Timelock delays must scale with TVL: the larger the protocol grows, the longer the delay should be. Both queuing and executing a timelocked action must require the full multisig threshold. Pausing is the single explicit exception: it must remain instantaneous to enable rapid response during an active exploit.

- [ ] Maintain up-to-date NatSpec on all contracts, an architecture diagram showing contract interactions and value flows, and a written invariant list that lives outside of test files.

- [ ] Every non-obvious implementation decision must have an inline comment explaining the reasoning - these are the exact spots auditors and incident responders will stop and question.

- [ ] Privileged keys should be secured using hardware wallets wherever possible, including those used for temporary or non-critical deployments. The device used for governance signing must be dedicated exclusively to that purpose, not used for daily browsing, dApp interactions, or anything else. The signing wallet must never be connected to other protocols or dApps; one malicious approval on an unrelated contract can compromise the same key that guards the treasury. A dedicated emergency pausing role may be assigned to an immediately accessible privileged key to enable rapid response, provided it is strictly limited in scope (pause-only) and supported by monitoring or automated controls.

- [ ] Every signer must simulate and inspect the raw transaction instructions before signing, not just the proposal title or description. Verify recipient addresses, amounts, and parameter values directly from the decoded instructions. Unexpected values (withdrawal limits far exceeding TVL, unrecognised addresses, parameter changes never discussed in team channels) are red flags that warrant stopping the signing process and raising an alert immediately.

- [ ] Every deployment script must go through the same review process as production contract code. Scripts that interact with live contracts carry the same risk as the contracts themselves.

- [ ] Write a formal key rotation procedure before mainnet launch. Needing to figure out key rotation under pressure, during or after an incident, is a serious operational risk.

- [ ] Write an incident response playbook before mainnet launch. It must define: who gets contacted first and through which channel, who leads fund rescue operations, and a ready-made list of white hats and security organisations to notify.

- [ ] Have a one-click script ready that pauses all protocol operations at once before going live. Test it. Every second spent figuring out the pause process during an incident is a second the attacker has.

- [ ] Run a monitoring tool that continuously tracks protocol inflows and outflows and alerts on anomalies - large unexpected withdrawals, sudden TVL drops, unusual single-asset movements, oracle price deviations, and similar signals. Monitor governance activity separately and with the same urgency: alert in real time on new proposals, threshold approvals, and execution events. If your team learns about a governance action from social media rather than an internal alert, your operational security has already failed.

- [ ] Host a bug bounty program, with payouts proportional to TVL and a defined triage process.
