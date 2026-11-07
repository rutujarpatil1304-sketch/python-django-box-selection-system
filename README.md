# AI-Assisted Box Selection System

A small Django application for recommending the lowest-cost shipping box that passes an order's weight limit and a heuristic 3-D packing check.

## Features
- Manage products, shipping boxes, orders and order items in Django admin.
- Check total order weight against each box's maximum weight.
- Try orthogonal rotations (six possible orientations) when placing products.
- Handle quantities and multiple products using a deterministic 3-D placement heuristic.
- Recommend the lowest-cost box found suitable.
- Automated tests for basic selection, rotation, weight limits, no-fit and empty orders.

## Technology
- Python 3.10+
- Django 5.2
- SQLite (default development database)

## Setup

```bash
python -m venv .venv
# Windows:
.venv\Scripts\activate
# macOS/Linux:
source .venv/bin/activate
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver
```

Open http://127.0.0.1:8000/ for the recommendation page and http://127.0.0.1:8000/admin/ to manage data. Create products and boxes, then create an order and add its order items in the order admin screen.

## Run tests

```bash
python manage.py test
```

Save the actual terminal output in `TEST_OUTPUT.md` after running the tests in your environment. Do not claim tests passed until they have actually run.

## Recommendation approach

1. Reject an empty order with a validation message.
2. Calculate total order weight as the sum of each product's unit weight multiplied by quantity.
3. Consider boxes in ascending cost order.
4. Reject any box whose maximum weight is below the order weight.
5. For each remaining box, expand order quantities and sort products largest-volume-first.
6. For each product, try all distinct orthogonal rotations and candidate corner coordinates derived from the edges of already placed products. Reject placements outside the box or intersecting an existing placement.
7. Return the first box for which the heuristic finds a complete placement. Because boxes are checked by cost, this is the lowest-cost box found suitable by this heuristic.

## Assumptions and limitations
- Product and box dimensions use the same units: centimetres. Weight uses kilograms. Cost is stored as a decimal currency amount (the sample interface displays ₹).
- Products are treated as rigid rectangular cuboids. Packaging material, clearance, fragility, stacking constraints, hazardous-goods rules and carrier-specific rules are not modelled.
- The placement algorithm is a deterministic heuristic, not an exact bin-packing solver. It can fail to find a valid arrangement even when another arrangement exists. A production implementation should use a thoroughly validated packing solver and domain-specific constraints.
- The current interface is intentionally small. Authentication, permissions, audit logs, deployment hardening and production database configuration are not included.

## Before submission
- Run the test suite and record the real output.
- Review the packing heuristic and explain its limitations in your own words.
- Change the development secret key and configure `DEBUG`, `ALLOWED_HOSTS`, database and static files for any deployment.
- Complete `AI_USAGE.md` with your actual prompts, accepted/rejected suggestions, mistakes and verification steps.
- Write your own learning reflection and export your actual chat transcript yourself, as required by the assignment.
