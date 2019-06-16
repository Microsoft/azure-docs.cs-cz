---
title: Kotvy vztahů a způsob hledání v prostorových kotvy Azure | Dokumentace Microsoftu
description: Další informace o konceptuálního modelu za kotvy vztahů. Další informace pro připojení ukotvení v rámci mezerou a k používání rozhraní API blízké ke splnění scénáři způsob hledání.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 008269a5883750dc8899d896c101c6a05bf7e814
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65969282"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Kotvy vztahů a způsob hledání v prostorových kotev vztahů Azure

Pomocí kotvy vztahů můžete vytvořit propojené ukotvení v prostoru a potom klást otázky, jako jsou tyto:

* Existují kotvy okolních?
* Jak daleko je mezi nimi?

## <a name="examples"></a>Příklady

V takových případech můžete použít připojené kotev vztahů:

* Pracovní proces potřebuje k dokončení úkolu, který zahrnuje navštívit různých umístěních v objektu pro vytváření průmyslu. Objekt pro vytváření prostorových kotvy má v každém umístění. HoloLens nebo mobilní aplikace pomůže průvodce pracovní proces z jednoho umístění do druhého. Aplikace žádá nejprve blízké prostorových ukotvení a pak provede pracovního procesu k další oblasti. Aplikace bude zobrazovat vizuálně Obecné směr a vzdálenost k další oblasti.

* Muzejních vytvoří prostorových kotvy na veřejné zobrazí. Společně tyto kotvy tvoří hodinová si muzejních základní veřejné zobrazí. Na veřejné zobrazení můžete otevřít návštěvníků muzejních pro hybridní realitu na svém mobilním zařízení. Potom ukazují kameře phone kolem místa, které chcete zobrazit obecné směr a vzdálenost k jiné veřejné zobrazí na prohlídku. Uživatel provede směrem k veřejné zobrazení, aplikace aktualizuje Obecné směr a vzdálenost provedou uživatele.

## <a name="set-up-way-finding"></a>Nastavit způsob hledání

Aplikaci, která využívá dohlednost směr a vzdálenost mezi ukotvení a přidal se návod používá *způsob zjištění*. Najít způsob, jak se liší od zapnout vypnout navigaci. V navigačním panelu na řadě vy zapnout která uživatele provádějí kolem stěn prostřednictvím dveře a mezi podlahy. Uživatel s způsob zjištění, získá tipy týkající se obecné směr cíl. Ale odvození nebo znalostní báze prostoru také pomáhá uživatelům procházet strukturou do cíle.

K vytvoření prostředí způsob zjištění, nejprve připravit mezeru prostředí a vývoj aplikací, které budou uživatelé používat. Jedná se o koncepční kroky:

1. **Plánování prostor**: Rozhodněte, která umístění v rámci prostoru bude součástí prostředí způsob hledání. Ve scénáři nadřízený objekt pro vytváření nebo koordinátor tour muzejních rozhodnout zahrnout do prostředí způsob zjištění umístění.
2. **Připojit kotvy**: Navštivte zvolené umístění vytvořit prostorových ukotvení. Můžete to provedete v režimu správce aplikace pro koncové uživatele, nebo v jiné aplikaci zcela. Budete připojení nebo se týkají ukotvení jednotlivých ostatním. Služba zajišťuje tyto vztahy.
3. **Spuštění prostředí pro koncové uživatele**: Uživatelé spustí aplikaci najít anchor, který může být v některém z vybrané umístění. Celkový návrh by měl určit umístění, kde mohou uživatelé zadat prostředí.
4. **Vyhledání okolního kotvy**: Poté, co uživatel vyhledá anchor, požádat o okolních kotvy aplikace. Tento postup vrátí pozici mezi zařízením a tyto ukotvení.
5. **Příručka pro uživatele**: Aplikace můžete používat póza ke každé z těchto kotvy poskytnout pokyny týkající se obecné směr uživatele a vzdálenost. Fotoaparát v aplikaci může například zobrazit ikonu a šipku pro reprezentaci jednotlivé potenciální cíle, jak ukazuje následující obrázek.
6. **Upřesnění pokynů**: Jako uživatel provede, můžete aplikaci pravidelně vypočítat nové pozice mezi zařízením a určení ukotvení. Aplikace dál Upřesnit odkazy na pokyny, které uživatel přejít na cíl.

    ![Příklad, jak aplikace může zobrazit způsob zjištění pokyny](./media/meeting-spot.png)

## <a name="connect-anchors"></a>Připojit kotvy

K vytvoření prostředí způsob zjištění, musíte nejprve umístit do zvoleného umístění ukotvení. V této části budeme předpokládat, že správce aplikace již byla dokončena tuto práci.

### <a name="connect-anchors-in-a-single-session"></a>Připojit ukotvení v jedné relaci

Připojení kotev vztahů:

1. Procházet na první místo a vytvořte ukotvení A s využitím CloudSpatialAnchorSession.
2. Procházet k druhé umístění. Základní platforma MR/AR sleduje přesun.
3. Vytvoříte pomocí stejného CloudSpatialAnchorSession B ukotvení. Ukotvení A a B teď připojeni. Služba prostorových kotvy udržuje tuto relaci.
4. Postup pro zbývající kotvy pokračujte.

### <a name="connect-anchors-in-multiple-sessions"></a>Připojit do několika seminářů zaměřených na ukotvení

Prostorové ukotvení se můžete připojit přes více relací. Pomocí této metody můžete vytvořit a současně připojit některé kotvy vztahů a potom později vytvořit a připojit další kotvy vztahů. 

Připojení přes více relací kotev vztahů:

1. Aplikace vytvoří v jedné CloudSpatialAnchorSession některé ukotvení. 
2. V jinou dobu vyhledá aplikace jednu z těchto kotvy (například ukotvení A) s použitím nového CloudSpatialAnchorSession.
3. Procházení do nového umístění. Základní platforma realitu nebo rozšířené reality sleduje přesun.
4. Vytvoříte pomocí stejného CloudSpatialAnchorSession C ukotvení. Ukotvení A, B a C se teď připojení. Služba prostorových kotvy udržuje tuto relaci.

Tento postup pro další ukotvení a další relace můžete pokračovat v průběhu času.

### <a name="verify-anchor-connections"></a>Ověření připojení ukotvení

Aplikace můžete ověřit, že dvě kotvy vztahů připojeni pomocí dotazu pro blízké kotev vztahů. Když výsledek tohoto dotazu obsahuje cílový ukotvení, ověření připojení ukotvení. Pokud nejste připojeni ukotvení, můžete zkusit aplikaci znovu připojte. 

Tady jsou některé důvody, proč nemusí podařit připojit kotev vztahů:

* Základní realitu nebo rozšířené reality platformy ztráty sledování během procesu připojování ukotvení.
* Kvůli chybě sítě při komunikaci se službou prostorových ukotvení nelze trvale uložit připojení ukotvení.

### <a name="find-sample-code"></a>Najít ukázky kódu

Ukázkový kód, který ukazuje, jak připojit ukotvení a provádět okolních dotazů najdete v tématu [prostorových kotvy ukázkové aplikace](https://github.com/Azure/azure-spatial-anchors-samples).
