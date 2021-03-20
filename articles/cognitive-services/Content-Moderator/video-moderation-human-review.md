---
title: Moderování videa pomocí nástroje pro kontrolu – Content Moderator
titleSuffix: Azure Cognitive Services
description: Použití moderování videa s asistencí počítače a nástroje pro kontrolu k mírnému nevhodnému obsahu
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: pafarley
ms.openlocfilehash: 05ece4d8ea24fb9da106ec3daa2cc1a63706dab9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96853494"
---
# <a name="video-moderation-with-the-review-tool"></a>Moderování videa pomocí nástroje pro kontrolu

K dosažení nejlepších výsledků pro vaši firmu použijte nástroj pro [moderování](video-moderation-api.md) a [kontrolu](Review-Tool-User-Guide/human-in-the-loop.md) videa, který je Content moderator s podporou počítačů, a získejte tak střední videa a Přepisy obsahu pro dospělé (explicitní) a pikantní (sugestivní).

## <a name="view-videos-under-review"></a>Zobrazit videa v rámci revize

Na řídicím panelu vyberte některou z front recenzí v rámci typu obsahu videa. Tím se spustí Kontrola a otevře se stránka pro moderování obsahu videa.

> [!div class="mx-imgBorder"]
> ![V Content Moderator (Preview) se posuvník zvýrazní a nastaví na 4 recenze. Rozostření všech a černých a bílých přepínačů je zvýrazněno a obě jsou nastaveny.](./Review-Tool-User-Guide/images/video-moderation-detailed.png)

### <a name="review-count"></a>Review count

Pomocí posuvníku vpravo nahoře nastavte počet revizí, které chcete zobrazit na stránce.

### <a name="view-type"></a>Typ zobrazení

Různé položky obsahu můžete zobrazit jako dlaždice nebo v podrobném zobrazení. **Podrobné** zobrazení vám umožní zobrazit klíčové snímky a další informace o vybraném videu. 

> [!NOTE]
> Místo vyplňování snímků v pravidelných intervalech služba pro moderování videa identifikuje a produkuje pouze potenciálně kompletní (dobré) snímky. Tato funkce umožňuje efektivní generování snímků pro dospělé a pikantní analýzu na úrovni snímků.

Zobrazení **vedle sebe** zobrazí každé video jako jednu dlaždici. Pokud chcete toto video zvětšit a Skrýt ostatní, vyberte tlačítko Rozbalit nad rámec videa.

### <a name="content-obscuring-effects"></a>Efekty překrývající obsah

Pomocí **rozostření všech** a **černého a bílé** přepínacího panelu nastavte tyto efekty, které se překrývají. Ve výchozím nastavení jsou zapnuté. V zobrazení **vedle sebe** můžete efekty přepínat jednotlivě pro každé video.

## <a name="check-video-details"></a>Podrobnosti o kontrole videa

V **podrobném** zobrazení se v pravém podokně zobrazí několik karet, které vám poskytnou podrobnosti o videu.

* Vyberte kartu **poznámky** a přidejte do videí vlastní poznámky.
* Vyberte kartu **přepis** , aby se zobrazil přepis videa &mdash; , který služba automaticky extrahuje přepis všech řeči ve videu. Když vyberete část textu, přehrávač videa se přeskočí do této části videa.
* Vyberte kartu **meta-data** a zobrazte metadata videosouborů.
* Vyberte kartu **Historie** , abyste viděli historii revize, například čas vytvoření a způsob úpravy.

> [!div class="mx-imgBorder"]
> ![Je zvýrazněno pravé podokno a je vybrána karta poznámky. K dispozici je oblast testu s označením přidat poznámku.](./Review-Tool-User-Guide/images/video-moderation-video-details.png)

## <a name="apply-moderation-tags"></a>Použít značky moderování

Hlavní úlohou kontroly videa je použití nebo odebrání značek moderování na videích a částech videí.

### <a name="bulk-tagging"></a>Hromadné označování

Panel nástrojů **hromadných značek** umožňuje přidat značky k několika vybraným videím najednou. Vyberte jedno nebo více videí, vyberte značky, které chcete použít, a klikněte na **Odeslat**. 

> [!div class="mx-imgBorder"]
> ![Tlačítko + je v podokně hromadné značky zvýrazněné.](./Review-Tool-User-Guide/images/video-moderation-bulk-tags.png)


### <a name="key-frame-tagging"></a>Označování klíčových snímků

Můžete také přidat značky moderování do konkrétních klíčových snímků. Vyberte snímky z podokna dlaždice klíčového rámce a potom vyberte **značky klíčového snímku +** pro použití požadovaných značek.

> [!NOTE]
> Pokud služba nemohla extrahovat klíčové snímky, nezobrazí se v podokně dlaždice klíčový rámec **žádné dostupné snímky** a možnost výběru klíčových snímků bude zobrazena šedě. V takovém případě můžete použít pouze značky na video jako celek (pomocí **značek videa +** tlačítko).

> [!div class="mx-imgBorder"]
> ![Zobrazují se všechny podoken dlaždice, přehrávač videa, značky klíčových snímků a okna značek videí. Značky klíčového snímku + a značky videa + se zvýrazní.](./Review-Tool-User-Guide/images/video-moderation-tagging-options.png)

## <a name="put-a-review-on-hold"></a>Umístit recenzi na blokování

Tlačítko **blokovat** v dolní části podokna video vám umožní blokovat blokování, abyste ho mohli načíst a dokončit později. Můžete to provést pro kontrolu, která vyžaduje konzultaci od jiného člena nebo správce týmu, který momentálně není k dispozici. 

Videa, která jsou podržená, můžete zobrazit kliknutím na tlačítko **blokovat** v horní části obrazovky. Na pravé straně se zobrazí podokno blokování. Odtud můžete vybrat více kontrol podržených a buď je uvolnit zpátky do fronty, nebo nastavit čas jejich vypršení platnosti. Po předkonfigurovaném časovém intervalu se kontroly blokování uvolní zpátky do fronty. Vyberte **Uložit** a začněte počítat z aktuálně vybraného času vypršení platnosti.

> [!div class="mx-imgBorder"]
> ![V podokně video se zvýrazní tlačítko Blokovat. V dolní části podokna se zvýrazní pole se seznamem doba blokování spolu s tlačítky uvolnit a uložit.](./Review-Tool-User-Guide/images/video-moderation-hold.png)

## <a name="submit-a-review"></a>Odeslat recenzi

Po použití značek vyberte tlačítko **Odeslat** ve spodní části podokna video. Pokud jste označili více videí, můžete je odeslat v rámci jedné revize nebo jako samostatné recenze.

## <a name="limbo-state"></a>Stav Limbo

Po odeslání recenze se video přesune do stavu **Limbo** , který můžete zobrazit tak, že v horní části obrazovky vyberete tlačítko **Limbo** . Videa zůstávají ve stavu Limbo pro předem nakonfigurované množství času (které můžete změnit v nabídce v dolní části) nebo dokud nebudou znovu zkontrolována nebo ručně odeslána.

Jakmile videa vyprší od Limbo, jejich recenze se označí jako dokončené.

## <a name="next-steps"></a>Další kroky

- Začněte s [rychlým startem pro moderování videa](video-moderation-api.md).
- Naučte se generovat [recenze videí](video-reviews-quickstart-dotnet.md) pro své lidské kontrolory ze svého moderovaného výstupu.
- Přidejte [recenze přepisu videa](video-transcript-reviews-quickstart-dotnet.md) do recenze videí.
