﻿# Android Project Guidelines

This is general guideline and rules to follow for android application development.

## Follow Field Naming Conventions

- Non-public, non-static field names start with m.
- Static field names start with s. (if it is private static)
- Other fields start with a lower case letter.
- Public static final fields (constants) are ALL_CAPS_WITH_UNDERSCORES.

For example:
	
```java
public class MyClass {
    public static final int SOME_CONSTANT = 42;
    public int publicField;
    private static MyClass sSingleton;
    int mPackagePrivate;
    private int mPrivate;
    protected int mProtected;
}
```


## All the java files and XML files should have four (4) space indent, **NOT TABS.** 

- Use four (4) space indents for blocks and never tabs. When in doubt, be consistent with the surrounding code.
- Use eight (8) space indents for line wraps, including function calls and assignments. 

For example, this is correct:

```java
Instrument i =
        someLongExpression(that, wouldNotFit, on, one, line);
```

while this is not correct:

```java
Instrument i =
    someLongExpression(that, wouldNotFit, on, one, line);
```


## Limit Line Length

- Each line of code in your project must be 100 characters long. More code should be surrounded and the limit remains as 100 characters long.
- If comment line containt an example commands or URL longer then 100 characters, then that line may be longer than 100 characters
- Import lines may also go longer then 100 characters long


## Application log should be written in proper manner with proper declaration (ERROR, WARNING, INFORMATION, DEBUG and VERBOSE). Final Log should be written in separate file. So it is easy to manage it any time. Also use Log instead of native System.out.printf or printfln.

- ERROR: Use when something fatal has happened.
- WARNING: Use when something serious and unexpected happened.
- INFORMATIVE: Use to note inportant information that is required for application to trace and look for.
- DEBUG: Use for debug purposes. 
- VERBOSE: Use for everything else. This level will only be logged on debug builds and should be surrounded by an if (LOCAL_LOGV) block (or equivalent) so it can be compiled out by default. Any string building will be stripped out of release builds and needs to appear inside the if (LOCAL_LOGV) block.

```java
Log.v(String, String) (verbose)
Log.d(String, String) (debug)
Log.i(String, String) (information)
Log.w(String, String) (warning)
Log.e(String, String) (error)
```
For example:

```java
Log.i("MyActivity", "MyClass.getView() — get item number " + position);
```


## Don’t ignore Exceptions. Don’t catch generic Exceptions. 

Exceptions has to be well cached and well-handled for each exceptions. Exception should be globally handled.

Check below unproper example:
```java
void setServerPort(String value) {
    try {
        serverPort = Integer.parseInt(value);
    } catch (NumberFormatException e) { }
}
```

Aslo don't catch generic exception like below:

```java
try {
   someComplicatedIOFunction();        // may throw IOException
   someComplicatedParsingFunction();   // may throw ParsingException
   someComplicatedSecurityFunction();  // may throw SecurityException
   // phew, made it all the way
} catch (Exception e) {                // I'll just catch all exceptions
   handleError();                      // with one generic handler!
}

```

Use comment to explaing exception as below example:

```java
/** If value is not a valid number, original port number is used. */
void setServerPort(String value) {
    try {
        serverPort = Integer.parseInt(value);
    } catch (NumberFormatException e) {
        // Method is documented to just ignore invalid user input.
        // serverPort will just be unchanged.
    }
}
```

Note: Define one class with name Exceptionhandler.java and mention genaric method to call for all exception with in the app. With this, you will have once place to handled/manage all exceptions of the application.


## Java-doc standard comments should be used for explanation of the method, class and declaration whenever required.

Every class and nontrivial public method you write must contain a Javadoc comment with at least one sentence describing what the class or method does. This sentence should start with a third person descriptive verb.

Examples:

```java

/** Returns the correctly rounded positive square root of a double value. */
static double sqrt(double a) {
    ...
}
```

or

```java
/**
 * Constructs a new String by converting the specified array of
 * bytes using the platform's default character encoding.
 */
public String(byte[] bytes) {
    ...
}
```
	

## Listener used in the activity has to be implemented first and then used its override methods in same activity. If there are more than 2-3 fields included with listener with in same activity then that should be handled by Switch-case, else normal if-else condition should be used.

Implementing a Listener is quite easy. There are 3 ways to implement an Listener and the have their advantages and disadvantages.
The three way to implement Listeners are

- Inline Implementation
- Using the implements keyword
- By using variables


**Recommended - Using the “implements” keyword**

