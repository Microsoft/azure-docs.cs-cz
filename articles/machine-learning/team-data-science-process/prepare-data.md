---
title: Přípravná data pro ML Studio (klasika) - Team Data Science Process
description: Předběžně zpracujte a vyčistěte data, abyste je připravili k efektivnímu použití pro strojové učení.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: caedcf313ab809e9607907545f26ca1b62bbeca7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720040"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Úlohy k přípravě dat pro rozšířené strojové učení
Předběžné zpracování a čištění dat jsou důležité úkoly, které musí být provedeny před datovou sadou lze použít pro trénování modelu. Nezpracovaná data jsou často hlučná a nespolehlivá a mohou chybět hodnoty. Použití těchto dat pro modelování může vést k zavádějícím výsledkům. Tyto úkoly jsou součástí procesu vědecké ho řízení o týmových datech (TDSP) a obvykle následují počáteční zkoumání datové sady použité ke zjištění a plánování požadovaného předběžného zpracování. Podrobnější pokyny k procesu TDSP naleznete v krocích uvedených v [procesu vědecké vědy o týmových datech](overview.md).

Úlohy předběžného zpracování a čištění, jako je úloha průzkumu dat, lze provádět v široké škále prostředí, jako je SQL nebo Hive nebo Azure Machine Learning Studio (klasické) a s různými nástroji a jazyky, jako je R nebo Python, v závislosti na tom, kde vaše data a jak je formátován. Vzhledem k tomu, že tdsp je iterativní povahy, tyto úkoly mohou probíhat v různých krocích v pracovním postupu procesu.

Tento článek představuje různé koncepty zpracování dat a úkoly, které lze před nebo po ingestování dat do Azure Machine Learning Studio (klasické).

Příklad zkoumání dat a předběžného zpracování provedeného v azure machine learningovém studiu (klasickém) najdete v [tématu Předběžné zpracování datového](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) videa.

## <a name="why-pre-process-and-clean-data"></a>Proč předběžně zpracovávat a čistit data?
Údaje z reálného světa jsou shromažďovány z různých zdrojů a procesů a mohou obsahovat nesrovnalosti nebo poškozená data, která ohrožují kvalitu datové sady. Typické problémy s kvalitou dat, které vznikají, jsou:

* **Neúplné**: Data postrádají atributy nebo obsahují chybějící hodnoty.
* **Hlučné**: Data obsahují chybné záznamy nebo odlehlé hodnoty.
* **Nekonzistentní**: Data obsahují konfliktní záznamy nebo nesrovnalosti.

Kvalitní data jsou předpokladem pro kvalitní prediktivní modely. Chcete-li se vyhnout "odpadky dovnitř, odpadky ven" a zlepšit kvalitu dat, a proto výkon modelu, je nutné provést obrazovku stavu dat na místě problémy s daty brzy a rozhodnout o odpovídající zpracování dat a čištění kroky.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Jaké jsou některé typické datové obrazovky, které jsou zaměstnány?
Můžeme zkontrolovat obecnou kvalitu údajů kontrolou:

* Počet **záznamů**.
* Počet **atributů** (nebo **funkcí).**
* Datové **typy atributů** (nominální, ordinální nebo kontinuální).
* Počet **chybějících hodnot**.
* **Dobře vytvořená** data.
  * Pokud jsou data v TSV nebo CSV, zkontrolujte, zda oddělovače sloupců a oddělovače čar vždy správně oddělují sloupce a řádky.
  * Pokud jsou data ve formátu HTML nebo XML, zkontrolujte, zda jsou data dobře tvarovaná na základě příslušných standardů.
  * Analýza může být rovněž nezbytná pro získání strukturovaných informací z polostrukturovaných nebo nestrukturovaných údajů.
* **Nekonzistentní datové záznamy**. Zkontrolujte, zda jsou povoleny rozsah hodnot. Například pokud data obsahují studentGPA (průměr bodu hodnocení), zkontrolujte, zda gpa je v určeném rozsahu, řekněme 0 ~ 4.

Když zjistíte problémy s daty, jsou nezbytné **kroky zpracování,** které často zahrnují čištění chybějících hodnot, normalizaci dat, diskretizaci, zpracování textu za účelem odstranění nebo nahrazení vložených znaků, které mohou ovlivnit zarovnání dat, smíšené datové typy v běžných polích a další.

**Azure Machine Learning spotřebovává dobře vytvořená tabulková data**.  Pokud jsou data již v tabulkové podobě, předběžné zpracování dat lze provést přímo pomocí Azure Machine Learning Studio (klasické) v Machine Learning.  Pokud data nejsou v tabulkové podobě, řekněme, že je v XML, může být pro převod dat do tabulkové ho formuláře vyžadována analýza.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Jaké jsou některé z hlavních úkolů v předběžném zpracování dat?
* **Čištění dat**: Vyplňte chybějící hodnoty, detekujte a odstraňte hlučná data a odlehlé hodnoty.
* **Transformace dat**: Normalizovat data za účelem snížení dimenzí a šumu.
* **Redukce dat**: Ukázkové datové záznamy nebo atributy pro snadnější zpracování dat.
* **Disketizace dat**: Převeďte průběžné atributy na kategorické atributy pro snadné použití s určitými metodami strojového učení.
* **Čištění textu**: Odstraňte vložené znaky, které mohou způsobit nesouosost dat, například vložené karty v datovém souboru odděleném tabulátory, vložené nové řádky, které mohou například přerušit záznamy.

