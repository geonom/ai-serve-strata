# ai-serve-strata

Makes [Strata](https://github.com/Niko1221/Strata) available as a language-model plugin of
[ai-serve](https://github.com/geonom/ai-serve). You only need this if you run Strata: one very large mixture-of-experts
model (125B) for PCs with a dedicated graphics card and plenty of RAM. While it runs it takes every GPU of the machine.

```bash
# in your ai-serve folder
./ai-serve plugin add https://github.com/geonom/ai-serve-strata     # clones into plugins/, runs setup.sh after you confirm
```

Then point it at your Strata install in `plugins/ai-serve-strata/.env`:

```bash
STRATA_DIR=/path/to/Strata
STRATA_CONFIG=/path/to/Strata/strata-iq3_s.json     # the strata-<model>.json Strata's setup wrote
```

and `./ai-serve restart`. ai-serve lists the model as `strata`; the first request starts Strata (loading takes 4–10
minutes) with every GPU reserved for it; stop it from the dashboard. More settings: `.env.example`. Whether Strata
suits your machine (made for dedicated GPUs; on Apple Silicon its VRAM/RAM split brings nothing, and this adapter is
untried there): ai-serve's [docs/strata.md](https://github.com/geonom/ai-serve/blob/main/docs/strata.md).

**How it plugs in** – an example of an `llm` plugin with `gpu: exclusive` (ai-serve's
[docs/plugins.md](https://github.com/geonom/ai-serve/blob/main/docs/plugins.md)): `llama-serve.service.json` declares
what it provides; `strata_plugin/app.py` answers the catalog without loading Strata, asks ai-serve for every GPU
before it starts Strata and confirms the reservation with Strata's PID, and forwards OpenAI requests to it. Tests:
`./setup.sh && .venv/bin/python -m pytest -q`.

Tried on Linux with two NVIDIA cards. License: AGPL-3.0, like ai-serve (Strata has its own license).
