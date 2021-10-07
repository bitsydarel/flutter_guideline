# Flutter Guideline

Hi! this document contains recommendations indicating different policies, procedures or standard on how development should be conducted.

General coding guideline can be found, in your project here **analysis_options.yaml** file.

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
|-- ui/
|-- exceptions/
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
 Future<String?> bookAppointment(...data required for booking...);
 Future<bool> login(...data required to login...);
 ```
 - Only use **Repositories**, **Services** interfaces not concrete implementations.
 - Suffix your class with Cubit, example LoginCubit.
 - Cubit class files should be under the cubits folder of the feature.
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
- Implementations of the **Repository API** should be named with the **repository class** name as suffix.
- Return Asynchronous type for public API (Future or Stream).
- Implementation of the repository should not change their public API (return type, method arguments).
- Repository class files should be under the repositories folder of the feature.

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
-  Suffix class name with **Service**.
- Only define services for features or scope of features and name them so.
- Use services to provide functionalities required to implement a business logic.
- Use data sources to access to data required to implement a functionality business logic.
- Do not use tools, platform related libraries nor perform network logic in services, implement it in data sources.
- Only use data sources inside services.
- Only receive as input primitive or business object and output business objects.
- Implementations of the **Service API** should be named with the **service class** name as suffix.
- Return Asynchronous type for public API (Future or Stream).
- Implementation of the service should not change their public API (return type, method arguments).
- Service class files should be under the services folder of the feature.

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
- Name implementation after library or tool used and the data source name as suffix, for example:
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
- Implementations of the **Data Source API** should be named with the **Data Source class** name as suffix.
- Return Asynchronous type for public API (Future or Stream).
- Implementation of the data sources should not change their public API (return type, method arguments).
- Data sources class files should be under the datasources folder of the feature.

</datasources>

<dtos>

## Data Transfer Objects - DTOS

Data transfer objects - used to transfer data from one system to another.
Example
```dart
class ApiRequest {
... fields...
}
class ApiResponse {
... fields...
}
```
```dart
class SQLiteTableDefinition {
... fields ...
}
```

Guidelines:

- Use DTO to represent database tables.
- Use DTO to represent api request and response.
- Use DTO to implement library required data.
   For example to use a persistence library your data need to extend an object or add more fields, create a DTO instead.
- Implement mappers in the DTO class, for example.

```dart
class ApiRequest {
	... fields...
	factory ApiRequest.fromBO(BOObject bo) {
		...mapper code...
	}
	BOObject toBO() {
		... mapper code...
	}
}
```
- Do not implement saving or fetching logic in DTO, implement it in data sources.
- DTO class files should be under the dtos folder of the feature.

</dtos>

<bo>

## Business Object

Business Object are data classes representation of a part of the business or an item within it, they are used to execute business logic independently of platform, library or tools.

A business object may represent, for example, **a person, place, event, business process, or concept** and  **invoice, a product, a transaction or even details of a person**.

Guideline:

- Business Object classes name should be suffixed with BO.
- Business Object classes should only contains data fields or method to format data fields.
- Business Object classes should implement equals and hash-code methods, fromJson and toJson.
- Business Object classes fields type should only be primitive or other business object.
- Business Object class files should be under the business_objects folder of the feature.

</bo>

<ui>

## User Interface (UI)

Code that are related to the user's device interface, for example: Components, Screens, PageBuilders.

### Screens

Guideline:

- Screens filename and classes should be suffixed with **Screen**.
- Screens classes should only interact with cubit classes.
- If the screen widget tree has different sections that should update without updating the full screen, group them by private widget classes that represent updated regions. For example:
```dart
class LoginScreen extends StatelessWidget {
... fields and constructor...
	@override  
	Widget build(BuildContext context) {  
	    return Column(  
		    children: <Widget>[
			    _LoginLogo,
			    _LoginForm,
			    _LoginActions,
			    _LoginFooter
		    ],
		);
	}
}

class _LoginLogo extends StatelessWidget {}
class _LoginForm extends StatefullWidget {}
class _LoginActions extends StatelesssWidget {}
class _LoginFooter extends StatelessWidget {}
```
- If a screen use a cubit, events for ui state changes should only be driven by the cubit.
- Use private widget classes instead of functions to build subtrees, for example:
```dart
class SomeWidget extends StatelessWidget {
	@override
	Widget build(BuildContext context) {
		... 
	}
}
class _SubTree1 extends StatelessWidget {}
class _SubTree2 extends StatelessWidget {}
class _SubTree3 extends StatelessWidget {}
class _SubTree4 extends StatelessWidget {}
```

</ui>

<localization>

## Localization
For localization we use bd_l10n tool:
```yaml
project-type: flutter
features:
  - name: 'Common Localization' # name of the feature is also used as name of the generated file and class.
    translation-dir: translation/common # where your localized messages are stored.
    translation-template: common_en.arb # which file to use as template.
    output-dir: lib/localizations/ # directory were the generated code will be generated.
```

When creating a new feature, for example Authentication, you would add the following settings:
```yaml
project-type: flutter  
  
features: # list of features used in the project, a project must have at least one feature.  
  - name: 'Common Localization' # name of the feature is also used as name of the generated file and classes.  
  translation-dir: translation/common # where your localized messages are stored.  
  translation-template: en.arb # which file to use as template, this file should be in the translation-dir  
  output-dir: lib/localizations/ # Were the generated localization classes written.  

  - name: 'Authentication Localization' # name of the feature is also used as name of the generated file and classes.  
  translation-dir: translation/authentication # where your localized messages are stored.  
  translation-template: en.arb # which file to use as template, this file should be in the translation-dir  
  output-dir: lib/localizations/ # Were the generated localization classes written.
```

<localization/>