Implement a listener by adding an interface to your base class. In java you can do this by adding “implements Interfacename” to the class declaration.

 ```java
public class ExampleImplements extends Activity implements OnClickListener {
 
   
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
 
    ...        
 
        // Set Click Listener
        btnLogin.setOnClickListener(this);
        btnCancel.setOnClickListener(this);
    }
 
    @Override
    public void onClick(View v) {
        if(v==btnLogin) {
            // Check Login
            String username = etUsername.getText().toString();
            String password = etPassword.getText().toString();
             
            if(username.equals("guest") && password.equals("guest")){
                lblResult.setText("Login successful.");
            } else {
                lblResult.setText("Login failed. Username and/or password doesn't match.");
            }
        } else if(v==btnCancel) {
            // Close the application
            finish();
        }
    }
}

 ```

**Advantages**

- Methods/Listener can be reused in many different widgets
- Code of multiple Listeners is located in the same section of code
- Can be used to create one method for similar Listeners


## Project Structure and files arrangement should be in such a way that can be identified easily by any developer. Follow below file arrangement and project structure for best practice.

There has to be separate files for each class in my Android projects, the only exception being AsyncTasks. Having this many java files means you have to have more packages than the base package. Here the example ended up with a package for each type of main class. Each class should named ending with its type.

- **com.example**
  - **activities (package):** Contains all the activities. Classes are all named with Activity at the end. That way, you can immediately know what it is when reading Java code that doesn't have its full package name.

  - **adapters (package):** Contains all the adapters.

  - **authenticator (package):** Contains any class related to signing a user in. I create a local account and having all related classes together is very handy.

  - **data (package):** Contains all classes related to data management such as ContentProvider and SQLiteHelper.

  - **data.migrations (package):** Contains all of my SQLite migrations. I created a class for migrations, read about it here, and put them all in this package.

  - **fragments (package):** Contains all fragments.

  - **helpers (package):** Contains helper classes. A helper class is a place to put code that is used in more than one place. I have a DateHelper for instance. Most of the methods are static.

  - **interfaces (package):** Contains all interfaces.

  - **models (package):** Contains all local models. When syncing from an HTTP API I parse the JSON into these Java objects using Jackson. I also pull Cursor rows into these models as well.

  - **preferences (package):** Contains all classes for custom preferences. When creating the preferences I required a custom PreferenceDialog as well as a custom PreferenceCategory. They live here.

  - **sync (package):** Contains all classes related to syncing. I use a SyncAdapter to pull data from an HTTP API. In addition to the SyncAdapter a SyncService is required, so I created a package.


## Use Gradle

- Since we are using gradle framework, project related information like version code, version name, package name, release keystore file location, keystore password and other required declaration should be written in to gradle itself.
  
  For example:

  ```java
  apply plugin: 'com.example.application'
  android {
    compileSdkVersion 23
    buildToolsVersion "23.0.2"
    defaultConfig {
        applicationId "com.google.googleio"
        minSdkVersion 22
        targetSdkVersion 23
        versionCode 1
        versionName "1.0"
    }
    buildTypes {
        ...
    }
  }
 dependencies {
  ...  
 }
 ```

- Use android manifest for declaring permission, user-permission, services, receivers, intent-filter, activities, application class and its attribute.
  
  For example
  ```xml
  <?xml version="1.0" encoding="utf-8"?>

  <manifest>

      <uses-permission />
      <permission />
      <permission-tree />
      <permission-group />
      <instrumentation />
      <uses-sdk />
      <uses-configuration />  
      <uses-feature />  
      <supports-screens />  
      <compatible-screens />  
      <supports-gl-texture />  
  
      <application>
  
          <activity>
              <intent-filter>
                  <action />
                  <category />
                  <data />
              </intent-filter>
              <meta-data />
          </activity>
  
          <activity-alias>
              <intent-filter> . . . </intent-filter>
              <meta-data />
          </activity-alias>
  
          <service>
              <intent-filter> . . . </intent-filter>
              <meta-data/>
          </service>
 
          <receiver>
              <intent-filter> . . . </intent-filter>
              <meta-data />
          </receiver>
  
          <provider>
              <grant-uri-permission />
              <meta-data />
              <path-permission />
          </provider>
  
          <uses-library />
  
      </application>
  
  </manifest>
  ```


## Use Semantic Versioning 

Give project version name and number as per below information and updates: 

For example: v2.4.10 (Where 2 is MAJOR, 4 is MINOR and 10 is PATCH)

