**Details-**
* Author Name: Aditya Saxena[https://github.com/adityaax]
* Product: CE Phoenix Cart[https://github.com/CE-PhoenixCart/PhoenixCart]
* Package: includes/system/versioned/1.0.8.1/star_rating.php
* Affected versions: <=1.1.0.2
* Patched version: 1.1.0.3
* Weakness: CWE-703[Improper Check or Handling of Exceptional Conditions]
* Severity: Moderate 5.7/10
* Vector String: CVSS:3.1/AV:N/AC:L/PR:L/UI:R/S:U/C:N/I:N/A:H
* Fix commit link: https://github.com/CE-PhoenixCart/PhoenixCart/commit/436e87e11517d5f581312756c8c8904aaea1371b#diff-6e7b4ef70f8aeb2fae113acc2686a4ffc46addee32d15231aeec643ae5b7f10d

**Vulnerability-**
Unvalidated Review Star Rating Causes Admin Panel UI Lockout

**Summary-**
The application allows customers to leave product reviews by selecting a star rating between 1–5. However, if a user intercepts the request and submits a star value greater than 5 (e.g., 6), the value is accepted and stored.
Once this malformed review is saved, the admin panel — specifically the reviews.php page — becomes unresponsive. All review entries disappear from the interface, and JavaScript-based dropdown menus and admin action buttons across the dashboard fail to work due to frontend rendering or script errors.
The only recovery method for the administrator is to manually navigate to /customers.php and delete the affected customer account, after which the panel functionality is restored.
This vulnerability causes a persistent denial of service for the admin and results in loss of visibility and control over the store's review management system.

**Vulnerable Code-**
$star_rating = round($this->rating, 0, PHP_ROUND_HALF_UP);

**Fixed Code-**
$star_rating = round(min($this->rating, 5), 0, PHP_ROUND_HALF_UP);

**PoC-**
https://drive.google.com/file/d/1oF3L0M9KJSpCMe7GfJajIDYyNK6vZdk_/view?usp=drive_link

**Steps to reproduce-**
1. Log in as a customer and make a product purchase.
2. Navigate to the review submission form.
3. Select any star (1–5) and intercept the request using a proxy (e.g., Burp Suite).
4. Modify the star field to a value >5 (e.g., star: 6).
5. Submit the request.
6. Check the admin account(refresh the URL), all the buttons in the navbar will become unusable, also navigate to /review.php you will see the data is corrupted and previous reviews are lost.
7. Go to /customers.php(type in URL) and delete the customer to fix.

**Impact-**
1. Admin panel's reviews.php page becomes broken/unresponsive
2. All dropdown buttons in the admin panel stop working (likely due to a JS error or DOM rendering failure)
3. Entire admin UI is disrupted (denial of service for the admin)
4. The only fix is manually deleting the customer account from the database
