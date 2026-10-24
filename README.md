
## Step 1: Identify the Equivalence Partitions

Equivalence partitioning is a black-box testing technique that divides inputs into groups that are expected to behave in the same way. Instead of testing every possible input, I can select representative values from each group.

For the North Vacation Booking application, I identified four test classifications:

### 1. Number of Nights
- Invalid low: 0 or fewer nights. Representative value: 0.
- Valid: 1 to 31 nights. Representative value: 7.
- Invalid high: More than 31 nights. Representative value: 32.

### 2. Number of Guests
- Invalid low: Fewer than 1 guest. Representative value: 0.
- Valid, standard rate: 1 to 5 guests. Representative value: 3.
- Valid, additional fee: 6 to 12 guests. Representative value: 7.
- Invalid high: More than 12 guests. Representative value: 13.

### 3. Check-in and Checkout Dates
- Valid dates: Check-in is before checkout. Example: November 10 to November 15, 2026.
- Invalid, same date: Check-in and checkout are the same. Example: November 10 to November 10, 2026.
- Invalid, reversed dates: Check-in is after checkout. Example: November 15 to November 10, 2026.
- Valid checkout exclusion: Checkout day is not counted as a reserved night. Example: November 10 to November 11, 2026, is 1 night.

### 4. Availability
- Valid, all nights available: Every requested night is available.
- Invalid, one night unavailable: At least one requested night is unavailable.
- Invalid, all nights unavailable: None of the requested nights are available.

These partitions help reduce the number of tests while still checking the important behaviors of the application.

## Step 2: Create Equivalence Partition Tables

### Number of Nights

| Classification | Partition | Range or Condition | Expected Result |
| -------------- | --------- | ------------------ | --------------- |
| Number of nights | Invalid low | 0 or fewer nights | `ValueError` |
| Number of nights | Valid | 1–31 nights | Reservation accepted if other conditions are valid |
| Number of nights | Invalid high | More than 31 nights | `ValueError` |

### Number of Guests

| Classification | Partition | Range or Condition | Expected Result |
| -------------- | --------- | ------------------ | --------------- |
| Number of guests | Invalid low | Fewer than 1 guest | `ValueError` |
| Number of guests | Valid, standard rate | 1–5 guests | No additional guest fee |
| Number of guests | Valid, additional fee | 6–12 guests | $25 per guest over 5, per night |
| Number of guests | Invalid high | More than 12 guests | `ValueError` |

### Check-in and Checkout Dates

| Classification | Partition | Range or Condition | Expected Result |
| -------------- | --------- | ------------------ | --------------- |
| Dates | Valid | Check-in before checkout, 1–31 nights | Reservation accepted if available |
| Dates | Invalid, same date | Check-in equals checkout | `ValueError` |
| Dates | Invalid, reversed | Check-in after checkout | `ValueError` |
| Dates | Valid, checkout excluded | Checkout is the day after check-in | Exactly 1 reserved night |

### Availability

| Classification | Partition | Range or Condition | Expected Result |
| -------------- | --------- | ------------------ | --------------- |
| Availability | Valid, all available | Every requested night is available | Reservation accepted |
| Availability | Invalid, partially unavailable | At least one requested night is unavailable | `UnavailableError` |
| Availability | Invalid, all unavailable | Every requested night is unavailable | `UnavailableError` |

## Step 3: Write the Test Cases

For these test cases, assume all requested nights are available unless the test specifically checks availability. All unspecified inputs are valid.

### TC01: Invalid Low Number of Nights

- **Classification:** Number of nights
- **Partition:** Invalid low
- **Input:** Check-in November 10, 2026; checkout November 10, 2026; 2 guests.
- **Expected Result:** `ValueError` because the reservation is for 0 nights.

### TC02: Valid Number of Nights

- **Classification:** Number of nights
- **Partition:** Valid
- **Input:** Check-in November 10, 2026; checkout November 17, 2026; 2 guests.
- **Expected Result:** Reservation accepted for 7 nights. Total cost is (7 × $200) + $50 = $1,450.