- MAJOR version when you make incompatible API changes,
- MINOR version when you add functionality in a backwards-compatible manner, and
- PATCH version when you make backwards-compatible bug fixes.
- Additional labels for pre-release and build metadata are available as extensions to the MAJOR.MINOR.PATCH format.


## Always use gradle to import external library

External library should be imported by maven or jCenter only. If library not available in maven or jCenter then that library (.jar file or module) should be placed into the libs folder under that particular module. (Such library should be also handled in .gitignore file to get committed along with code)

For example:

```java
dependencies {
    compile 'org.hibernate:hibernate-core:3.6.7.Final'
}
```


## Avoid reduddent call of same method

Method should not be called redundant. The Value of the method should store in one variable and that should be used whenever required. Method should be called only when it’s required to.

Check beow method:

```java
public static boolean isResponseOk(ResponseHandler responseHandler, Activity mContext) {
	boolean isResponseOk = false;
	long responseCode = responseHandler.getResponseCode();
	String responseString = responseHandler.getResponseString();
	if(responseHandler != null) {
	   if(responseCode == HttpURLConnection.HTTP_OK){
		   isResponseOk = true;
		   WLogger.d(TAG, responseCode + " ==== " + responseString);
	   }else if(responseCode == HttpURLConnection.HTTP_FORBIDDEN) {
		   // some task
		   WLogger.e(TAG, responseCode + " ==== " + responseString);
	   }else if(responseCode == HttpURLConnection.HTTP_VERSION){
		   // some other task
		   WLogger.e(TAG, responseCode + " ==== " + responseString);
	   }else{
		   WLogger.e(TAG, responseCode + " ==== " + responseString);
	   }
	}
    return isResponseOk;
}
```

In this example, the value of method **getResponseCode()** and **getResponseString()** is stored into another variable and that is going to used in the same method when needed.


## Use proper arrangement of AndroidManifest file

