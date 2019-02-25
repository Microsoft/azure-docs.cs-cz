---
title: Kotvy vztahů a způsob hledání v prostorových kotvy Azure | Dokumentace Microsoftu
description: Popište konceptuálního modelu za kotvy vztahů. Popis procesu připojování ukotvení v rámci mezeru a proces pomocí rozhraní API pro blízké ke splnění scénáři způsob hledání. Po s vysvětlením konceptuální model, přejděte na vývojáře našich ukázkových aplikací, které umožní tak s tím můžou začít implementace této situace ve svých vlastních aplikacích.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 56f59a76ac3d11677d5b1f76904cf74e933fa7f6
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753020"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Kotvy vztahů a způsob hledání v prostorových kotev vztahů Azure

Kotvy vztahů umožňují vytvořit propojené ukotvení v prostoru a pak o nich jako klást otázky:

* Existují kotvy okolních?
* Jak daleko je mezi nimi?

## <a name="examples"></a>Příklady

Případy použití, které můžete povolit pomocí připojených kotvy patří:

1. Pracovní proces musí provádět procedury, která zahrnuje navštívit různých umístěních v objektu pro vytváření průmyslu. Objekt pro vytváření umístěna prostorových ukotvení v každé lokalitě účastnících se. HoloLens nebo mobilní aplikace pomůže průvodce pracovní proces z jednoho umístění do druhého. Nejdřív byste požádat o prostorových kotev vztahů, které jsou poblíž a potom provede pracovního procesu k další oblasti. Aplikace zobrazí visual indikátory Obecné směr a vzdálenost do následujícího umístění, k dokončení úlohy.

2. Muzejních vytvoří prostorových kotvy na veřejné zobrazení, které společně vytvářejí konkrétní seznámíte muzejních, jako je například "Hodinová si základní veřejné zobrazí". Při jedné veřejné zobrazované návštěvníci, otevřou muzejních hybridní realitu na svém mobilním zařízení. Potom by jejich přejděte svůj telefon kolem prostor a prostřednictvím informačního kanálu fotoaparátu a naleznete obecné směr a vzdálenost k jiné veřejné zobrazí na prohlídku. Jako uživatel spustí procházení na jednu z veřejné zobrazí, aktualizuje aplikaci postupně Obecné směr a vzdálenost k poskytování pomoci uživatelům průvodce existuje.

## <a name="way-finding"></a>Way-finding

Představte si, že aplikace používá směr "řádku veškerá" a vzdálenost mezi kotvy poskytnout nápovědu, pokyny pro uživatele. Označujeme jako způsob, jak najít tento celkové scénář. Je důležité si uvědomit, že se liší od zapnout vypnout navigační způsob hledání. V navigačním panelu na řadě vy zapnout uživatelé provádějí kolem stěn prostřednictvím dveře a mezi podlahy. S způsob zjištění, uživateli se nabídnou tipy týkající se obecné směr cíl. Ale odvození nebo znalostní báze prostoru uživatele také pomáhá procházet strukturu do cíle.

Vytváření prostředí způsob hledání zahrnuje mezeru Příprava prostředí a vyvíjet aplikace, která bude koncovým uživatelům pracovat. Koncepční kroky patří:

