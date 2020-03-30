---
title: Kotevní vztahy a hledání cest
description: Další informace o koncepčním modelu za vztahy ukotvení. Naučte se připojit kotvy v prostoru a používat rozhraní API v okolí ke splnění scénáře hledání cesty.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: f2fd8f4b7d03be8822c3ec12e2be589054942ce3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270599"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Ukotvení vztahů a hledání cest v prostorových kotvách Azure

Pomocí kotevních vztahů můžete vytvořit připojené kotvy v prostoru a pak klást otázky, jako jsou tyto:

* Jsou tu poblíž kotvy?
* Jak daleko jsou?

## <a name="examples"></a>Příklady

Připojené kotvy můžete použít v případech, jako jsou tyto:

* Pracovník musí dokončit úkol, který zahrnuje návštěvu různých míst v průmyslové továrně. Továrna má prostorové kotvy na každém místě. HoloLens nebo mobilní aplikace pomáhá vést pracovníka z jednoho místa na druhé. Aplikace nejprve požádá o blízké prostorové kotvy a poté navede pracovníka na další místo. Aplikace vizuálně zobrazuje obecný směr a vzdálenost k dalšímu umístění.

* Muzeum vytváří prostorové kotvy na veřejných výstavách. Společně tyto kotvy tvoří hodinovou prohlídku základních veřejných displejů muzea. Na veřejném displeji mohou návštěvníci otevřít aplikaci muzea pro smíšenou realitu na svém mobilním zařízení. Pak nasměrují svou telefonní kameru kolem prostoru, aby viděli obecný směr a vzdálenost k ostatním veřejným displejům na prohlídce. Když uživatel přejde k veřejnému displeji, aplikace aktualizuje obecný směr a vzdálenost, aby pomohla uživatele vést.

## <a name="set-up-way-finding"></a>Nastavení způsobu hledání

Aplikace, která používá směr zorného pole a vzdálenost mezi kotvami k navádění, používá *way-finding*. Way-finding se liší od navigace úplně za otočnou. V turn-by-turn navigace, uživatelé jsou vedeni kolem stěn, přes dveře, a mezi patry. Při hledání cesty získá uživatel rady o obecném směru cíle. Ale odvození nebo znalost prostoru také pomáhá uživateli procházet strukturou k cíli.

Chcete-li vytvořit způsob hledání prostředí, nejprve připravit prostor pro zkušenosti a vyvinout aplikaci, která uživatelé budou komunikovat s. Jedná se o koncepční kroky:

1. **Naplánujte prostor**: Rozhodněte se, která místa v prostoru budou součástí zážitku z hledání cesty. V našich scénářích se může vedoucí továrny nebo koordinátor prohlídky muzea rozhodnout, která místa mají být zahrnuta do zážitku z hledání cesty.
2. **Připojit kotvy**: Navštivte vybraná místa a vytvořte prostorové kotvy. Můžete to udělat v režimu správce aplikace koncového uživatele nebo v úplně jiné aplikaci. Připojíte se nebo se připojíte ke každé kotvě s ostatními. Služba udržuje tyto vztahy.
3. **Spuštění prostředí pro koncové uživatele**: Uživatelé spouštějí aplikaci a vyhledávají kotvu, která může být v libovolném z vybraných umístění. Celkový návrh by měl určit umístění, kam mohou uživatelé vstoupit do prostředí.
4. **Najít kotvy v okolí**: Poté, co uživatel najde kotvu, může aplikace požádat o blízké kotvy. Tento postup vrátí představovat mezi zařízením a tyto kotvy.
5. **Průvodce uživatele**: Aplikace může použít pózu pro každou z těchto kotev, aby poskytla vodítko o obecném směru a vzdálenosti uživatele. Například zdroj kamery v aplikaci může zobrazovat ikonu a šipku představující každý potenciální cíl, jak ukazuje následující obrázek.
6. **Upřesnit pokyny**: Jak uživatel chodí, aplikace může pravidelně vypočítat novou pózu mezi zařízením a cílovou kotvou. Aplikace pokračuje v upřesnění pokynů, které pomáhají uživateli dorazit do cíle.

    ![Příklad toho, jak může aplikace zobrazit pokyny k hledání způsobu hledání](./media/meeting-spot.png)

## <a name="connect-anchors"></a>Připojení kotev

Chcete-li vytvořit způsob hledání zkušenosti, musíte nejprve umístit kotvy ve vybraných místech. V této části budeme předpokládat, že správce aplikace již dokončil tuto práci.

### <a name="connect-anchors-in-a-single-session"></a>Připojení kotev v jedné relaci

Připojení kotev:

1. Přechod na první umístění a vytvořit kotvu A pomocí CloudSpatialAnchorSession.
2. Jděte na druhé místo. Základní platforma MR/AR sleduje pohyb.
3. Vytvořte kotvu B pomocí stejné Hody CloudSpatialAnchorSession. Kotvy A a B jsou nyní připojeny. Služba Prostorové kotvy udržuje tento vztah.
4. Pokračujte v postupu pro zbývající kotvy.

### <a name="connect-anchors-in-multiple-sessions"></a>Připojení kotev ve více relacích

Prostorové kotvy můžete propojit s více relacemi. Pomocí této metody můžete vytvořit a připojit některé kotvy najednou a později vytvořit a připojit více kotev.

Připojení kotev k více relacím:

1. Aplikace vytvoří některé kotvy v jednom CloudSpatialAnchorSession.
2. V jiném čase aplikace vyhledá jednu z těchto kotev (například Anchor A) pomocí nové Horečné CloudSpatialAnchorSession.
3. Procházka na nové místo. Základní platforma smíšené reality nebo rozšířené reality sleduje pohyb.
4. Vytvořte kotvu C pomocí stejné Hody CloudSpatialAnchorSession. Kotvy A, B a C jsou nyní připojeny. Služba Prostorové kotvy udržuje tento vztah.

Tento postup můžete v průběhu času pokračovat pro více ukotvení a další relace.

### <a name="verify-anchor-connections"></a>Ověření kotevních připojení

Aplikace můžete ověřit, že dvě kotvy jsou připojeny vydáním dotazu pro blízké kotvy. Pokud výsledek dotazu obsahuje cílovou kotvu, je ověřeno připojení ukotvení. Pokud kotvy nejsou připojené, aplikace se je může pokusit znovu připojit.

Zde jsou některé důvody, proč se kotevy nemusí připojit:

* Základní platforma smíšené reality nebo rozšířené reality ztratila sledování během procesu připojování kotev.
* Z důvodu chyby sítě během komunikace se službou Prostorové kotvy nelze připojení ukotvení trvalé.

### <a name="find-sample-code"></a>Najít ukázkový kód

Ukázkový kód, který ukazuje, jak připojit kotvy a dělat dotazy v okolí, najdete [v tématu Prostorové kotvy ukázkové aplikace](https://github.com/Azure/azure-spatial-anchors-samples).
