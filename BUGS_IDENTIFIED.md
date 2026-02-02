# 3 Critical Bugs Identified

## Bug #1: Cache Data Leakage (CRITICAL)
**File:** `backend/app/services/cache.py` line 15  
**Problem:** Cache key missing `tenant_id`
```python
cache_key = f"revenue:{property_id}"  # ❌ No tenant isolation
```
**Impact:** Client B sees Client A's revenue data (privacy breach)  
**Fix:** Add tenant_id to cache key
```python
cache_key = f"revenue:{tenant_id}:{property_id}"  # ✅ Fixed
```

---

## Bug #2: Decimal Precision Loss (CRITICAL)
**File:** `backend/app/api/v1/dashboard.py` line 18  
**Problem:** Converting decimal to float
```python
total_revenue_float = float(revenue_data['total'])  # ❌ Loses precision
```
**Impact:** Finance team reports revenue "off by cents"  
**Fix:** Keep as string for exact precision
```python
total_revenue_str = revenue_data['total']  # ✅ Fixed
```

---

## Bug #3: Timezone Missing Reservations (MAJOR)
**File:** `backend/app/services/reservations.py` lines 1, 23-25  
**Problem:** Using naive datetimes without timezone
```python
start_date = datetime(year, month, 1)  # ❌ No timezone
```
**Impact:** Client A's March revenue missing reservations  
**Fix:** Use UTC-aware datetimes
```python
from datetime import datetime, timezone
start_date = datetime(year, month, 1, tzinfo=timezone.utc)  # ✅ Fixed
```

---

## Summary
- ✅ All 3 bugs fixed
- ✅ Privacy: Tenant data now isolated
- ✅ Accuracy: Financial precision preserved
- ✅ Completeness: All reservations included