### TC03: Invalid High Number of Nights

- **Classification:** Number of nights
- **Partition:** Invalid high
- **Input:** Check-in November 1, 2026; checkout December 3, 2026; 2 guests.
- **Expected Result:** `ValueError` because the reservation is for 32 nights.

### TC04: Invalid Low Number of Guests

- **Classification:** Number of guests
- **Partition:** Invalid low
- **Input:** 0 guests, November 10–12, 2026.
- **Expected Result:** `ValueError` because at least 1 guest is required.

### TC05: Valid Standard Number of Guests

- **Classification:** Number of guests
- **Partition:** Valid, standard rate
- **Input:** 3 guests, November 10–12, 2026.
- **Expected Result:** Reservation accepted with no additional guest fee. Total cost is (2 × $200) + $50 = $450.

### TC06: Valid Additional Guest Fee

- **Classification:** Number of guests
- **Partition:** Valid, additional fee
- **Input:** 7 guests, November 10–12, 2026.
- **Expected Result:** Reservation accepted. Two guests are over the limit of 5, so the additional fee is 2 × $25 × 2 nights = $100. Total cost is $400 + $50 + $100 = $550.

### TC07: Invalid High Number of Guests

- **Classification:** Number of guests
- **Partition:** Invalid high
- **Input:** 13 guests, November 10–12, 2026.
- **Expected Result:** `ValueError` because the maximum number of guests is 12.

### TC08: Valid Check-in and Checkout Dates

- **Classification:** Check-in and checkout dates
- **Partition:** Valid dates
- **Input:** Check-in November 10, 2026; checkout November 15, 2026; 2 guests.
- **Expected Result:** Reservation accepted for 5 nights. Total cost is (5 × $200) + $50 = $1,050.

### TC09: Invalid Same Check-in and Checkout Date

- **Classification:** Check-in and checkout dates
- **Partition:** Invalid, same date
- **Input:** Check-in November 10, 2026; checkout November 10, 2026; 2 guests.
- **Expected Result:** `ValueError` because check-in must be before checkout.

### TC10: Invalid Reversed Dates

- **Classification:** Check-in and checkout dates
- **Partition:** Invalid, reversed dates
- **Input:** Check-in November 15, 2026; checkout November 10, 2026; 2 guests.
- **Expected Result:** `ValueError` because checkout occurs before check-in.

### TC11: Checkout Date Not Counted

- **Classification:** Check-in and checkout dates
- **Partition:** Valid, checkout excluded
- **Input:** Check-in November 10, 2026; checkout November 11, 2026; 2 guests.
- **Expected Result:** Reservation accepted for exactly 1 night. Total cost is $200 + $50 = $250.

### TC12: All Nights Available

- **Classification:** Availability
- **Partition:** Valid, all available
- **Input:** November 10–13, 2026; 2 guests. All 3 nights are available.
- **Expected Result:** Reservation accepted. Total cost is (3 × $200) + $50 = $650.

### TC13: One Night Unavailable

- **Classification:** Availability
- **Partition:** Invalid, partially unavailable
- **Input:** November 10–13, 2026; 2 guests. November 11 is unavailable.
- **Expected Result:** `UnavailableError` because at least one requested night is unavailable.

### TC14: All Nights Unavailable

- **Classification:** Availability
- **Partition:** Invalid, all unavailable
- **Input:** November 10–13, 2026; 2 guests. All 3 nights are unavailable.
- **Expected Result:** `UnavailableError` because none of the requested nights are available.

## Conclusion

Equivalence partitioning helps reduce the number of test cases by grouping similar inputs together. In this assignment, I created 14 test cases to check valid and invalid reservations, guest fees, date calculations, and availability.

By selecting representative values from each partition, I can test the expected behavior of North Vacation Booking without needing to see the application's source code.

## Reference

GeeksforGeeks. (2026, July 3). *Equivalence partitioning method*. https://www.geeksforgeeks.org/software-engineering/equivalence-partitioning-method/
