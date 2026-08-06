# Premiers pas avec Rust

Un tutoriel pour découvrir Rust en partant de zéro, jusqu'à un petit programme fonctionnel.

## 1. Installer Rust

Rust s'installe avec `rustup`, l'outil officiel qui gère aussi les mises à jour et les versions.

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Une fois installé, vérifie que tout fonctionne :

```bash
rustc --version
cargo --version
```

- `rustc` est le compilateur.
- `cargo` est le gestionnaire de projet/paquets (l'équivalent de `npm` pour Node, ou `pip` + `venv` pour Python). En pratique, tu utiliseras `cargo` presque tout le temps plutôt que `rustc` directement.

## 2. Créer un premier projet

```bash
cargo new bonjour_rust
cd bonjour_rust
```

Cette commande crée une structure de projet :

```
bonjour_rust/
├── Cargo.toml     # métadonnées + dépendances
└── src/
    └── main.rs    # ton code
```

Ouvre `src/main.rs`, tu y trouveras déjà :

```rust
fn main() {
    println!("Hello, world!");
}
```

Lance-le avec :

```bash
cargo run
```

Cargo compile le code puis l'exécute. `cargo build` compile sans exécuter, `cargo check` vérifie juste que ça compile (beaucoup plus rapide, pratique pendant qu'on code).

## 3. Variables et mutabilité

En Rust, les variables sont **immuables par défaut** — une différence importante avec la plupart des langages.

```rust
fn main() {
    let x = 5;
    println!("x vaut {}", x);

    // x = 6; // erreur de compilation : x n'est pas mutable

    let mut y = 5;
    y = 6; // OK, y est déclaré mutable
    println!("y vaut {}", y);
}
```

Ce choix n'est pas anodin : il force à être explicite sur ce qui peut changer, ce qui évite pas mal de bugs.

### Types de base

```rust
let entier: i32 = 42;
let flottant: f64 = 3.14;
let booleen: bool = true;
let caractere: char = 'z';
let texte: &str = "une chaîne de caractères";
```

Rust est **typé statiquement**, mais le compilateur infère souvent le type tout seul — tu n'as pas besoin de l'annoter partout.

## 4. Fonctions

```rust
fn main() {
    let resultat = additionner(3, 4);
    println!("3 + 4 = {}", resultat);
}

fn additionner(a: i32, b: i32) -> i32 {
    a + b // pas de point-virgule = valeur de retour (expression)
}
```

Point important : si tu mets un point-virgule après `a + b`, ça devient une instruction qui ne retourne rien, et le compilateur te le signalera. En Rust, la dernière expression d'un bloc (sans `;`) en est la valeur.

## 5. Le concept clé : ownership (propriété)

C'est **la** particularité de Rust, celle qui lui permet de gérer la mémoire sans garbage collector, tout en évitant les bugs mémoire classiques (use-after-free, double free, etc.).

Trois règles :
1. Chaque valeur a un unique "propriétaire" (une variable).
2. Il ne peut y avoir qu'un seul propriétaire à la fois.
3. Quand le propriétaire sort de portée, la valeur est libérée automatiquement.

```rust
fn main() {
    let s1 = String::from("bonjour");
    let s2 = s1; // la propriété de la valeur est TRANSFÉRÉE à s2

    // println!("{}", s1); // erreur : s1 n'est plus valide !
    println!("{}", s2); // OK
}
```

Contrairement à `String`, les types simples comme `i32` implémentent `Copy` : ils sont dupliqués, pas déplacés.

```rust
let a = 5;
let b = a; // a est copié, pas déplacé
println!("{} {}", a, b); // les deux fonctionnent
```

### Emprunt (borrowing)

Pour utiliser une valeur sans en prendre la propriété, on l'**emprunte** avec `&` :

```rust
fn main() {
    let s = String::from("bonjour");
    afficher(&s); // on prête une référence
    println!("{}", s); // s est toujours valide ici
}

fn afficher(texte: &String) {
    println!("{}", texte);
}
```

