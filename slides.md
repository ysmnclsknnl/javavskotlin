<!-- .slide: data-background="./assets/kotlinvsjava.png" -->
---

<!-- .slide: style="font-size: 32px; border:1px, solid, black; display: flex; flex-direction: column; justify-content: flex-end; align-items: flex-end;" -->

Yasemin Çalışkan\
Backend Intern @ CodeStar
---
### Three Aspects Of The Comparison

- Code Conciseness
- Handling Null Pointer Exceptions
- Functional Programming 

---

## Pet Shop REST API
- Admin users can add and list pets.
- Customer users can reserve and list pets.
- Once the application run, the database is seeded with fake pet data.


---
<!-- .slide: style="font-size: 32px;"> -->
## Conciseness (Class Definitions) 
"Coding is more reading than writing".

```java
@Getter
@Setter
@RequiredArgsConstructor
@AllArgsConstructor(onConstructor = @__(@PersistenceCreator))
@Document(collection = "pet")
public class Pet {
    ...
    private ObjectId id = new ObjectId();
    private final String name;
    private final String description;
    private final Integer age;
    private final PetType type;
    private final String photoLink;
    private Boolean adopted = false;
}
  
```

Figure 1 - Java Pet Class with Lombok Annotations

---

<!-- .slide: style="font-size: 32px;"> -->
### Conciseness (Class Definitions)

```kt
@Document(collection = "pet")
data class Pet(
    ...
    val id: ObjectId = ObjectId(),
    val name: String,
    val description: String,
    val age: Int,
    val type: PetType,
    var adopted: Boolean = false,
    val photoLink: String,
)
```

Figure 2 - Kotlin Pet Class

---
<!-- .slide: style="font-size: 32px;"> -->
### Conciseness (Chaining Methods)

```java
@Component
public class PetDataSeeder implements CommandLineRunner {
  ...
    public void seedData() {
        Faker faker = new Faker();
        List<Pet> pets = IntStream.range(0, 5)
                .mapToObj(i -> createPet(faker))
                .toList();

        petRepository.saveAll(pets);
    }
    ...
    }
```

Figure 3 - Java Pet Data Seeder

---
<!-- .slide: style="font-size: 32px;"> -->
## Conciseness (Chaining Methods)

```kt
@Component
class PetDataSeeder(val petRepository: PetRepository) : CommandLineRunner {

    fun seedData() {
        val faker = Faker()
        generateSequence { createPet(faker) }
            .take(5)
            .toList()
            .let(petRepository::saveAll) 
        }
            ...
    }
```

  Figure 4 - Kotlin Pet Data Seeder

---

<!-- .slide: style="font-size: 32px;"> -->
## Conciseness (Named Parameters)

```java
...
private Pet createPet(Faker faker) {
List<String> imageUrls = List.of(
"https://i0.wp...",
"https://p1.pxfuel.com/...",
"https://p0.pxfuel.com/..."
);

        ObjectId id = new ObjectId();
        String petName = faker.cat().name();
        String description = faker.cat().breed();
        int randomIndex = faker.number().numberBetween(0, imageUrls.size());
        String photoLink = imageUrls.get(randomIndex);

        return new Pet(id, petName, description, age, type, adopted, photoLink);
    }
    
```
Figure 5 - Java Create Pet Method
---

<!-- .slide: style="font-size: 32px;"> -->
## Conciseness (Named Parameters)
```kt
    ... 
    private fun createPet(faker: Faker): Pet {
        val imageUrls = listOf(
            "https://i0.wp...",
            "https://p1.pxfuel.com/...",
            "https://p0.pxfuel.com/...",
        )
        return Pet(
            name = faker.cat().name(),
            description = faker.cat().breed(),
            ...
            photoLink = imageUrls.random(),
        )
    }
```
Figure 6 - Kotlin Create Pet Method
---
<!-- .slide: style="font-size: 32px;"> -->
## Summary Of Conciseness Experience

- From the beginning, needing an extra library to reduce boilerplate code is a major drawback in Java!
- Named parameters, scope functions and list methods are really powerful built-in features to make code concise in Kotlin! 

---

<!-- .slide: style="font-size: 32px;"> -->
## Handling Null Pointer Exceptions(Java Optional)

```java 

public Optional<IllegalArgumentException> getValidationExceptions() {
        List<String> errors = Stream.of(
        name != null && name.length() >= 3 ? null : "Name must be at least 3 characters.",
        ...
        ).filter(Objects::nonNull)
        .toList();

        return errors.isEmpty() ?
        Optional.empty() :
        Optional.of(new IllegalArgumentException(String.join(" ", errors)));
        }

public Pet addPet(CreatePetDTO pet) {
        pet.getValidationExceptions().ifPresent(ex -> {
        throw ex;
        });
        ...
        }
```
Figure 7 - Java getValidationExceptions & addPet Functions
---

<!-- .slide: style="font-size: 32px;"> -->
## Handling Null Pointer Exceptions(? operator and safety checks)

```kt
internal fun CreatePetDTO.getValidationExceptions(): IllegalArgumentException? = listOfNotNull(
    if (name.length >= 3) null else "Name must be at least 3 characters.",
    ...
).let { errors ->
    if (errors.isEmpty()) null
    else IllegalArgumentException(errors.joinToString(" "))
}

fun addPet(pet: CreatePetDTO): Pet {
  pet.getValidationExceptions()?.let { throw it }
    ...
}
```
Figure 8 - Kotlin getValidationExceptions & addPet Functions
---
<!-- .slide: style="font-size: 32px;"> -->
## Functional Programming
- Java is originally designed for imperative programming, so I had to search for methods or keywords to apply functional programming principles in Java.
- Kotlin is designed to support functional programming. Data structures are immutable by default and methods avoid object mutation in Kotlin.
So I just focused on the logic of the code.

---

<!-- .slide: style="font-size: 32px;"> -->

Thank you for listening!\
Any questions or comments?
