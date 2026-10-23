# Fields `/chat` reads from Express (hand-off for the backend engineer)

All calls are `GET`, 5 s timeout each, fetched concurrently, cached for 10 s. Envelope `{ data }` is unwrapped.
FastAPI never calls a write endpoint and never receives Supabase credentials.

| Endpoint | Fields used |
|---|---|
| `GET /api/requests` | id, area, population, urgency, vulnerable, accessibility, status, hoursWaiting, fulfilmentPct, score{total,parts}, items[item,qtyNeeded,qtyAllocated] |
| `GET /api/allocation` | id, requestId, area, depotId, depotName, item, qty, qtyNeeded, unfulfilled, fulfilmentPct, distanceKm, etaMin, status, reason |
| `GET /api/depots` | id, name, dailyCapacity, stock[item,qty,lowThreshold,isLow], vehicles[status] (counted only) |
| `GET /api/dashboard` | passed through as-is (KPIs, criticalShortages, inventoryTotals, lowStockAlerts) |
| `GET /api/deliveries` | allocationId, requestId, area, depotId, depotName, vehicleId, status, progressPct, distanceKm, etaMin, items (route polyline and lat/lng are dropped) |

If any of the five fails, `/chat` returns `503 KARWAN_API_UNAVAILABLE` and Gemini is not called.
