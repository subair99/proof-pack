# Claim recipes

Each recipe tells the Skill what to search for, what to extract and what a complete pack must contain. "Photo window" is the follow-up period used in Steps 3 and 6. Statuses are decided by [evidence-rules.md](evidence-rules.md). A required item that is not found is reported as Missing. It is never invented or substituted.

Back to [SKILL.md](../SKILL.md).

## 1. Vehicle damage (`vehicle-damage`)

- **Camera keywords**: driveway, garage, gate, parking, street
- **Photo window**: incident start to 48 hours after incident end
- **Document keywords**: motor insurance, policy, certificate, schedule, repair, quote, estimate, police, report
- **Document date range**: policy documents any date; quotes and reports from incident start to 30 days after

| Required item | Weak if |
|---|---|
| Camera event in the window, or the user's statement explaining why none exists | Event is outside the window by more than 1 hour |
| At least 2 photos of the damage | All photos taken more than 48 hours after the incident |
| Insurance policy or schedule | Incident date is outside the cover period |
| Repair quote or estimate | Quote is dated before the incident |
| Police report reference (if the insurer requires one for third-party damage) | Not applicable |

**Fields to extract**: policy number, insurer, cover period, excess amount, vehicle registration (from the policy); quote amount, garage name and quote date (from the quote); reference number (from the police report).

**Reconcile**: incident date inside the cover period; quote amount compared with the excess; registration on the policy matches the registration in the photos' filenames or tags where present.

## 2. Missing or damaged delivery (`delivery`)

- **Camera keywords**: front door, porch, gate, entrance, doorbell
- **Photo window**: delivery date 00:00 to 48 hours after
- **Document keywords**: order, confirmation, invoice, receipt, tracking, delivered, courier
- **Document date range**: 30 days before the delivery date to 7 days after

| Required item | Weak if |
|---|---|
| Order confirmation or invoice | Item description is missing |
| Courier "delivered" notice or tracking record | Delivered time is outside the window |
| Camera events at the entrance for the claimed delivery time | Not applicable |
| Photo of the package or its damage (damaged-item claims only) | Taken more than 48 hours later |

**Fields to extract**: order number, seller, item, amount paid, order date; courier name, tracking number, claimed delivery time.

**Reconcile**: the courier's claimed delivery time is compared with entrance camera events. If there are no person events at the entrance within 30 minutes of the claimed time, the item is marked Conflict and both facts are shown ("Courier record: delivered 14:40. Front Door camera: no events 14:10 to 15:10"). It is never reported as proof of non-delivery.

## 3. Product warranty (`warranty`)

- **Camera keywords**: none (camera sweep is skipped and reported as "Not needed for this claim type")
- **Photo window**: fault date to 7 days after
- **Document keywords**: receipt, invoice, warranty, guarantee, serial, manual, product name from the user
- **Document date range**: any

| Required item | Weak if |
|---|---|
| Proof of purchase (receipt or invoice) | Seller or date is unreadable |
| Warranty terms or card | Fault date is after the warranty end date |
| Serial number from at least one document | Serial numbers differ between documents |
| Photo or short video of the fault | Not applicable |

**Fields to extract**: purchase date, seller, price, product model, serial number, warranty length; warranty end date (purchase date plus warranty length, shown with the calculation).

**Reconcile**: fault date inside the warranty period; serial number matches across receipt, warranty card and any photo of the label.

## 4. Rental deposit dispute (`deposit`)

- **Camera keywords**: none by default (indoor cameras in a rented home are not assumed)
- **Photo windows**: move-in window (comparison window) and move-out window (incident window)
- **Document keywords**: lease, tenancy, agreement, inventory, check-in, check-out, deposit, landlord
- **Document date range**: any

| Required item | Weak if |
|---|---|
| Lease or tenancy agreement | Deposit amount is not readable |
| Move-in photos | Taken more than 14 days after the move-in date |
| Move-out photos | Taken more than 7 days after the move-out date |
| Inventory or check-in report | Not applicable |
| Landlord's deduction claim (if received) | Not applicable |

**Fields to extract**: deposit amount, lease start and end dates, the condition and cleaning clauses (quoted with the clause number), and the deductions listed by the landlord.

**Pairing**: move-in and move-out photos are grouped by room using the photo location or album name where present. Otherwise they are grouped by the room names the user gives. Rooms with photos from only one period are listed under Weak.

## 5. Home damage (`home-damage`)

- **Camera keywords**: garden, yard, back, side, roof, gate, any outdoor camera
- **Photo window**: incident start to 72 hours after
- **Document keywords**: home insurance, buildings, contents, policy, schedule, plumber, repair, quote, invoice, weather
- **Document date range**: policy any date; quotes and invoices from incident start to 30 days after

| Required item | Weak if |
|---|---|
| Home or contents insurance policy | Incident date outside cover period |
| At least 3 photos of the damage | All taken more than 72 hours later |
| Repair quote or invoice | Dated before the incident |
| Receipts for damaged items over the policy's single-item limit (if stated) | Not applicable |
| Camera events in the window (if outdoor cameras exist) | Not applicable |

**Fields to extract**: policy number, cover type, excess, single-item limit; quote or invoice amounts; item receipts' purchase dates and prices.

**Reconcile**: incident date inside cover; total quoted repairs compared with the excess; each damaged-item receipt compared with the single-item limit.
