# Ferrous Hub Architecture Migration Checklist

## Decision trail

- [ ] Add ADR-0004 as **Proposed**.
- [ ] Link the umbrella architecture issue.
- [ ] Link the draft pull request.
- [ ] Record the P2600 purchase and architectural consequence.
- [ ] Mark unresolved production choices as deferred.

## Repository story

- [ ] README identifies Ferrous Hub as the platform centre.
- [ ] Architecture overview shows energy modules feeding the Hub.
- [ ] Battery documents use the term **energy module** where appropriate.
- [ ] Trinity Ring is documented as the rider-facing behavioural interface.
- [ ] Garmin or the ride computer owns detailed metrics.
- [ ] P2600 is documented as the first serious peripheral.
- [ ] nRF54L15 is documented as the primary development platform.
- [ ] nRF52832 is documented only as an optional prototype target.

## Cross-document consistency

- [ ] Remove current-tense references to battery-centred architecture.
- [ ] Preserve historical references in ADR and changelog context.
- [ ] Replace obsolete **Triad Pulse** terminology with **Tre Pulse**.
- [ ] Use **Trinity Ring** consistently.
- [ ] Distinguish power indicator, mode indicator, and Trinity Ring.
- [ ] Ensure P60B work is not described as a prerequisite for Hub V0.1.

## Architecture quality

- [ ] Core behaviour is separated from board drivers.
- [ ] Hub and energy-module ownership is explicit.
- [ ] Safety override order is explicit.
- [ ] Lighting can operate without optional monitoring.
- [ ] Ring failure cannot block safe shutdown or lighting behaviour.
- [ ] Experimental values are labelled assumptions.

## Planning

- [ ] Roadmap begins with Ferrous Hub V0.1.
- [ ] Separate architecture migration from implementation PRs.
- [ ] Create follow-up issue for ring bring-up.
- [ ] Create follow-up issue for touch bring-up.
- [ ] Create follow-up issue for P2600 interface investigation.
- [ ] Create follow-up issue for INA226 evaluation.
- [ ] Keep P60B module development on a separate track.

## Merge readiness

- [ ] Markdown renders correctly.
- [ ] Mermaid diagrams render correctly.
- [ ] Internal links resolve.
- [ ] Terminology scan completed.
- [ ] Historical documents remain accurate.
- [ ] CI checks pass.
- [ ] Draft PR explains the migration clearly.
- [ ] ADR status is updated only after review.
