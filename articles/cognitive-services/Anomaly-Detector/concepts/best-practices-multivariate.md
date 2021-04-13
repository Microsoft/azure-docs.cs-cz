---
title: Osvědčené postupy pro používání rozhraní lineární API pro detekci anomálií
titleSuffix: Azure Cognitive Services
description: Osvědčené postupy pro používání rozhraní lineární API pro detekci anomálií pro použití detekce anomálií na data časových řad.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: detekce anomálií, strojové učení, algoritmy
ms.openlocfilehash: 7de25b4a099c706c05b32b52492096923033f822
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315487"
---
# <a name="multivariate-time-series-anomaly-detector-best-practices"></a>Osvědčené postupy pro detekci anomálií lineární Time Series

Tento článek obsahuje pokyny týkající se doporučených postupů, které se použijí při použití rozhraní API detektoru anomálií lineární.

## <a name="how-to-prepare-data-for-training"></a>Příprava dat pro školení

Aby bylo možné používat rozhraní API pro detekci anomálií lineární, musíme před použitím detekce vyškolit náš vlastní model. Data použitá pro školení jsou dávkou časových řad, přičemž každá časová řada by měla být ve formátu CSV se dvěma sloupci, časovým razítkem a hodnotou. Všechny časové řady by měly být v jednom souboru zip a nahrány do Azure Blob Storage. Ve výchozím nastavení se název souboru použije k reprezentaci proměnné pro časovou řadu. Alternativně je možné do souboru ZIP zahrnout další meta.js, pokud chcete, aby název proměnné byl jiný než název souboru. zip. Jakmile vygenerujeme [adresu URL (SAS) objektu BLOB (Shared Access signaturs)](../../../storage/common/storage-sas-overview.md), můžeme ji použít pro školení.

## <a name="data-quality-and-quantity"></a>Kvalita a množství dat

Rozhraní lineární API pro detekci anomálií používá špičkové neuronové sítě, které vám pomohou se naučit běžné vzorce z historických dat a předpovídá, jestli jsou budoucí hodnoty anomálie. Kvalita a množství školicích dat je důležité pro výuku optimálního modelu. Jak model zjišťuje normální vzorce z historických dat, data školení by měla představovat celkový normální stav systému. Je těžké, aby se model dozvěděl s těmito typy vzorů, pokud jsou školicí data zaplněna anomáliemi. Model má také miliony parametrů a vyžaduje minimální počet datových bodů, aby se dozvěděla optimální sada parametrů. Obecným pravidlem je, že je nutné zadat alespoň 15 000 datových bodů na proměnnou, aby bylo možné model správně naučit. Další data, lepší model.

Je běžné, že řada časových řad neobsahuje hodnoty, což může mít vliv na výkon vyškolených modelů. Chybějící poměr všech časových řad by měl být pod rozumnou hodnotou. V časové řadě s 90% hodnotami chybí krátké informace o běžných vzorech systému. I horší, model může považovat vyplněné hodnoty za normální vzory, což jsou obvykle přímé segmenty nebo konstantní hodnoty. Když se nová data proudí, mohou být data zjištěna jako anomálie.

Doporučená maximální prahová hodnota pro chybějící hodnotu je 20%, ale za určitých okolností může být přijatelné vyšší prahová hodnota. Například pokud máte časovou řadu s členitou hustotou a jinou časovou řadou s hodinovou členitost.  Každá hodina je 60 datových bodů za minutu dat a 1 datový bod pro hodinová data, což znamená, že chybějící poměr pro hodinová data je 98,33%. Je však možné vyplnit hodinová data jedinou hodnotou v případě, že časová řada hodin obvykle nekolísá příliš mnoho.

## <a name="parameters"></a>Parametry

### <a name="sliding-window"></a>Posuvné okno

Detekce anomálií lineární přebírá segment datových bodů délky `slidingWindow` jako vstup a rozhoduje o tom, jestli je další datový bod anomálií. Čím větší je délka vzorku, tím více dat se bude považovat za rozhodnutí. Měli byste mít na paměti dvě věci při volbě správné hodnoty pro `slidingWindow` : vlastnosti vstupních dat a kompromis mezi školením/odvozeným časem a potenciálním zlepšením výkonu. `slidingWindow` obsahuje celé číslo mezi 28 a 2880. Můžete určit, kolik datových bodů se bude používat jako vstup na základě toho, jestli jsou data periodická, a vzorkovací frekvence pro vaše data.

Když jsou data periodická, můžete jako vstup zahrnout 1-3 cykly, a když se data vzorkují s vysokou frekvencí (malou členitost), jako jsou data na úrovni minut nebo na druhé úrovni, můžete jako vstup vybrat víc dat. Dalším problémem je, že delší vstupy můžou způsobit delší dobu školení/odvození a není nijak zaručeno, že více vstupních bodů vede k nárůstu výkonu. Vzhledem k tomu, že je příliš málo datových bodů, může být model obtížně sblížen do optimálního řešení. Například je obtížné detekovat anomálie, pokud vstupní data obsahují pouze dva body.

### <a name="align-mode"></a>Režim zarovnání

