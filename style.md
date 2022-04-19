<!--

Editing this document:

- Discuss all changes in GitHub issues first.
- Update the table of contents as new sections are added or removed.
- Use tables for side-by-side code samples. See below.

Code Samples:

Use 2 spaces to indent. Horizontal real estate is important in side-by-side
samples.

For side-by-side code samples, use the following snippet.

~~~
<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
BAD CODE GOES HERE
```

</td><td>

```go
GOOD CODE GOES HERE
```

</td></tr>
</tbody></table>
~~~

(You need the empty lines between the <td> and code samples for it to be
treated as Markdown.)

If you need to add labels or descriptions below the code samples, add another
row before the </tbody></table> line.

~~~
<tr>
<td>DESCRIBE BAD CODE</td>
<td>DESCRIBE GOOD CODE</td>
</tr>
~~~

-->

# Conventions de codage Uber pour le langage Go

## Table des matières

- [Conventions de codage Uber pour le langage Go](#conventions-de-codage-uber-pour-le-langage-go)
  - [Table des matières](#table-des-matières)
  - [Introduction](#introduction)
  - [Conventions](#conventions)
    - [Pointeurs sur les Interfaces](#pointeurs-sur-les-interfaces)
    - [Vérifiez la conformité des Interfaces](#vérifiez-la-conformité-des-interfaces)
    - [Receveurs et Interfaces](#receveurs-et-interfaces)
    - [Les valeurs zéro des Mutex sont valides](#les-valeurs-zéro-des-mutex-sont-valides)
    - [Copiez tranches (Slices) et dictionnaires (Maps) aux Frontières](#copiez-tranches-slices-et-dictionnaires-maps-aux-frontières)
      - [Réception de Tranches (Slices) et dictionnaires (Maps)](#réception-de-tranches-slices-et-dictionnaires-maps)
      - [Renvoyer des Tranches (Slices) et dictionnaires (Maps)](#renvoyer-des-tranches-slices-et-dictionnaires-maps)
    - [Différez pour Nettoyer](#différez-pour-nettoyer)
    - [Limitez la taille d'un Canal (Channel) à Un ou Rien](#limitez-la-taille-dun-canal-channel-à-un-ou-rien)
    - [Commencez les énumérations à Un](#commencez-les-énumérations-à-un)
    - [Utilisez `"time"` pour gérer le temps](#utilisez-time-pour-gérer-le-temps)
      - [Utilisez `time.Time` pour les instants de temps](#utilisez-timetime-pour-les-instants-de-temps)
      - [Utilisez `time.Duration` pour des périodes de temps](#utilisez-timeduration-pour-des-périodes-de-temps)
      - [Utilisez `time.Time` et `time.Duration` avec des systèmes externes](#utilisez-timetime-et-timeduration-avec-des-systèmes-externes)
    - [Erreurs](#erreurs)
      - [Types d'erreurs](#types-derreurs)
      - [Encapsulation des erreurs](#encapsulation-des-erreurs)
      - [Nommage des Erreurs](#nommage-des-erreurs)
    - [Gérez les échecs d'assertion de type](#gérez-les-échecs-dassertion-de-type)
    - [Ne paniquez pas](#ne-paniquez-pas)
    - [Utilisez go.uber.org/atomic](#utilisez-gouberorgatomic)
    - [Évitez les variables globales muables](#évitez-les-variables-globales-muables)
    - [Évitez d'embarquer des types dans des structures publiques](#évitez-dembarquer-des-types-dans-des-structures-publiques)
    - [Évitez d'utiliser des identifiants prédéclarés](#évitez-dutiliser-des-identifiants-prédéclarés)
    - [Évitez `init()`](#évitez-init)
    - [Sortez dans Main](#sortez-dans-main)
      - [Sortez au plus une fois](#sortez-au-plus-une-fois)
    - [Utilisez les balises de champ dans les structures sérialisées](#utilisez-les-balises-de-champ-dans-les-structures-sérialisées)
  - [Performance](#performance)
    - [Préférez strconv à fmt](#préférez-strconv-à-fmt)
    - [Évitez la conversion de chaîne de caractères en octets (`byte`)](#évitez-la-conversion-de-chaîne-de-caractères-en-octets-byte)
    - [Préférez spécifier la capacité des conteneurs](#préférez-spécifier-la-capacité-des-conteneurs)
      - [Spécification des indices de capacité pour les dictionnaires (Maps)](#spécification-des-indices-de-capacité-pour-les-dictionnaires-maps)
      - [Spécification des indices de capacité pour les Tranches (Slices)](#spécification-des-indices-de-capacité-pour-les-tranches-slices)
  - [Style](#style)
    - [Évitez les lignes trop longues](#évitez-les-lignes-trop-longues)
    - [Soyez Cohérent](#soyez-cohérent)
    - [Regroupez les déclarations similaires](#regroupez-les-déclarations-similaires)
    - [Ordonnez les groupes d'Import](#ordonnez-les-groupes-dimport)
    - [Noms des Packages](#noms-des-packages)
    - [Noms des fonctions](#noms-des-fonctions)
    - [Alias des Imports](#alias-des-imports)
    - [Regroupement et Ordre des Fonctions](#regroupement-et-ordre-des-fonctions)
    - [Réduisez l'imbrication](#réduisez-limbrication)
    - [`Else` Inutiles](#else-inutiles)
    - [Déclarations de variables de niveau supérieur](#déclarations-de-variables-de-niveau-supérieur)
    - [Préfixez les variables globales non exportées par _](#préfixez-les-variables-globales-non-exportées-par-_)
    - [Embarquement dans les Structs](#embarquement-dans-les-structs)
    - [Déclarations de variables locales](#déclarations-de-variables-locales)
    - [nil est une Tranche (Slice) valide](#nil-est-une-tranche-slice-valide)
    - [Réduisez la portée des variables](#réduisez-la-portée-des-variables)
    - [Évitez les paramètres nus](#évitez-les-paramètres-nus)
    - [Utilisez des littéraux de chaîne de caractères bruts pour éviter les échappements](#utilisez-des-littéraux-de-chaîne-de-caractères-bruts-pour-éviter-les-échappements)
    - [Initialisation des structures](#initialisation-des-structures)
      - [Utilisez les noms de champ pour initialiser les structures](#utilisez-les-noms-de-champ-pour-initialiser-les-structures)
      - [Omettez les champs de valeur zéro dans les structures](#omettez-les-champs-de-valeur-zéro-dans-les-structures)
      - [Utilisez `var` pour les structures à valeur zéro](#utilisez-var-pour-les-structures-à-valeur-zéro)
      - [Initialisation des références de Structure](#initialisation-des-références-de-structure)
    - [Initialisation des dictionnaires (maps)](#initialisation-des-dictionnaires-maps)
    - [Formatez les chaînes en dehors de Printf](#formatez-les-chaînes-en-dehors-de-printf)
    - [Nommez les fonctions de style Printf](#nommez-les-fonctions-de-style-printf)
  - [Modèles](#modèles)
    - [Tables de tests](#tables-de-tests)
    - [Options fonctionnelles](#options-fonctionnelles)
  - [Linting](#linting)
    - [Lanceurs d'outils de Linting](#lanceurs-doutils-de-linting)

## Introduction

Les styles sont les conventions qui régissent notre code. Le terme style est un peu
mal choisi, car ces conventions couvrent bien plus que le simple formatage des sources - `gofmt` s'en charge déjà pour nous.

L'objectif de ce guide est de gérer cette complexité en décrivant en détail les choses à faire et à ne pas faire lorsqu'on est amenés à travailler sur du code Go chez Uber. Ces règles ont pour but de garantir la maintenabilité du code sur le long terme, tout en permettant aux ingénieurs d'utiliser les fonctionnalités du langage Go
de manière productive.

Ce guide a été créé à l'origine par [Prashant Varanasi](https://github.com/prashantv) et [Simon Newton](https://github.com/nomis52) comme
un moyen de familiariser certains collègues à l'utilisation de Go. Au fil des années, il a été modifié en fonction des commentaires d'autres personnes.

Ce guide documente les conventions idiomatiques dans le code Go que nous suivons chez Uber. Beaucoup d'entre elles sont des directives générales pour Go, tandis que d'autres s'appuient sur des conventions externes:

1. [Effective Go](https://golang.org/doc/effective_go.html)
2. [Go Common Mistakes](https://github.com/golang/go/wiki/CommonMistakes)
3. [Go Code Review Comments](https://github.com/golang/go/wiki/CodeReviewComments)

De manière générale, tout le code doit être sans erreur lorsqu'il est exécuté via `golint` et `go vet`. Nous vous recommandons de configurer votre éditeur pour :

- exécuter `goimports` lors de la sauvegarde des fichiers modifiés
- exécuter `golint` et `go vet` pour vérifier les erreurs

Vous trouverez des informations sur le support des éditeurs par les outils Go ici: <https://github.com/golang/go/wiki/IDEsAndTextEditorPlugins>

## Conventions

### Pointeurs sur les Interfaces

Il n'y a presque jamais besoin d'un pointeur sur une interface. Les interfaces doivent être passées par valeur — la donnée sous-jacente peut toujours être un pointeur.

Une interface est constituée de deux champs:

1. Un pointeur sur des informations spécifiques au type. On le représente comme le "type".
2. Un pointeur sur la donnée. Si la donnée stockée est un pointeur, elle est stockée directement telle quelle. Sinon s'il s'agit d'une valeur, alors c'est le pointeur vers cette valeur qui est stocké.

Si vous voulez définir des méthodes d'interfaces qui modifient des données sous-jacentes, alors il vous faut passer par un pointeur.

### Vérifiez la conformité des Interfaces

Le cas échéant, il faut toujours vérifier la conformité d'une interface au moment de la compilation. Par conformité, il faut comprendre:

- Les types exportés requis pour implémenter des interfaces spécifiques dans le cadre de leur contrat d'API
- Les types exportés ou non exportés faisant partie d'une collection de types qui implémentent une même interface
- Tous les autres cas où ne pas respecter une interface pourrait causer des problèmes aux utilisateurs

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
type Handler struct {
  // ...
}



func (h *Handler) ServeHTTP(
  w http.ResponseWriter,
  r *http.Request,
) {
  ...
}
```

</td><td>

```go
type Handler struct {
  // ...
}

var _ http.Handler = (*Handler)(nil)

func (h *Handler) ServeHTTP(
  w http.ResponseWriter,
  r *http.Request,
) {
  // ...
}
```

</td></tr>
</tbody></table>

L'instruction `var _ http.Handler = (*Handler)(nil)` échouera à être compilée si 
`*Handler` ne répond plus au contrat de l'interface `http.Handler`.

Le côté droit de l'affectation doit être la valeur zéro du type vérifié; c'est-à-dire `nil` pour les pointeurs (comme `*Handler`), les tranches (`slices`), les dictionnaires (`maps`), et les structures  vides pour les types `struct`.

