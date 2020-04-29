---
title: Přizpůsobení terminologie
description: Přizpůsobování používá terminologii při výuce. Tyto výrazy se používají v Azure Portal a rozhraní API.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: f75437c5afd5d3fd7f7570079be410d3db1ca8db
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "77624272"
---
# <a name="terminology"></a>Terminologie

Přizpůsobování používá terminologii při výuce. Tyto výrazy se používají v Azure Portal a rozhraní API.

## <a name="conceptual-terminology"></a>Koncepční terminologie

* **Výuková smyčka**: pro každou část aplikace můžete vytvořit prostředek přizpůsobeného, označovaný jako _výuková smyčka_, pro každou část vaší aplikace, která může být výhodou přizpůsobení. Pokud máte více než jedno prostředí pro přizpůsobení, vytvořte smyčku pro každý z nich.

* **Model**: model pro přizpůsobení zachycuje všechna data zjištěná v souvislosti s uživatelským chováním a získá školicí data z kombinace argumentů, které odesíláte do volání funkce Rank a disměna, a s chováním školení stanoveným pomocí zásad učení.

## <a name="personalizer-configuration"></a>Konfigurace přizpůsobení

Přizpůsobování je nakonfigurované z [Azure Portal](https://portal.azure.com).

* **Ceny**: konfigurujte výchozí hodnoty pro dobu čekání na měnu, výchozí odměňování a agregační zásady pro odměnu.

* **Průzkum**: Konfigurace procenta volání pořadí, která se mají použít pro průzkum

* **Frekvence aktualizace modelu**: jak často je model převlakované.

* **Uchovávání dat**: počet dní, po které se má ukládat data. To může mít dopad na offline hodnocení, které se používá ke zlepšení výukového cyklu.

## <a name="use-rank-and-reward-apis"></a>Použití rozhraní API pro řazení a měnu

* **Pořadí**: vzhledem k akcím s funkcemi a kontextovým funkcím, použijte funkci prozkoumat nebo zneužít k vrácení nejvyšší akce (položka obsahu).

    * **Akce**: akce jsou položky obsahu, jako jsou produkty nebo propagační akce, ze kterých si můžete vybrat. Přizpůsobení zvolí hlavní akci (vrácenou akci odměnu), která se uživatelům zobrazí prostřednictvím rozhraní API pro řazení.

    * **Kontext**: Chcete-li zadat přesnější pořadí, zadejte informace o kontextu, například:
        * Váš uživatel.
        * Zařízení, na kterém jsou.
        * Aktuální čas.
        * Další informace o aktuální situaci.
        * Historická data o uživateli nebo kontextu.

        Vaše konkrétní aplikace mohou mít různé kontextové informace.

    * **[Funkce](concepts-features.md)**: jednotka informací o položce obsahu nebo kontextu uživatele. Ujistěte se, že používáte jenom agregované funkce. Nepoužívejte konkrétní časy, ID uživatelů ani jiná neagregovaná data jako funkce.

        * _Funkce Action_ je metadata týkající se obsahu.
        * _Kontextová funkce_ je metadata o kontextu, ve kterém je zobrazený obsah.

* **Průzkum**: služba přizpůsobeného přizpůsobuje, kdy místo vrácení nejlepší akce zvolí pro uživatele jinou akci. Služba přizpůsobeného přizpůsobování zabraňuje posunu, stagnation a přizpůsobení probíhajícímu uživatelskému chování, a to zkoumáním.

* **Využití**: služba přizpůsobeného modelu používá aktuální model k určení nejlepší akce na základě minulých dat.

* **Doba trvání experimentu**: doba, po kterou služba přizpůsobené může čekat na určitou odměnu od okamžiku, kdy pro danou událost proběhlo volání pořadí.

* **Neaktivní události**: neaktivní událost je ta, kde se říkáte Rank, ale nejste si jistí, že uživatel uvidí výsledek z důvodu rozhodnutí klientské aplikace. Neaktivní události umožňují vytvořit a uložit výsledky přizpůsobení a pak se rozhodnout o jejich zrušení později, aniž by to ovlivnilo model strojového učení.


* **Odměňování**: míra, kterou uživatel odpověděl na rozhraní API pro řazení, vrátilo ID akce odměňování jako skóre mezi 0 a 1. Hodnota 0 až 1 je nastavená vaší obchodní logikou na základě toho, jak volba pomáhá dosáhnout vašich obchodních cílů přizpůsobení. Výuková smyčka neukládá tuto odměnu jako historii jednotlivých uživatelů.

## <a name="offline-evaluations"></a>Offline vyhodnocení

* **Vyhodnocení**: offline vyhodnocení Určuje doporučené zásady učení pro vaši smyčku na základě dat vaší smyčky.

* **Zásady učení**: jak přizpůsobovat vlaky na každou událost se určí pomocí některých parametrů, které mají vliv na to, jak algoritmus strojového učení funguje. Nová výuková smyčka začíná výchozími **zásadami učení**, což může přinést střední výkon. Při spuštění [hodnocení](concepts-offline-evaluation.md)vytvoří přizpůsobené nové zásady učení speciálně optimalizované pro případy použití smyčky. Přizpůsobený přizpůsobování bude výrazně lepší díky zásadám optimalizovaným pro každou specifickou smyčku vygenerovanou během hodnocení. Zásady učení se nazývají _Nastavení učení_ v **Nastavení modelu a učení** pro prostředek přizpůsobeného v Azure Portal.