Parametr `alignMode` slouží k označení, jak chcete v časových razítkách zarovnat více časových řad. Důvodem je, že řada časových řad postrádá hodnoty a před dalším zpracováním je potřeba je zarovnat na stejná časová razítka. Pro tento parametr jsou k dispozici dvě možnosti `inner join` a `outer join` . `inner join` znamená, že výsledky zjišťování budou nahlášeny na časová razítka, na kterých **každá časová řada** obsahuje hodnotu, a `outer join` to znamená, že výsledky zjišťování budou vykazovat časová razítka pro **Všechny časové řady** , které mají hodnotu.  **`alignMode` Bude mít vliv také na vstupní sekvenci modelu**, takže vyberte vhodný `alignMode` pro váš scénář, protože výsledky se můžou výrazně lišit.

Tady zobrazujeme příklad pro vysvětlení různých `alignModel` hodnot.

#### <a name="series1"></a>Series1

|časové razítko | hodnota|
----------| -----|
|`2020-11-01`| 1  
|`2020-11-02`| 2  
|`2020-11-04`| 4  
|`2020-11-05`| 5

#### <a name="series2"></a>Series2

časové razítko | hodnota  
--------- | -
`2020-11-01`| 1  
`2020-11-02`| 2  
`2020-11-03`| 3  
`2020-11-04`| 4

#### <a name="inner-join-two-series"></a>Vnitřní spojení – dvě řady
  
časové razítko | Series1 | Series2
----------| - | -
`2020-11-01`| 1 | 1
`2020-11-02`| 2 | 2
`2020-11-04`| 4 | 4

#### <a name="outer-join-two-series"></a>Vnější spojení – dvě řady

časové razítko | series1 | series2
--------- | - | -
`2020-11-01`| 1 | 1
`2020-11-02`| 2 | 2
`2020-11-03`| NA | 3
`2020-11-04`| 4 | 4
`2020-11-05`| 5 | NA

### <a name="fill-not-available-na"></a>Výplň není k dispozici (NA)

Po zarovnávání proměnných na časovém razítku vnějším spojením, může `Not Available` `NA` v některých proměnných existovat hodnota (). Můžete zadat metodu pro vyplnění této hodnoty NEDEF. Možnosti pro `fillNAMethod` jsou `Linear` ,,, `Previous` a `Subsequent`  `Zero` `Fixed` .

| Možnost     | Metoda                                                                                           |
| ---------- | -------------------------------------------------------------------------------------------------|
| Lineární     | Vyplnit hodnoty lineární interpolací                                                           |
| Předchozí   | Rozšíří poslední platnou hodnotu, aby se mezery vyplnily. Příklad: `[1, 2, nan, 3, nan, 4]` -> `[1, 2, 2, 3, 3, 4]` |
| Návazn | K vyplnění mezer použijte další platnou hodnotu. Příklad: `[1, 2, nan, 3, nan, 4]` -> `[1, 2, 3, 3, 4, 4]`       |
| Žádnou       | Naplňte hodnoty NA hodnotu 0.                                                                           |
| Pevný      | Vyplňte hodnoty NA hodnotu zadanou platnou hodnotou, která se má zadat v `paddingValue` .          |

## <a name="model-analysis"></a>Analýza modelu

### <a name="training-latency"></a>Latence školení

Školení pro detekci anomálií lineární může být časově náročné. Hlavně v případě, že máte velké množství časových razítek používaných pro školení. Proto umožňujeme, aby část školicího procesu byla asynchronní. Obvykle uživatelé odesílají výukovou úlohu prostřednictvím rozhraní API modelu výuky. Pak Získejte stav modelu prostřednictvím `Get Multivariate Model API` . Tady ukážeme, jak vyextrahovat zbývající čas před dokončením školení. V odpovědi rozhraní API modelu lineární je k dispozici položka s názvem `diagnosticsInfo` . V této položce je `modelState` prvek. Abychom mohli vypočítat zbývající čas, musíme použít `epochIds` a `latenciesInSeconds` . Epocha představuje jeden úplný cyklus prostřednictvím školicích dat. Každých 10 epochs vypíšeme výstup informací o stavu. V celkovém případě budeme zaškolit 100 epochs, latence indikuje, jak dlouho epocha trvá. S těmito informacemi ví, jak zbývající čas vyškolí model.

### <a name="model-performance"></a>Výkon modelu

Lineární detekci anomálií jako model, který není pod dohledem. Nejlepším způsobem, jak ho vyhodnotit, je ověřit výsledky anomálií ručně. V odpovědi modelu Get lineární poskytujeme některé základní informace, abychom mohli analyzovat výkon modelu. V `modelState` elementu vráceném rozhraním API získat lineární lze použít `trainLosses` a `validationLosses` k vyhodnocení, zda byl model vyškolený podle očekávání. Ve většině případů se tyto dvě ztráty postupně zmenšují. Další informace, které nám poslouží k analýze výkonu modelu, je v `variableStates` . Seznam stavů proměnných je seřazen `filledNARatio` v sestupném pořadí. Čím větší je náš výkon, obvykle musíme `NA ratio` co nejvíce snížit. `NA` může to být způsobeno chybějícími hodnotami nebo zarovnanými proměnnými z perspektivy časového razítka.

## <a name="next-steps"></a>Další kroky

- [Rychlé starty](../quickstarts/client-libraries-multivariate.md).
- [Přečtěte si o základních algoritmech, které lineární detektory výkonu.](https://arxiv.org/abs/2009.02040)