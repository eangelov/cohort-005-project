# Deliver Notifications through Knock

Cadence sends its Notifications through one hosted vendor, Knock, and we never self-host. Knock was the only candidate that met the whole brief on its free plan. It gives 10K messages a month. Its feed requires signed RS256 user tokens, which our server signs for whichever user is in the session, so they work with the DevUI switcher. It stores preferences by category × channel, and it has a headless feed API, so we can build the Notification Feed ourselves.

## Considered Options

- **MagicBell.** Rejected even though it has native VAPID web push. Its free plan allows only 1K deliveries a month, and web push can only ever be an extra channel people opt into. iOS needs a Home Screen install and delivery is best-effort, so push shouldn't decide the vendor.
- **Courier.** Runner-up. Scoped JWTs, topic × channel preferences, and pay-as-you-go pricing at $0.005 per send. It's our fallback if volume ever gets near Knock's free limit.
- **Novu Cloud.** Rejected: its feed auth is a static HMAC of the subscriber id with no expiry, and its preferences have no categories.
- **SuprSend.** Rejected: vendor-stored preferences aren't on its free plan, and category preferences need the $250/mo Business plan.

## Consequences

- Any web push has to go through Firebase Cloud Messaging: a Firebase project, the Firebase SDK and a service worker.
- Above 10K messages a month, Knock jumps straight to $250/mo (Starter). If volume gets close to that, revisit Courier rather than pay for the jump by default.
- Preferences and feed state live with Knock, so moving to another vendor means migrating that state and rewriting token signing.
