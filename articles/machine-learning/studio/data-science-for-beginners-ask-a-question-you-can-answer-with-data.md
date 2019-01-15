---
Název: Položte že otázku dat dokáže odpovědět titleSuffix: Azure Machine Learning Studio Popis: Zjistěte, jak formulovat otázku sharp data science v datová věda pro začátečníky videa 3. Obsahuje porovnání klasifikačních a regresních dotazy.
Services: machine learningu ms.service: ms.component strojového učení: studio ms.topic: článku

Autor: garyericson ms.author: garye ms.custom: předchozí Autor = heatherbshapiro předchozí ms.author=hshapiro ms.date: 01/03/2018
---
# <a name="ask-a-question-you-can-answer-with-data"></a>Položení otázky, na kterou lze odpovědět pomocí dat
## <a name="video-3-data-science-for-beginners-series"></a>Video 3: Datová věda pro začátečníky řady
Zjistěte, jak na otázky v datová věda pro začátečníky videa 3 formulovat problém datové vědy. Toto video obsahuje porovnání otázek klasifikačních a regresních algoritmů.

Využijte naplno řady, můžete sledujte všechny. [Přejít na seznam videí](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>Další videa v této sérii
*Datová věda pro začátečníky* článek obsahuje rychlý úvod do datových věd v pěti krátkých videích.

* Video 1: [5 otázky, které odpovídá vědecké zkoumání dat](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 sek)*
* Video 2: [Jsou vaše data připravená pro vědecké zpracování dat?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 sek)*
* Video 3: Položení otázky, na kterou lze odpovědět pomocí dat
* Video 4: [Předpovídání odpovědi pomocí jednoduchého modelu](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 sek)*
* Video 5: [Kopírování práce jiných lidí pro vědecké zkoumání dat](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sek)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Přepisu: Položení otázky, na kterou lze odpovědět pomocí dat
Vítá vás třetí videa v této sérii "Datová věda pro začátečníky."  

Do tohoto objektu za získáte některé tipy pro formulování otázky, na kterou lze odpovědět pomocí dat.

Může se zobrazit informace z tohoto videa, pokud nejprve sledovat dvě starší videa v této sérii: "5 otázek datové vědy můžete odpovědět na" a "Je vaše data připravená pro vědecké zpracování dat?"

## <a name="ask-a-sharp-question"></a>Zeptejte se sharp
Už jsme mluvili o tom, jak pro datové vědy je proces použití názvů (také nazývané kategorií nebo popisky) a čísel pro předpověď odpovědi na dotaz. Ale nemůže být jen na každou otázku; je třeba *sharp otázku.*

Vágní dotaz nemusí jako odpověď získat název nebo číslo. Musí být sharp otázku.

Představte si, že jste našli magic lamp s genie, který bude pravdivě odpovědět na každou otázku, kterou požadujete. Ale je mischievous genie a že budete akci k jeho odpovědí vágní a matoucí jak si můžete okamžitě začít s. Chcete připnout mu dotazu vzduchotěsným tak, že mu nemůže pomoct ale říct, co potřebujete vědět.

Pokud byste chtěli položit vágní dotaz, jako je "Co se to stát se Moje akcie?", může odpovědět genie, "se změní cena". Který je truthful odpověď, ale není velmi užitečné.

Ale pokud byste chtěli položit otázku sharp, jako je "Co Moje akcie prodejní cena bude příští týden?", nelze genie pomoci ale poskytují konkrétní odpovědět a předpovědět cenu prodej.

## <a name="examples-of-your-answer-target-data"></a>Příklady odpovědi: Cíl dat
Jakmile jste formulovat otázku, zaškrtněte, pokud chcete zjistit, jestli máte příklady odpovědi ve vašich datech.

Pokud je naše otázku "Co Moje akcie prodejní cena bude příští týden?" potom máme Ujistěte se, že naše data obsahují historii minimální cenu akcie.

Pokud je naše otázku "které car v mé fleet bude selhání nejprve?" potom máme Ujistěte se, že naše data zahrnují informace o předchozích chybách.

![Cíl dat – příklady odpověď. Formulovat otázku datové vědy.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/target-data.png)

Tyto příklady odpovědi se označují jako cíl. Cílem je, co se pokoušíme předpovídat budoucí datové body, ať už jde o kategorii nebo číslo.

Pokud nemáte k dispozici žádná data target, budete muset získat některé. Nebude moct vaši otázku odpověděl bez něj.

## <a name="reformulate-your-question"></a>Byla znovu formulována svůj dotaz
Někdy může změna znění váš dotaz, chcete-li získat odpovědi na další užitečné.

Na otázku "Je tento datový bod A nebo B?" předpovídá kategorie (nebo název nebo popisek) určitého objektu. Přijměte hovor, použijeme *klasifikační algoritmus*.

Na otázku "Kolik?" nebo "Kolik?" předvídá množství. Přijměte hovor, použijeme *regresní algoritmus*.

Pokud chcete zobrazit, jak můžete transformujeme tyto, Podívejme se na otázku "které text zprávy je nejzajímavějších tento čtečku?" Požádá predikcí jeden podle výběru z mnoha možností – jinými slovy "Je tento A nebo B nebo C nebo D?" - a využije klasifikační algoritmus.

Ale tento dotaz může být jednodušší zodpovědět, pokud Změna znění jako "jak zajímavé je každý scénář v tomto seznamu prohlížeč?" Nyní můžete poskytnout každý článek v číselné skóre a potom je snadno rozpoznat článek nejvyšší hodnocení. Jedná se naformulovat jinak klasifikace otázku do pole pro otázku regrese nebo kolik?

![Byla znovu formulována svůj dotaz. Klasifikace otázka vs. regrese otázku.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/classification-question-vs-regression-question.png)

Jak položit otázku je vodítko, které algoritmus vám může poskytnout odpověď.

Zjistíte, že jsou určité druhy algoritmy – stejně jako ty v našem příkladu textu zprávy – úzce souvisí. Byla znovu formulována váš dotaz použít algoritmus, který poskytuje nejužitečnější odpovědí.

Ale nejdůležitější položit otázku, kterou lze odpovědět pomocí dat tohoto sharp dotaz -. A ujistěte se, že máte správné dat pro potřeby odpovědí jej.

Už jsme mluvili o některé základní principy položení dotazu, že vám pomůže odpovědět s daty.

Nezapomeňte se podívat na další videa v "Datové vědě pro začátečníky" ze služby Microsoft Azure Machine Learning.

## <a name="next-steps"></a>Další postup
* [Zkuste prvního experimentu vědy data s Machine Learning Studio](create-experiment.md)
* [Úvod do strojového učení v Microsoft Azure](what-is-machine-learning.md)
