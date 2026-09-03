# BusGo

BusGo is a premium, mobile-first transportation platform for route discovery, live bus tracking, digital tickets, and driver GPS publishing.

> **Demo status:** This is a presentation-ready static demo. It works without a production database using sample routes and local demo interactions. Supabase Realtime and Auth are supported when configured.

## Live Demo

- **Passenger app:** `[Open Live App](https://bus-go-opal.vercel.app/index.html)`
- **Driver portal:** `[Open Driver Portal](https://bus-go-opal.vercel.app/driver.html)`
- **Conductor ticket verifier:** `[Open Scanner](https://bus-go-opal.vercel.app/scan.html)`

Replace the placeholder domain after the first Vercel deployment.

## Features

### Passenger

- Responsive premium glassmorphism dashboard
- Search by origin, destination, date, and passenger count
- Popular route cards with schedules, duration, seats, and prices
- Live Leaflet map with OpenStreetMap tiles
- Real-time bus marker updates through Supabase Realtime
- Demo checkout with test card `4242 4242 4242 4242`
- Generated QR boarding pass
- Downloadable demo ticket
- PWA installation support

### Driver / Conductor

- Supabase email/password authentication
- Private authenticated driver sessions
- Bus selection and four-digit journey PIN
- Smartphone GPS publishing with `watchPosition()`
- Five-second publishing throttle
- Wake Lock support
- Start and end journey controls
- GPS status, coordinates, speed, and last-ping telemetry
- Desktop route simulator with predefined waypoints

### Conductor

- Dedicated ticket verification page at `/scan.html`
- Booking ID validation without scanner hardware
- Demo booking ID: `BG-82491`

## Run Locally

```bash
npm run dev
```

Open `http://localhost:3000/` or `http://localhost:3001/` depending on the local server port.

## Supabase Setup

The browser app uses only the public Supabase URL and anon key from `config.js`. Never place a `service_role` key in frontend code.

1. Create a free Supabase project.
2. Run `supabase-schema.sql` in the SQL Editor.
3. Open **Authentication > Users > Add user**.
4. Create a driver email/password account.
5. Copy the driver's UUID.
6. Assign the driver to a bus:

```sql
update public.buses
set driver_id = 'DRIVER_AUTH_USER_UUID'
where id = 'bus-01';
```

7. Confirm `buses` is enabled under **Database > Publications > supabase_realtime**.
8. Put the Project URL and anon public key in `config.js`.
9. Open `/driver.html`, sign in, enter the four-digit PIN, and start the journey.

The RLS policies allow public read access for passenger tracking, while authenticated drivers can update only rows whose `driver_id` matches their Supabase Auth user ID.

## Demo vs Production

The following are intentionally local demo implementations:

- Route data is embedded in `app.js`.
- Checkout simulates payment and charges no money.
- The QR ticket is generated in the browser.
- The conductor page validates the demo booking ID.
- The route simulator does not write fake GPS data to Supabase.

For production, persist routes and bookings in Supabase, add passenger authentication if needed, use a server-side payment provider integration, and replace the demo verifier with a protected validation workflow.

## Deploy to Vercel

This project is static and can be deployed directly from GitHub.

### Dashboard method

1. Push this repository to GitHub.
2. Open [vercel.com](https://vercel.com).
3. Import the GitHub repository.
4. Keep the framework preset as **Other**.
5. Leave the build command empty.
6. Set the output directory to `.`.
7. Deploy.

### CLI method

```bash
npm install -g vercel
vercel
vercel --prod
```

After deployment, update the Live Demo URLs in this README with the real Vercel domain.

## Build the Android APK

After the Vercel site is live over HTTPS:

```bash
npm install -g @bubblewrap/cli
bubblewrap init --manifest https://YOUR-VERCEL-DOMAIN.vercel.app/manifest.webmanifest
bubblewrap build
```

The generated APK is for Android demo installation. Google Play distribution requires a separate developer account.

## Project Structure

```text
index.html             Passenger dashboard
styles.css             Main visual system
typography.css         Font sizing overrides
driver.html            Driver GPS portal
driver.js              Authenticated GPS publisher
driver-polish.css      Driver action styling
scan.html              Conductor ticket verifier
app.js                 Passenger map, search, payment, and QR demo
config.js              Public Supabase configuration
supabase-schema.sql    Tables, Realtime, and RLS policies
manifest.webmanifest   PWA metadata
sw.js                  Service worker
icon.svg               BusGo app icon
```
