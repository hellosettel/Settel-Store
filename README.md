# Settel Store

**No decisions. Just dinner.**

A simple storefront for Settel's weekly frozen meal menu with live inventory tracking.

---

## Setup (One Time)

1. Upload all these files to your `settel-store` GitHub repository
2. Create an `images/` folder in the repo for meal photos
3. Add your logo as `images/settel-logo.png`
4. Connect your GitHub repo to Netlify:
   - Log into Netlify → **Add new site** → **Import an existing project**
   - Select GitHub → Choose `settel-store`
   - Click **Deploy site**
5. Point your GoDaddy domain to Netlify (Netlify will walk you through this)

---

## Weekly Menu Updates

Each week when you have new meals ready:

1. Go to your `settel-store` repo on GitHub
2. Click on `meals.json`
3. Click the pencil icon (edit)
4. Update each meal's info:

```json
{
  "id": "meal-1",
  "name": "Your Meal Name",
  "description": "A short tasty description.",
  "price": 9.99,
  "quantity": 20,
  "image": "images/your-photo.jpg"
}
```

5. Upload new meal photos to the `images/` folder if needed
6. Commit your changes
7. Netlify automatically deploys — your site updates in about 30 seconds

---

## File Structure

```
settel-store/
├── index.html       ← The storefront customers see
├── admin.html       ← Your order management page (yoursite.com/admin.html)
├── meals.json       ← Edit this weekly to update meals
├── images/          ← Drop meal photos here
│   ├── settel-logo.png
│   ├── spaghetti.jpg
│   └── ...
└── README.md
```

---

## How Orders Work

1. Customer visits your site and sees available meals
2. They add meals to their order using the + / - buttons
3. They click "Place Order" and fill in their contact info
4. You receive the order and follow up via phone/text to arrange Venmo payment and logistics

---

## Receiving Orders

**Important:** Since this is a static site (no backend server), orders are currently saved in the customer's browser via localStorage. To receive orders reliably, you'll want to set up a free form endpoint:

### Recommended: Formspree (Free)
1. Go to [formspree.io](https://formspree.io) and create a free account
2. Create a new form — you'll get a URL like `https://formspree.io/f/xxxxxxxx`
3. Open `index.html`, find this line:
   ```js
   const formEndpoint = ''; // PUT YOUR FORMSPREE URL HERE
   ```
4. Paste your Formspree URL between the quotes
5. Now every order gets emailed to you automatically

---

## Notes

- **Inventory resets** when you update `meals.json` (which is what you want — fresh count each week)
- **Admin page** at `yoursite.com/admin.html` shows orders stored in the browser you're viewing from
- **No monthly fees** — just free Netlify hosting and free Formspree for order notifications
