# Flutter Guideline

Hi! this document contains recommendations indicating different policies, procedures or standard on how development should be conducted.

General coding guideline can be found here:
> https://gitlab.nortal.com/neomid/neos1-flutter/-/blob/master/analysis_options.yaml

<project>
	
## Project code structure

The project code should be structured with a mix of **folder-by-feature** and  **folder-by-type**.

Meaning at the root level you have folders defining features.
For example:
```
|-- common/
|-- authentication/
|-- settings/
```
Inside of each feature folder you have folders defined by types.
Here are folder types that would be part of feature folder.
```
|-- cubits/
|-- services/
|-- repositories/
|-- datasources/
|-- business_objects/
|-- dtos/
|-- exceptions/
|-- ui/
```

If there's more than one file related to a single file, you can group them in a folder, for example generated code for a class in a file.
For example:
```
|-- authentication/
|---- cubits/
|------ login_screen/
|-------- login_screen_cubit.dart
|-------- login_screen_cubit.freezed.dart
|-------- login_screen_cubit.g.dart
```
</project>

<cubits>

## Cubits

[Cubits](https://pub.dev/packages/flutter_bloc) are classes that contains business logic for your UI, cubit is bound to a state, that represent the UI state.

Guideline Recommendations:
 - Only cover business logic needed for the UI.
 - Return Futures for public actions.
 - Only interact with high level classes, meaning: Repositories, Services or any other high level coordinators.
 - Return final actions result to caller. For example:
 ```dart
 Future<String> bookAppointment(...data required for booking...);
 Future<boo> login(...data required to login...);
 ```
 - Only use **Repositories**, **Services** interfaces not concrete implementations.
 - Suffix your class with Cubit, example LoginCubit.
</cubits>

<cubitState>

## Cubit's State.
A cubit state class represent the state of ui bounded to it a given time.

Guideline Recommendations:
 - Use a single class for state or define a base class and define subclass for each state, you can use the dart meta [sealed](https://api.flutter.dev/flutter/meta/sealed-constant.html) annotation to have you with that.
 - Final actions result should not be part of the state. for example:
```dart
class LoginState {
	bool logginSuceeded // avoid this.
}
```
 - Keep the class in the same file were the Cubit class is defined.

</cubitState>

<repository>

## Repositories
Classes that provide access to data using **only** **CRUD** operations for a specific feature or a scope of a feature, for example:
```dart
abstract class BookingRepository {
	Futute<Booking> getBookingById(String id);
	Future<List<Booking>> getBookings();
	Future<void> deleteBookingById(String id);
	Future<void> saveBooking(Booking booking);
}
```

Guideline Recommendations:
 - Suffix class name with Repository.
 - Only define repositories for features or scope of features and name them so.
 - Use repositories to provide access to data and manipulate data.
 - Repositories should coordinating access between data sources or decide which data source to use for example by feature flag. Example:
```dart
class DefaultBookingRepository implements BookingRepository {
	final LocalBookingDataSource local;
	final RemoteBookingDataSource remote;

	Future<Booking?> getBookingById(String id) async {
		Booking? savedBooking = await local.getBookingById(id);
		
		if (savedBooking == null) {
			Booking? remoteBooking = await remote.getBookingById(id);
			
			if (remoteBooking != null) {
				await local.saveBooking(remoteBooking);
			}
			return remoteBooking;
		}
		return savedBooking;
	} 
	... other operations ...
}
```
 - Define interfaces for repository api and concrete implementation by type, for example:
 
 Interface
 ```dart
abstract class BookingRepository {}
```
Implementations
```dart
class DefaultBookingRepository implements BookingRepository {}
class AnonymousBookingRepository implements BookingRepository {}
class PremiumBookingRepository implements BookingRepository {}
```
- Do not use tools, platform related libraries nor perform network logic in repositories, encapsulate that in data sources.
- Only use data sources inside repositories.
- Only receive as input primitive or business object and output business objects.
- Return Futures for public API.

</repository>

<services>

## Services

Classes that provide access to functionalities for a specific feature or a scope of a feature, for example:
```dart
abstract class AuthenticationService {
	Future<void> authenticate(String username, String password);
	... other functionalities...
}
abstract class AppointmentService {
	Future<void> register(Appointment appointment);
	... other functionalities...
}
```
Guideline Recommendations.
-  Suffix class name with Service.
- Only define services for features or scope of features and name them so.
- Use services to provide functionalities required to implement a business logic.
- Use data sources to access to data required to implement a functionality business logic.
- Do not use tools, platform related libraries nor perform network logic in services, implement it in data sources.
- Only use data sources inside services.
- Only receive as input primitive or business object and output business objects.
- Return Futures for public API.
</services>

<datasources>

## Data sources
Classes that implement access to data located locally or remotely.
Example
```dart
abstract class LocalBookingDataSource {
	Futture<void> saveBooking(Booking booking);
	... other functionalities ...
}

abstract class RemoteBookingDataSource {
	Future<booking> saveBooking();
}
```

Guideline Recommendations:
- Keep the base type of data sources to local and remote.
- Name implementation after library or tool used, for example:
```dart
class SQLiteBookingDataSource implements LocalBookingDataSource {
............... Implementation ................
}

class RestApiBookingDataSource implements RemoteBookingDataSource {
............... Implementation ................
}

class MemoryBookingDataSource implements LocalBookingDataSource {
............... Implementation ................
}
```
- Handle library or tool errors in data source and convert them to app custom exception.
- Only receive as input primitive or business object and output business objects or primitive.
- Return Futures for public API.
</datasources>

<dtos>

## Data Transfer Objects - DTOS

Data transfer objects - used to transfer data from one system to another.

```dart

```

</dtos>
