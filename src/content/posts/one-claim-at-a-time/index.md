---
title: One Claim at a Time
published: 2026-08-07
description: 'Why this site publishes rebuilt claims instead of paper summaries, and what a post here looks like.'
image: ''
tags: [Meta, Reproducibility]
category: 'Notes'
draft: false
lang: ''
---

Paper summaries are a solved problem. There are more of them than anyone can read, and most of them prove only that the author opened the PDF.

So this site does something narrower. I pick one paper, take **one claim** out of it, and try to rebuild that claim in the smallest amount of code that could settle it. Then I publish what happened.

## The rule

Three parts, and all three have to be there:

1. **A claim, not a summary.** Not "this paper proposes X." Instead: "X is faster than Y because of Z." Something that can turn out to be wrong.
2. **Code that runs.** Every post links a repository. If you can't run it, I didn't finish.
3. **The result, whatever it was.** If the numbers don't reproduce, that gets published too. A failed rebuild is a real finding about the claim, the setup, or my understanding. Hiding it would make the whole exercise decorative.

The last one is the part that matters. A blog that only publishes successes is a blog that quietly stops publishing.

## What a post looks like

Take a claim you can write down in one line, like the one behind scaled dot-product attention:

$$
\text{Attention}(Q, K, V) = \text{softmax}\!\left(\frac{QK^{\top}}{\sqrt{d_k}}\right)V
$$

The scaling by $\sqrt{d_k}$ is not decoration. The claim is that without it, the dot products grow with dimension, the softmax saturates, and gradients collapse. That is a testable statement, and it fits in a few lines:

```python
import torch

def attention(q, k, v, scale=True):
    scores = q @ k.transpose(-2, -1)
    if scale:
        scores = scores / (q.size(-1) ** 0.5)
    return torch.softmax(scores, dim=-1) @ v

# The claim: as d_k grows, the unscaled softmax saturates.
for d_k in (8, 64, 512):
    q = torch.randn(1, 16, d_k)
    k = torch.randn(1, 16, d_k)
    raw = (q @ k.transpose(-2, -1))
    print(d_k, raw.std().item(), torch.softmax(raw, -1).max().item())
```

Run it, look at what the numbers actually do, write down whether the claim held. That is the whole format.

## Where the code goes

Each rebuild ships as a repository you can clone. The work I already do in the open follows the same idea, in medical imaging:

::github{repo="Project-MONAI/MONAI"}

## Why bother

Two reasons, and I'd rather state them plainly than pretend it's pure curiosity.

The first is pressure. Committing to publish makes me actually read the paper instead of skimming the abstract. The deadline is the point.

The second is evidence. A summary shows what I read. A rebuild shows what I can do. Only one of those is worth anything to someone deciding whether to work with me.

First real rebuild goes up soon.
