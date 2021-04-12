---
title: Srovnávací testy výkonu
titleSuffix: Azure Cognitive Search
description: Přečtěte si o výkonu Azure Kognitivní hledání v různých srovnávacích testech výkonu.
author: dereklegenzoff
manager: luisca
ms.author: delegenz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 9f4473d6c8a584bf60e5c8fe2d69d6a56a55e71d
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108214"
---
# <a name="azure-cognitive-search-performance-benchmarks"></a>Srovnávací testy výkonu Azure Kognitivní hledání

Výkon Azure Kognitivní hledání závisí na [nejrůznějších faktorech](search-performance-tips.md) , včetně velikosti vyhledávací služby a typů dotazů, které posíláte. Abychom vám pomohli odhadnout velikost vyhledávací služby potřebné pro vaše zatížení, spustili jsme několik srovnávacích testů, které vám pomůžou zdokumentovat výkon různých služeb vyhledávání a konfigurací. Tyto srovnávací testy nijak nezaručují určitou úroveň výkonu vaší služby, ale můžou vám poskytnout představu o výkonu, který můžete očekávat.

Pro pokrytí řady různých případů použití jsme spustili srovnávací testy pro dva hlavní scénáře:

* **Hledání elektronického obchodování** – tento srovnávací test emuluje scénář pro skutečný elektronický obchod a je založený na severské společnosti pro elektronické obchodování [CDON](https://cdon.com).
* **Hledání dokumentů** – tento scénář se skládá z hledání klíčových slov na základě úplných textových dokumentů ze [sémantické Scholar](http://s2-public-api-prod.us-west-2.elasticbeanstalk.com/corpus/download/). Tím se emuluje typické řešení hledání dokumentů.

I když tyto scénáře odrážejí různé případy použití, každý scénář je jiný, takže vždycky doporučujeme testování výkonu jednotlivých úloh. Publikovali jsme [řešení testování výkonu pomocí JMeter](https://github.com/Azure-Samples/azure-search-performance-testing) , takže můžete spouštět podobné testy na vlastní službě.

## <a name="testing-methodology"></a>Metodologie testování

Pro srovnávací testy výkonu Azure Kognitivní hledání jsme spustili testy dvou různých scénářů na různých úrovních a kombinací repliky/oddílu.

Pro vytvoření těchto srovnávacích testů se použila následující metodologie:

1. Test začíná na `X` dotazech za sekundu (QPS) po dobu 180 sekund. To bylo obvykle 5 nebo 10 QPS.
2. QPS se pak zvýšila `X` a běžela dalších 180 sekund.
3. Každých 180 sekund se test zvýšil o `X` QPS, dokud Průměrná latence zvýšená nad 1000 MS nebo menší než 99% dotazů bylo úspěšné.

Následující graf obsahuje vizuální příklad toho, jak vypadá zátěž testu jako:

![Příklad testu](./media/performance-benchmarks/example-test.png)

Každý scénář používal alespoň 10 000 jedinečných dotazů, aby se testy nemusely přenášet do mezipaměti.

> [!IMPORTANT]
> Tyto testy zahrnují pouze úlohy dotazů. Pokud očekáváte, že budete mít vysokou volumne operací indexování, nezapomeňte zvážit, zda se má provést testování odhadu a výkonu. Vzorový kód pro simulaci indexování najdete v tomto [kurzu](tutorial-optimize-indexing-push-api.md).

### <a name="definitions"></a>Definice

- **Maximum QPS** – níže uvedená maximální QPS čísla jsou založena na nejvyšší QPS dosažené v testu, kde 99% dotazů bylo úspěšně dokončeno bez omezení a průměrné latence nechali pod 1000 MS.

- **Procento** maximálního QPS – procento z MAXIMÁLNÍho QPSu dosaženého pro určitý test. Například pokud daný test dosáhl maxima 100 QPS, 20% maximální QPS by byl 20 QPS.

- **Latence** – latence serveru pro dotaz; Tato čísla nezahrnují [prodlevu](https://en.wikipedia.org/wiki/Round-trip_delay)za dobu odezvy. Níže uvedené hodnoty jsou v milisekundách (MS).

### <a name="disclaimer"></a>Disclaimer

Kód, který jsme použili pro spuštění těchto srovnávacích testů, je k dispozici [zde](https://github.com/Azure-Samples/azure-search-performance-testing/tree/main/other_tools). Je potřeba poznamenat, že jsme zjistili mírně nižší úrovně QPS pomocí [řešení testování výkonu JMeter](https://github.com/Azure-Samples/azure-search-performance-testing) než v následujících srovnávacích testech. Rozdíly mohou být přiděleny na rozdíly ve stylu testů. V tomto předplatném je důležité, aby testy výkonu byly podobné vašim produkčním úlohám, jak je to možné.

Tyto srovnávací testy nijak nezaručují určitou úroveň výkonu vaší služby, ale můžou vám poskytnout představu o výkonu, který můžete očekávat na základě vašeho scénáře.

Pokud máte nějaké dotazy nebo obavy, můžete nás kontaktovat na adrese azuresearch_contact@microsoft.com .

## <a name="benchmark-1-e-commerce-search"></a>Srovnávací test 1: hledání elektronického obchodování

:::row:::
   :::column span="1":::
      ![Logo CDON](./media/performance-benchmarks/cdon-logo-160px2.png)
   :::column-end:::
   :::column span="3":::
      Tento srovnávací test byl vytvořen ve spolupráci s firmou elektronického obchodování, [CDON](https://cdon.com)a největším online tržišti ve severské oblasti s operacemi ve Švédsku, Finsku, Norsku a Dánsku. V rámci svých 1 500 obchodníků nabízí CDON řadu široké škály, která zahrnuje více než 8 000 000 produktů. V 2020 má CDON více než 120 000 000 návštěvníků a 2 000 000 aktivní zákazníky. V [tomto článku](https://pulse.microsoft.com/transform/na/fa1-how-cdon-has-been-using-technology-to-become-the-leading-marketplace-in-the-nordics/)se dozvíte víc o používání Azure kognitivní hledání v CDON.
   :::column-end:::
:::row-end:::

Pro spuštění těchto testů jsme použili snímek indexu vyhledávání v CDON a tisíce jedinečných dotazů z jejich [webu](https://cdon.com).

### <a name="scenario-details"></a>Podrobnosti scénáře

- **Počet dokumentů**: 6 000 000 
- **Velikost indexu**: 20 GB
- **Schéma indexu**: celé indexy s 250 poli Celkem, 25 prohledávatelných polí a 200 polí Faced/Filtered.
- **Typy dotazů**: fulltextové vyhledávací dotazy včetně omezujících vlastností, filtrů, řazení a profilů vyhodnocování

### <a name="s1-performance"></a>Výkon S1

#### <a name="queries-per-second"></a>Počet dotazů za sekundu

Následující graf znázorňuje nejvyšší načtení dotazu, které může služba zpracovat po delší dobu v dotazech za sekundu (QPS).

![Nejvyšší udržovatelná QPS elektronického obchodování S1](./media/performance-benchmarks/s1-ecom-qps.png)

#### <a name="query-latency"></a>Latence dotazů

Latence dotazů se liší v závislosti na zatížení služby a služeb v rámci vyššího zatížení budou mít průměrnou latenci dotazu vyšší. Následující tabulka ukazuje 25, 50, percentil, devadesát, 95. a 99 percentily latence dotazů pro tři různé úrovně využití.

| Procento maximálního QPS  | Průměrná latence | 25% | 75% | 90 % | 95 % | 99 %|
|---|---|---|---| --- | --- | --- | 
| 20 %  | 104 MS  | 35 MS  | 115 MS   | 177 MS | 257 MS | 738 MS |
| 50%  | 140 MS  | 47 MS  | 144 MS   | 241 MS | 400 MS | 1175 MS |
| 80 %  | 239 MS  | 77 MS  | 248 MS   | 466 MS | 763 MS | 1752 MS | 


### <a name="s2-performance"></a>Výkon S2

#### <a name="queries-per-second"></a>Počet dotazů za sekundu

Následující graf znázorňuje nejvyšší načtení dotazu, které může služba zpracovat po delší dobu v dotazech za sekundu (QPS).

![Nejvyšší udržovatelná QPSa elektronického obchodování S2](./media/performance-benchmarks/s2-ecom-qps.png)

#### <a name="query-latency"></a>Latence dotazů

Latence dotazů se liší v závislosti na zatížení služby a služeb v rámci vyššího zatížení budou mít průměrnou latenci dotazu vyšší. Následující tabulka ukazuje 25, 50, percentil, devadesát, 95. a 99 percentily latence dotazů pro tři různé úrovně využití.

| Procento maximálního QPS  | Průměrná latence | 25% | 75% | 90 % | 95 % | 99 %|
|---|---|---|---| --- | --- | --- | 
| 20 %  | 56 MS | 21 MS | 68 MS  | 106 MS  | 132 MS | 210 MS | 
| 50%  | 71 MS  | 26 MS  | 83 MS   | 132 MS | 177 MS | 329 MS |
| 80 %  | 140 MS  | 47 MS  | 153 MS   | 293 MS | 452 MS | 924 MS | 

### <a name="s3-performance"></a>Výkon S3

#### <a name="queries-per-second"></a>Počet dotazů za sekundu

Následující graf znázorňuje nejvyšší načtení dotazu, které může služba zpracovat po delší dobu v dotazech za sekundu (QPS).

![Nejvyšší udržovatelná QPS elektronického obchodování S3](./media/performance-benchmarks/s3-ecom-qps.png)

V tomto případě vidíte, že přidání druhého oddílu významně zvyšuje maximální QPS, ale přidání třetího oddílu poskytuje snížení mezních hodnoty. Menší vylepšení je pravděpodobně způsobeno tím, že všechna data jsou již načítána do S3's aktivní paměti s pouze dvěma oddíly.

#### <a name="query-latency"></a>Latence dotazů

Latence dotazů se liší v závislosti na zatížení služby a služeb v rámci vyššího zatížení budou mít průměrnou latenci dotazu vyšší. Následující tabulka ukazuje 25, 50, percentil, devadesát, 95. a 99 percentily latence dotazů pro tři různé úrovně využití.

| Procento maximálního QPS  | Průměrná latence | 25% | 75% | 90 % | 95 % | 99 %|
|---|---|---|---| --- | --- | --- |
| 20 %  | 50 MS  | 20 MS  | 64 MS   | 83 MS | 98 MS | 160 MS |
| 50%  | 62 MS  | 24 MS  | 80 MS   | 107 MS | 130 MS | 253 MS |
| 80 %  | 115 MS  | 38 MS  | 121 MS   | 218 MS | 352 MS | 828 MS |

## <a name="benchmark-2-document-search"></a>Srovnávací testy 2: hledání dokumentů

### <a name="scenario-details"></a>Podrobnosti scénáře

- **Počet dokumentů**: 7 500 000
- **Velikost indexu**: 22 GB
- **Schéma indexu**: 23 polí; 8 prohledávatelný, 10 Filtered/Faced
- **Typy dotazů**: hledání klíčových slov s omezujícími vlastnostmi a zvýrazněním přístupů

### <a name="s1-performance"></a>Výkon S1

#### <a name="queries-per-second"></a>Počet dotazů za sekundu

Následující graf znázorňuje nejvyšší načtení dotazu, které může služba zpracovat po delší dobu v dotazech za sekundu (QPS).

![Nejvyšší udržovatelná QPS hledání dokumentů S1](./media/performance-benchmarks/s1-docsearch-qps.png)

#### <a name="query-latency"></a>Latence dotazů

Latence dotazů se liší v závislosti na zatížení služby a služeb v rámci vyššího zatížení budou mít průměrnou latenci dotazu vyšší. Následující tabulka ukazuje 25, 50, percentil, devadesát, 95. a 99 percentily latence dotazů pro tři různé úrovně využití.

| Procento maximálního QPS  | Průměrná latence | 25% | 75% | 90 % | 95 % | 99 %|
|---|---|---|---| --- | --- | --- |
| 20 %  | 67 MS  | 44 MS  | 77 MS   | 103 MS | 126 MS | 216 MS |
| 50%  | 93 MS  | 59 MS  | 110 MS   | 150 MS | 184 MS | 304 MS |
| 80 %  | 150 MS  | 96 MS  | 184 MS   | 248 MS | 297 MS | 424 MS |

### <a name="s2-performance"></a>Výkon S2

#### <a name="queries-per-second"></a>Počet dotazů za sekundu

Následující graf znázorňuje nejvyšší načtení dotazu, které může služba zpracovat po delší dobu v dotazech za sekundu (QPS).

![Nejvyšší udržovatelná QPSa pro hledání dokumentů S2](./media/performance-benchmarks/s2-docsearch-qps.png)

#### <a name="query-latency"></a>Latence dotazů

Latence dotazů se liší v závislosti na zatížení služby a služeb v rámci vyššího zatížení budou mít průměrnou latenci dotazu vyšší. Následující tabulka ukazuje 25, 50, percentil, devadesát, 95. a 99 percentily latence dotazů pro tři různé úrovně využití.

| Procento maximálního QPS  | Průměrná latence | 25% | 75% | 90 % | 95 % | 99 %|
|---|---|---|---| --- | --- | --- |
| 20 %  | 45 MS  | 31 MS  | 55 MS   | 73 MS | 84 MS | 109 MS |
| 50%  | 63 MS  | 39 MS  | 81 MS   | 106 MS | 123 MS | 163 MS |
| 80 %  | 115 MS  | 73 MS  | 145 MS   | 191 MS | 224 MS | 291 MS |

## <a name="takeaways"></a>Shrnutí

Pomocí těchto srovnávacích testů můžete získat představu o výkonu Azure Kognitivní hledání nabídky. Můžete také zobrazit rozdíl mezi službami na různých úrovních.

Mezi tyto srovnávací testy patří některé klávesy:

* S2 může obvykle zpracovávat alespoň čtyřikrát svazek dotazu jako S1.
* S2 má obvykle nižší latenci než S1 na srovnatelných svazcích dotazů.
* Když přidáváte repliky, služba QPS, která se může normálně škálovat, se obvykle škáluje lineárně (například pokud jedna replika může zvládnout 10 QPS, pak pět replik obvykle může zpracovávat 50 QPS).
* Čím vyšší je zatížení služby, tím vyšší bude Průměrná latence.

Můžete si také prohlédnout, že se výkon může v různých scénářích významně lišit. Pokud nezískáváte výkon, který byste očekávali, podívejte se na [tipy pro lepší výkon](search-performance-tips.md).

## <a name="next-steps"></a>Další kroky

Teď, když jste viděli srovnávací testy výkonu, si můžete přečíst další informace o tom, jak analyzovat Kognitivní hledání výkon a klíčové faktory, které mají vliv na výkon.

> [!div class="nextstepaction"]
> [Analýza výkonu](search-performance-analysis.md) 
>  [Tipy pro lepší výkon](search-performance-tips.md)