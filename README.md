# Muskan Electric Pvt. Ltd. — Website Source

A mobile-first, bilingual English/Nepali dealership website and private operations dashboard for Muskan Electric Pvt. Ltd. (ME), intended for GitHub source control and deployment to Hostinger after client approval.

## Project contents

- Public pages: Home, Vehicles, Vehicle detail template, Offers, EMI calculator, Exchange, Test ride, Service & Warranty, Showrooms, About, Contact, Blog, Privacy, Terms, Thank-you and 404.
- `admin.html`: private sign-in and dashboard for products, images, stock movements, leads/bookings, offers, banners, showroom details, blog posts and a basic accounting transaction register.
- `assets/styles.css`: shared responsive design system.
- `assets/site.js`: bilingual switcher, public catalogue/filter/compare, calculators, product details, analytics consent and public form submission.
- `assets/admin.js`: admin CRUD and reporting interactions.
- `assets/config.js`: public website configuration placeholders only.
- `supabase/migrations/`: database schema, RLS and storage policies.
- `supabase/functions/`: public content endpoint and validated enquiry submission endpoint.
- `scripts/prerender-products.mjs`: generate crawlable, individual HTML pages for published models and blog posts, plus sitemap and robots files.
- `.htaccess`: Apache/Hostinger clean routes, HTTPS redirect, headers and caching.
- `docs/`: deployment, configuration, content and SEO checklists.

## Preview locally

No frontend build step is required. From this folder run:

```bash
python3 -m http.server 8000
```

Open `http://localhost:8000`. The public pages can be browsed in preview mode. Admin data and public form submissions will not work until Supabase and the required security/notification services are configured.

## Deploy the backend to Supabase

1. Create a Supabase project for Muskan Electric.
2. Install the Supabase CLI and authenticate.
3. Link this repository to the project:

   ```bash
   supabase login
   supabase link --project-ref YOUR_PROJECT_REF
   supabase db push
   ```

4. Deploy both Edge Functions:

   ```bash
   supabase functions deploy public-content
   supabase functions deploy submit-lead
   ```

5. Set Edge Function secrets using `docs/HANDOVER.md`. The service-role key must remain server-side and must never be added to `assets/config.js`, HTML, JavaScript served to visitors, GitHub, or Hostinger public files.
6. In `assets/config.js`, set the public Supabase project URL, public anon/publishable key and Cloudflare Turnstile site key. Optional GA4 and Meta Pixel IDs can also be set there.
7. Create an admin user in Supabase Auth, then add their Auth UUID to `public.admin_roles` as described in the handover guide. Disable public sign-ups.

## Generate SEO pages for published products and blog posts

Run from the project root on a trusted local machine or CI runner. Supply the service-role key only as a local environment variable or CI secret:

```bash
SITE_URL="https://YOUR-DOMAIN" \
SUPABASE_URL="https://YOUR-PROJECT.supabase.co" \
SUPABASE_SERVICE_ROLE_KEY="YOUR_SERVER_ONLY_KEY" \
node scripts/prerender-products.mjs
```

This writes `/vehicles/<slug>.html` and `/blog/<slug>.html`, refreshes `sitemap.xml` and `robots.txt`, and adds LocalBusiness structured data only for showroom records with both a confirmed address and phone. Review generated pages and sitemap before deployment. Never commit the service-role key.

## Deploy on Hostinger after client approval/payment

Upload the website files and folders to the domain's `public_html` directory. Keep the `.htaccess` file. Enable SSL and force HTTPS. The clean route rewrites/security headers depend on Apache `mod_rewrite` and `mod_headers`; use the supplied `.html` pages if the hosting plan does not support these modules. Configure DNS/domain, then test the public pages, mobile layout, forms, admin sign-in, image uploads, notification delivery, redirects and 404 handling.

## Important scope and launch notes

- Real model names, brands, photos, prices, range, speed, battery, warranty and brochures must be supplied and confirmed by Muskan Electric before publishing vehicle records.
- Branch names Birgunj, Parwanipur and Simara are included as service areas. Exact public branch addresses, phone numbers, hours and map pins must be verified before publishing them as individual showrooms.
- 0% EMI and 12/24-month options are displayed as an enquiry/estimate feature, not a finance approval. Confirm offer eligibility, partner, down payment, fees, documents and dates in writing before advertising a specific scheme.
- The accounting area currently provides a basic income/expense/purchase/sale transaction register. It is not a complete Nepal VAT/PAN/tax-compliant ERP, general ledger or statutory accounting package.
- This source has not been connected to a live Supabase project, Turnstile site key, notification provider, Google Analytics/Meta Pixel, custom domain or Hostinger account. Those need configuration and end-to-end acceptance testing before production use.
- Customer photo uploads use the private Supabase `customer-uploads` bucket. Vehicle and banner images use the public `vehicle-images` bucket for public display.
