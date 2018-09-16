# Kotlin Tips And Tricks

This post is a series of tips and tickets that [Elliot](https://github.com/elroid) compiled and showed off for his Kotlin Knights meetup in August.

## `when` exhaustive

This is an easy trick to force you to consider all the cases of an Enum or Sealed class. The secret here is to trick the Kotlin compiler into treating `when` as an expression so that it enforces the compile time check of making you specify all the possible branches.

```Kotlin
val <T> T.exhaustive: T
get() = this

// Expression style is exhaustive by default
val x = when { }

// However we can force a non expression style to be exhaustive too!
when(state) {
  FindCarContract.MyLocationState.DISABLED ->
  findMeButton.setImageResource(R.drawable.ic_location_disabled_24dp)
  FindCarContract.MyLocationState.INACTIVE, FindCarContract.MyLocationState.SEARCHING ->
  findMeButton.setImageResource(R.drawable.ic_location_searching_24dp)
  FindCarContract.MyLocationState.FOUND ->
  findMeButton.setImageResource(R.drawable.ic_my_location_24dp)
}.exhaustive
```
From [ProAndroidDev Medium post](https://proandroiddev.com/til-when-is-when-exhaustive-31d69f630a8b).

## Elvis operator and inline variables in strings

This one is a trick that Groovy users will be familiar with.

```Kotlin
// Instead of
val vehicle = it.getVehicle(vid)
if(vehicle == null) throw Exception("No vehicle found with id $vid")

// You can do
val vehicle = it.getVehicle(vid) ?: throw Exception("No vehicle found with id $vid")			
```
## Lint errors

Do you find that errors keep showing up? Keep typing! Lint errors often look worse than they are, especially if you are accidentally typing Java!


## Kotlin scoping functions

Writing Kotlin scoping functions can help make your code more expressive. Tricky at first but once you get used to it you can see the power they bring.

```Kotlin
// If you are accessing members/methods
view?.apply{
  setText(R.string.something)
  visibility = View.VISIBLE
}

// If you need to pass 'it'
view?.let{
layout.addView(it)
}

// Same with this
view?.run {
  layout.addView(this)
}
```

Check out more [from this post](https://medium.com/@fatihcoskun/kotlin-scoping-functions-apply-vs-with-let-also-run-816e4efb75f5).

## Working with Extension functions

[Extension](https://kotlinlang.org/docs/reference/extensions.html) functions in Kotlin are helpful when you want to extend a class you do not own but do not want to inherit from it. Elliot's top tip here is to place all related extensions in a separate file that they extend off.

For example

```Kotlin
String.kt
fun String.wacky() : String {
  return "$this is wacky"
}

Integer.kt
fun Integer.wacky() : Integer  {
  return this + 11424234214
}
```

## Surprising how clumsy inline interface implementations are in Kotlin..... but that's ok!
```Kotlin
interface OnTheMoveListener {
  fun onTheMoveChanged(enabled: Boolean)
}

// In Kotlin
fragment.onTheMoveListener = object : OnTheMoveListener {
  override fun onTheMoveChanged(enabled: Boolean) {
    presenter.setOnTheMove(enabled)
  }
}

// In Java 8		
fragment.setOnTheMoveListener(enabled -> {
  presenter.setOnTheMove(enabled)
})
```

## `forEachIndexed`

```Java
// In Java you can loop like this:
for(Event event : events) {
  // do some loops
}

// But if you need the index for something:
for(int i=0; i < events.size(); i++) {
  // do some indexed loops
}
```

```Kotlin
// In kotlin:
events.forEach { event -> // fruity loops }

//is easy:
events.forEachIndexed { index, event -> // fruity indexed loops }
```

## KTX synthetic view ids... consider them nullable

Synthetic view ids in Kotlin are great on Android. However a word of caution: treat them as if they are nullable. The IDE does not highlight this fact but occasionally we have noticed runtime crashes.

```Kotlin
import kotlinx.android.synthetic.main.fragment_insurance.*

nameValue?.text = name
renewalValue?.text = printDate(renewalDate, FORMAT_SHORT_DATE)
regValue?.text = regNum
```

## `when` is really useful in `onActivityResult`

The `when` syntax can be useful if you have many request codes that you need to handle in a single result function.

```Kotlin
startActivityForResult(TripLocationActivity.createIntent(getCtx(), location, address), REQUEST_TRIP_START)

override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
  when(requestCode) {
    REQUEST_TRIP_START -> {
      if(resultCode == Activity.RESULT_OK && data != null) {
        presenter.startLocationEdited(TripLocationActivity.getLocation(data))
      }
    }
    ...
  }
}
```

It also works great with menu clicked listeners.