1. Plánování místo: Určení umístění v rámci oboru, které jsou součástí prostředí způsob hledání. V předchozích příkladech tato aktivita může dokončit nadřízený objekt pro vytváření nebo koordinátor muzejních tour.
2. Připojování kotev vztahů: Někdo navštíví zvolené umístění a vytvoří prostorových kotvy existuje. Tento úkol můžete udělat pomocí režimu správce aplikace koncového uživatele nebo jinou aplikaci zcela. Pomocí tohoto procesu je ukotvení jednotlivých připojen nebo související s ostatními. Tyto vztahy jsou zachována ve službě.
3. Spuštění prostředí pro koncové uživatele: Prvním krokem pro koncové uživatele je jeden z ukotvení pomocí aplikace, které mohou být takové zvoleného umístění vyhledejte. Určení umístění, kde můžou koncoví uživatelé zadat prostředí je součástí návrhu celkové prostředí.
4. Hledání okolních kotev vztahů: Jakmile uživatel pružný jeden ukotvení, aplikace můžou požádat o okolních kotev vztahů. Tento postup vrátí pozici mezi zařízením a tyto ukotvení.
5. Která bude obsahovat uživatele: Aplikace můžete využít výhod pozici pro každý z těchto kotev vztahů k vykreslení nápovědu užitečné návody, jejich Obecné směr a vzdálenost. Například může být objektem icon a šipku na kamera kanálu v mobilní aplikaci reprezentující jednotlivé potenciální cíle stejně jako na obrázku níže.
6. Upřesnění pokynů: Jako uživatel provede, můžete aplikaci pravidelně vypočítat nové pozice mezi zařízením a určení ukotvení. Aplikace dál Upřesnit odkazy na pokyny, které uživatel přejít na cíl.

![Přímé schůzky](./media/meeting-spot.png)

## <a name="connecting-anchors"></a>Připojení kotvy

K vytvoření prostředí způsob zjištění, potřebujete umístit připojených kotvy ve zvoleném umístění. Níže budeme předpokládat, že tuto práci provádí správce aplikace.

### <a name="connecting-anchors-in-a-single-session"></a>Připojení ukotvení v jedné relaci

Postup připojení kotev vztahů jsou:

1. Správce provede na první místo a vytvoří ukotvení A použití CloudSpatialAnchorSession.
2. Správce vás do druhé umístění, zatímco základní platformy MR/AR pokračuje ve sledování uživatele.
3. Správce vytvoří se stejný CloudSpatialAnchorSession ukotvení B. Ukotvení A a B teď připojení, ale tento vztah se spravuje pomocí služby Azure prostorových kotvy vztahů.
4. Pokračujte v postupu pro všechny kotev vztahů, které chcete připojit.

### <a name="multiple-sessions"></a>Více relací

Prostorové kotvy můžete také připojit přes více relací. Tato metoda umožňuje vytvořit a připojit některé kotev vztahů najednou a později vytvořit a připojit další ukotvení. Připojení ukotvení s více relací:

1. Aplikace vytvoří v jedné CloudSpatialAnchorSession některé ukotvení.
2. Později například na jiný den aplikace vyhledá jeden z těchto ukotvení s novou CloudSpatialAnchorSession (například ukotvení A).
3. Uživatel provede do nového umístění, zatímco základní platformy MR/AR pokračuje ve sledování uživatele.
4. Pomocí stejného CloudSpatialAnchorSession, uživatel vytvoří kotvy C. ukotvení A, B a C připojení a tento vztah se spravuje pomocí Azure prostorových ukotvení.
5. Tento postup pro další ukotvení a další relace můžete pokračovat v průběhu času.

### <a name="verifying-anchor-connections"></a>Ověřuje se připojení ukotvení

Aplikace můžete ověřit, že dvě kotvy vztahů připojeni pomocí dotazu pro blízké kotev vztahů. Pokud výsledek dotazu obsahuje požadovanou cílovou ukotvení, má aplikace potvrzení, že jsou připojené ukotvení. Pokud nejsou připojeni, můžete aplikaci znovu opakujte postup připojení. Tady jsou některé důvody, proč nemusí podařit připojit kotev vztahů:

1. Základní sledování MR/AR ztráty sledování během procesu připojování ukotvení.
2. Došlo k chybě síťové komunikaci se službou Azure prostorových ukotvení a nelze trvale uložit připojení ukotvení.

### <a name="sample-code"></a>Ukázka kódu

Zobrazí se ukázkový kód, který ukazuje, jak připojit ukotvení a proveďte okolních dotazy. Odkazovat na ukázkové aplikace Azure prostorových kotvy na Githubu.