Keep arrangement for android Manifest tags as per structure given in [this section.](https://github.com/ShreyashPromact/guidelines/blob/master/Mobile/Android/CodeGuidelines.md#use-gradle).


## Don't code for same layout, either include it

Commonly used layout should be kept into different layout file and that should be included whenever needed in xml layout. Don’t draw same layout again and again by code.

For example:

Below is **include_toolbar.xml** which is commanly used in other layout file as well.

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v7.widget.Toolbar
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/toolbar"
    android:layout_height="?attr/actionBarSize"
    android:layout_width="match_parent"
    android:background="?attr/colorPrimary"
    app:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    app:popupTheme="@style/ThemeOverlay.AppCompat.Light"/>
```

Now, include that layout as below:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <android.support.v4.widget.SwipeRefreshLayout
	.../>
        <android.support.v7.widget.RecyclerView
	    .../>
    </android.support.v4.widget.SwipeRefreshLayout>

    <!-- Here I have included toolbar layout -->    
    <include layout="@layout/includes_toolbar" />

    <TextView
        ...
	/>
</RelativeLayout>

```


## Commonly used strings & functions in java files should be defined in a common class. 

Strings and method that are often used in to class should be kept into the seperate class then that can be accessable threw the instance of the class. 
Note: Strings and method should not be statically define in the project.

For example: **GlobalInstance.java** is the class used to keep commanly used Strings and methods of the project. 

Here **AZURE_USER** and **getAzureUser()** is the String and method respectivly used commanly in all other class of the project.

```java
public class GlobalInstance {
    private static GlobalInstance uniqueInstance = new GlobalInstance();
    private GlobalInstance(){}
    public static GlobalInstance getInstance()
    {
        return uniqueInstance;
    }
    
    String AZURE_USER = "user";
    
    public String getAzureUser(){
        return AZURE_USER;
    }
}
```

Now, Checkout how to use that string and method in other classes.

First, you need to create instance of the GlobalInstance class.

```java
private GlobalInstance instance = GlobalInstance.getInstance();
```

Now, you can use the String and method as follow:

```java
String someString = instance.AZURE_USER;
String someOtherString = instance.getAzureUser();
```


## Content description should be there for all required fields in xml layout files.

Content description gives more general information about the view. Defining content description for view, gives more indentification of the view and that can also usefull for automation testing to identify view.

For example:
```xml
<Button
    android:id=”@+id/pause_button”
    android:src=”@drawable/pause”
    android:contentDescription=”@string/pause”/>
```


## Use resource files to store literal string, dimension, styles, theme etc

- Use **res/values/strings.xml** file to store string literal for xml layout

  For example:
  ```xml
  <?xml version = "1.0" encoding = "utf-8"?> 
  <resources> 
      <string name="hello"> Hello! </string> 
  </resources>
  ```

- Use **res/values/dimens.xml** file to store dimention used in xml layout

  For example:
  ```xml
  <?xml version = "1.0" encoding = "utf-8"?> 
  <resources> 
      <dimen name="textview_height">25dp</dimen> 
  </resources>
  ```

- Use **res/values/styles.xml** file to create style for view

  For example:
  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <resources>
     <style name="CustomText" parent="@style/Text">
        <item name="android:textSize">20sp</item>
        <item name="android:textColor">#008</item>
     </style>
  </resources>
  ```
  Now, use that style in to main layout as below:

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <EditText
     style="@style/CustomText"
     android:layout_width="fill_parent"
     android:layout_height="wrap_content"
     android:text="Hello, World!" />
  ```

- Use **res/values/themes.xml** file to create theme for layout of whole app or view

  For example:
  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <resource>
    <style name="AppTheme" parent="android:Theme.Material">	
      <item name ="android:color/primary">@color/primary</item>
      <item name ="android:color/primaryDark">@color/primary_dark</item>
      <item name ="android:colorAccent/primary">@color/accent</item>
    </style>
  <resource>
  ```


## IDs name should be well define in manner that can easily identify

  IDs should be prefixed with the name of the element in lowercase underscore. 

  For better way, start String names with a prefix that identifies the section they belong to

  For example:

  ```xml
  <resources> 
      <string name="registration_email_hint"> email </string> 
      <string name="registration_name_hint"> name </string> 
  </resources>
  ```  

## Java source file organization

- Each Java source file contains a single public class or interface. When private classes and interfaces are associated with a public class, you can put them in the same source file as the public class. The public class should be the first class or interface in the file.

- Java source files have the following ordering:

     - Beginning comments
     - Package and Import statements
     - Class and interface declarations : 
     - Part of Class/Interface Declaration :
       - Class/interface documentation comment
       - Class or interface statement
       - Class/interface implementation comment, if necessary
       - Class (static) variables :
           - public
           - protected
           - private
       - Instance variables :
           - public
           - protected
           - private
       - Constructors
       - Methods
			

## Line breaks

When an expression will not fit on a single line, break it according to these general principles:

- Break after a comma.
- Break before an operator. 
- Prefer higher-level breaks to lower-level breaks. 
- Align the new line with the beginning of the expression at the same level on the previous line. 

For example :

```java
	var = function1(longExpression1,
			function2(longExpression2,
				longExpression3));
```
	
## Declarations

###Placement

Put declarations only at the beginning of blocks. (A block is any code surrounded by curly
braces “{” and “}”.) Don’t wait to declare variables until their first use; it can confuse the
unwary programmer and hamper code portability within the scope.

For example :
```java
		void MyMethod() {
			int int1; // beginning of method block
			if (condition) {
				int int2; // beginning of "if" block
				...
			}
		}
```
	
###Class and Interface Declarations

When coding Java classes and interfaces, the following formatting rules should be followed:

- No space between a method name and the parenthesis “(“ starting its parameter list
- Open brace “{” appears at the end of the same line as the declaration statement
- Closing brace “}” starts a line by itself indented to match its corresponding opening statement, except when it is a null statement the “}” should appear immediately after the “{“.
- Methods are separated by a blank line
- For example :

```java
	class Sample extends Object {
		int ivar1;
		int ivar2;
		
		Sample(int i, int j) {
			ivar1 = i;
			ivar2 = j;
		}
		
		int emptyMethod() {}
		...
	}
```

## File naming

- Class names are written in UpperCamelCase.

    For classes that extend an Android component, the name of the class should end with the name of the component;

    For example - `SignInActivity`, `SignInFragment`, `ImageUploaderService`, `ChangePasswordDialog`

- Resources file names are written in lowercase_underscore.

	| Asset Type   	| Prefix        | Example  		|
	| --------------|:-------------:| ---------------------:|
	| Menu 		| `menu_`	| menu_bg.9.png		|
	| Button   	| `btn_`     	| btn_pressed.9.png 	|
	| Icon     	| `ic_` 	| ic_star.9.png 	|
	| Tab      	| `tab_`      	| tab_focused.9.png 	|
	
- Layout files should match the name of the Android components that they are intended for but moving the top level component name to the beginning.

	| Component	| Class Name		| Layout Name			|
	| --------------|:---------------------:| -----------------------------:|
	| Activity	| UserProfileActivity	| activity_user_profile.xml  	|
	| Fragment	| SignUpFragment	| fragment_sign_up.xml		|
	| Dialog	| ChangePasswordDialog	| dialog_change_password.xml 	|


## XML style rules

 - **Use self closing tags :** When an XML element doesn't have any contents, you must use self-closing tags.

 - **Resource naming :** Resource IDs and names are written in lowercase_underscore. IDs should be prefixed with the name of the element in lowercase underscore.
	
	| Element   	| Prefix        |
	| --------------|:-------------:|
	| TextView 	| `text_`	|
	| ImageView   	| `image_`     	|
	| Button     	| `button_`	| 
	| Menu      	| `menu_`      	|
 
 For example :
 
 ```xml
 <ImageView
    android:id="@+id/image_profile"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content" />
 ```
- **Attributes ordering :** As a general rule you should try to group similar attributes together. A good way of ordering the most common attributes is:

     - View Id
     - Style
     - Layout width and layout height
     - Other layout attributes
     - Remaining attributes
 
## String constants, naming, and values

- Many elements of the Android SDK such as SharedPreferences, Bundle, or Intent use a key-value pair approach so it's very likely that even for a small app you end up having to write a lot of String constants. When using one of these components, you must define the keys as a static final fields and they should be prefixed as indicated below.

		Element			| Field Name Prefix 	|
		------------------------|:---------------------:|
		SharedPreferences	| PREF_			|
		Bundle			| BUNDLE_		|	
		Fragment Arguments	| ARGUMENT_		|
		Intent Extra		| EXTRA_		|
		Intent Action		| ACTION_		|


- String names start with a prefix that identifies the section they belong to. 
For example - registration_email_hint, registration_name_hint. 

- If a string doesn't belong to any section, then you should follow the rules below:

		Prefix	| Description				|
		--------|:-------------------------------------:|
		error_	| An error message			|
		msg_	| A regular information message		|
		title_	| A title, i.e. a dialog title		|
		action_	| An action such as "Save" or "Create" 	|


# GENERAL APPROACH

## Learn git CLI by heart, stop using GUI

The first and most important practice of git is learn all the git commands by heart and use them, do not rely on GUI for git. Don’t use direct options available in IDE for code repository.

## Using Android Product Flavors

ProductFlavor is a very powerful feature available in the Android gradle plugin that allows.
For best practice, define flavour of the project in gradle for development, stagging & production environment.
Make more flavour if there are more environment for the project needed. 

## Using Crashing Reporting Tools

As every app developer surely knows, monitoring your app’s stability is a must. And there’s hardly anything more important than keeping your crash count down to a minimum.
Fortunately, there is a variety of crash reporting tools at your disposal with which you can arm yourself. 
Always integrate any one such tool before delivering an App to Tester or Client. It helps us to identify and respond to common crashes in a timely manner.

## Use Rest Client

Almost all of the apps we develop need to communicate in some way or another with a remote backend. To do this, either come up with our own solution or  make use of any of the many battle tested libraries available.
Always create an interface which contains the common network related methods like addHeaders(), addParam(), doGet(), doPost() etc. Then implement the interface and write your own logic to handle API call. 

## Using 9 patch images

9 Patch images are stretchable, repeatable images reduced to their smallest size.the image won't stretch and loose proportions in different screen sizes. One more and biggest advantage is memory. 
Same small size memory can be reused for different screen size devices. Well-designed 9-patch images are less error-prone and have high re-usability. 





### Reference
http://source.android.com/source/code-style.html#follow-field-naming-conventions
https://source.android.com/source/code-style.html#use-spaces-for-indentation
https://source.android.com/source/code-style.html#limit-line-length
https://source.android.com/source/code-style.html#log-sparingly
https://source.android.com/source/code-style.html#dont-ignore-exceptions
https://source.android.com/source/code-style.html#use-javadoc-standard-comments
https://developer.android.com/guide/topics/ui/ui-events.html#EventListeners
http://blog.smartlogic.io/2013-07-09-organizing-your-android-development-code-structure/
https://developer.android.com/studio/publish/versioning.html
http://semver.org/
https://developer.android.com/guide/topics/manifest/manifest-intro.html
http://www.oracle.com/technetwork/java/codeconventions-150003.pdf