# Collections in Kotlin


[![Made in SteelKiwi](https://github.com/steelkiwi/Getting-started-with-Kotlin/blob/master/made_in_steelkiwi.png)](http://steelkiwi.com/blog/collections-kotlin/)

### About Kotlin development

Developing android app with Kotlin is gaining popularity among the Java developers. If you missed this topic by some reason, we strongly recommend to take a look at this [survey](http://steelkiwi.com/blog/getting-started-kotlin-libraries-Glide-Dagger/).

This topic is relevant for versed developers that have serious intentions to come to grips with Kotlin and to be in the forefront of the trends, of course. Let’s look how to use collections in Kotlin together, it will be exciting!

Kotlin’s Collections are based on [Java Collection Framework](https://www.tutorialspoint.com/java/java_collections.htm). This article focuses on some features of [kotlin.collections](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/) package.

### Data Processing
Kotlin has a feature called [Extension Functions](https://kotlinlang.org/docs/reference/extensions.html) which enables [Kotlin Standard Library](https://kotlinlang.org/api/latest/jvm/stdlib/index.html) (stdlib) to extend functionality of JDK’s classes. For instance, if you open _Collections.kt file from stdlib you can find many methods similar to this:

```kotlin
/**
* Returns a list containing only elements matching the given [predicate].
*/
public inline fun <T> Iterable<T>.filter(predicate: (T) -> Boolean): List<T> {
   return filterTo(ArrayList<T>(), predicate)
}
```
Acquiring this, your code may look like the following:

```kotlin
val originalList = listOf(1, 2, 3, 4, 5, 6)
assertEquals(listOf(2, 4, 6), originalList.filter { it % 2 == 0 })

val originalList = listOf(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
val result = originalList.firstOrNull { it > 4 }
assertEquals(result, 5)

val originalList = listOf(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
val result = originalList.getOrElse(12) { 12 }
assertEquals(result, 12)

val originalList = listOf(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
val result = originalList.dropWhile { it < 5 }
assertEquals(result, listOf(5, 6, 7, 8, 9, 10))

val originalList = listOf(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
val result = originalList
               .dropWhile { it < 5 }
               .find { it < 7 }
assertEquals(result, 5)

```

You should note that filter and dropWhile return a new instance like many other operators. That means originalList remains unchanged.

For better comprehension of what happens under the hood, take a look at the source code of listOf() function:


```kotlin
/** Returns a new read-only list of given elements.  The returned list is serializable (JVM). */
public fun <T> listOf(vararg elements: T): List<T> = if (elements.size > 0) elements.asList() else emptyList()
```

[RxJava](https://github.com/ReactiveX/RxJava) and [Stream API](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html) of [Java 8](https://www.scaler.com/topics/java-8-features/) contain similar methods, so most likely you are familiar with them. As the Android developers can’t use Stream API, you can often come up with the usage of RxJava for Data Handling. However, this approach is not entirely correct and here is why. The thing is that RxJava is [Event processing](http://www.ibm.com/support/knowledgecenter/SSGMCP_4.2.0/com.ibm.cics.ts.eventprocessing.doc/concepts/dfhep_definition.html) library, and it is not meant for [Data Processing](https://en.wikipedia.org/wiki/Data_processing). Now you can use Kotlin and forget about this inconvenience.

### Immutable Collections
If immutable object sounds like something new to you then we recommend you to check [this](https://docs.oracle.com/javase/tutorial/essential/concurrency/immutable.html) out, and after you’ve done it, go [here](https://docs.oracle.com/javase/tutorial/essential/concurrency/imstrat.html).

Unlike many languages, Kotlin distinguishes between mutable and immutable collections (lists, sets, maps, etc). Precise control over exact situations when collections can be edited is useful for eliminating bugs, and for designing proper APIs.

Kotlin allows you to create an instance of any Collection similar to Java:
```kotlin
val list = ArrayList<String>()
```
Here everything is simple and clear.
And now, this is the best part!
```kotlin
val list: kotlin.collections.List<String> = java.util.ArrayList()
```
We have just created a reference for kotlin.collections.List that is an immutable collection at the same time. You don’t believe it? Just look at the source code with your own eyes:
```kotlin
public interface List<out E> : Collection<E> {
   // Query Operations
   override val size: Int
   override fun isEmpty(): Boolean
   override fun contains(element: @UnsafeVariance E): Boolean
   override fun iterator(): Iterator<E>

   // Bulk Operations
   override fun containsAll(elements: Collection<@UnsafeVariance E>): Boolean

   // Positional Access Operations
   /**
    * Returns the element at the specified index in the list.
    */
   public operator fun get(index: Int): E

   // Search Operations
   /**
    * Returns the index of the first occurrence of the specified element in the list, or -1 if the specified
    * element is not contained in the list.
    */
   public fun indexOf(element: @UnsafeVariance E): Int

   /**
    * Returns the index of the last occurrence of the specified element in the list, or -1 if the specified
    * element is not contained in the list.
    */
   public fun lastIndexOf(element: @UnsafeVariance E): Int

   // List Iterators
   /**
    * Returns a list iterator over the elements in this list (in proper sequence).
    */
   public fun listIterator(): ListIterator<E>

   /**
    * Returns a list iterator over the elements in this list (in proper sequence), starting at the specified [index].
    */
   public fun listIterator(index: Int): ListIterator<E>

   // View
   /**
    * Returns a view of the portion of this list between the specified [fromIndex] (inclusive) and [toIndex] (exclusive).
    * The returned list is backed by this list, so non-structural changes in the returned list are reflected in this list, and vice-versa.
    */
   public fun subList(fromIndex: Int, toIndex: Int): List<E>
}
```
As you can see there is no add() method, no remove() method or any other else method that would allow to change collection content. In this case, the instance itself is of the same kind as a generic java.util.ArrayList. Here comes the next example:


```kotlin
val list: kotlin.collections.MutableList<String> = java.util.ArrayList()
list.add("string")
```
Better look the Kotlin example at the source code yourself:
```kotlin
public interface MutableList<E> : List<E>, MutableCollection<E> {
   // Modification Operations
   override fun add(element: E): Boolean
   override fun remove(element: E): Boolean

   // Bulk Modification Operations
   override fun addAll(elements: Collection<E>): Boolean

   /**
    * Inserts all of the elements in the specified collection [elements] into this list at the specified [index].
    *
    * @return `true` if the list was changed as the result of the operation.
    */
   public fun addAll(index: Int, elements: Collection<E>): Boolean
   override fun removeAll(elements: Collection<E>): Boolean
   override fun retainAll(elements: Collection<E>): Boolean
   override fun clear(): Unit

   // Positional Access Operations
   /**
    * Replaces the element at the specified position in this list with the specified element.
    *
    * @return the element previously at the specified position.
    */
   public operator fun set(index: Int, element: E): E

   /**
    * Inserts an element into the list at the specified [index].
    */
   public fun add(index: Int, element: E): Unit

   /**
    * Removes an element at the specified [index] from the list.
    *
    * @return the element that has been removed.
    */
   public fun removeAt(index: Int): E

   // List Iterators
   override fun listIterator(): MutableListIterator<E>
   override fun listIterator(index: Int): MutableListIterator<E>

   // View
   override fun subList(fromIndex: Int, toIndex: Int): MutableList<E>
}
```
### One of the most popular questions is “How to explain the fact that ArrayList of Java is equal to List of Kotlin?”

```kotlin
val list: kotlin.collections.List<String> = java.util.ArrayList()
```
In fact, there is no sacrosanct mystery. Kotlins’ Collections implements interface List of Java. Let’s take a look at the kotlin.collections.Collection.kt. file.

![Screenshot](https://github.com/steelkiwi/collection_in_kotlin/blob/master/data/img.png)

As mentioned before, this file contains all Extension Functions for Collections. Here you can see that it is possible to use all these functions in Java by means of CollectionsKt class. Also here is cherished import java.util.*;
* Let’s check what we get in Java code:

```kotlin
java.util.List<Integer> list =  kotlin.collections.CollectionsKt.listOf(3, 4, 5);
java.util.List<Integer> filteredList = CollectionsKt.filter(list, item -> item > 4);
```

You can now see in sober fact that Kotlin works with List Java and all Extension Functions are accessible as static methods.
## Let’s sum it up

Android programming language Kotlin turned out to be a rich language that can help us to write cleaner and safer code. And the great addition is that it is fully compatible with Java. Read more informative articles in our [blog](http://steelkiwi.com/blog/) and follow our feed. [SteelKiwi](http://steelkiwi.com/) team will try our best to surprise you!
