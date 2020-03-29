---
title: Terminologie - Personalista
description: Personalizátor používá terminologii z posilování učení. Tyto termíny se používají na webu Azure Portal a api.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: f75437c5afd5d3fd7f7570079be410d3db1ca8db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624272"
---
# <a name="terminology"></a>Terminologie

Personalizátor používá terminologii z posilování učení. Tyto termíny se používají na webu Azure Portal a api.

## <a name="conceptual-terminology"></a>Pojmová terminologie

* **Učení Smyčky**: Můžete vytvořit personalizační prostředek, volal _učení smyčky_, pro každou část aplikace, které mohou těžit z přizpůsobení. Pokud máte více než jednu zkušenost přizpůsobit, vytvořte smyčku pro každou z nich.

* **Model**: Model personalisty zachycuje všechna data o chování uživatelů, získává trénovací data z kombinace argumentů, které odesíláte do volání Hodnost a Odměna, a s trénováním chováním určeným zásadami učení.

## <a name="personalizer-configuration"></a>Konfigurace personalizátoru

Personalizátor je nakonfigurovaný z [portálu Azure](https://portal.azure.com).

* **Odměny**: Nakonfigurujte výchozí hodnoty pro čekací dobu odměny, výchozí zásady odměny a agregace odměn.

* **Průzkum**: konfigurace procenta Rank volání použít pro průzkum

* **Frekvence aktualizace modelu**: Jak často je model přetrénovaný.

* **Uchovávání dat**: Kolik dní dat k uložení. To může mít vliv na offline hodnocení, které se používají ke zlepšení vaší smyčky učení.

## <a name="use-rank-and-reward-apis"></a>Použití api pro hodnocení a odměny

* **Pořadí**: Vzhledem k akcím s funkcemi a kontextovými funkcemi použijte prozkoumat nebo zneužít k vrácení nejvyšší akce (položky obsahu).

    * **Akce**: Akce jsou položky obsahu, jako jsou produkty nebo propagační akce, ze kterých si můžete vybrat. Personalizátor zvolí nejlepší akci (vrácené ID akce odměny), která se uživatelům zobrazí prostřednictvím rozhraní Rank API.

    * **Kontext**: Chcete-li poskytnout přesnější pořadí, poskytněte informace o kontextu, například:
        * Váš uživatel.
        * Zařízení, na které jsou.
        * Aktuální čas.
        * Další údaje o aktuální situaci.
        * Historická data o uživateli nebo kontextu.

        Vaše konkrétní aplikace může mít různé informace o kontextu.

    * **[Funkce:](concepts-features.md)** Jednotka informací o položce obsahu nebo uživatelském kontextu. Nezapomeňte používat pouze funkce, které jsou agregované. Nepoužívejte jako funkce konkrétní časy, ID uživatelů ani jiná neagregovaná data.

        * _Funkce akce_ je metadata o obsahu.
        * _Funkce kontextu_ je metadata o kontextu, ve kterém je obsah prezentován.

* **Průzkum**: Služba Personalizace zkoumá, kdy místo vrácení nejlepší akce zvolí pro uživatele jinou akci. Služba Personalizace zabraňuje driftu, stagnaci a může se přizpůsobit průběžnému chování uživatelů zkoumáním.

* **Využití**: Služba Personalizace používá aktuální model k rozhodnutí o nejlepší akci na základě minulých dat.

* **Doba trvání experimentu**: Doba, po kterou služba Personalista čeká na odměnu, počínaje okamžikem, kdy došlo k volání hodnosti pro danou událost.

* **Neaktivní události**: Neaktivní událost je událost, kde jste volali Pořadí, ale nejste si jisti, že uživatel někdy uvidí výsledek, z důvodu rozhodnutí klientské aplikace. Neaktivní události umožňují vytvářet a ukládat výsledky přizpůsobení a později se rozhodnout je zahodit, aniž by to mělo vliv na model strojového učení.


* **Odměna**: Míra toho, jak uživatel reagoval na ID vrácené akce odměny rozhraní Rank API, jako skóre mezi 0 až 1. Hodnota 0:1 je nastavena obchodní logikou na základě toho, jak volba pomohla dosáhnout vašich obchodních cílů personalizace. Učení smyčky neukládá tuto odměnu jako individuální historii uživatelů.

## <a name="offline-evaluations"></a>Offline vyhodnocení

* **Hodnocení:** Offline hodnocení určuje nejlepší zásady učení pro vaši smyčku na základě dat smyčky.

* **Zásady učení**: Jak personalizátor trénuje model na každé události, bude určen některými parametry, které ovlivňují fungování algoritmu strojového učení. Nová smyčka učení začíná výchozí **zásady učení**, které mohou přinést mírný výkon. Při spuštění [hodnocení](concepts-offline-evaluation.md), Personalizátář vytvoří nové zásady učení speciálně optimalizované pro případy použití smyčky. Personalizár bude fungovat výrazně lépe s zásadami optimalizovanými pro každou konkrétní smyčku, generované během hodnocení. Zásady učení se nazývají _nastavení učení_ na modelu a **nastavení učení** pro prostředek personalikátor na webu Azure portal.