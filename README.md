### Lazy loading
A képekre először tennék `loading="lazy"` attribútumot. Ezzel a kép csak akkor töltődik le a felhasználó eszközére amint a közelébe görget. Viszont előfordulhat, hogy a kép hamarabb letöltődik mint ahogy a szeme odaér, így ha szeretnénk egy szép animációval "fade-in"-elni a képet akkor használhatunk `InterserctionObservert` az alábbi módon:

(Ez konkrétan egy ReactJS-es megoldás de natív JavaScriptben is hasonló)
```Javascript
useEffect(() => {
    const gallerySlider = document.getElementById('gallery-slider');
    const observer = new IntersectionObserver(
      ([entry]) => {
        // funkció helye
        // pl. itt állíthatunk a képnek egy osztályt amivel animálhatjuk a megjelenését
      },
      { threshold: 0.1 }, // ha a megadott elem 10%-a látható akkor lefut
    );

    // IntersectionObserver rákötése az elemre
    if (gallerySlider) {
      observer.observe(gallerySlider);
    }

    // Leirítkozás a megfigyelésről, hogy ne pazaroljunk erőforrást ha már nincs rá szükség
    return () => {
      if (gallerySlider) {
        observer.unobserve(gallerySlider);
      }
    };
  }, []);
```

### CLS
A tartalomugrást (Content Layout Shift vagy CLS) úgy lehet elkerülni, hogy megadjuk a képnek a szélességét és magasságát, hogy lefoglalja a helyét a DOM-ban (Document Object Model). Vagy ennek hiányában a befogadó szülő egységnek adhatunk meg egy fix magasságot, viszont ekkor ügyelni kell arra, hogy a különböző képernyőméreteken más magasságot adjunk meg, mivel a képek új sorba törhetnek, növelve a szülő magasságát.

### Placeholder
A felhasználói élményt javítandó megadhatunk egy alapértelmezett foglaló képet (placeholder), ami látható addig, amíg a kép nem töltődik le (vagy rosszabb esetben nem tud betöltődni valami hiba miatt), hogy a felhasználó ne maradjon tartalom nélkül, és ne bontsa meg az oldal összképét egy hiányzó kép. Vagy ha használunk kész UI keretrendszert, akkor egy úgynevezett skeleton komponenssel (egyszerű, átlátszó, animált dobozok, helyfoglalók) mutathatjuk a felhasználónak, hogy itt képes tartalom lesz, amint minden betöltődik.

### Optimalizálás
Ezen felül, ha nem konkrét képmegosztó oldalról beszélünk akkor optimalizálnám a képformátumot, lehetőleg .webp-re, illetve a méreteket is, hogy csak olyan méretű kép töltődjön le amit a felhasználó eszköze garantál. Például felesleges 4K-s hero képet betölteni ha a felhasználó telefonról jön, aminek a szélessége mondjuk 720 pixel. Képfeltöltéskor generálni kell különböző eszközméretekre képeket és az `srcset` és `sizes` attribútomok segítségével betölteni a megfelelő képet. `srcset`: Felsoroljuk a képeket és azok valós szélességét pixelben (a w betű jelzi a szélességet). `sizes`: Megmondjuk a böngészőnek, hogy a különböző képernyőszélességeknél (media query-k) mekkora helyet fog elfoglalni a kép a dizájnban.

### Példa
A teljes példa pedig így nézne ki:
```html
<div class="section-row">
  <div class="lazy-container">
    <img
      srcset="kis-kep.jpg 500w,
        kozepes-kep.jpg 1000w,
        nagy-kep.jpg 2000w"
      sizes="(max-width: 600px) 480px,
        (max-width: 1200px) 800px,
        1200px"
      alt="Network4 tartalom"
      loading="lazy"
      class="lazy-image"
      width="1200"
      height="600"
    />
  </div>
</div>
```
