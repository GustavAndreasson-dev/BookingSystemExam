DEGUGGING

Issue where it was sometimes possible to create overlapping booking in the same room.

To test this the following was tried:<br>
Booking with time 9:00-11:00 and room Aspen already exists.<br>
When entering the following bookings;<br>
a) Booking 08:00-10:00 is incorrectly created.<br>
b) Booking 10:00-12:00 is correctly rejected.<br>
c) Booking 09:30-10:30 is correctly rejected.<br>
d) Booking 08:00-12:00 is incorrectly created.<br>
This caused he tests relating to a and d in Create_WhenBookingOverlapsExistingBooking_ReturnsFailure.<br>

What was wrong:<br>
The failing code was the Validate method in BookingService. The validation only checked if the new starting time was later than the existing StartTime and if the new StartTime was earlier than the existing EndTime. This did not check anything about the EndTime. It only checked if the StartTime was in the interval. This caused two issues, it was possible to create a booking where the EndTime was in the interval (a) and it was possible to create a booking where starting was earlier than the interval and the ending was later (d).<br>

The fix:<br>
To fix this you could add additional checks to combat the specific issues, for example check if StartTime is earlier than the existing StartTime AND the EndTime is later than the existing EndTime. <br>
But a much cleaner way to do it is to check if the StartTime is before existing EndTime AND EndTime is after existing StartTime. Which resulted in the following code replacing the old one.<br>
booking.StartTime < existing.EndTime && booking.EndTime > existing.StartTime);<br>
This fix also makes sure that it is possible to create a booking where the StartTime is equal to the existing Endtime and the other way around.<br>

To check that the issue was resolved and not causing any other issues the test were ran again and all four were green. It was also tested to manually entering a-d which all was rejected correctly. Also checked that adjacent bookings like 11:00-12:00 and 09:00 was still correctly allowed to be created.<br>
