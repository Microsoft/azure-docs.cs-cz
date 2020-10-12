---
title: Relace mezi kotvami a hledání cesty
description: Přečtěte si o koncepčním modelu za vztahy kotvy. Naučte se spojit kotvy v rámci prostoru a použít rozhraní API v okolí k uspokojení scénáře, jak se hledání provést.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 13c85dff40e266287d893d9e45b32a66a33f027e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006020"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Vztahy ukotvení a způsoby hledání v prostorových kotvách Azure

Pomocí vztahů kotvy můžete vytvořit připojené kotvy v prostoru a pak klást otázky následujícím způsobem:

* Existují kotvy na blízkosti?
* Jak daleko jsem je?

## <a name="examples"></a>Příklady

Připojené kotvy můžete použít v následujících případech:

* Pracovní proces musí dokončit úlohu, která zahrnuje návštěvu různých míst v průmyslové továrně. Objekt pro vytváření obsahuje prostorové kotvy na každém místě. HoloLens nebo mobilní aplikace pomáhají pracovnímu procesu z jednoho místa na další. Aplikace nejdřív požádá o okolní prostorové kotvy a pak pracovníka provede až do dalšího umístění. Aplikace vizuálně zobrazuje obecný směr a vzdálenost k dalšímu umístění.

* Museum vytvoří prostorové kotvy na veřejných displejích. Společně tyto kotvy tvoří jednorázovou prohlídku základní veřejné obrazovky Museum. Na veřejném displeji můžou Návštěvníci na svém mobilním zařízení otevřít aplikaci Mixed reality Museum. Pak ukazují jejich telefonní kameru na místo, abyste viděli obecný směr a vzdálenost k ostatním veřejným displejům na prohlídce. Když uživatel projde k veřejnému zobrazení, aplikace aktualizuje obecný směr a vzdálenost, které vám pomůžou s tímto uživatelem.

## <a name="set-up-way-finding"></a>Nastavit způsob – hledání

Aplikace, která používá směr pohledu a vzdálenost mezi kotvami k poskytnutí pokynů, je používána *způsobem hledání*. Způsob hledání se liší od navigace. V uživatelsky zapínání navigace se uživatelé provedou kolem stěn, přes dveře a mezi podlahami. Pomocí způsobu vyhledávání uživatel získá nápovědu týkající se obecného směru cíle. Ale odvození nebo znalosti prostoru také pomáhá uživateli procházet strukturu do cíle.

Chcete-li vytvořit způsob vyhledávání, nejprve připravte prostor pro prostředí a vytvořte aplikaci, se kterou uživatelé budou pracovat. Toto jsou koncepční kroky:

1. **Plánování prostoru**: Rozhodněte, která umístění v rámci prostoru budou součástí způsobu vyhledávání. V našich scénářích se může správce výroby nebo koordinátor Museum Tour rozhodnout, která umístění se mají zahrnout do způsobu vyhledávání.
2. **Spojit kotvy**: navštivte vybraná umístění a vytvořte prostorové kotvy. To můžete provést v režimu správce aplikace pro koncové uživatele nebo v jiné aplikaci. Připojíte se nebo spojíte jednotlivé kotvy k ostatním. Tato služba tyto vztahy udržuje.
3. **Spusťte prostředí koncového uživatele**: uživatelé spustí aplikaci, aby vyhledali kotvu, která může být ve zvolených umístěních. Celkový návrh by měl určovat umístění, kde můžou uživatelé vstoupit do prostředí.
4. **Najít okolní kotvy**: Jakmile uživatel najde kotvu, aplikace může požádat o okolní kotvy. Tento postup vrátí pozici mezi zařízením a těmito kotvami.
5. **Průvodce uživatelem**: aplikace může použít pozici pro každé z těchto kotev k poskytnutí pokynů k obecnému směru a vzdálenosti uživatele. Například kanál kamery v aplikaci může zobrazit ikonu a šipku představující jednotlivé potenciální cíle, jak ukazuje následující obrázek.
6. **Upřesnění pokynů**: když uživatel projde, aplikace může pravidelně počítat novou pozici mezi zařízením a cílovou kotvou. Aplikace bude pokračovat v doladění pokynů pro pokyny, které uživateli pomůžou dorazit na cíl.

    ![Příklad, jak může aplikace zobrazit pokyny pro hledání](./media/meeting-spot.png)

## <a name="connect-anchors"></a>Spojit kotvy

Chcete-li vytvořit způsob, jak najít, je nejprve nutné umístit kotvy do zvolených umístění. V této části předpokládáme, že správce aplikace už dokončil tuto práci.

### <a name="connect-anchors-in-a-single-session"></a>Připojit kotvy v jedné relaci

Připojení kotev:

1. Projde k prvnímu umístění a vytvoří kotvu a pomocí CloudSpatialAnchorSession.
2. Projde k druhému umístění. Základní platforma MR/AR sleduje pohyb.
3. Vytvořte kotvu B pomocí stejného CloudSpatialAnchorSession. Kotvy a a B jsou nyní připojeny. Služba prostorových ukotvení udržuje tento vztah.
4. Pokračujte postupem pro zbývající kotvy.

### <a name="connect-anchors-in-multiple-sessions"></a>Spojit kotvy ve více relacích

Prostorové kotvy můžete propojit s více relacemi. Pomocí této metody můžete vytvořit a propojit některé kotvy najednou a pak později vytvořit a připojit další kotvy.

Připojení kotev přes více relací:

1. Aplikace vytvoří některé kotvy v jednom CloudSpatialAnchorSession.
2. V jinou dobu aplikace vyhledá jednu z těchto kotev (například kotvu A) pomocí nového CloudSpatialAnchorSession.
3. Procházení k novému umístění. Pohyb vychází z základní nebo rozšířené platformy pro hybridní realitu.
4. Vytvořte kotvu C pomocí stejného CloudSpatialAnchorSession. Kotvy a, B a C jsou nyní připojeny. Služba prostorových ukotvení udržuje tento vztah.

Tento postup můžete i nadále používat pro další kotvy a další relace v průběhu času.

### <a name="verify-anchor-connections"></a>Ověřit kotvy připojení

Aplikace může ověřit, zda jsou dvě kotvy propojeny vyvoláním dotazu pro okolní kotvy, které provedete nastavením `NearAnchorCriteria` na `CloudSpatialAnchorWatcher` . Pokud výsledek dotazu obsahuje cílovou kotvu, je připojení ukotvení ověřeno. Pokud kotvy nejsou připojené, aplikace se může pokusit o jejich připojení znovu.

Zde jsou některé důvody, proč se mohou kotvy nedaří připojit:

* V průběhu procesu připojení kotev se ztratilo sledování v rámci smíšené reality nebo rozšíření realit.
* Kvůli chybě sítě během komunikace se službou prostorové kotvy nelze připojení k kotvám zachovat.

### <a name="find-sample-code"></a>Najít vzorový kód

Chcete-li najít vzorový kód, který ukazuje, jak propojit kotvy a dělat okolní dotazy, přečtěte si téma " [prostorové kotvy ukázkové aplikace](https://github.com/Azure/azure-spatial-anchors-samples)".
