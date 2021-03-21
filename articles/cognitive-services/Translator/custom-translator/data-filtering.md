---
title: Filtrování dat – vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: Když odesíláte dokumenty, které se mají použít pro školení vlastního systému, provedou dokumenty řadu kroků zpracování a filtrování, které se připraví na školení.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 53dea20e356f735a521dec8c22edf8cb2aa7122d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895862"
---
# <a name="data-filtering"></a>Filtrování dat

Když odesíláte dokumenty, které se mají použít pro školení vlastního systému, provedou dokumenty řadu kroků zpracování a filtrování, které se připraví na školení. Tyto kroky jsou vysvětleny zde. Znalosti tohoto filtrování vám můžou porozumět počtu vět zobrazeným ve vlastním překladateli a postupům, které můžete provést při přípravě dokumentů pro školení pomocí vlastního překladatele.

## <a name="sentence-alignment"></a>Přidružení vět
Pokud váš dokument není ve formátu XLIFF, TMX nebo ALIGN, vlastní Překladatel zarovnává věty vašich zdrojových a cílových dokumentů navzájem a větu po větě. Vlastní Překladatel neprovádí zarovnání dokumentu – sleduje pojmenování dokumentů a hledá odpovídající dokument v jiném jazyce. V rámci dokumentu se vlastní Překladatel pokusí najít odpovídající větu v jiném jazyce. Pomocí značek dokumentu, jako jsou vložené značky HTML, usnadňuje zarovnání.  

Pokud se zobrazí velký rozdíl mezi počtem vět ve zdrojovém a cílovém dokumentu na straně, váš dokument možná nebude v prvním místě paralelní nebo z jiných důvodů nelze zarovnávat. Dvojice dokumentů s velkým rozdílem (>10%) vět na každé straně má za cíl druhý vzhled, abyste se ujistili, že jsou skutečně paralelní. Vlastní Překladatel zobrazuje upozornění vedle dokumentu, pokud se počet vět liší podezřelě.  


## <a name="deduplication"></a>Odstranění duplicit
Vlastní Překladatel odstraní věty, které jsou k dispozici v testování a ladění dokumentů ze školicích dat.K odebrání dochází dynamicky uvnitř školicího běhu, nikoli v kroku zpracování dat. Vlastní Překladatel oznamuje počet vět v přehledu projektu před tímto odebráním.  

## <a name="length-filter"></a>Filtr délky
* Odeberte věty s pouze jedním slovem na obou stranách.
* Odstraňte věty s více než 100 slovy na obou stranách.Čínština, japonština, korejština jsou vyloučené.
* Odeberte věty s míň než 3 znaky. Čínština, japonština, korejština jsou vyloučené.
* Odeberte věty s více než 2000 znaky pro čínštinu, japonštinu a korejštinu.
* Odeberte věty s méně než 1% alfa znaky.
* Odebrat položky slovníku obsahující více než 50 slov.

## <a name="white-space"></a>Prázdné znaky
* Nahraďte jakoukoli sekvenci prázdných znaků, včetně tabulátorů a sekvencí CR/LF s jedním znakem mezery.
* Odebrat na začátku nebo na konci věty mezeru

## <a name="sentence-end-punctuation"></a>Koncová interpunkce věty
Nahraďte více interpunkčních znaků konce věty jedinou instancí.  

## <a name="japanese-character-normalization"></a>Normalizace japonského znaku
Převede písmena a číslice s plnou šířkou na znaky s poloviční šířkou.

## <a name="unescaped-xml-tags"></a>Neřídicí značky XML
Filtrování transformuje neřídicí značky do řídicích značek:
* `&lt;` stane `&amp;lt;`
* `&gt;` stane `&amp;gt;`
* `&amp;` stane `&amp;amp;`

## <a name="invalid-characters"></a>Neplatné znaky
Vlastní Překladatel odebere věty, které obsahují znak Unicode U + FFFD. Znak U + FFFD označuje neúspěšný převod kódování.

## <a name="next-steps"></a>Další kroky

- [Výuka modelu](how-to-train-model.md) ve vlastním překladateli.
