Bing podporuje podle zvýraznění, který označuje vyhledávacích dotazů (nebo jiné podmínky této Bing najde relevantní) v řetězců zobrazení některých odpovědi. Například z webové stránky `name`, `displayUrl`, a `snippet` pole označit podmínky dotazu.

Ve výchozím nastavení Bing neobsahuje zvýraznění značek v řetězců zobrazení. Chcete-li zahrnout značek, zahrňte `textDecorations` parametr ve vaší žádosti dotazu a nastavte ji na **true**. Bing označí vyhledávacích dotazů pomocí znaků E000 a E001 Unicode označit začátek a konec platnosti. Například pokud termín dotazu je řízení ledě a v poli existuje buď termín, termín ohraničeno přístupů zvýraznění znaků jak je znázorněno v následujícím příkladu:  
  
![Zvýrazňování položek](./media/cognitive-services-bing-hit-highlighting/bing-hit-highlighting.PNG) 

Před zobrazením řetězec v uživatelském rozhraní, by znaky znakové sady Unicode nahraďte znaky, které jsou vhodné pro vaše formát zobrazení. Například, chcete-li zobrazit text ve formátu HTML, může zvýraznění termín dotazu nahrazením E000 s < b\> a E001 s < /b\>. Pokud nechcete použít formátování, odeberte z řetězce značek. 

Bing poskytuje možnost pomocí znaků Unicode nebo značky jazyka HTML jako značky. Chcete-li určit, které značky používat, zahrňte `textFormat` parametr dotazu. Chcete-li označit obsah s znaky znakové sady Unicode, nastavte `textFormat` Raw (výchozí) a označit obsah s značky HTML, nastavte `textFormat` do formátu HTML. 
  
Pokud `textDecorations` je **true**, Bing může zahrnovat následující značky v zobrazované řetězce odpovědi. Pokud žádný ekvivalent HTML, buněk tabulky HTML je prázdný.

|Kódování Unicode|HTML|Popis
|-|-|-
|U + E000|\<b >|Označuje začátek termín dotazu (přístupů zvýraznění)
|U + E001|\</b >|Označuje konec termín dotazu
|U + E002|\<Můžu >|Označuje začátek kurzívou obsahu 
|U + E003|\</i >|Označuje konec kurzívou obsahu
|U + E004|\<br / >|Označuje konec řádku
|U + E005||Označuje začátek telefonní číslo
|U + E006||Označuje konec telefonní číslo
|U + E007||Označuje začátek adresu
|U + E008||Označuje konec adresu
|U + E009|\&nbsp;|Označí pevné mezery
|U + E00C|\<strong >|Označuje začátek tučné obsahu
|U + E00D|\</ strong >|Označuje konec tučné obsahu
|U + E00E||Označuje začátek obsahu, jehož pozadí by měl být písmo, než jeho okolního pozadí
|U + E00F||Označuje konec obsahu, jehož pozadí by měl být písmo, než jeho okolního pozadí
|U + E010||Označuje začátek obsahu, jehož pozadí by měl být tmavší než jeho okolního pozadí
|U + E011||Označuje konec obsahu, jehož pozadí by měl být tmavší než jeho okolního pozadí
|U + E012|\<del >|Označuje začátek obsah, který by měl být i prostřednictvím
|U + E013|\</ del >|Označuje konec obsahu, který by měl být i prostřednictvím
|U + E016|\<Sub >|Označuje začátek dolního indexu obsahu
|U + E017|\</ sub >|Označuje konec obsahu dolního indexu
|U + E018|\<sup >|Označuje začátek superscript obsahu
|U + E019|\</ sup >|Označuje konec superscript obsahu

Následující příklad ukazuje `Computation` odpovědí, který obsahuje dolního indexu značek pro termín dotazu, log(2). `expression` Pole obsahuje jenom Pokud značek `textDecoration` je **true**.

![Výpočet značek](./media/cognitive-services-bing-hit-highlighting/bing-markers-computation.PNG) 

Pokud požadavek nepožádali dekorace, bude výraz log10(2). 
  
