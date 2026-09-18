# For You ranking weights cheat sheet

Source: `home-mixer/params/param.rs` on upstream `xai-org/x-algorithm` (commit tracked on branch `elkaix/fixes`).

**Critical reminder:** each weight multiplies the *predicted probability* (or continuous prediction) that *this viewer* will take that action — not raw like/report counts. Do not treat weight ratios as “1 report = N likes.”

Final score ≈ Σ (weightᵢ × P̂(actionᵢ)) then diversity / OON / new-author adjustments.

## Positive action weights (defaults)

| Action | Param | Default |
|--------|-------|---------|
| Favorite (like) | `FavoriteWeight` | **0.5** |
| Reply | `ReplyWeight` | **5.0** |
| Quote | `QuoteWeight` | **5.0** |
| Share (generic) | `ShareWeight` | **2.0** |
| Share via DM | `ShareViaDmWeight` | **5.0** |
| Share via copy link | `ShareViaCopyLinkWeight` | **20.0** |
| Retweet / repost | `RetweetWeight` | **1.0** |
| Follow author | `FollowAuthorWeight` | **4.0** |
| Post click | `ClickWeight` | **0.4** |
| Open link | `OpenLinkWeight` | **0.2** |
| Photo expand | `PhotoExpandWeight` | **0.05** |
| Video open | `VideoOpenWeight` | **0.07** |
| Dwell (binary-ish) | `DwellWeight` | **0.05** |
| Continuous dwell time | `ContDwellTimeWeight` | **0.004** |
| Quoted click | `QuotedClickWeight` | **0.05** |
| Profile click | `ProfileClickWeight` | **0.0** |
| VQV / quoted VQV | `VqvWeight` / `QuotedVqvWeight` | **0.0** |
| Post “unexplored” bonus | `PostUnexploredWeight` | **0.02** (in-network only by default) |

## Mutual-follow boosts

| Param | Default | Effect |
|-------|---------|--------|
| `BidirectionalFollowReplyWeightBoost` | **15.0** | Extra reply-weight boost for mutual follows |
| `BidirectionalFollowDwellWeightBoost` | **0.0** | Dwell boost for mutuals (off by default in this snapshot) |

See also `docs/BIDIRECTIONAL_BOOST_CHANGE.md` for rollout history.

## Negative action weights (defaults)

| Action | Param | Default |
|--------|-------|---------|
| Not interested | `NotInterestedWeight` | **-43.2** |
| Block author | `BlockAuthorWeight` | **-31.2** |
| Mute author | `MuteAuthorWeight` | **-58.8** |
| Report | `ReportWeight` | **-234.0** |
| Not dwelled | `NotDwelledWeight` | **-0.02** |

Negative weights look huge vs likes because baseline P(report) ≪ P(like). Mass coordinated reports from dissimilar users do not cleanly cancel likes for everyone — predictions are personalized; Home Timeline impressions matter more than drive-by profile visits.

## Structural multipliers (not action weights)

| Param | Default | Meaning |
|-------|---------|---------|
| `AuthorDiversityDecay` | **0.5** | Each extra post from same author in the feed is decayed |
| `AuthorDiversityFloor` | **0.25** | Floor for that decay |
| `OonWeightFactor` | **0.75** | Out-of-network posts scaled by this |
| `TopicOonWeightFactor` | **0.5** | Topic OON scaling |
| `EnableOonRescoreForInNetworkRepliesRetweets` | **true** | In-network replies/reposts can get OON-style rescoring |

## Practical takeaways for @moelkholy95

1. Optimize for **replies, quotes, copy-link shares, follows** — not raw like count.
2. **Mutual follows** get a large reply boost (15) — engage your reciprocal network.
3. Copy-link share weight (20) is extreme in this table — content people forward out-of-app is prized (rare event, high weight).
4. Avoid mute/block/report triggers in your niche; negative preds are heavy.
5. Hit early: pre-score path still ages out candidates (~48h filter in README).
6. Re-check this file after `git pull upstream main` — production defaults sync into the OSS tree over time.

## How we maintain this fork

- `upstream` → `https://github.com/xai-org/x-algorithm.git`
- `origin` → `https://github.com/elkaix/x-algorithm.git`
- Working branch: `elkaix/fixes`

```bash
git fetch upstream
git merge upstream/main   # or rebase
git push origin elkaix/fixes
```