```go
type LogHandler struct {
  h   http.Handler
  log *zap.Logger
}

var _ http.Handler = LogHandler{}

func (h LogHandler) ServeHTTP(
  w http.ResponseWriter,
  r *http.Request,
) {
  // ...
}
```

### Receveurs et Interfaces

Les méthodes avec des receveurs de valeurs peuvent être appelées via des pointeurs ainsi que via des valeurs.

Les méthodes avec des receveurs de pointeurs ne peuvent être appelées que via des pointeurs ou des [valeurs adressables](https://golang.org/ref/spec#Method_values).

Par example,

```go
type S struct {
  data string
}

func (s S) Read() string {
  return s.data
}

func (s *S) Write(str string) {
  s.data = str
}

sVals := map[int]S{1: {"A"}}

// Read peut être appeléee en utilisant une valeur
sVals[1].Read()

// Mais pas Write. Ceci ne compilera pas:
//  sVals[1].Write("test")

sPtrs := map[int]*S{1: {"A"}}

// Read et Write peuvent être appelées via un pointeur
sPtrs[1].Read()
sPtrs[1].Write("test")
```

De même, une interface peut être satisfaite par un pointeur, même si elle ne définit que des méthodes avec des receveurs de valeurs.

```go
type F interface {
  f()
}

type S1 struct{}

func (s S1) f() {}

type S2 struct{}

func (s *S2) f() {}

s1Val := S1{}
s1Ptr := &S1{}
s2Val := S2{}
s2Ptr := &S2{}

var i F
i = s1Val
i = s1Ptr
i = s2Ptr

// Ceci ne compilera pas, parce que s2Val est une valeur, et il n'y a pas de receveur de valeur pour f.
//   i = s2Val
```

Effective Go a une bonne documentation (en Anglais) concernant les [Pointeurs et les Valeurs](https://golang.org/doc/effective_go.html#pointers_vs_values)

### Les valeurs zéro des Mutex sont valides

La valeur zéro de `sync.Mutex` et `sync.RWMutex` est valide. Il n'y a donc quasiment jamais besoin de recourir à un pointeur sur un mutex.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
mu := new(sync.Mutex)
mu.Lock()
```

</td><td>

```go
var mu sync.Mutex
mu.Lock()
```

</td></tr>
</tbody></table>

Si vous utilisez une structure par pointeur, le mutex ne doit pas être un champ de type pointeur sur cette structure. N'embarquez pas le mutex dans la structure, même si la structure n'est pas exportée.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
type SMap struct {
  sync.Mutex

  data map[string]string
}

func NewSMap() *SMap {
  return &SMap{
    data: make(map[string]string),
  }
}

func (m *SMap) Get(k string) string {
  m.Lock()
  defer m.Unlock()

  return m.data[k]
}
```

</td><td>

```go
type SMap struct {
  mu sync.Mutex

  data map[string]string
}

func NewSMap() *SMap {
  return &SMap{
    data: make(map[string]string),
  }
}

func (m *SMap) Get(k string) string {
  m.mu.Lock()
  defer m.mu.Unlock()

  return m.data[k]
}
```

</td></tr>

<tr><td>

Le champ `Mutex` field, ainsi que ses méthodes `Lock` et `Unlock` font involontairement partie de l'API exportée de `SMap`.

</td><td>

Le mutex et ses méthodes sont des détails d'implémentation de `SMap`, cachés à ses appelants.

</td></tr>
</tbody></table>

### Copiez tranches (Slices) et dictionnaires (Maps) aux Frontières

Tranches (`Slices`) et dictionnaires (`Maps`) contiennent des pointeurs vers des données sous-jacentes. Attention donc aux scénarios où ils doivent être copiés.

#### Réception de Tranches (Slices) et dictionnaires (Maps)

Gardez toujours à l'esprit que les utilisateurs peuvent modifier un dictionnaire (`Map`) ou une tranche (`Slice`) que vous avez reçue en argument si vous stockez une référence à celui-ci.

<table>
<thead><tr><th>Non Recommandé</th> <th>Recommandé</th></tr></thead>
<tbody>
<tr>
<td>

```go
func (d *Driver) SetTrips(trips []Trip) {
  d.trips = trips
}

trips := ...
d1.SetTrips(trips)

// Vouliez-vous modifier d1.trips ?
trips[0] = ...
```

</td>
<td>

```go
func (d *Driver) SetTrips(trips []Trip) {
  d.trips = make([]Trip, len(trips))
  copy(d.trips, trips)
}

trips := ...
d1.SetTrips(trips)

// trips[0] peut maintenant être modifié
// sans risque d'affecter d1.trips.
trips[0] = ...
```

</td>
</tr>

</tbody>
</table>

#### Renvoyer des Tranches (Slices) et dictionnaires (Maps)

De même, méfiez-vous des modifications apportées par l'utilisateur aux dictionnaires (Maps) ou aux Tranches (Slices) exposant des états internes.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
type Stats struct {
  mu sync.Mutex
  counters map[string]int
}

// Snapshot renvoit l'état courant.
func (s *Stats) Snapshot() map[string]int {
  s.mu.Lock()
  defer s.mu.Unlock()

  return s.counters
}

// snapshot n'est plus protégé par le mutex.
// Tout accès au snapshot est donc sujet à 
// une situation de concurrence (data race).
snapshot := stats.Snapshot()
```

</td><td>

```go
type Stats struct {
  mu sync.Mutex
  counters map[string]int
}

func (s *Stats) Snapshot() map[string]int {
  s.mu.Lock()
  defer s.mu.Unlock()

  result := make(map[string]int, len(s.counters))
  for k, v := range s.counters {
    result[k] = v
  }
  return result
}

// Snapshot est maintenant une copie.
snapshot := stats.Snapshot()
```

</td></tr>
</tbody></table>

### Différez pour Nettoyer

Utilisez `defer` pour nettoyer les ressources telles que les fichiers et les verrous.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
p.Lock()
if p.count < 10 {
  p.Unlock()
  return p.count
}

p.count++
newCount := p.count
p.Unlock()

return newCount

// déverouillages faciles à oublier en raison des multiples branches de retour
```

</td><td>

```go
p.Lock()
defer p.Unlock()

if p.count < 10 {
  return p.count
}

p.count++
return p.count

// plus lisible
```

</td></tr>
</tbody></table>

`Defer` a un surcoût extrêmement faible et ne doit être évité que si vous pouvez
prouver que le temps d'exécution de votre fonction est de l'ordre de la nanoseconde. Le gain en lisibilité apporté par `defer` vaut le coût minime de son utilisation. Cela
est particulièrement vrai pour les méthodes plus longues qui effectuent plus que de simples accès en mémoire, où les autres calculs sont plus importants que le `defer`.

### Limitez la taille d'un Canal (Channel) à Un ou Rien

Les Canaux (Channels) doivent généralement avoir une taille de un ou être sans tampon. Par défaut, les canaux ne sont pas tamponnés et ont une taille de zéro. Toute autre taille doit faire l'objet d'un examen plus approfondi. Analysez comment la taille est
déterminée, ce qui empêche le canal de se remplir lorsqu'il y a de la charge et de  bloquer ce qui y écrit, et ce qui se passe lorsque cela se produit.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
// Devrait être suffisant pour tout le monde!
c := make(chan int, 64)
```

</td><td>

```go
// Taille de un
c := make(chan int, 1) // or
// Canal sans tampon, taille de zéro
c := make(chan int)
```

</td></tr>
</tbody></table>

### Commencez les énumérations à Un

La façon standard d'introduire des énumérations dans Go est de déclarer un type personnalisé et un groupe "const" avec "iota". Comme les variables ont une valeur par défaut de 0, vous devrez généralement commencer vos énumérations sur une valeur différente de zéro.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
type Operation int

const (
  Ajouter Operation = iota
  Soustraire
  Multiplier
)

// Ajouter=0, Soustraire=1, Multiplier=2
```

</td><td>

```go
type Operation int

const (
  Ajouter Operation = iota + 1
  Soustraire
  Multiplier
)

// Ajouter=1, Soustraire=2, Multiplier=3
```

</td></tr>
</tbody></table>

Il y a des cas où l'utilisation de la valeur zéro est logique, par exemple lorsque le
ce cas est le comportement par défaut souhaitable.

```go
type LogOutput int

const (
  LogToStdout LogOutput = iota
  LogToFile
  LogToRemote
)

// LogToStdout=0, LogToFile=1, LogToRemote=2
```

### Utilisez `"time"` pour gérer le temps

La gestion du temps est compliquée. Les hypothèses incorrectes souvent faites à propos du temps incluent par exemple ce qui suit.

1. Une journée compte 24 heures
2. Une heure compte 60 minutes
3. Une semaine compte 7 jours
4. Une année compte 365 jours
5. [Et bien plus](https://infiniteundo.com/post/25326999628/falsehoods-programmers-believe-about-time)

Par exemple, *1* signifie que l'ajout de 24 heures à un instant ne pourra pas toujours
donner un nouveau jour calendaire.

Par conséquent, utilisez toujours le package [`"time"`](https://golang.org/pkg/time/) lorsque vous traitez avec le temps car il aide à traiter ces hypothèses incorrectes de manière plus sûre et plus précise.

#### Utilisez `time.Time` pour les instants de temps

Utilisez [`time.Time`](https://golang.org/pkg/time/#Time) lorsque vous traitez des instants de temps, et les méthodes sur
`time.Time` lors de la comparaison, l'ajout ou la soustraction de temps.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
func isActive(now, start, stop int) bool {
  return start <= now && now < stop
}
```

</td><td>

```go
func isActive(now, start, stop time.Time) bool {
  return (start.Before(now) || start.Equal(now)) && now.Before(stop)
}
```

</td></tr>
</tbody></table>

#### Utilisez `time.Duration` pour des périodes de temps

Utilisez [`time.Duration`](https://golang.org/pkg/time/#Duration) pour des périodes de temps.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
func poll(delay int) {
  for {
    // ...
    time.Sleep(time.Duration(delay) * time.Millisecond)
  }
}

poll(10) // était-ce en secondes ou en millisecondes ?
```

</td><td>

```go
func poll(delay time.Duration) {
  for {
    // ...
    time.Sleep(delay)
  }
}

poll(10*time.Second)
```

</td></tr>
</tbody></table>

Revenant à l'exemple de l'ajout de 24 heures à un instant de temps, la méthode que nous avons utilisée pour ajouter du temps dépend de notre objectif. Pour avoir la même heure de la journée, mais sur le jour calendaire suivant, nous devrions utiliser [`Time.AddDate`](https://golang.org/pkg/time/#Time.AddDate). Par contre, si nous voulons un
instant de temps garanti 24 heures après l'heure précédente, nous devrions plutôt utiliser [`Time.Add`](https://golang.org/pkg/time/#Time.Add).

```go
newDay := t.AddDate(0 /* years */, 0 /* months */, 1 /* days */)
maybeNewDay := t.Add(24 * time.Hour)
```

#### Utilisez `time.Time` et `time.Duration` avec des systèmes externes

Si possible, utilisez `time.Duration` et `time.Time` dans les interactions avec des systèmes externes. Par example:

- Options de ligne de commande: [`flag`](https://golang.org/pkg/flag/) supporte `time.Duration` via [`time.ParseDuration`](https://golang.org/pkg/time/#ParseDuration)
- JSON: [`encoding/json`](https://golang.org/pkg/encoding/json/) supporte l'encodage de `time.Time` en tant que chaîne de caractères [RFC 3339](https://tools.ietf.org/html/rfc3339) via sa [méthode `UnmarshalJSON`](https://golang.org/pkg/time/#Time.UnmarshalJSON)
- SQL: [`database/sql`](https://golang.org/pkg/database/sql/) supporte la conversion de colonnes de type `DATETIME` ou `TIMESTAMP` en `time.Time` et vice-versa si le pilote sous-jacent le supporte
- YAML: [`gopkg.in/yaml.v2`](https://godoc.org/gopkg.in/yaml.v2) supporte `time.Time` en tant que chaîne de caractère [RFC 3339](https://tools.ietf.org/html/rfc3339), et `time.Duration` via [`time.ParseDuration`](https://golang.org/pkg/time/#ParseDuration).

Lorsqu'il n'est pas possible d'utiliser `time.Duration` dans ces interactions, utilisez
`int` ou `float64` et incluez l'unité dans le nom du champ.

Par exemple, étant donné que `encoding/json` ne prend pas en charge `time.Duration`, l'unité
est incluse dans le nom du champ.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
// {"interval": 2}
type Config struct {
  Interval int `json:"interval"`
}
```

</td><td>

```go
// {"intervalMillis": 2000}
type Config struct {
  IntervalMillis int `json:"intervalMillis"`
}
```

</td></tr>
</tbody></table>

Lorsqu'il n'est pas possible d'utiliser `time.Time` dans ces interactions, à moins qu'une autre
alternative ne soit convenue, utilisez `string` et formatez les horodatages (timestamps) comme défini dans la [RFC3339](https://tools.ietf.org/html/rfc3339). Ce format est utilisé par défaut par [`Time.UnmarshalText`](https://golang.org/pkg/time/#Time.UnmarshalText) et est
disponible pour être utilisé dans `Time.Format` et `time.Parse` via [`time.RFC3339`](https://golang.org/pkg/time/#RFC3339).

Bien que cela tende à ne pas être un problème dans la pratique, gardez à l'esprit que le paquet `"time"` ne prend pas en charge l'analyse des horodatages avec des secondes intercalaires
([golang/go#8728](https://github.com/golang/go/issues/8728)), ni ne tient compte des secondes intercalaires dans les calculs ([golang/gp#15190](https://github.com/golang/go/issues/8728)). Si
vous comparez deux instants de temps, la différence ne comprendra pas les secondes intercalaires qui auraient pu s'être écoulées entre ces deux instants.

<!-- TODO: section on String methods for enums -->

### Erreurs

#### Types d'erreurs

Il existe quelques options pour déclarer les erreurs.
Considérez ce qui suit avant de choisir l'option la mieux adaptée à votre cas d'utilisation.

- L'appelant doit-il faire correspondre l'erreur à un autre type d'erreur pour pouvoir la gérer ?
  Si oui, nous devons supporter les fonctions [`errors.Is`](https://golang.org/pkg/errors/#Is) ou [`errors.As`](https://golang.org/pkg/errors/#As), en déclarant une variable d'erreur au niveau plus haut ou un type personnalisé.
- Le message d'erreur est-il une chaîne de caractères statique, ou est-ce une chaîne de caractères dynamique qui nécessite des informations contextuelles ?
  Pour le premier cas, nous pouvons utiliser [`errors.New`](https://golang.org/pkg/errors/#New), mais pour le second, nous devons utiliser [`fmt.Errorf`](https://golang.org/pkg/fmt/#Errorf) ou un type d'erreur personnalisé.
- Sommes-nous en train de propager une nouvelle erreur renvoyée par une fonction en aval ?
  Si c'est le cas, consultez la [section sur l'encapsulation des erreurs](#encapsulation-des-erreurs).

[`errors.Is`] : https://golang.org/pkg/errors/#Is
[`errors.As`] : https://golang.org/pkg/errors/#As

| Correspondance d'erreurs ? | Message d'erreur | Recommendation                                     |
|----------------------------|------------------|----------------------------------------------------|
| Non                        | statique         | [`errors.New`](https://golang.org/pkg/errors/#New) |
| Non                        | dynamique        | [`fmt.Errorf`](https://golang.org/pkg/fmt/#Errorf) |
| Oui                        | statique         | `var` au plus haut niveau avec [`errors.New`]      |
| Oui                        | dynamique        | type `error` personnalisé                          |


Par example, utilisez [`errors.New`](https://golang.org/pkg/errors/#New) pour une erreur avec une chaîne de caractères statique.
Exportez cette erreur en tant que variable pour prendre en charge la correspondance avec `errors.Is` si l'appelant doit pouvoir la faire correspondre à un autre type et gérer cette erreur.

<table>
<thead><tr><th>Pas de correspondance d'erreur</th><th>Correspondance d'erreurs</th></tr></thead>
<tbody>
<tr><td>

```go
// package foo

func Open() error {
  return errors.New("n'a pas pu ouvrir")
}

// package bar

if err := foo.Open(); err != nil {
  // Can't handle the error.
  panic("erreur inconnue")
}
```

</td><td>

```go
// package foo

var ErrCouldNotOpen = errors.New("n'a pas pu ouvrir")

func Open() error {
  return ErrCouldNotOpen
}

// package bar

if err := foo.Open(); err != nil {
  if errors.Is(err, foo.ErrCouldNotOpen) {
    // handle the error
  } else {
    panic("erreur inconnue")
  }
}
```

</td></tr>
</tbody></table>

Pour une erreur avec une chaîne de caractères dynamique, utilisez [`fmt.Errorf`](https://golang.org/pkg/fmt/#Errorf) si l'appelant n'a pas besoin de la faire correspondre à une autre type d'erreur, et une "erreur" personnalisée si l'appelant doit pouvoir la faire correspondre à un autre type d'erreur.

<table>
<thead><tr><th>Pas de correspondance d'erreur</th><th>Correspondance d'erreurs</th></tr></thead>
<tbody>
<tr><td>

```go
// package foo

func Open(file string) error {
  return fmt.Errorf("fichier %q non trouvé", file)
}

// package bar

if err := foo.Open("testfile.txt"); err != nil {
  // Can't handle the error.
  panic("erreur inconnue")
}
```

</td><td>

```go
// package foo

type NotFoundError struct {
  File string
}

func (e *NotFoundError) Error() string {
  return fmt.Sprintf("fichier %q non trouvé", e.File)
}

func Open(file string) error {
  return &NotFoundError{File: file}
}


// package bar

if err := foo.Open("testfile.txt"); err != nil {
  var notFound *NotFoundError
  if errors.As(err, &notFound) {
    // handle the error
  } else {
    panic("unknown error")
  }
}
```

</td></tr>
</tbody></table>

Notez que si vous exportez des variables ou des types d'erreur dans un package, ils feront partie de l'API publique de ce package.

#### Encapsulation des erreurs

Il existe principalement trois options pour propager les erreurs en cas d'échec d'un appel :

- retourner l'erreur d'origine telle quelle
- ajouter du contexte avec `fmt.Errorf` et le verbe `%w`
- ajouter du contexte avec `fmt.Errorf` et le verbe `%v`

Retournez l'erreur d'origine telle quelle s'il n'y a pas de contexte supplémentaire à ajouter. Cela permet de conserver le type d'erreur et le message d'origine, et est bien adapté aux cas où le message d'erreur sous-jacent dispose de suffisamment d'informations pour savoir d'où il vient.

Sinon, ajoutez du contexte au message d'erreur si possible de sorte qu'au lieu d'une vague erreur telle que "connexion refusée", vous pourrez avoir des erreurs plus utiles telles que "appel au service foo : connexion refusée".

Utilisez `fmt.Errorf` pour ajouter du contexte à vos erreurs, en choisissant entre les verbes `%w` ou `%v` en fonction de ce qu'on veut que l'appelant soit en mesure de faire: faire correspondre l'erreur à un autre type d'erreur ou pouvoir en extraire la [cause](https://godoc.org/github.com/pkg/errors#Cause) sous-jacente.

- Utilisez `%w` si l'appelant doit avoir accès à l'erreur sous-jacente.
  C'est une bonne valeur par défaut pour la plupart des erreurs encapsulées,
  mais sachez que les appelants peuvent commencer à se fier à ce comportement.
  Donc, pour les cas où l'erreur enveloppée est une `var` ou un type connu,
  documentez-le et testez-le dans le cadre du contrat de votre fonction.
- Utilisez `%v` pour masquer l'erreur sous-jacente.
  Les appelants ne pourront pas faire correspondre l'erreur à un autre type d'eeur,
  mais vous pourrez passer à `%w` à l'avenir si nécessaire.

Lorsque vous ajoutez du contexte aux erreurs renvoyées, gardez le contexte succinct en évitant
des phrases comme "n'a pas réussi", qui énoncent l'évidence et s'accumulent au fur et à mesure dans la pile des messages d'erreurs :

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
s, err := store.New()
if err != nil {
    return fmt.Errorf(
        "failed to create new store: %w", err)
}
```

</td><td>

```go
s, err := store.New()
if err != nil {
    return fmt.Errorf(
        "new store: %w", err)
}
```

</td></tr><tr><td>

```
failed to x: failed to y: failed to create new store: the error
```

</td><td>

```
x: y: new store: the error
```

</td></tr>
</tbody></table>

Cependant, une fois l'erreur envoyée à un autre système, il doit être clair que le message est une erreur (par exemple, une balise `err` ou le préfixe "Failed" dans les journaux).

Voir aussi (en anglais) [Don't just check errors, handle them gracefully](https://dave.cheney.net/2016/04/27/dont-just-check-errors-handle-them-gracefully).

#### Nommage des Erreurs

Pour les valeurs d'erreur stockées en tant que variables globales,
utilisez le préfixe `Err` ou `err` selon qu'ils sont exportés ou non.
Cette convention remplace [Préfixer les variables globales non exportées par _](#préfixer-les-variables-globales-non-exportées-par-_).

```go
var (
  // Les deux erreurs suivantes sont exportées
  // pour que les utilisateurs de ce package puissent les faire correspondre
  // avec errors.Is.

  ErrBrokenLink = errors.New("link is broken")
  ErrCouldNotOpen = errors.New("could not open")

  // Cette erreur n'est pas exportée car
  // nous ne voulons pas l'intégrer à notre API publique.
  // Nous pouvons toujours l'utiliser à l'intérieur du package
  // avec errors.Is.

  errNotFound = errors.New("not found")
)
```

Pour les types d'erreur personnalisés, utilisez plutôt le suffixe `Error`.

```go
// De même, cette erreur est exportée
// pour que les utilisateurs de ce package puissent le faire correspondre
// avec errors.As.

type NotFoundError struct {
  File string
}

func (e *NotFoundError) Error() string {
  return fmt.Sprintf("file %q not found", e.File)
}

// Et cette erreur n'est pas exportée car
// nous ne voulons pas l'intégrer à l'API publique.
// Nous pouvons toujours l'utiliser dans le package
// avec errors.As.

type resolveError struct {
  Path string
}

func (e *resolveError) Error() string {
  return fmt.Sprintf("resolve %q", e.Path)
}
```

### Gérez les échecs d'assertion de type

Une [assertion de type](https://golang.org/ref/spec#Type_assertions) paniquera sur un type incorrect, lorsqu'on l'appelle sous sa forme qui renvoit une seule valeur de retour. Par conséquent, utilisez toujours l'idiome "virgule ok".

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
t := i.(string)
```

</td><td>

```go
t, ok := i.(string)
if !ok {
  // gère l'erreur comme il se doit
}
```

</td></tr>
</tbody></table>

<!-- TODO: There are a few situations where the single assignment form is
fine. -->

### Ne paniquez pas

Le code exécuté en production doit éviter les paniques. Les paniques sont une source majeure d'[échecs en cascade](https://en.wikipedia.org/wiki/Cascading_failure). Si une erreur se produit, la fonction doit retourner une erreur et
permettre à l'appelant de décider comment le gérer.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
func run(args []string) {
  if len(args) == 0 {
    panic("an argument is required")
  }
  // ...
}

func main() {
  run(os.Args[1:])
}
```

</td><td>

```go
func run(args []string) error {
  if len(args) == 0 {
    return errors.New("an argument is required")
  }
  // ...
  return nil
}

func main() {
  if err := run(os.Args[1:]); err != nil {
    fmt.Fprintln(os.Stderr, err)
    os.Exit(1)
  }
}
```

</td></tr>
</tbody></table>

La panique/récupération n'est pas une stratégie de gestion des erreurs. Un programme ne doit paniquer que lorsque
quelque chose d'irrécupérable se produit comme un déréférencement nul. Une exception à cela est
l'initialisation d'un programme : des choses inattendues au démarrage qui devraient interrompre le
programme peuvent semer la panique.

```go
var _statusTemplate = template.Must(template.New("name").Parse("_statusHTML"))
```

Même dans les tests, préférez `t.Fatal` ou `t.FailNow` aux paniques pour vous assurer que le test est correctement marqué comme ayant échoué.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
// func TestFoo(t *testing.T)

f, err := ioutil.TempFile("", "test")
if err != nil {
  panic("failed to set up test")
}
```

</td><td>

```go
// func TestFoo(t *testing.T)

f, err := ioutil.TempFile("", "test")
if err != nil {
  t.Fatal("failed to set up test")
}
```

</td></tr>
</tbody></table>

<!-- TODO: Explain how to use _test packages. -->

### Utilisez go.uber.org/atomic

Les opérations atomiques avec le package [sync/atomic](https://golang.org/pkg/sync/atomic/) fonctionnent sur les types bruts (`int32`, `int64`, etc.); il est donc facile d'oublier d'utiliser l'opération atomique pour lire ou modifier les variables.

[go.uber.org/atomic](https://godoc.org/go.uber.org/atomic) ajoute la sécurité de type à ces opérations en masquant le
type sous-jacent. De plus, il inclut un type `atomic.Bool` très pratique.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
type foo struct {
  running int32  // atomic
}

func (f* foo) start() {
  if atomic.SwapInt32(&f.running, 1) == 1 {
     // already running…
     return
  }
  // start the Foo
}

func (f *foo) isRunning() bool {
  return f.running == 1  // race!
}
```

</td><td>

```go
type foo struct {
  running atomic.Bool
}

func (f *foo) start() {
  if f.running.Swap(true) {
     // already running…
     return
  }
  // start the Foo
}

func (f *foo) isRunning() bool {
  return f.running.Load()
}
```

</td></tr>
</tbody></table>

### Évitez les variables globales muables

Évitez de modifier les variables globales; optez plutôt pour l'injection de dépendances.
Cela s'applique aux pointeurs de fonction ainsi qu'à d'autres types de valeurs.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
// sign.go

var _timeNow = time.Now

func sign(msg string) string {
  now := _timeNow()
  return signWithTime(msg, now)
}
```

</td><td>

```go
// sign.go

type signer struct {
  now func() time.Time
}

func newSigner() *signer {
  return &signer{
    now: time.Now,
  }
}

func (s *signer) Sign(msg string) string {
  now := s.now()
  return signWithTime(msg, now)
}
```
</td></tr>
<tr><td>

```go
// sign_test.go

func TestSign(t *testing.T) {
  oldTimeNow := _timeNow
  _timeNow = func() time.Time {
    return someFixedTime
  }
  defer func() { _timeNow = oldTimeNow }()

  assert.Equal(t, want, sign(give))
}
```

</td><td>

```go
// sign_test.go

func TestSigner(t *testing.T) {
  s := newSigner()
  s.now = func() time.Time {
    return someFixedTime
  }

  assert.Equal(t, want, s.Sign(give))
}
```

</td></tr>
</tbody></table>

### Évitez d'embarquer des types dans des structures publiques

Ces types intégrés divulguent les détails d'implémentation, inhibent l'évolution des types et obscurcissent la documentation.

En supposant que vous ayiez implémenté une variété de types de liste à l'aide d'une structure commune `AbstractList`, évitez d'intégrer `AbstractList` dans votre liste concrète implémentations.
Au lieu de cela, écrivez à la main uniquement les méthodes de votre liste concrète qui délégueront
à `AbstractList`.

```go
type AbstractList struct {}

// Add adds an entity to the list.
func (l *AbstractList) Add(e Entity) {
  // ...
}

// Remove removes an entity from the list.
func (l *AbstractList) Remove(e Entity) {
  // ...
}
```

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
// ConcreteList is a list of entities.
type ConcreteList struct {
  *AbstractList
}
```

</td><td>

```go
// ConcreteList is a list of entities.
type ConcreteList struct {
  list *AbstractList
}

// Add adds an entity to the list.
func (l *ConcreteList) Add(e Entity) {
  l.list.Add(e)
}

// Remove removes an entity from the list.
func (l *ConcreteList) Remove(e Entity) {
  l.list.Remove(e)
}
```

</td></tr>
</tbody></table>

Go autorise [l'embarquement de type](https://golang.org/doc/effective_go.html#embedding) comme compromis entre l'héritage et la composition.
Le type externe obtient des copies implicites des méthodes du type embarqué.
Ces méthodes, par défaut, délèguent à la même méthode de l'objet embarqué.

La structure gagne également un champ du même nom que le type.
Ainsi, si le type embarqué est public, le champ est public.
Pour maintenir la rétrocompatibilité, chaque version future du type externe doit conserver le type intégré.

Un type embarqué est rarement nécessaire. C'est une commodité qui vous aide à éviter d'écrire des méthodes déléguées fastidieuses.

Même le fait d'embarquer une *interface* AbstractList compatible, au lieu de la structure, offrirait au développeur plus de flexibilité pour changer à l'avenir, mais toujours en divulgant le détail que les listes concrètes utilisent une implémentation abstraite.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
// AbstractList is a generalized implementation
// for various kinds of lists of entities.
type AbstractList interface {
  Add(Entity)
  Remove(Entity)
}

// ConcreteList is a list of entities.
type ConcreteList struct {
  AbstractList
}
```

</td><td>

```go
// ConcreteList is a list of entities.
type ConcreteList struct {
  list AbstractList
}

// Add adds an entity to the list.
func (l *ConcreteList) Add(e Entity) {
  l.list.Add(e)
}

// Remove removes an entity from the list.
func (l *ConcreteList) Remove(e Entity) {
  l.list.Remove(e)
}
```

</td></tr>
</tbody></table>

Que ce soit avec une structure embarquée ou une interface embarquée, le type embarqué
pose des limites à l'évolution du type.

- L'ajout de méthodes à une interface embarquée est un changement qui casse la compatiblité.
- La suppression de méthodes d'une structure embarquée est un changement qui casse la compatiblité.
- La suppression du type embarqué est une modification avec rupture de compatibilité.
- Remplacer le type embarqué, même par une alternative implémentant la même interface, est un changement qui casse la compatiblité.

Bien que l'écriture de ces méthodes déléguées soit fastidieuse, l'effort supplémentaire cache
un détail d'implémentation, laisse plus de possibilités de changement, et aussi élimine l'indirection pour découvrir l'interface complète de la liste dans la documentation.

### Évitez d'utiliser des identifiants prédéclarés

La [spécification du langage Go](https://golang.org/ref/spec) définit plusieurs [identifiants prédéclarés](https://golang.org/ref/spec#Predeclared_identifiers) qui ne doivent pas être utilisés comme noms dans les programmes Go.

Selon le contexte, l'ombrage ou la réutilisation de ces identifiants en tant que noms masquera l'original dans la portée lexicale actuelle (et toutes les portées lexicales filles) ou rendra le code affecté déroutant.
Dans le meilleur des cas, le compilateur se plaindra. Dans le pire des cas, un tel code peut introduire des bogues latents difficiles à identifier.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
var error string
// `error` shadows the builtin

// or

func handleErrorMessage(error string) {
    // `error` shadows the builtin
}
```

</td><td>

```go
var errorMessage string
// `error` refers to the builtin

// or

func handleErrorMessage(msg string) {
    // `error` refers to the builtin
}
```

</td></tr>
<tr><td>

```go
type Foo struct {
    // While these fields technically don't
    // constitute shadowing, grepping for
    // `error` or `string` strings is now
    // ambiguous.
    error  error
    string string
}

func (f Foo) Error() error {
    // `error` and `f.error` are
    // visually similar
    return f.error
}

func (f Foo) String() string {
    // `string` and `f.string` are
    // visually similar
    return f.string
}
```

</td><td>

```go
type Foo struct {
    // `error` and `string` strings are
    // now unambiguous.
    err error
    str string
}

func (f Foo) Error() error {
    return f.err
}

func (f Foo) String() string {
    return f.str
}
```

</td></tr>
</tbody></table>

Notez que le compilateur ne générera pas d'erreurs lors de l'utilisation d'identifiants prédéclarés, mais des outils tels que "go vet" devraient correctement signaler ces cas et d'autres cas d'ombrage.

### Évitez `init()`

Évitez `init()` dans la mesure du possible. Lorsque `init()` est inévitable ou souhaitable, le code
devrait tenter :

1. d'être complètement déterministe, quel que soit l'environnement du programme ou de l'appel.
2. d'éviter de dépendre de l'ordre ou des effets secondaires des autres fonctions `init()`.
   Bien que l'ordre des `init()` soit bien connu, le code peut changer, et donc
   les relations entre les fonctions `init()` peuvent rendre le code fragile et
   sujet aux erreurs.
3. d'éviter d'accéder ou de manipuler l'état global ou de l'environnement, tel que les informations de la machine, les variables d'environnement, répertoire de travail, arguments/entrées du programme, etc.
4. d'éviter les Entrées/Sorties, y compris les appels au système de fichiers, au réseau et les appels système.

Le code qui ne peut pas satisfaire ces exigences appartient probablement à la catégorie d'outils
appelés dans le cadre de `main()` (ou ailleurs dans le cycle de vie d'un programme), ou écrits dans le cadre de `main()` lui-même. En particulier, les bibliothèques destinées à être utilisés par d'autres programmes doivent prendre un soin particulier à être complètement déterministes et ne pas effectuer de "magie d'init".

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
type Foo struct {
    // ...
}

var _defaultFoo Foo

func init() {
    _defaultFoo = Foo{
        // ...
    }
}
```

</td><td>

```go
var _defaultFoo = Foo{
    // ...
}

// or, better, for testability:

var _defaultFoo = defaultFoo()

func defaultFoo() Foo {
    return Foo{
        // ...
    }
}
```

</td></tr>
<tr><td>

```go
type Config struct {
    // ...
}

var _config Config

func init() {
    // Bad: based on current directory
    cwd, _ := os.Getwd()

    // Bad: I/O
    raw, _ := ioutil.ReadFile(
        path.Join(cwd, "config", "config.yaml"),
    )

    yaml.Unmarshal(raw, &_config)
}
```

</td><td>

```go
type Config struct {
    // ...
}

func loadConfig() Config {
    cwd, err := os.Getwd()
    // handle err

    raw, err := ioutil.ReadFile(
        path.Join(cwd, "config", "config.yaml"),
    )
    // handle err

    var config Config
    yaml.Unmarshal(raw, &config)

    return config
}
```

</td></tr>
</tbody></table>

Compte tenu de ce qui précède, il existe néanmoins certaines situations dans lesquelles `init()` peut être préférable ou
nécessaire :

- Expressions complexes qui ne peuvent pas être représentées comme des affectations uniques.
- Logiques prêtes à l'appel, tels que les dialectes `database/sql`, les registres de type d'encodage, etc.
- Optimisations pour [Google Cloud Functions](https://cloud.google.com/functions/docs/bestpractices/tips#use_global_variables_to_reuse_objects_in_future_invocations) et d'autres formes de pré-calcul déterministe.

### Sortez dans Main

Les programmes Go utilisent [`os.Exit`](https://golang.org/pkg/os/#Exit) ou [`log.Fatal*`](https://golang.org/pkg/log/#Fatal) pour quitter immédiatement. (Paniquer n'est pas un bon moyen de quitter les programmes, s'il vous plaît [ne paniquez pas](#ne-paniquez-pas).)

Appelez soit `os.Exit` soit `log.Fatal*` **uniquement dans `main()`**. Toutes les autres fonctions doivent renvoyer des erreurs pour signaler un échec.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
func main() {
  body := readFile(path)
  fmt.Println(body)
}

func readFile(path string) string {
  f, err := os.Open(path)
  if err != nil {
    log.Fatal(err)
  }

  b, err := ioutil.ReadAll(f)
  if err != nil {
    log.Fatal(err)
  }

  return string(b)
}
```

</td><td>

```go
func main() {
  body, err := readFile(path)
  if err != nil {
    log.Fatal(err)
  }
  fmt.Println(body)
}

func readFile(path string) (string, error) {
  f, err := os.Open(path)
  if err != nil {
    return "", err
  }

  b, err := ioutil.ReadAll(f)
  if err != nil {
    return "", err
  }

  return string(b), nil
}
```

</td></tr>
</tbody></table>

Justification : les programmes dotés de plusieurs fonctions qui se terminent présentent quelques problèmes :

- Flux de contrôle non évident : n'importe quelle fonction peut quitter le programme et il devient
  difficile de raisonner sur le flux de contrôle.
- Difficile à tester : Une fonction qui sort du programme sortira également du test
  l'appelant. Cela rend la fonction difficile à tester et introduit un risque de
  sauter d'autres tests qui n'ont pas encore été exécutés par `go test`.
- Nettoyage ignoré : lorsqu'une fonction quitte le programme, elle ignore les appels de fonction
  mis en file d'attente avec des instructions `defer`. Cela augmente le risque de sauter des étapes importantes
  de nettoyage.

#### Sortez au plus une fois

Si possible, préférez appeler `os.Exit` ou `log.Fatal` **au plus une fois** dans votre
`main()`. S'il existe plusieurs scénarios d'erreurs qui arrêtent l'exécution du programme,
déplacez cette logique sous une fonction distincte et faites-la renvoyer les erreurs.

Cela a pour effet de raccourcir votre fonction `main()` et de mettre toutes les logiques métiers clées dans une fonction distincte et testable.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
package main

func main() {
  args := os.Args[1:]
  if len(args) != 1 {
    log.Fatal("missing file")
  }
  name := args[0]

  f, err := os.Open(name)
  if err != nil {
    log.Fatal(err)
  }
  defer f.Close()

  // If we call log.Fatal after this line,
  // f.Close will not be called.

  b, err := ioutil.ReadAll(f)
  if err != nil {
    log.Fatal(err)
  }

  // ...
}
```

</td><td>

```go
package main

func main() {
  if err := run(); err != nil {
    log.Fatal(err)
  }
}

func run() error {
  args := os.Args[1:]
  if len(args) != 1 {
    return errors.New("missing file")
  }
  name := args[0]

  f, err := os.Open(name)
  if err != nil {
    return err
  }
  defer f.Close()

  b, err := ioutil.ReadAll(f)
  if err != nil {
    return err
  }

  // ...
}
```

</td></tr>
</tbody></table>

### Utilisez les balises de champ dans les structures sérialisées

Tout champ struct qui est sérialisé en JSON, YAML, ou d'autres formats prenant en charge la dénomination de champ basée sur des balises doit être annoté avec la balise appropriée.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
type Stock struct {
  Price int
  Name  string
}

bytes, err := json.Marshal(Stock{
  Price: 137,
  Name:  "UBER",
})
```

</td><td>

```go
type Stock struct {
  Price int    `json:"price"`
  Name  string `json:"name"`
  // Safe to rename Name to Symbol.
}

bytes, err := json.Marshal(Stock{
  Price: 137,
  Name:  "UBER",
})
```

</td></tr>
</tbody></table>

Justification:
La forme sérialisée de la structure est un contrat entre différents systèmes.
Les modifications apportées à la structure sérialisée, y compris les noms de champ, romperaient ce contrat.
Spécifier des noms de champs à l'intérieur des balises rend le contrat explicite,
et il protège contre la rupture accidentelle du contrat en refactorisant ou en renommant les champs.

## Performance

Les recommendations spécifiques aux performances s'appliquent uniquement au chemin le plus critique (en termes de performance).

### Préférez strconv à fmt

Lors de la conversion de primitives vers/depuis des chaînes, `strconv` est plus rapide que `fmt`.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
for i := 0; i < b.N; i++ {
  s := fmt.Sprint(rand.Int())
}
```

</td><td>

```go
for i := 0; i < b.N; i++ {
  s := strconv.Itoa(rand.Int())
}
```

</td></tr>
<tr><td>

```
BenchmarkFmtSprint-4    143 ns/op    2 allocs/op
```

</td><td>

```
BenchmarkStrconv-4    64.2 ns/op    1 allocs/op
```

</td></tr>
</tbody></table>

### Évitez la conversion de chaîne de caractères en octets (`byte`)

Ne créez pas de tranches (`slices`) d'octets (`byte`) à partir d'une chaîne de caractères fixe de manière répétée. Effectuez plutôt la conversion une fois et capturez le résultat.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
for i := 0; i < b.N; i++ {
  w.Write([]byte("Bonjour"))
}
```

</td><td>

```go
data := []byte("Bonjour")
for i := 0; i < b.N; i++ {
  w.Write(data)
}
```

</td></tr>
<tr><td>

```
BenchmarkBad-4   50000000   22.2 ns/op
```

</td><td>

```
BenchmarkGood-4  500000000   3.25 ns/op
```

</td></tr>
</tbody></table>

### Préférez spécifier la capacité des conteneurs

Spécifiez la capacité du conteneur si possible afin d'allouer de la mémoire pour le
conteneur en avance de phase. Cela minimise les allocations ultérieures (en copiant et
redimensionnement du conteneur) au fur et à mesure que des éléments y sont ajoutés.

#### Spécification des indices de capacité pour les dictionnaires (Maps)

Dans la mesure du possible, fournissez des indices de capacité lors de l'initialisation
d'un dictionnaire (map) avec `make()`.

```go
make(map[T1]T2, hint)
```

Fournir un indice de capacité à `make()` essaie de dimensionner correctement le
dictionnaire (map) au moment de son initialisation, ce qui réduit le besoin de croissance
le dictionnaire et les allocations au fur et à mesure que des éléments y sont ajoutés.

Notez que, contrairement aux tranches (slices), les indications de capacité sur les dictionnaires (maps) ne garantissent pas la complète allocation préemptive, mais sont utilisées pour estimer le nombre de compartiments nécessaires. Par conséquent, des allocations peuvent encore se produire lors de l'ajout d'éléments au ditionnaire, même jusqu'à la capacité spécifiée.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
m := make(map[string]os.FileInfo)

files, _ := ioutil.ReadDir("./files")
for _, f := range files {
    m[f.Name()] = f
}
```

</td><td>

```go

files, _ := ioutil.ReadDir("./files")

m := make(map[string]os.FileInfo, len(files))
for _, f := range files {
    m[f.Name()] = f
}
```

</td></tr>
<tr><td>

`m` est créé sans indice sur sa taille; il peut y avoir plus d'allocations au moment de l'assignation.

</td><td>

`m` est créé avec un indice sur sa taille; il peut y avoir moins d'allocations au momen de l'assignation.

</td></tr>
</tbody></table>

#### Spécification des indices de capacité pour les Tranches (Slices)

Dans la mesure du possible, fournissez des indications de capacité lors de l'initialisation des tranches(slices) avec `make()`, en particulier lors de l'ajout.

```go
make([]T, length, capacity)
```

Contrairement aux dictionnaires (maps), la capacité des tranches (slices) n'est pas juste un indice : le compilateur allouera suffisamment de mémoire pour la capacité de la tranche fournie à `make()`, ce qui signifie que
les opérations `append()` suivantes n'entraîneront aucune allocation (jusqu'à ce que la longueur de la tranche corresponde à la capacité, après quoi tout ajout nécessitera un redimensionnement pour contenir des éléments supplémentaires).

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
for n := 0; n < b.N; n++ {
  data := make([]int, 0)
  for k := 0; k < size; k++{
    data = append(data, k)
  }
}
```

</td><td>

```go
for n := 0; n < b.N; n++ {
  data := make([]int, 0, size)
  for k := 0; k < size; k++{
    data = append(data, k)
  }
}
```

</td></tr>
<tr><td>

```
BenchmarkBad-4    100000000    2.48s
```

</td><td>

```
BenchmarkGood-4   100000000    0.21s
```

</td></tr>
</tbody></table>

## Style

### Évitez les lignes trop longues

Évitez les lignes de code qui obligent les lecteurs à faire défiler l'écran horizontalement ou à trop tourner la tête.

Nous recommandons une limite de longueur de ligne souple de **99 caractères**.
Les développeurs devraient envisager de retourner à la ligne avant d'atteindre cette limite,
mais ce n'est pas une limite stricte.
Le code est autorisé à dépasser cette limite.

### Soyez Cohérent

Certaines des recommendations décrites dans ce document peuvent être évaluées objectivement;
d'autres sont contextuels ou subjectifs.

Par-dessus tout, **soyez cohérent**.

Un code cohérent est plus facile à maintenir, plus facile à rationaliser, nécessite moins de
surcoût cognitif, et est plus facile à migrer ou à mettre à jour à mesure que de nouvelles conventions émergent
ou des classes de bogues sont corrigés.

Inversement, avoir plusieurs styles disparates ou conflictuels au sein d'une même base de code entraîne une surcharge de maintenance, une incertitude et une dissonance cognitive, qui peuvent tous directement contribuer à réduire la vélocité, à accroître les révisions de code pénibles, et les bogues.

Lors de l'application de ces recommendations à une base de code, il est recommandé que les modifications
soient faites au niveau du package (ou plus) : les appliquer au niveau du sous-package
va à l'encontre de la recommendation ci-dessus en introduisant plusieurs styles dans le même code.

### Regroupez les déclarations similaires

Go prend en charge le regroupement de déclarations similaires.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
import "a"
import "b"
```

</td><td>

```go
import (
  "a"
  "b"
)
```

</td></tr>
</tbody></table>

Cela s'applique également aux constantes, aux variables et aux déclarations de type.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go

const a = 1
const b = 2



var a = 1
var b = 2



type Area float64
type Volume float64
```

</td><td>

```go
const (
  a = 1
  b = 2
)

var (
  a = 1
  b = 2
)

type (
  Area float64
  Volume float64
)
```

</td></tr>
</tbody></table>

Regroupez uniquement les déclarations qui sont liées entre elles. Ne regroupez pas les déclarations qui n'ont aucun lien entre elles.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
type Operation int

const (
  Add Operation = iota + 1
  Subtract
  Multiply
  EnvVar = "MY_ENV"
)
```

</td><td>

```go
type Operation int

const (
  Add Operation = iota + 1
  Subtract
  Multiply
)

const EnvVar = "MY_ENV"
```

</td></tr>
</tbody></table>

Les groupes ne sont pas limités à l'endroit où ils peuvent être utilisés. Par exemple, vous pouvez les utiliser
à l'intérieur des fonctions.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
func f() string {
  red := color.New(0xff0000)
  green := color.New(0x00ff00)
  blue := color.New(0x0000ff)

  // ...
}
```

</td><td>

```go
func f() string {
  var (
    red   = color.New(0xff0000)
    green = color.New(0x00ff00)
    blue  = color.New(0x0000ff)
  )

  // ...
}
```

</td></tr>
</tbody></table>

Exception : les déclarations de variables, en particulier à l'intérieur des fonctions, doivent être
regroupées si ces variables sont déclarés adjacentes à d'autres variables. Faites ceci pour les variables
déclarés ensemble même si elles ne sont pas liées.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
func (c *client) request() {
  caller := c.name
  format := "json"
  timeout := 5*time.Second
  var err error

  // ...
}
```

</td><td>

```go
func (c *client) request() {
  var (
    caller  = c.name
    format  = "json"
    timeout = 5*time.Second
    err error
  )

  // ...
}
```

</td></tr>
</tbody></table>

### Ordonnez les groupes d'Import

Il devrait y avoir deux groupes d'Import:

- Bibliothèque standard
- Tout le reste

Il s'agit du regroupement appliqué par défaut par `goimports`.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
import (
  "fmt"
  "os"
  "go.uber.org/atomic"
  "golang.org/x/sync/errgroup"
)
```

</td><td>

```go
import (
  "fmt"
  "os"

  "go.uber.org/atomic"
  "golang.org/x/sync/errgroup"
)
```

</td></tr>
</tbody></table>

### Noms des Packages

Lorsque vous nommez des packages, choisissez un nom qui a les caractéristiques suivantes :

- Tout en minuscules. Pas de majuscules ni d'underscores.
- N'a pas besoin d'être renommé à l'aide d'imports nommées là où il est appelé.
- Court et succinct. Rappelez-vous que le nom est identifié en entier à chaque appel.
- Pas au pluriel. Par exemple, `net/url`, et non `net/urls`.
- Non "commun", "util", "partagé" ou "lib". Ce sont des noms mauvais et non informatifs.

Voir également [Package Names](https://blog.golang.org/package-names) et [Style guideline for Go packages](https://rakyll.org/style-packages/).

### Noms des fonctions

Nous suivons la convention de la communauté Go d'utiliser [MixedCaps pour les noms des fonctions](https://golang.org/doc/effective_go.html#mixed-caps). Une exception est faite pour les fonctions de tests, qui peuvent contenir des underscores dans le but de regrouper des cas de test connexes, par exemple,
`TestMyFunction_WhatIsBeingTested`.

### Alias des Imports

Utilisez un alias d'import si le nom du package ne correspond pas au dernier élément du chemin d'import.

```go
import (
  "net/http"

  client "example.com/client-go"
  trace "example.com/trace/v2"
)
```

Dans tous les autres cas, les alias d'import doivent être évités, à moins qu'il n'y ait un conflit direct entre les imports.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
import (
  "fmt"
  "os"


  nettrace "golang.net/x/trace"
)
```

</td><td>

```go
import (
  "fmt"
  "os"
  "runtime/trace"

  nettrace "golang.net/x/trace"
)
```

</td></tr>
</tbody></table>

### Regroupement et Ordre des Fonctions

- Les fonctions doivent être triées approximativement suivant l'ordre d'appel.
- Les fonctions d'un fichier doivent être regroupées par receveur.

Par conséquent, les fonctions exportées doivent apparaître en premier dans un fichier, après les définitions des `struct`, `const`, `var`.

Un `newXYZ()`/`NewXYZ()` peut apparaître après la définition du type, mais devrait l'être avant le reste des méthodes sur le receveur.

Étant donné que les fonctions sont regroupées par récepteur, les fonctions purement utilitaires doivent apparaître vers la fin du fichier.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
func (s *quelquechose) Cout() {
  return calcCout(s.poids)
}

type quelquechose struct{ ... }

func calcCout(n []int) int {...}

func (s *quelquechose) Stop() {...}

func newQuelquechose() *quelquechose {
    return &quelquechose{}
}
```

</td><td>

```go
type quelquechose struct{ ... }

func newQuelquechose() *quelquechose {
    return &quelquechose{}
}

func (s *quelquechose) Cout() {
  return calcCout(s.poids)
}

func (s *quelquechose) Stop() {...}

func calcCout(n []int) int {...}
```

</td></tr>
</tbody></table>

### Réduisez l'imbrication

Le code doit Réduisez l'imbrication dans la mesure du possible en gérant les cas d'erreur/conditions spéciales en premier lieu et en retournant plus tôt ou en continuant la boucle. Réduire la quantité de code imbriqué sur plusieurs niveaux.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
for _, v := range data {
  if v.F1 == 1 {
    v = process(v)
    if err := v.Call(); err == nil {
      v.Send()
    } else {
      return err
    }
  } else {
    log.Printf("Invalid v: %v", v)
  }
}
```

</td><td>

```go
for _, v := range data {
  if v.F1 != 1 {
    log.Printf("Invalid v: %v", v)
    continue
  }

  v = process(v)
  if err := v.Call(); err != nil {
    return err
  }
  v.Send()
}
```

</td></tr>
</tbody></table>

### `Else` Inutiles

Si une variable est définie dans les deux branches d'un `if`, elle peut être remplacée par un seul `if`.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
var a int
if b {
  a = 100
} else {
  a = 10
}
```

</td><td>

```go
a := 10
if b {
  a = 100
}
```

</td></tr>
</tbody></table>

### Déclarations de variables de niveau supérieur

Au niveau supérieur, utilisez le mot-clé standard `var`. Ne précisez pas le type,
à moins qu'il ne s'agisse pas du même type que l'expression.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
var _s string = F()

func F() string { return "A" }
```

</td><td>

```go
var _s = F()
// Puisque F déclare déjà retourner une chaîne de caractères,
// l'on n'a pas besoin de spécifier le type de _s une nouvelle fois.

func F() string { return "A" }
```

</td></tr>
</tbody></table>

Spécifiez le type si le type de l'expression ne correspond pas exactement au type souhaité.


```go
type myError struct{}

func (myError) Error() string { return "error" }

func F() myError { return myError{} }

var _e error = F()
// F retourne un objet de type myError,
// mais on souhaite plutôt avoir un type error
```

### Préfixez les variables globales non exportées par _

Préfixez les `var` et `const` de niveau supérieur non exportés avec `_` pour indiquer clairement quand ils sont utilisés qu'ils sont des symboles globaux.

Exception : valeurs d'erreur non exportées, qui doivent être préfixées par `err`.

Justification : les variables et les constantes de niveau supérieur ont une portée de niveau package. Utiliser un nom générique accroît le risque d'utiliser la mauvaise valeur dans un autre fichier.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
// foo.go

const (
  defaultPort = 8080
  defaultUser = "user"
)

// bar.go

func Bar() {
  defaultPort := 9090
  ...
  fmt.Println("Default port", defaultPort)

  // Pas d'erreur de compilation
  // si la première ligne de Bar() est supprimée
}
```

</td><td>

```go
// foo.go

const (
  _defaultPort = 8080
  _defaultUser = "user"
)
```

</td></tr>
</tbody></table>

**Exception** : les valeurs d'erreur non exportées peuvent utiliser le préfixe `err` sans `_`. Voir [Nommage des Erreurs](#nommage-des-erreurs).

### Embarquement dans les Structs

Les types embarqués doivent figurer en haut de la liste des champs d'un `struct`, et il doit y avoir une ligne vide séparant les champs embarqués des champs normaux des champs.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
type Client struct {
  version int
  http.Client
}
```

</td><td>

```go
type Client struct {
  http.Client

  version int
}
```

</td></tr>
</tbody></table>

L'embarquement doit fournir des avantages tangibles, comme l'ajout ou l'enrichissement d'une fonctionnalité d'une manière sémantiquement appropriée. Cela devrait se faire sans aucun effet de bord visible de l'utilisateur (voir aussi : [Évitez d'embarquer des types dans des structures publiques](#évitez-dembarquer-des-types-dans-des-structures-publiques)).

Exception : les mutex ne doivent pas être embarqués, même sur des types non exportés. Voir aussi : [Les valeurs zéro des Mutex sont valides](#les-valeurs-zéro-des-mutex-sont-valides).

L'embarquement **ne devrait pas** :

- Être purement cosmétique ou axé sur la commodité.
- Rendre les types extérieurs plus difficiles à construire ou à utiliser.
- Affecter les valeurs zéro des types externes. Si le type externe a une valeur zéro utile, il devrait toujours avoir une valeur zéro utile après avoir embarqué le type interne.
- Exposer des fonctions ou des champs non liés du type externe comme effet secondaire dû eu fait d'embarquer le type interne.
- Exposer des types non exportés.
- Affecter la sémantique de copie des types externes.
- Modifier l'API ou la sémantique du type externe.
- Embarquer une forme non canonique du type interne.
- Exposer les détails d'implémentation du type externe.
- Permettre aux utilisateurs d'observer ou de contrôler la logique interne du type.
- Modifier le comportement général des fonctions internes en les enveloppant d'une manière qui surprendrait raisonnablement les utilisateurs.

En termes simples, embarquez consciemment et intentionnellement. Un bon test décisif est: "toutes ces méthodes/champs internes exportés pourraient-il être ajoutés directement au type externe?" ;
si la réponse est "certaines ou certains" ou "non", n'embarquez pas le type interne - utilisez un champ à la place.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
type A struct {
    // Non Recommandé: A.Lock() and A.Unlock() sont
    //      dorénavant disponibles, sans aucun
    //      bénéfice fonctionnel, et permettent
    //      aux utilisateurs de contrôler les détails
    //      concernant la logique interne de A.
    sync.Mutex
}
```

</td><td>

```go
type countingWriteCloser struct {
    // Recommandé: Write() est mis à disposition de
    //       cette couche externe dans un but
    //       bien précis, et délègue du travail
    //       à la méthode Write() du type interne.
    io.WriteCloser

    count int
}

func (w *countingWriteCloser) Write(bs []byte) (int, error) {
    w.count += len(bs)
    return w.WriteCloser.Write(bs)
}
```

</td></tr>
<tr><td>

```go
type Book struct {
    // Bad: pointer changes zero value usefulness
    io.ReadWriter

    // other fields
}

// later

var b Book
b.Read(...)  // panic: nil pointer
b.String()   // panic: nil pointer
b.Write(...) // panic: nil pointer
```

</td><td>

```go
type Book struct {
    // Good: has useful zero value
    bytes.Buffer

    // other fields
}

// later

var b Book
b.Read(...)  // ok
b.String()   // ok
b.Write(...) // ok
```

</td></tr>
<tr><td>

```go
type Client struct {
    sync.Mutex
    sync.WaitGroup
    bytes.Buffer
    url.URL
}
```

</td><td>

```go
type Client struct {
    mtx sync.Mutex
    wg  sync.WaitGroup
    buf bytes.Buffer
    url url.URL
}
```

</td></tr>
</tbody></table>

### Déclarations de variables locales

Les déclarations de variables courtes (`:=`) doivent être utilisées si une variable est définie sur une certaine valeur de manière explicite.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
var s = "foo"
```

</td><td>

```go
s := "foo"
```

</td></tr>
</tbody></table>

Cependant, il est des cas où la valeur par défaut est plus claire lorsque le mot-clé `var` est utilisé. [Déclarer des tranches (slices) vides](https://github.com/golang/go/wiki/CodeReviewComments#declaring-empty-slices), par exemple.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
func f(list []int) {
  filtered := []int{}
  for _, v := range list {
    if v > 10 {
      filtered = append(filtered, v)
    }
  }
}
```

</td><td>

```go
func f(list []int) {
  var filtered []int
  for _, v := range list {
    if v > 10 {
      filtered = append(filtered, v)
    }
  }
}
```

</td></tr>
</tbody></table>

### nil est une Tranche (Slice) valide

`nil` est une tranche (slice) valide de longueur 0. Cela signifie que:

- Vous ne devez pas retourner explicitement une tranche (slice) de longueur zéro. Retournez `nil` plutôt.

  <table>
  <thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
  <tbody>
  <tr><td>

  ```go
  if x == "" {
    return []int{}
  }
  ```

  </td><td>

  ```go
  if x == "" {
    return nil
  }
  ```

  </td></tr>
  </tbody></table>

- Pour vérifier si une tranche (slice) est vide, utilisez toujours `len(s) == 0`. Ne vérifiez pas si elle vaut `nil`.

  <table>
  <thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
  <tbody>
  <tr><td>

  ```go
  func isEmpty(s []string) bool {
    return s == nil
  }
  ```

  </td><td>

  ```go
  func isEmpty(s []string) bool {
    return len(s) == 0
  }
  ```

  </td></tr>
  </tbody></table>


- La valeur zéro (une tranche (slice) déclarée avec `var`) est utilisable immédiatement sans `make()`.

  <table>
  <thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
  <tbody>
  <tr><td>

  ```go
  nums := []int{}
  // or, nums := make([]int)

  if add1 {
    nums = append(nums, 1)
  }

  if add2 {
    nums = append(nums, 2)
  }
  ```

  </td><td>

  ```go
  var nums []int

  if add1 {
    nums = append(nums, 1)
  }

  if add2 {
    nums = append(nums, 2)
  }
  ```

  </td></tr>
  </tbody></table>

N'oubliez pas que, bien qu'il s'agisse d'une tranche (slice) valide, une tranche `nil` n'est pas équivalente à une tranche (slice) allouée de longueur 0; l'une est nulle et l'autre non - et les deux peuvent être traitées différemment dans différentes situations (telles que la sérialisation).

### Réduisez la portée des variables

Dans la mesure du possible, réduisez la portée des variables. Ne réduisez pas la portée si elle est en conflit avec [Réduisez l'imbrication](#reduisez-imbrication).

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
err := ioutil.WriteFile(name, data, 0644)
if err != nil {
 return err
}
```

</td><td>

```go
if err := ioutil.WriteFile(name, data, 0644); err != nil {
 return err
}
```

</td></tr>
</tbody></table>

Si vous avez besoin d'un résultat d'appel de fonction en dehors du `if`, alors vous ne devriez pas essayer de réduire la portée.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
if data, err := ioutil.ReadFile(name); err == nil {
  err = cfg.Decode(data)
  if err != nil {
    return err
  }

  fmt.Println(cfg)
  return nil
} else {
  return err
}
```

</td><td>

```go
data, err := ioutil.ReadFile(name)
if err != nil {
   return err
}

if err := cfg.Decode(data); err != nil {
  return err
}

fmt.Println(cfg)
return nil
```

</td></tr>
</tbody></table>

### Évitez les paramètres nus

Les paramètres nus dans les appels de fonction peuvent nuire à la lisibilité. Ajouter des commentaires de style C (`/* ... */`) pour les noms de paramètres lorsque leur sens n'est pas évident.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
// func printInfo(name string, isLocal, done bool)

printInfo("foo", true, true)
```

</td><td>

```go
// func printInfo(name string, isLocal, done bool)

printInfo("foo", true /* isLocal */, true /* done */)
```

</td></tr>
</tbody></table>

Mieux encore, remplacez les types `bool` nus par des types personnalisés pour plus de lisibilité et de sûreté de type. Cela permet plus que deux états (vrai/faux) pour ces
paramètres à l'avenir.

```go
type Region int

const (
  UnknownRegion Region = iota
  Local
)

type Status int

const (
  StatusReady Status = iota + 1
  StatusDone
  // Maybe we will have a StatusInProgress in the future.
)

func printInfo(name string, region Region, status Status)
```

### Utilisez des littéraux de chaîne de caractères bruts pour éviter les échappements

Go prend en charge les [littéraux de chaîne de caractères bruts] (https://golang.org/ref/spec#raw_string_lit), qui peuvent s'étendre sur plusieurs lignes et inclure des guillemets. Utilisez-les pour éviter les chaînes de caractères échappées à la main qui sont beaucoup plus difficiles à lire.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
wantError := "unknown name:\"test\""
```

</td><td>

```go
wantError := `unknown error:"test"`
```

</td></tr>
</tbody></table>

### Initialisation des structures

#### Utilisez les noms de champ pour initialiser les structures

Vous devez presque toujours spécifier les noms de champ lors de l'initialisation des structures. C'est dorénavant quelque chose de vérifié par [`go vet`](https://golang.org/cmd/vet/).

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
k := User{"John", "Doe", true}
```

</td><td>

```go
k := User{
    FirstName: "John",
    LastName: "Doe",
    Admin: true,
}
```

</td></tr>
</tbody></table>

Exception : Les noms de champ *peuvent* être omis dans les tables de test lorsqu'il y a 3 champs ou moins.

```go
tests := []struct{
  op Operation
  want string
}{
  {Add, "add"},
  {Subtract, "subtract"},
}
```

#### Omettez les champs de valeur zéro dans les structures

Lors de l'initialisation de structures avec des noms de champ, omettez les champs qui ont des valeurs zéro à moins qu'ils ne fournissent un contexte significatif. Sinon, laissez Go les mettre à leurs valeurs zéro automatiquement.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
user := User{
  FirstName: "John",
  LastName: "Doe",
  MiddleName: "",
  Admin: false,
}
```

</td><td>

```go
user := User{
  FirstName: "John",
  LastName: "Doe",
}
```

</td></tr>
</tbody></table>

Cela aide à réduire le bruit pour les lecteurs en omettant les valeurs par défaut dans
ce contexte. Seules les valeurs significatives sont spécifiées.

Incluez des valeurs zéro là où les noms de champ fournissent un contexte significatif. Par example, les cas de test dans [Test Tables](#test-tables) peuvent bénéficier de noms de champs même lorsqu'ils ont leur valeur zéro.

```go
tests := []struct{
  give string
  want int
}{
  {give: "0", want: 0},
  // ...
}
```

#### Utilisez `var` pour les structures à valeur zéro

Lorsque tous les champs d'une structure sont omis dans une déclaration, utilisez la forme `var` pour déclarer la structure.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
user := User{}
```

</td><td>

```go
var user User
```

</td></tr>
</tbody></table>

Cela différencie les structures à valeur zéro de celles avec des champs non nuls,
de manière similaire à la distinction créée pour [l'initialization des dictionnaires](#initializing-maps), et correspond à la façon dont nous préférons [déclarer des tranches (slices) vides](#declarer-tranches-vides).

#### Initialisation des références de Structure

Utilisez `&T{}` au lieu de `new(T)` lors de l'initialisation des références de structure afin que ce soit cohérent avec l'initialisation de la structure.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
sval := T{Name: "foo"}

// inconsistent
sptr := new(T)
sptr.Name = "bar"
```

</td><td>

```go
sval := T{Name: "foo"}

sptr := &T{Name: "bar"}
```

</td></tr>
</tbody></table>

### Initialisation des dictionnaires (maps)

Préférez `make(..)` pour les dictionnaires (maps) vides et les dictionnaires (maps) remplies par programme. Cela rend l'initialisation du dictionnaire visuellement
distincte de la déclaration, et il est facile d'ajouter des indices sur la taille plus tard s'ils sont disponibles.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
var (
  // m1 is safe to read and write;
  // m2 will panic on writes.
  m1 = map[T1]T2{}
  m2 map[T1]T2
)
```

</td><td>

```go
var (
  // m1 is safe to read and write;
  // m2 will panic on writes.
  m1 = make(map[T1]T2)
  m2 map[T1]T2
)
```

</td></tr>
<tr><td>

Déclaration et initialisation sont visuellement similaires.

</td><td>

Déclaration et initialisation sont visuellement distinctes.

</td></tr>
</tbody></table>

Dans la mesure du possible, fournissez des indices de capacité lors de l'initialisation des dictionnaires (maps) avec `make()`. Voir
[Spécification des conseils de capacité de la carte](#specifying-map-capacity-hints)
pour plus d'informations.

D'autre part, si le dictionnaire (map) contient une liste fixe d'éléments,
utilisez des littéraux de dictionnaires pour initialiser la carte.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
m := make(map[T1]T2, 3)
m[k1] = v1
m[k2] = v2
m[k3] = v3
```

</td><td>

```go
m := map[T1]T2{
  k1: v1,
  k2: v2,
  k3: v3,
}
```

</td></tr>
</tbody></table>

La règle de base consiste à utiliser des littéraux de dictionnaires lors de l'ajout d'un ensemble fixe d'éléments au moment de l'initialisation; sinon utilisez `make` (et spécifiez un indice de taille si disponible).

### Formatez les chaînes en dehors de Printf

Si vous déclarez des chaînes de formatage pour les fonctions de style `Printf` en dehors d'une chaîne littérale, faites-en des valeurs `const`.

Cela aide `go vet` à effectuer une analyse statique de la chaîne de formatage.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
msg := "unexpected values %v, %v\n"
fmt.Printf(msg, 1, 2)
```

</td><td>

```go
const msg = "unexpected values %v, %v\n"
fmt.Printf(msg, 1, 2)
```

</td></tr>
</tbody></table>

### Nommez les fonctions de style Printf

Lorsque vous déclarez une fonction de style `Printf`, assurez-vous que `go vet` peut détecter et vérifiez la chaîne de formatage.

Cela signifie que vous devez utiliser la fonction prédéfinie de style `Printf`
noms si possible. `go vet` les vérifiera par défaut. Voir [Famille Printf](https://golang.org/cmd/vet/#hdr-Printf_family)
pour plus d'informations.

Si l'utilisation des noms prédéfinis n'est pas une option, terminez le nom que vous choisissez par `f` : `Wrapf`, pas `Wrap`. `go vet` peut être invité à vérifier les noms spécifique de fonction de style `Printf`, mais ils doivent se terminer par `f`.

```shell
$ go vet -printfuncs=wrapf,statusf
```

Voir aussi [go vet: Printf family check](https://kuzminva.wordpress.com/2017/11/07/go-vet-printf-family-check/).

## Modèles

### Tables de tests

Utilisez des tests basés sur des tables avec [sous-tests](https://blog.golang.org/subtests) pour éviter de dupliquer le code lorsque le coeur de la logique de test est répétitif.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
// func TestSplitHostPort(t *testing.T)

host, port, err := net.SplitHostPort("192.0.2.0:8000")
require.NoError(t, err)
assert.Equal(t, "192.0.2.0", host)
assert.Equal(t, "8000", port)

host, port, err = net.SplitHostPort("192.0.2.0:http")
require.NoError(t, err)
assert.Equal(t, "192.0.2.0", host)
assert.Equal(t, "http", port)

host, port, err = net.SplitHostPort(":8000")
require.NoError(t, err)
assert.Equal(t, "", host)
assert.Equal(t, "8000", port)

host, port, err = net.SplitHostPort("1:8")
require.NoError(t, err)
assert.Equal(t, "1", host)
assert.Equal(t, "8", port)
```

</td><td>

```go
// func TestSplitHostPort(t *testing.T)

tests := []struct{
  give     string
  wantHost string
  wantPort string
}{
  {
    give:     "192.0.2.0:8000",
    wantHost: "192.0.2.0",
    wantPort: "8000",
  },
  {
    give:     "192.0.2.0:http",
    wantHost: "192.0.2.0",
    wantPort: "http",
  },
  {
    give:     ":8000",
    wantHost: "",
    wantPort: "8000",
  },
  {
    give:     "1:8",
    wantHost: "1",
    wantPort: "8",
  },
}

for _, tt := range tests {
  t.Run(tt.give, func(t *testing.T) {
    host, port, err := net.SplitHostPort(tt.give)
    require.NoError(t, err)
    assert.Equal(t, tt.wantHost, host)
    assert.Equal(t, tt.wantPort, port)
  })
}
```

</td></tr>
</tbody></table>

Les tables de test facilitent l'ajout nouveaux cas de tests et de contexte aux messages d'erreur et réduisent les doublons dans la logique.

Nous suivons la convention selon laquelle la tranche (slice) de structures est appelée "tests" et chaque cas de test `tt`. De plus, nous encourageons l'explicitation des valeurs d'entrée et de sortie pour chaque cas de test avec les préfixes `give` et `want`.

```go
tests := []struct{
  give     string
  wantHost string
  wantPort string
}{
  // ...
}

for _, tt := range tests {
  // ...
}
```

Les tests parallèles, comme certaines boucles spécialisées (par exemple, celles qui génèrent des goroutines ou capturent des références dans le corps de la boucle),
doivent prendre soin d'affecter explicitement les variables de boucle dans la portée de la boucle afin de s'assurer qu'ils ont bien les valeurs attendues.

```go
tests := []struct{
  give string
  // ...
}{
  // ...
}

for _, tt := range tests {
  tt := tt // for t.Parallel
  t.Run(tt.give, func(t *testing.T) {
    t.Parallel()
    // ...
  })
}
```

Dans l'exemple ci-dessus, nous devons déclarer une variable `tt` dont la porté est la boucle d'itérations en raison de l'utilisation de `t.Parallel()` en dessous.
Si nous ne le faisons pas, la plupart ou tous les tests recevront une valeur inattendue pour `tt`, ou une valeur qui change au fur et à mesure de leur exécution.

### Options fonctionnelles

Les options fonctionnelles sont un modèle dans lequel vous déclarez un type opaque `Option` qui enregistre des informations dans une structure interne. Vous acceptez un numéro variable de ces options et agissez sur la base de l'information complète enregistrée par les options sur la structure interne.

Utilisez ce modèle pour les arguments facultatifs dans les constructeurs et autres API publiques que vous prévoyez avoir besoin d'agrandir, surtout si vous avez déjà trois ou
plus d'arguments sur ces fonctions.

<table>
<thead><tr><th>Non Recommandé</th><th>Recommandé</th></tr></thead>
<tbody>
<tr><td>

```go
// package db

func Open(
  addr string,
  cache bool,
  logger *zap.Logger
) (*Connection, error) {
  // ...
}
```

</td><td>

```go
// package db

type Option interface {
  // ...
}

func WithCache(c bool) Option {
  // ...
}

func WithLogger(log *zap.Logger) Option {
  // ...
}

// Open creates a connection.
func Open(
  addr string,
  opts ...Option,
) (*Connection, error) {
  // ...
}
```

</td></tr>
<tr><td>

Les paramètres de cache et de journalisation doivent toujours être fournis, même si l'utilisateur souhaite utiliser la valeur par défaut.


```go
db.Open(addr, db.DefaultCache, zap.NewNop())
db.Open(addr, db.DefaultCache, log)
db.Open(addr, false /* cache */, zap.NewNop())
db.Open(addr, false /* cache */, log)
```

</td><td>

Les options sont fournies uniquement si nécessaire.

```go
db.Open(addr)
db.Open(addr, db.WithLogger(log))
db.Open(addr, db.WithCache(false))
db.Open(
  addr,
  db.WithCache(false),
  db.WithLogger(log),
)
```

</td></tr>
</tbody></table>

La méthode que nous suggérons pour implémenter ce modèle consiste à utiliser une interface `Option` qui contient une méthode non exportée, enregistrant des options sur une structure `options` non exportée.

```go
type options struct {
  cache  bool
  logger *zap.Logger
}

type Option interface {
  apply(*options)
}

type cacheOption bool

func (c cacheOption) apply(opts *options) {
  opts.cache = bool(c)
}

func WithCache(c bool) Option {
  return cacheOption(c)
}

type loggerOption struct {
  Log *zap.Logger
}

func (l loggerOption) apply(opts *options) {
  opts.logger = l.Log
}

func WithLogger(log *zap.Logger) Option {
  return loggerOption{Log: log}
}

// Open creates a connection.
func Open(
  addr string,
  opts ...Option,
) (*Connection, error) {
  options := options{
    cache:  defaultCache,
    logger: zap.NewNop(),
  }

  for _, o := range opts {
    o.apply(&options)
  }

  // ...
}
```

Notez qu'il existe une méthode pour implémenter ce modèle avec des closures mais nous
pensons que le modèle ci-dessus offre plus de flexibilité aux auteurs et est
plus facile à déboguer et à tester pour les utilisateurs. En particulier, il permet aux options d'être comparées les unes aux autres dans les tests et les mocks, par rapport aux closures où cela est impossible. De plus, il permet aux options d'implémenter d'autres interfaces, y compris `fmt.Stringer` qui permet des représentations sous forme de chaîne de caractères lisibles par l'utilisateur des options.

Voir également,

- [Fonctions auto-référentielles et conception des options](https://commandcenter.blogspot.com/2014/01/self-referential-functions-and-design.html)
- [Options fonctionnelles pour les API conviviales](https://dave.cheney.net/2014/10/17/functional-options-for-friendly-apis)

<!-- TODO: replace this with parameter structs and functional options, when to
use one vs other -->

## Linting

Plus important encore que n'importe quel ensemble "béni" de linters, appliquez le lintint de manière cohérente à travers la base de code.

Nous recommandons d'utiliser au minimum les linters suivants, car nous estimons qu'ils
aident à détecter les problèmes les plus courants et à établir une barre haute pour la qualité du code sans être inutilement prescriptif :

- [errcheck](https://github.com/kisielk/errcheck) pour s'assurer que les erreurs sont gérées
- [goimports](https://godoc.org/golang.org/x/tools/cmd/goimports) pour formater le code et gérer les imports
- [golint](https://github.com/golang/lint) pour signaler les erreurs de style courantes
- [govet](https://golang.org/cmd/vet/) pour analyser le code à la recherche d'erreurs courantes
- [staticcheck](https://staticcheck.io/) pour effectuer diverses vérifications d'analyse statique

### Lanceurs d'outils de Linting

Nous recommandons [golangci-lint](https://github.com/golangci/golangci-lint) comme lanceur incontournable d'outils de linting pour le code Go, en grande partie grâce
à ses performances sur des bases de code plus importantes et à sa capacité à configurer et à utiliser de nombreux linters canoniques à la fois. Ce dépôt a un exemple de fichier de configuration [.golangci.yml](https://github.com/uber-go/guide/blob/master/.golangci.yml) avec les linters et réglages recommandés.

golangci-lint a [divers linters](https://golangci-lint.run/usage/linters/) disponibles pour utilisation. Les linters ci-dessus sont recommandés comme ensemble de base, et nous encourageons les équipes à ajouter des linters supplémentaires qui font sens pour leurs projets.