Règle du borrow checker : à un instant donné, tu peux avoir soit **une seule référence mutable**, soit **plusieurs références immuables**, jamais les deux en même temps. C'est ce qui empêche les data races à la compilation, avant même d'exécuter le programme.

## 6. Structs et enums

```rust
struct Utilisateur {
    nom: String,
    age: u32,
}

fn main() {
    let alice = Utilisateur {
        nom: String::from("Alice"),
        age: 30,
    };
    println!("{} a {} ans", alice.nom, alice.age);
}
```

Les enums permettent de modéliser des choix, et sont très puissants combinés au pattern matching :

```rust
enum Direction {
    Nord,
    Sud,
    Est,
    Ouest,
}

fn decrire(dir: Direction) -> &'static str {
    match dir {
        Direction::Nord => "vers le nord",
        Direction::Sud => "vers le sud",
        Direction::Est => "vers l'est",
        Direction::Ouest => "vers l'ouest",
    }
}
```

## 7. Gestion des erreurs : `Option` et `Result`

Rust n'a pas de `null`. À la place :

- `Option<T>` représente une valeur qui peut être absente : `Some(valeur)` ou `None`.
- `Result<T, E>` représente une opération qui peut échouer : `Ok(valeur)` ou `Err(erreur)`.

```rust
fn diviser(a: f64, b: f64) -> Result<f64, String> {
    if b == 0.0 {
        Err(String::from("division par zéro"))
    } else {
        Ok(a / b)
    }
}

fn main() {
    match diviser(10.0, 2.0) {
        Ok(resultat) => println!("Résultat : {}", resultat),
        Err(erreur) => println!("Erreur : {}", erreur),
    }
}
```

Ces types forcent à gérer explicitement les cas d'erreur ou d'absence — impossible d'oublier un cas, le compilateur te le rappellera.

## 8. Petit exercice pratique

Un programme qui calcule la moyenne d'une liste de nombres, en gérant le cas de la liste vide :

```rust
fn moyenne(nombres: &[f64]) -> Option<f64> {
    if nombres.is_empty() {
        return None;
    }
    let somme: f64 = nombres.iter().sum();
    Some(somme / nombres.len() as f64)
}

fn main() {
    let notes = vec![15.5, 12.0, 18.0, 9.5];

    match moyenne(&notes) {
        Some(m) => println!("Moyenne : {:.2}", m),
        None => println!("Aucune note à calculer"),
    }

    let vide: Vec<f64> = vec![];
    match moyenne(&vide) {
        Some(m) => println!("Moyenne : {:.2}", m),
        None => println!("Liste vide, pas de moyenne"),
    }
}
```

Essaie de le taper toi-même dans un nouveau projet (`cargo new moyenne`) plutôt que de le copier-coller — c'est en tapant le code, en voyant les erreurs du compilateur et en les corrigeant que le borrow checker devient intuitif.

## 9. Pour la suite

Une fois à l'aise avec ces bases, les étapes naturelles sont :
- **Vec, HashMap et les itérateurs** (`map`, `filter`, `collect`...)
- **Les traits** (l'équivalent des interfaces, avec `impl`)
- **Les lifetimes explicites** (`'a`) quand l'inférence ne suffit plus
- **Le code asynchrone** avec `tokio`
- Le livre officiel, gratuit et très bien fait : *The Rust Programming Language* (`rust-lang.org/book`), et `rustlings`, une série d'exercices interactifs en ligne de commande pour pratiquer.

## Bon à savoir

Le compilateur Rust est réputé pour avoir des messages d'erreur exceptionnellement clairs — il explique souvent exactement quoi corriger et propose une solution. N'aie pas peur de rencontrer des erreurs de compilation au début : c'est normal, et c'est justement cette rigueur qui évite des bugs qui apparaîtraient bien plus tard dans d'autres langages.
