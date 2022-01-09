# [DataBindingUsage](https://classroom.udacity.com/courses/ud9012/lessons/4f6d781c-3803-4cb9-b08b-8b5bcc318d1c/concepts/a39e5191-f206-43a4-b293-3f1d6f6a6549)

<img width="348" src="https://github.com/YamamotoDesu/DataBindingUsage/blob/master/app/src/main/java/com/codewithkyo/aboutme/gif/InputNickname.gif"> 

## Data Binding - The Idea
* The big idea about data binding is to create an object that connects/maps/binds two pieces of distant information together at compile time, so that you don't have to look for it at runtime.
* The object that surfaces these bindings to you is called the Binding object. It is created by the compiler, and while understanding how it works under the hood is interesting, it is not necessary to know for basic uses of data binding.

## Data Binding and findViewById
* findViewById is a costly operation because it traverses the view hierarchy every time it is called. 
* With data binding enabled, the compiler creates references to all views in a <layout> that have an id, and gathers them in a Binding object.
In your code, you create an instance of the binding object, and then reference views through the binding object with no extra overhead.
  
## 1. [Enable data binding](https://github.com/YamamotoDesu/ViewBinding)
```gradle
  android {
    ...
    buildFeatures {
        dataBinding true
    }
```

```kt
      private lateinit var binding: ActivityMainBinding
  
      override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        //setContentView(R.layout.activity_main)
        binding = DataBindingUtil.setContentView(this, R.layout.activity_main)


```
  
## 2. Create data class 
```kt 
data class MyName(var name: String = "", var nickname: String = "")
```
  
## 3. Set Layout 
```xml 
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">
   <data>
       <variable
           name="myName"
           type="com.codewithkyo.aboutme.MyName" />
   </data>
   <LinearLayout
      android:layout_width="match_parent"
      android:layout_height="match_parent"
     ...
    
    <TextView
      android:id="@+id/textView"
      style="@style/NameStyle"
      android:layout_width="match_parent"
      android:layout_height="wrap_content"
      android:text="@={myName.name}"
      android:textAlignment="center" />

     <EditText
       android:id="@+id/nickname_edit"
       style="@style/NameStyle"
       android:layout_width="match_parent"
       android:layout_height="wrap_content"
       android:hint="@string/what_is_nickname"
       android:inputType="none|textPersonName|textImeMultiLine"
       android:minHeight="48dp"
       android:textAlignment="center" />
  
      <TextView
        android:id="@+id/nickname_text"
        style="@style/NameStyle"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textAlignment="center"
        android:visibility="gone"
        android:text="@={myName.nickname}"/>
</layout>
```
  
## 4. Bind Data 

* Original
```kt 
  class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = DataBindingUtil.setContentView(this, R.layout.activity_main)
  
       findViewById<Button>(R.id.done_button).setOnClickListener {
           addNickname(it)
       }
    }
  
    private fun addNickname(view: View) {
       val editText = findViewById<EditText>(R.id.nickname_edit)
       val nicknameTextView = findViewById<TextView>(R.id.nickname_text)

       nicknameTextView.text = editText.text
       editText.visibility = View.GONE
       view.visibility = View.GONE
       nicknameTextView.visibility = View.VISIBLE

        // Hide the keyboard
        val imm = getSystemService(Context.INPUT_METHOD_SERVICE) as InputMethodManager
        imm.hideSoftInputFromWindow(view.windowToken, 0)
    }
```
  
* Data Binding
```kt 
  class MainActivity : AppCompatActivity() {

    private lateinit var binding: ActivityMainBinding

    private val myName: MyName = MyName("Kyo Yamamoto")

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = DataBindingUtil.setContentView(this, R.layout.activity_main)

        binding.myName = myName
        binding.doneButton.setOnClickListener {
            addNickname(it)
        }
    }
  
    private fun addNickname(view: View) {

        binding.apply {
            //nicknameText.text = nicknameEdit.text
            myName?.nickname = nicknameEdit.text.toString()
            invalidateAll()
            nicknameEdit.visibility = View.GONE
            doneButton.visibility = View.GONE
            nicknameText.visibility = View.VISIBLE
        }

        // Hide the keyboard
        val imm = getSystemService(Context.INPUT_METHOD_SERVICE) as InputMethodManager
        imm.hideSoftInputFromWindow(view.windowToken, 0)
    }
```
