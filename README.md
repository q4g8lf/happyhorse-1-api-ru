# HappyHorse 1.0 API (`happyhorse-1.0`)

<!-- conv-kit:v1 -->

<p align="center">
  <img src="assets/badges/price.svg" alt="observed unit price"> <img src="assets/badges/billing.svg" alt="billing model"> <img src="assets/badges/compat.svg" alt="OpenAI-compatible endpoint">
</p>

<p align="center">
  <img src="assets/01-product-ad-ceramic-mug-thumb.jpg" width="820" alt="Sample video frame generated through APIMart">
</p>

> **from $0.13 per second** on the APIMart route — одна OpenAI-совместимая конечная точка на `https://api.apimart.ai/v1`, ежемесячный тариф не нужен. *(данные на 2026-10-12)*

**[Получить API-ключ](https://go.apimart.ai/k-7fd0a6)** · **[Актуальные цены](https://go.apimart.ai/k-74e4e6)** · **[Страница модели](https://go.apimart.ai/k-6cbe6b)** · [⚡ Быстрый старт за 60 секунд](#quickstart)

**Почему команды используют HappyHorse 1.0 (`happyhorse-1.0`) через APIMart**

- **Один ключ — весь каталог.** Тот же base URL `https://api.apimart.ai/v1` и тот же заголовок `Authorization` дают доступ к HappyHorse 1.0 (`happyhorse-1.0`) и ещё 300+ моделям для изображений, видео и языка — менять нужно поле `model`, а не клиент.
- **Минимум $1, оплата по факту.** Ни подписки, ни предоплаченного тарифа, который нужно заранее рассчитать: пополняйте от $1 и расходуйте на вызовы.
- **Сумма списания возвращается в ответе.** Каждая завершённая задача сообщает списанную сумму (`cost` / `credits_cost`); задача со статусом `failed` возвращается на баланс.
- **Асинхронность по умолчанию.** Отправьте задачу, получите `task_id`, опрашивайте `GET /v1/tasks/{id}` — пакетная обработка и повторы это обычная работа с очередью, а не отдельная интеграция.

<!-- /conv-kit:v1 -->



HappyHorse 1.0 is Alibaba ATH-AI's unified multimodal video model, available on APIMart as the HappyHorse API. It generates 1080p video with synchronized native audio in a single forward pass, supports 7-language lip-sync, and ranks #1 on Artificial Analysis text-to-video and image-to-video leaderboards. Also written as Happy Horse 1.0, this HappyHorse AI video model supports text-to-video, first-frame and reference-image generation, and source-video editing on this page.

## Model id and routes

| Route | `model` value | Billing | Notes |
| --- | --- | --- | --- |
| Text-to-video / image-to-video | `happyhorse-1.0` | per generated second, by resolution | see the [model page](https://go.apimart.ai/k-6cbe6b) for the exact tiers |

Endpoint: `POST https://api.apimart.ai/v1/videos/generations`, then poll `GET /v1/tasks/{task_id}`.

## Pricing

<!-- pricing:model:start -->
| Resolution | List price | Effective price |
| --- | --- | --- |
| default | 0.2875 | 0.23 |


<!-- pricing:model:end -->

Prices are a snapshot; the [pricing page](https://go.apimart.ai/k-74e4e6) and [`data/model.json`](data/model.json) are refreshed by CI, and a completed task reports the exact amount in its `cost` field.

## Request parameters

| Field | Type | Default | Notes |
| --- | --- | --- | --- |
| `model` | string | required | the model id shown in the route table above |
| `prompt` | string | required | the shot description; camera and subject motion belong here |
| `duration` | integer | route default | clip length in seconds — billing is per generated second |
| `resolution` | string | route default | 480P / 720P / 1080P or the route’s own tiers |
| `size` / `aspect_ratio` | string | `16:9` | output framing |
| `image_urls` | string[] | — | first frame, first + last frame, or style references, where supported |
| `seed` | integer | — | fix the seed to reproduce a take |

The table lists the fields accepted by the APIMart `videos/generations` endpoint. Check the [model page](https://go.apimart.ai/k-6cbe6b) for anything this model adds on top.

## Quickstart

```bash
curl --request POST --url https://api.apimart.ai/v1/videos/generations \
  --header "Authorization: Bearer $APIMART_API_KEY" --header 'Content-Type: application/json' \
  -d '{"model":"happyhorse-1.0","prompt":"A bamboo forest at sunrise, slow push-in","duration":5,"resolution":"720P","size":"16:9"}'
```

```python
import os, time, requests

BASE = "https://api.apimart.ai/v1"
HEADERS = {"Authorization": f"Bearer {os.environ['APIMART_API_KEY']}", "Content-Type": "application/json"}

created = requests.post(f"{BASE}/videos/generations", headers=HEADERS, timeout=60, json={
    "model": "happyhorse-1.0", "prompt": "A bamboo forest at sunrise, slow push-in", "duration": 5,
}).json()
task_id = created["data"]["id"]

while True:
    task = requests.get(f"{BASE}/tasks/{task_id}", headers=HEADERS, timeout=60).json()["data"]
    if task["status"] in ("completed", "failed"):
        break
    time.sleep(5)
print(task.get("cost"), task["result"]["videos"][0]["url"])
```

Runnable versions: [`examples/`](examples). The task lifecycle is `pending → processing → completed | failed`.

## FAQ

**Какой id у модели HappyHorse 1.0?**

`happyhorse-1.0`. Смотрите таблицу маршрутов выше.

**Как тарифицируется?**

**from $0.13 per second** on the APIMart route. Завершённая задача указывает точную сумму в поле `cost`; неудачная задача возвращается на баланс.

**How do I get a key?**

Use the [Get an API key](https://go.apimart.ai/k-7fd0a6) link — $1 minimum top-up, no plan required.

<!-- related-repos:auto -->
## Related repositories

- **Same model, this language** — the other half of this pair: [happyhorse-1-prompts-ru](https://github.com/q4g8lf/happyhorse-1-prompts-ru)
- **Same model, other languages** — 10 more language builds: English [happyhorse-1-api](https://github.com/lxgh0k/happyhorse-1-api) · 中文 [happyhorse-1-api-cn](https://github.com/f1q34m3/happyhorse-1-api-cn) · Deutsch [happyhorse-1-api-de](https://github.com/k8-b3qiaf/happyhorse-1-api-de) · Español [happyhorse-1-api-es](https://github.com/ig50vvfeo8/happyhorse-1-api-es) · Français `happyhorse-1-api-fr` · Relay/gateway `happyhorse-1-api-gateway` · Bahasa [happyhorse-1-api-id](https://github.com/b73ynyxyc77o/happyhorse-1-api-id) · 日本語 `happyhorse-1-api-ja` · 한국어 [happyhorse-1-api-ko](https://github.com/jhtduez/happyhorse-1-api-ko) · Português `happyhorse-1-api-pt`
- **More models in this language** — [midjourney-api-ru](https://github.com/sfpxvicw/midjourney-api-ru) · [gemini-2-5-flash-image-preview-api-ru](https://github.com/zwtdva8/gemini-2-5-flash-image-preview-api-ru) · [nano-banana-api-ru](https://github.com/ggpbi40ss/nano-banana-api-ru) · [gemini-3-1-flash-image-preview-api-ru](https://github.com/lu60av/gemini-3-1-flash-image-preview-api-ru) · [nano-banana-2-api-ru](https://github.com/jnp3hlry/nano-banana-2-api-ru) · [gemini-3-pro-image-preview-api-ru](https://github.com/dbxz24/gemini-3-pro-image-preview-api-ru)
- **Topic hubs** — every build of this model, all languages: [happyhorse-1](https://github.com/topics/happyhorse-1) · [happyhorse-1-api](https://github.com/topics/happyhorse-1-api)

This repository is one build in a cross-linked set of API guides for the same model family; each language build links back to the others.
<!-- /related-repos:auto -->

## Attributed links (how this repository is measured)

| Purpose | Attributed link | Target |
| --- | --- | --- |
| Model page | <https://go.apimart.ai/k-6cbe6b> | `apimart.ai/ru/model/happyhorse-1-0` |
| Live pricing | <https://go.apimart.ai/k-74e4e6> | `apimart.ai/ru/pricing` |
| Get an API key | <https://go.apimart.ai/k-7fd0a6> | `apimart.ai/ru/keys` |

Outbound APIMart links are minted through the promo link API; hand-made tracking parameters are rejected by
`tools/check_links.py` in CI.

## Disclosure

HappyHorse 1.0 is a third-party model served through APIMart; this repository documents how to call it and publishes
model ids and prices, and does not claim official status. Model names, prices and documentation belong to their
respective owners.

## License

MIT — see [LICENSE](LICENSE).
