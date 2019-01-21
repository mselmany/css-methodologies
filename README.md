# Yaygın kullanılan CSS Metodolojileri ve Araçları

1. Araçlar

   - Postcss

2. Metodolojiler
   - BEM
   - SMACSS
   - OOCSS
   - Bize özel (sonra, demo ile gösterilecek...)

# Araçlar

## [Postcss](https://postcss.org/)

CSS'i pluginler yardımıyla istediğin syntax/formattan tarayıcının anlayacağı formata dönüştürülmesinde kullanılıyor.SASS gibi ama API ve plugin desteğiyle uçuk kaçık işler yapılabiliyor.Yeni nesil CSS'i, tarayıcı desteği sorunu olmadan kullanabiliyoruz. Broccoli desteği var.

Biz kullanırsak;

- [postcss-preset-env](https://github.com/csstools/postcss-preset-env) ile CSS4 ortamını kullanırız; CSS'in W3C tarafından onaylanmış [yeni özelliklerini](https://cssdb.org/) şimdiden tüm tarayıcılarda kullanmak için geliştirilen paket plugin. Eğer tarayıcı yeni özelliği desteklemiyorsa fallback koyuyor, [iç-içe kurallar](https://preset-env.cssdb.org/features#nesting-rules) ve [css değişkenleri](https://preset-env.cssdb.org/features#custom-properties) önemli (profile özel css configlerinde esneklik sağlayacak)

- [css-modules](https://github.com/css-modules/css-modules) ile CSS'in C'sinden kaçınma (Cascade/Inheriting); Bir widgetın css'i diğer widgetlara etki edemeyecek, widgetta korkmadan css değişikliği yapmamızı sağlayacak. Stil conflictlerinden kaçınmamızı sağlar. `.symbol-search` olarak girer `.symbol-search_r43e7` olarak çıkar, dolayısıyla bize hash'li classların olduğu json dosyası verir. Templatelerimizdeki classları bunlarla [build ederken](https://stackoverflow.com/questions/45974263/better-way-of-applying-css-modules-to-angularjs/45985719#45985719) eşleştirmemiz(map) gerekecek ya da [runtime'da](https://github.com/css-modules/css-modules/blob/master/docs/css-modules-with-angular.js.md) vm.css'e atar one-way kullanırız

- [autoprefixer](https://github.com/postcss/autoprefixer) - tarayıcı bazlı önek kullanmamıza gerek kalmıyor. (Örn. -webkit-, -ms-, -moz- lara gerek yok, kendi otomtk koyuyor)

# Metodolojiler

## [BEM - Block, Element, Modifier (block\_\_element_modifier)](https://en.bem.info/methodology/css/)

Scale ve Reusability kolaylığı sağlar, genel olarak bu veya bundan türemiş yaklaşımlar benimsenir. Zamanında bende kullandım ama Vuejs deki scope/css-module ile gerek kalmadı. Framework kullanmayanlarlar için birebir.

- Kendine has isimlendirme kuralı vardır, class adıyla o classın ne işe yaradığı direk anlaşılır,
- **Block**: Component/Widget'a denk gelir (Örn: `symbol-search`),
- **Element**: _Block_'a aittir. Widget içinde fonksiyonu olan herhangi katmanı ifade eder. Block ve Element arasında `__` (2 alt tire) bulunur (Örn: symbol-search içindeki input: `symbol-search__input` veya sembol listesi: `symbol-search__list` gibi),
- **Modifier**: _Block_'a aittir. Widget'ın nasıl görüneceğini (davranış, state vs.) belirtir. Block/Element le arasında `_` (tek alt tire) bulunur (Örn: `symbol-search_fullwidth`, `symbol-search__list_compact`, `symbol-search_compact` gibi),
- Sadece `class` larla yazılır,
- İç içe class yazımını önermez (CSS specificity ile ilgili)
- css-modules'in manual hali olarak düşünebiliriz
- Kendisi dışında hiçbirşeye bağlı değildir.
- BEM'de componenti htmlde istediğin yerde istediğin kadar koy aynı görünür, dışardan herhangi kalıtım almaz(kural/reusability).
- İç içe component kullanılabilir.(`button` componenti < `symbol-search` componenti içinde kullanılabilir.)
- CSS specificity(baskınlık) yönetiminde kolaylık sağlar (yani !important kullanmayız, böylelikle CSS'i rahatça scale edebiliriz.)

Kod örneği;

```css
/*symbol-search componenti*/
.symbol-search {
} /*block*/
.symbol-search__input {
} /*element*/
.symbol-search__button-container {
} /*element*/
.symbol-search_compact {
} /*modifier*/

/*button componenti*/
.button {
} /*block*/
.button_full-width {
} /*modifier*/
.button_ligth {
} /*modifier*/
```

```html
<div class="symbol-search symbol-search_compact">
  <!-- symbol-search componenti -->
  <input type="text" class="symbol-search__input" />
  <div class="symbol-search__button-container">
    <button class="button button_full-width button_ligth">
      <!-- button componenti -->
    </button>
  </div>
</div>
```

## [SMACSS - Scable and Modular Architecture](https://smacss.com/)

CSS'ini base/general, layout, module, state, theme/skin bağlamında yazmanı dayatır, yani kategorilendirir.

- Dosyalama yapısı(file structre) ister,
- **base.css**: css resetleri, tüm projelik tanımlar,
- **layout**: component yerleştirmede, yani `position: absolute`, `width: 100px` vs. gibi componentin nerede olacağını belirten tanımlamalar bu dosyada tanımlanır. Modülleri bir araya getirip yeni Modül oluşuturulan yerdir.
- **module**: bir veya daha fazla componentin birleşiminden oluşur, değişkenler dışında asıl css burda yazılır,
- **state**: genelde javascript ile eklenen classlar içindir (Örn: `.is-selected` gibi)
- **theme**: `color: red; background-color: blue` gibi neye benzediğini belirten tanımlamalar burda halledilir.

## OOCSS

- Yapıyı(Structure) ve Temalandırmayı(Skin/Theme) ayrı classlarda ele alır.Yani nerde olacağı ve nasıl görüneceği ayrı classlarda belirtilmeli (Örn: `width` ve `background-color` aynı class içinde tanımlanmamalı)
- Kapsayıcı(Container) ve İçerik(Content) yine ayrı classlarda ele alınır (yazar burda iç içe class yazmamaya çalış diyor)
- İsimlendirme dayatmaz, BEM tarzı kullanılabilir,
- HTML'e bakarak CSS yazma diyor (yine CSS specificity ile ilgili)

## Bize özel

...