Níže uvedené části podrobně popisují některé z těchto kroků zpracování dat.

## <a name="how-to-deal-with-missing-values"></a>Jak se vypořádat s chybějícími hodnotami?
Chcete-li vyřešit chybějící hodnoty, je nejlepší nejprve identifikovat důvod chybějící hodnoty lépe zpracovat problém. Typické metody zpracování chybějících hodnot jsou:

* **Odstranění**: Odebrání záznamů s chybějícími hodnotami
* **Maketa substituce**: Chybějící hodnoty nahraďte fiktivní hodnotou: např. *unknown*
* **Střední substituce**: Pokud jsou chybějící data číselná, nahraďte chybějící hodnoty střední hodnotou.
* **Časté nahrazování**: Pokud jsou chybějící data kategorická, nahraďte chybějící hodnoty nejčastější položkou
* **Regresní substituce**: Pomocí regresní metody nahraďte chybějící hodnoty regresními hodnotami.  

## <a name="how-to-normalize-data"></a>Jak normalizovat data?
Normalizace dat změní měřítko číselných hodnot na zadaný rozsah. Mezi oblíbené metody normalizace dat patří:

* **Normalizace min-max**: Lineárně transformujte data na rozsah, řekněme mezi 0 a 1, kde je hodnota min zmenšena na hodnotu 0 a max na hodnotu 1.
* **Normalizace Z-skóre**: Měřítko dat na základě střední a směrodatné odchylky: vydělte rozdíl mezi daty a střední hodnotou směrodatnou odchylkou.
* **Desetinné měřítko**: Měřítko dat přesunutím desetinné čárky hodnoty atributu.  

## <a name="how-to-discretize-data"></a>Jak diskretize data?
Data mohou být diskretizována převodem spojitých hodnot na nominální atributy nebo intervaly. Některé způsoby, jak toho dosáhnout, jsou:

* **Binning se stejnou šířkou**: Rozdělte rozsah všech možných hodnot atributu do skupin N stejné velikosti a přiřaďte hodnoty, které spadají do přihrádky s číslem přihrádky.
* **Binning se stejnou výškou**: Rozdělte rozsah všech možných hodnot atributu do skupin N, z nichž každá obsahuje stejný počet instancí, a pak přiřaďte hodnoty, které spadají do přihrádky s číslem přihrádky.  

## <a name="how-to-reduce-data"></a>Jak snížit množství dat?
Existují různé metody pro snížení velikosti dat pro snadnější zpracování dat. V závislosti na velikosti dat a doméně lze použít následující metody:

* **Vzorkování záznamů**: Vzorkujte datové záznamy a z dat zvolte pouze reprezentativní podmnožinu.
* **Vzorkování atributů**: Z dat vyberte pouze podmnožinu nejdůležitějších atributů.  
* **Agregace**: Rozdělte data do skupin a uložte čísla pro každou skupinu. Například denní čísla tržeb řetězce restaurací za posledních 20 let lze agregovat do měsíčních výnosů, aby se zmenšila velikost dat.  

## <a name="how-to-clean-text-data"></a>Jak vyčistit textová data?
**Textová pole v tabulkových datech** mohou obsahovat znaky, které ovlivňují zarovnání sloupců nebo hranice záznamu. Například vložené karty v souboru odděleném tabulátory způsobují nesouosost sloupců a vložené nové znaky řádku zalomí řádky záznamů. Nesprávné zpracování kódování textu při psaní nebo čtení textu vede ke ztrátě informací, neúmyslnému zavedení nečitelných znaků (například nul) a může také ovlivnit analýzu textu. Může být nutná pečlivá analýza a úprava, aby bylo možné vyčistit textová pole pro správné zarovnání a/nebo extrahovat strukturovaná data z nestrukturovaných nebo částečně strukturovaných textových dat.

**Zkoumání dat** nabízí včasné zobrazení dat. Během tohoto kroku lze odhalit řadu problémů s daty a k řešení těchto problémů lze použít odpovídající metody.  Je důležité klást otázky, jako je to, co je zdrojem problému a jak mohla být tato otázka zavedena. Tento proces také pomáhá rozhodnout o krocích zpracování dat, které je třeba provést k jejich vyřešení. Identifikace případy konečného použití a personas lze také použít k upřednostnění úsilí o zpracování dat.

## <a name="references"></a>Odkazy
> *Dolování dat: Koncepty a techniky*, Třetí vydání, Morgan Kaufmann, 2011, Jiawei Han, Micheline Kamber a Jian Pei
> 
> 

