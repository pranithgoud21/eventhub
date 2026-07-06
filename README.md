\# EventHub - Backend Ticketing API



EventHub is a lightweight backend API built with Django and Django REST Framework (DRF) designed to handle core event ticketing workflows. It allows users to view events, reserve seating blocks, and process cancellations while safely managing seat inventory constraints.



\---



\## Project Architecture \& Setup



\### Requirements

\* Python 3.7+

\* Django 3.2+

\* Django REST Framework



\### Running the App Locally



1\. Install project dependencies:

```bash

pip install -r requirements.txt



python manage.py makemigrations

python manage.py migrate



python manage.py runserver



API Endpoints Summary

1\. Events Endpoint (/api/events/)

GET /api/events/ - Lists all events ordered by date.



Query Filters: Filter results by exact status (?status=upcoming) or search venues (?venue=bangalore).



POST /api/events/ - Registers a new event profile. Requires tracking fields (title, venue, date, total\_seats, available\_seats).



2\. Reservations Endpoint (/api/reservations/)

GET /api/reservations/ - Displays registration records.



Query Filters: Lookup specific event ledger historical data (?event\_id=1).



POST /api/reservations/ - Claims seat counts on an active event. Deducts requested quantities out of available\_seats immediately on creation.



POST /api/reservations/{id}/cancel/ - Custom action path that flags a ticket reservation status as cancelled and restores the original seat reservation count back to the parent event resource automatically.



Technical Design Decisions

In-Serializer Business Validation

Instead of pulling database modification logic into views or keeping checks split out across multiple validation functions, the inventory changes and model assertions are centralized directly inside ReservationSerializer.



Why this layout was selected:

Placing data sanitation constraints (validate()) right next to database generation tasks (create()) guarantees that invalid parameters—such as overbooking errors or booking onto an outdated event status—fail out immediately before touching the storage tier.



While an enterprise-scale ecosystem experiencing heavy traffic concurrency would require explicit database transactions (transaction.atomic()) or database row-level constraints to entirely rule out race conditions, keeping these validations localized inside the serialization engine decouples structural layout definitions from routing mechanics cleanly and satisfies standard DRF best practices for this assignment tier.



Postman Screenshots

Reservation Creation (Success)
c:\Users\prani_fje1y5n\eventhub\success_reservation.png

Overbooking Validation Failure
c:\Users\prani_fje1y5n\eventhub\overbooking_error.png

Successful Ticket Cancellation
c:\Users\prani_fje1y5n\eventhub\success_cancellation.png


