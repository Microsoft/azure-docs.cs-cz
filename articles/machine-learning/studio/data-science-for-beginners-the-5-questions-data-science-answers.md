---
Název: Datová věda pro začátečníky titleSuffix: Azure Machine Learning Studio Popis: Vědecké zpracování dat pro začátečníky je vás naučí základní koncepce při 5 krátkých videí, počínaje The 5 odpovídá vědecké zkoumání dat pro dotazy. Ze služby Azure Machine Learning.
Services: machine learningu ms.service: ms.component strojového učení: studio ms.topic: článku

Autor: garyericson ms.author: garye ms.custom: seodec18 ms.date: 01/03/2018
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Datová věda pro začátečníky – video 1: 5 otázky, které odpovídá vědecké zkoumání dat
Rychlý úvod do datových věd z *datová věda pro začátečníky* v pěti krátkých videích z hlavní vědec. Tato videa jsou užitečné, ale základní, ať už vás zajímá datových věd nebo pracujete s odborníky přes data.

Je toto první video o druzích otázek, které dokáže odpovědět pro datové vědy. Využijte naplno řady, můžete sledujte všechny. [Přejít na seznam videí](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>Další videa v této sérii
*Datová věda pro začátečníky* článek obsahuje rychlý úvod do data science trvá přibližně 25 minut celkový počet. Prostudujte si všechna videa pět:

* Video 1: 5 otázky, které odpovídá vědecké zkoumání dat
* Video 2: [Jsou vaše data připravená pro vědecké zpracování dat?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 sek)*
* Video 3: [Položit dotaz lze odpovědět pomocí dat](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sek)*
* Video 4: [Předpovídání odpovědi pomocí jednoduchého modelu](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 sek)*
* Video 5: [Kopírování práce jiných lidí pro vědecké zkoumání dat](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sek)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Přepisu: 5 otázky, které odpovídá vědecké zkoumání dat
Ahoj! Vítá vás série videí *datová věda pro začátečníky*.

Vědecké zpracování dat může být vám nemusí nahánět strach, tak I budete zavádět základy zde bez jakýchkoli rovnice nebo počítač programování žargonu.

V tomto videu první budeme mluvit o "5 otázky, které odpovídá vědecké zkoumání dat."

Vědecké zpracování dat používá pro předpověď odpovědi na otázky názvy (označované také jako kategorií nebo popisky) a čísla.

To může překvapí vás, ale *nějaké otázky, zobrazuje se jenom pět této odpovídá vědecké zkoumání dat*:

* Je tento A nebo B?
* Je to divné?
* Kolik – nebo – kolik?
* Toto uspořádání?
* Co mám dělat další?

Jedna z těchto otázek je zodpoví samostatné řady metod machine learning, neboli algoritmy.

Je vhodné uvažovat o algoritmu jako recept a vaše data způsobem složek. Algoritmus, který určuje, jak zkombinovat a kombinovat data, aby bylo možné získat odpověď. Počítače jsou podobné blender. Většina těžkou práci algoritmu dělají za vás a dělají to hodně rychlé.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Otázka 1: Je tento A nebo B? používá klasifikaci algoritmy
Začněme na otázku: Je tento A nebo B?

![Klasifikace algoritmy: Je tento A nebo B?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

Tato řada algoritmy se nazývá dvěma třídami klasifikace.

To je užitečné pro libovolný dotaz, který má jenom dvě možné odpovědi.

Příklad:

* Selže v dalším 1 000 mil tento můžete zadat: Ano nebo ne?
* Což přináší větší počet zákazníků: 5 USD kupónu nebo 25 % slevu?

Tento dotaz můžete také rephrased zahrnout více než dvě možnosti: Je tento A nebo B nebo C nebo D, atd.?  Tento postup se nazývá klasifikace víc tříd a je užitečné, pokud máte několik, nebo několik tisíc – je to možné odpovědi. Klasifikace víc tříd vybere ten největší pravděpodobností.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>Otázka 2: Je to divné? používá algoritmy detekce anomálií
Další otázky, na které můžete odpovědět pro datové vědy je: Je to divné? Tento dotaz zodpovězen podle řady algoritmů volá detekce anomálií.

![Algoritmy detekce anomálií: Je to divné?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

Pokud máte platební kartu, jste využívali již detekce anomálií. Vaše společnost platební karty analyzuje vaše postupy nákupní tak, aby se vám upozornění na možný podvod. Náklady, které jsou "divné" může být nákup v úložišti, kde není nakupování obvykle nebo nákupu neobvykle nákladných položky.

Tento dotaz může být užitečné v mnoha způsoby. Příklad:

* Pokud máte automobilu s měřidla přetížení, můžete chtít vědět: Při čtení tohoto měřidla tlak normální?
* Pokud monitorujete Internetu, byste měli vědět: Tato zpráva je z Internetu typické?

Detekce anomálií příznaky neočekávaná nebo neobvyklá událostí nebo chování. Poskytuje příčiny, kde hledat problémů.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>Otázka 3: Kolik? nebo jak mnoho? používá algoritmy regrese
Machine learning můžete také předpověď odpovědi jak mnohem? nebo jak mnoho? Algoritmus skupina, která odpovídá na tuto otázku se nazývá regrese.

![Regrese algoritmy: Kolik? nebo jak mnoho?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

Regrese, algoritmy predikci číselné, jako například:

* Jaké teplota budou další úterý?  
* Co bude svoje prodeje na čtvrté čtvrtletí

Pomáhají odpovědět na každou otázku, která vyzve k zadání čísla.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>Otázka 4: Toto uspořádání? použití clusteringu s algoritmy
Poslední dva dotazy jsou nyní bit rozšířené.

Někdy chcete podrobněji seznámit se strukturou datové sady – toto uspořádání? Pro tento dotaz nemáte příklady, které už znáte výsledky.

Existuje mnoho způsobů, jak tease struktury data. Jedním z přístupů je clustering. Rozděluje data do fyzické "seskupí," pro snazší výkladu. S clusteringem, neexistuje žádná správná odpověď jeden.

![Clustering algoritmy: Toto uspořádání?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

Mezi běžné příklady clusteringu dotazy jsou:

* Které prohlížeče, jako jsou stejné typy filmy?
* Které modely tiskáren selhání stejným způsobem?

Když porozumíte tomu, jakým způsobem je organizována data, můžete lépe pochopit – a předpovídat - chování a události.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>Otázka 5: Co mám teď? používá učení se supervizí zpětnovazebnému
Otázka – poslední, co mám dělat, nyní? – používá řadu algoritmy volá zpětnovazebnému učení.

Zpětnovazebnému učení se inspirovat jak mozky potkanů a lidé reagují na smrti a odměny. Tyto algoritmy Učte se od výsledky a rozhodnout o další akci.

Zpětnovazebnému učení je obvykle vhodné pro automatizované systémy, které se musí provést velké množství malých rozhodnutí bez lidské pokyny.

![Algoritmy zpětnovazební učení: Co mám dělat další?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

Otázky, které odpovídá jsou vždy o jaká akce se má vzít – obvykle na počítači nebo robot. Můžete například:

* Pokud jsem systému řízení teploty domu: Upravit teplota nebo nechat tam, kde je?  
* Pokud jsem samořídicí automobilu: Žlutý indikátor brzdových nebo zrychlení?  
* Pro takový robot: Zachovat vacuuming nebo přejděte zpět na nabíjení stanice?

Algoritmy zpětnovazební učení shromažďování dat, jako jsou go, učit se z omyl a.

Tak je to – 5 otázek datové vědy můžete odpovědět.

## <a name="next-steps"></a>Další postup
* [Zkuste prvního experimentu vědy data s Machine Learning Studio](create-experiment.md)
* [Úvod do strojového učení v Microsoft Azure](what-is-machine-learning.md)
