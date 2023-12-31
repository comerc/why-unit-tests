# Зачем нужны модульные тесты и как заставить их работать на вас

### Программное обеспечение

Преимущество программного обеспечения заключается в том, что оно может изменяться. Именно поэтому его называют "_soft_" обеспечение - оно более податливо, чем аппаратное обеспечение. Отличная команда инженеров должна быть замечательным активом компании, создавая системы, которые могут развиваться вместе с бизнесом, чтобы продолжать приносить пользу.

Так почему же мы так плохо справляемся с этой задачей? Сколько проектов, о которых вы слышали, полностью проваливаются? Или становятся "наследием", и их приходится полностью переписывать (и переписывать тоже часто неудачно!).

Как вообще происходит "отказ" программной системы? Разве ее нельзя просто менять до тех пор, пока она не станет правильной? Именно это нам и обещают!

Многие люди выбирают Go для создания систем, потому что в нем сделан ряд решений, которые, как можно надеяться, сделают его более устойчивым к наследию.

- По сравнению с моим предыдущим рассказом о Scala, где [я описывал, что в ней достаточно веревок, чтобы повеситься](http://www.quii.dev/Scala_-_Just_enough_rope_to_hang_yourself), в Go всего 25 ключевых слов, и _многие_ системы можно построить на основе стандартной библиотеки и нескольких других небольших библиотеках. Есть надежда, что на Go можно написать код и вернуться к нему через 6 месяцев, и он все еще будет иметь смысл.
- Инструментарий для тестирования, бенчмаркинга, линтинга и доставки является первоклассным по сравнению с большинством альтернатив.
- Стандартная библиотека великолепна.
- Очень высокая скорость компиляции, позволяющая работать с узкими петлями обратной связи.
- Обещание обратной совместимости с Go. Похоже, что в будущем Go получит дженерики и другие возможности, но разработчики пообещали, что даже код на Go, написанный 5 лет назад, будет по-прежнему собираться. Я буквально потратил несколько недель на обновление проекта со Scala 2.8 до 2.10.

Даже обладая всеми этими замечательными свойствами, мы все равно можем создавать ужасные системы, поэтому нам следует обратиться к прошлому и понять уроки программной инженерии, которые применимы независимо от того, насколько блестящим (или не очень) является ваш язык.

В 1974 году умный инженер-программист по имени [Мэнни Леман](<https://en.wikipedia.org/wiki/Manny_Lehman_(computer_scientist)>) написал [законы Лемана об эволюции программного обеспечения](https://en.wikipedia.org/wiki/Lehman's_laws_of_software_evolution).

> Эти законы описывают баланс между силами, стимулирующими новые разработки, с одной стороны, и силами, замедляющими прогресс, с другой стороны.

Эти силы представляются важными для понимания, если у нас есть надежда не оказаться в бесконечном цикле поставки систем, которые превращаются в наследие и затем переписываются снова и снова.

## Закон непрерывных изменений

> Любая программная система, используемая в реальном мире, должна меняться или становиться все менее и менее полезной в этой среде.

Кажется очевидным, что система _должна_ меняться, иначе она становится менее полезной, но как часто это игнорируется?

Многие команды заинтересованы в том, чтобы выполнить проект к определенной дате, а затем перейти к следующему проекту. Если программа "удачная", то ее, по крайней мере, передают другому человеку, который будет ее поддерживать, но, конечно, не они ее написали.

Часто люди пытаются выбрать фреймворк, который поможет им "сделать быстро", но не обращают внимания на долговечность системы с точки зрения того, как она должна развиваться.

Даже если вы великолепный инженер-программист, вы все равно станете жертвой незнания будущих потребностей вашей системы. По мере изменения бизнеса часть написанного вами блестящего кода перестает быть актуальной.

Леман был на подъеме в 70-е годы, потому что он дал нам еще один закон, который можно пожевать.

## Закон возрастающей сложности

> По мере развития системы ее сложность возрастает, если не предпринимаются меры по ее снижению.

Он говорит о том, что нельзя превращать команды разработчиков программного обеспечения в слепые фабрики по производству функций, нагромождающие все новые и новые функции в надежде, что в долгосрочной перспективе программа выживет.

Мы **должны** постоянно управлять сложностью системы по мере изменения знаний о нашей области.

## Рефакторинг

Существует _множество_ аспектов программной инженерии, которые обеспечивают гибкость программного обеспечения, например:

- Расширение прав и возможностей разработчиков
- В целом "хороший" код. Разумное разделение задач и т.д. и т.п.
- Коммуникативные навыки
- Архитектура
- Наблюдаемость
- Развертываемость
- Автоматизированные тесты
- Петли обратной связи

Я собираюсь сосредоточиться на рефакторинге. Это фраза, которую часто произносят: "Нам нужно рефакторить это", - и которую не задумываясь произносят разработчики в первый день программирования.

Откуда взялась эта фраза? Чем рефакторинг отличается от написания кода?

Я знаю, что я и многие другие думали, что занимаемся рефакторингом, но ошибались.

[Мартин Фаулер описывает, как люди ошибаются](https://martinfowler.com/bliki/RefactoringMalapropism.html)

> Однако термин "рефакторинг" часто используется не по назначению. Если кто-то говорит о том, что во время рефакторинга система будет сломана на пару дней, можно быть уверенным, что он не занимается рефакторингом.

Так что же это такое?

### Факторизация

При изучении математики в школе вы, вероятно, узнали о факторизации. Вот очень простой пример

Вычислить `1/2 + 1/4`

Для этого необходимо произвести _факторизацию_ знаменателей, превратив выражение в

`2/4 + 1/4`, которое затем можно превратить в `3/4`.

Из этого можно извлечь несколько важных уроков. При _факторизации выражения_ мы **не изменили его значения**. Оба выражения равны `3/4`, но нам стало проще с ними работать; изменив `1/2` на `2/4`, мы легче вписываем их в нашу "область".

Когда вы рефакторизуете свой код, вы пытаетесь найти способы сделать его более понятным и "вписать" в ваше текущее понимание того, что должна делать система. Очень важно, что при этом **не следует изменять поведение**.

**Пример на языке Go**

Вот функция, которая приветствует `name` на определенном `language`

```go
func Hello(name, language string) string {

  if language == "es" {
     return "Hola, " + name
  }

  if language == "fr" {
     return "Bonjour, " + name
  }

  // imagine dozens more languages

  return "Hello, " + name
}
```

Десятки операторов `if` - это не очень хорошо, и у нас есть дублирование конкатенации приветствия, специфичного для языка, с `, ` и `name`. Поэтому я рефакторю код.

```go
func Hello(name, language string) string {
  	return fmt.Sprintf(
  		"%s, %s",
  		greeting(language),
  		name,
  	)
}

var greetings = map[string]string {
  "es": "Hola",
  "fr": "Bonjour",
  //etc..
}

func greeting(language string) string {
  greeting, exists := greetings[language]

  if exists {
     return greeting
  }

  return "Hello"
}
```

Характер этого рефакторинга не так важен, важно то, что я не изменил поведение.

При рефакторинге можно делать все, что угодно: добавлять интерфейсы, новые типы, функции, методы и т.д. Единственное правило - не менять поведение.

### При рефакторинге кода вы не должны менять поведение

Это очень важно. Если вы одновременно изменяете поведение, вы делаете две вещи одновременно. Как инженеры-программисты мы учимся разбивать системы на различные файлы/пакеты/функции/и т.д., потому что знаем, что пытаться разобраться в большой куче вещей очень сложно.

Мы не хотим думать о множестве вещей одновременно, потому что в этом случае мы совершаем ошибки. Я был свидетелем того, как многие начинания по рефакторингу проваливались из-за того, что разработчики откусывали больше, чем могли прожевать.

Когда я выполнял факторизацию на уроках математики с помощью ручки и бумаги, мне приходилось вручную проверять, не изменил ли я смысл выражений в своей голове. Как узнать, что мы не меняем поведение при рефакторинге, когда работаем с кодом, особенно в нетривиальной системе?

Те, кто предпочитает не писать тесты, обычно полагаются на ручное тестирование. Для любого, кроме небольшого проекта, это будет огромной потерей времени и не позволит масштабировать систему в долгосрочной перспективе.

**Для безопасного рефакторинга необходимы модульные тесты**, поскольку они обеспечивают

- Уверенность в том, что вы можете изменить код, не беспокоясь об изменении поведения
- Документацию для людей о том, как должна вести себя система
- Гораздо более быструю и надежную обратную связь, чем при ручном тестировании.

**Пример на языке Go**

Юнит-тест для нашей функции `Hello` может выглядеть следующим образом

```go
func TestHello(t *testing.T) {
  got := Hello("Chris", es)
  want := "Hola, Chris"

  if got != want {
     t.Errorf("got %q want %q", got, want)
  }
}
```

В командной строке я могу запустить `go test` и получить немедленную обратную связь о том, изменили ли мои усилия по рефакторингу поведение. На практике лучше всего освоить волшебную кнопку для запуска тестов в редакторе/IDE.

Вы хотите достичь состояния, когда вы делаете

- Небольшой рефакторинг
- Запустить тесты
- Повторить

И все это в рамках очень жесткой обратной связи, чтобы не заблудиться в кроличьих норах и не наделать ошибок.

Наличие проекта, в котором все ключевые модели поведения протестированы и дают обратную связь менее чем за секунду, является очень мощной защитной сеткой, позволяющей проводить смелый рефакторинг, когда это необходимо. Это помогает нам справиться с наступающей силой сложности, которую описывает Леман.

## Если модульные тесты так хороши, то почему иногда возникает сопротивление их написанию?

С одной стороны, есть люди (например, я), которые говорят, что модульные тесты важны для долгосрочного здоровья системы, поскольку они позволяют уверенно продолжать рефакторинг.

С другой стороны, есть люди, которые рассказывают о том, что модульные тесты _мешают_ рефакторингу.

Спросите себя, как часто вам приходится менять тесты при рефакторинге? За годы работы я участвовал во многих проектах с очень хорошим тестовым покрытием, но инженеры не хотят заниматься рефакторингом из-за предполагаемых усилий по изменению тестов.

Это прямо противоположно тому, что нам обещают!

### Почему так происходит?

Представьте, что вас попросили разработать квадрат, и мы решили, что лучший способ добиться этого - склеить два треугольника.

![Два прямоугольных треугольника образуют квадрат](./assets/ela7SVf.jpeg)

Мы пишем наши модульные тесты вокруг квадрата, чтобы убедиться, что стороны равны, а затем пишем тесты вокруг наших треугольников. Мы хотим убедиться, что наши треугольники отображаются корректно, поэтому мы утверждаем, что углы в сумме составляют 180 градусов, возможно, проверяем, что у нас их два, и т.д. и т.п. Покрытие тестами очень важно, а написать эти тесты довольно просто, так почему бы и нет?

Несколько недель спустя закон непрерывных изменений поражает нашу систему, и новый разработчик вносит некоторые изменения. Теперь он считает, что было бы лучше, если бы квадраты образовывались из двух прямоугольников, а не из двух треугольников.

![Два прямоугольника образуют квадрат](./assets/1G6rYqD.jpeg)

Он пытается выполнить этот рефактор и получает неоднозначные сигналы от ряда неудачных тестов. Действительно ли он нарушил здесь важное поведение? Теперь ему приходится копаться в этих тестах с треугольниками и пытаться понять, что происходит.

_На самом деле не так уж важно, что квадрат был образован из треугольников_, но **наши тесты ложно возвысили важность деталей реализации**.

## Отдавайте предпочтение тестированию поведения, а не деталей реализации

Когда я слышу жалобы на модульные тесты, это часто связано с тем, что тесты находятся на неправильном уровне абстракции. Они проверяют детали реализации, чрезмерно следят за взаимодействующими сторонами и слишком много имитируют.

Я считаю, что это происходит из-за непонимания того, что такое модульные тесты, и погони за суетными показателями (тестовое покрытие).

Если я говорю, что нужно тестировать только поведение, то не следует ли нам писать только системные/черно-ящичные тесты? Такие тесты действительно имеют большую ценность с точки зрения проверки ключевых действий пользователя, но они, как правило, дороги в написании и медленны в выполнении. По этой причине они не слишком полезны для _рефакторинга_, поскольку петля обратной связи работает медленно. Кроме того, по сравнению с модульными тестами, тесты "черного ящика" не слишком помогают в поиске первопричин.

Так каков же правильный уровень абстракции?

## Написание эффективных модульных тестов - это проблема проектирования

Если на время забыть о тестах, то желательно иметь внутри системы автономные, разрозненные "блоки", сосредоточенные вокруг ключевых понятий в вашей области.

Мне нравится представлять себе эти блоки как простые кирпичики Lego, имеющие согласованные API, которые я могу объединять с другими кирпичиками для создания более крупных систем. Под этими API могут быть десятки вещей (типов, функций и т.д.), взаимодействующих между собой, чтобы заставить их работать так, как нужно.

Например, если вы пишете банк на Go, у вас может быть пакет "account". Он будет представлять API, не раскрывающий деталей реализации и легко интегрируемый.

Если у вас есть такие модули, которые соответствуют этим свойствам, вы можете написать модульные тесты для их публичных API. _По определению_ эти тесты могут тестировать только полезное поведение. Под этими модулями я могу свободно рефакторить реализацию столько, сколько мне нужно, и тесты, по большей части, не должны мешать.

### Являются ли эти тесты модульными?

**ДА**. Модульные тесты направлены против "модулей", как я описал. Они никогда не были направлены только против одного класса/функции/чего-либо еще.

## Объединение этих концепций

Мы рассмотрели

- Рефакторинг
- Модульные тесты
- Проектирование модулей

Мы видим, что эти аспекты проектирования программного обеспечения усиливают друг друга.

### Рефакторинг

- Дает нам сигналы о состоянии наших модульных тестов. Если приходится проводить ручные проверки, значит, нужно больше тестов. Если тесты ошибочно не работают, значит, наши тесты находятся на неправильном уровне абстракции (или не имеют никакого значения и должны быть удалены).
- Помогает нам справляться со сложностями внутри и между модулями.

### Модульные тесты

- Обеспечивают безопасность при рефакторинге.
- Проверяют и документируют поведение наших модулей.

### (Хорошо спроектированные) блоки

- Легко писать _содержательные_ модульные тесты.
- Легко рефакторить.

Существует ли процесс, который поможет нам достичь точки, когда мы сможем постоянно рефакторить наш код, чтобы управлять сложностью и сохранять гибкость наших систем?

## Зачем нужна разработка, управляемая тестами (TDD)

Некоторые люди, возможно, воспримут цитаты Лемана о том, что программное обеспечение должно меняться, и будут слишком много думать над сложными проектами, тратить много времени, пытаясь создать "идеальную" расширяемую систему, а в итоге все будет неправильно и ни к чему не приведет.

Это старые добрые времена программного обеспечения, когда команда аналитиков тратила 6 месяцев на написание документа с требованиями, а команда архитекторов - еще 6 месяцев на разработку дизайна, а через несколько лет весь проект проваливался.

Я говорю "старые добрые времена", но это все еще происходит!

Agile учит нас, что мы должны работать итеративно, начиная с малого и развивая программное обеспечение, чтобы мы получали быструю обратную связь о дизайне нашего программного обеспечения и о том, как оно работает с реальными пользователями; TDD реализует этот подход.

TDD учитывает законы, о которых говорит Леман, и другие уроки, которые трудно извлечь из истории, поощряя методологию постоянного рефакторинга и итеративной доставки.

### Маленькие шаги

- Напишите небольшой тест для небольшого количества желаемого поведения
- Проверьте, что тест не работает с явной ошибкой (красный цвет)
- Напишите минимальное количество кода, чтобы тест прошел (зеленый)
- Рефакторинг
- Повторить

По мере освоения такой способ работы станет естественным и быстрым.

Вы станете ожидать, что этот цикл обратной связи не займет много времени, и будете испытывать беспокойство, если окажетесь в состоянии, когда система не "зеленая", поскольку это указывает на то, что вы, возможно, находитесь в кроличьей норе.

Вы всегда будете продвигать небольшую и полезную функциональность, комфортно подкрепленную обратной связью от ваших тестов.

## Подведение итогов

- Сила программного обеспечения в том, что мы можем его менять. Большинство программ со временем потребует непредсказуемых изменений, но не стоит пытаться переборщить с проектированием, поскольку предсказать будущее слишком сложно.
- Вместо этого мы должны сделать так, чтобы наше программное обеспечение оставалось податливым. Для того чтобы изменить программное обеспечение, мы должны рефакторить его по мере развития, иначе оно превратится в беспорядок.
- Хороший набор тестов может помочь вам рефакторить быстрее и с меньшим стрессом.
- Написание хороших модульных тестов - это проблема дизайна, поэтому подумайте о том, как структурировать свой код, чтобы у вас были осмысленные блоки, которые вы можете соединить вместе, как кирпичики Lego.
- TDD может помочь в этом и заставить вас разрабатывать хорошо структурированное программное обеспечение итеративно, подкрепляя его тестами, чтобы помочь будущей работе по мере ее появления.

---

Мои пять копеек. Go позволяет одинаково именовать пакет в файлах с кодом модуля и с тестами, тогда у нас есть доступ к внутренней реализации модуля (**императив**), а не только к API модуля (**декларатив**). В статье нас призывают к TDD, при этом не спускаясь на императивный уровень, а формулируя тесты перед кодингом только на декларативном уровне. Но при рефакторинге я предпочёл бы иметь покрытие на императивном уровне. В наше время этого легко добиться с помощью ChatGPT. Тогда такие императивные модульные тесты не жалко выбрасывать вместе с модифицируемым кодом, если потребуется. Хорошо. А как бы улучшить Developer Experience для декларативных модульных тестов? Сплю и вижу процесс разработки по схеме: [Event Modeling + BDD](https://eventmodeling.org/posts/what-is-event-modeling/blueprint_large.jpg) > Integration/Unit Tests (via [gherkingen](https://github.com/hedhyw/gherkingen)) > tests-first development for external API of modules > Unit Tests Coverage for internal functions in modules (via ChatGPT).
