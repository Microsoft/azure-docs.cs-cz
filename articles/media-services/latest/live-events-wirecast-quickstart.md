---
title: Vytvoření živého datového proudu Mediálních služeb Azure
description: Zjistěte, jak vytvořit živý přenos Mediálních služeb Azure pomocí portálu a drátového vysílání.
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/25/2020
ms.openlocfilehash: 459f11844f873a911b3e5702e8c768b1cd22e504
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984993"
---
# <a name="create-an-azure-media-services-live-stream"></a>Vytvoření živého datového proudu Mediálních služeb Azure

Tento rychlý start vám pomůže vytvořit živý přenos Mediálních služeb Azure pomocí portálu Azure a telestreamového drátového vysílání. Předpokládá, že máte předplatné Azure a vytvořili účet mediálních služeb.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Otevřete svůj webový prohlížeč a přejděte na [portál Microsoft Azure](https://portal.azure.com/). Zadejte přihlašovací údaje pro přihlášení k portálu. Výchozím zobrazením je váš řídicí panel služby.

V tomto rychlém startu se budeme zabývat:

- Nastavení místního kodéru s bezplatnou zkušební verzí telestreamového wirecastu.
- Nastavení živého přenosu.
- Nastavení výstupů živého datového proudu.
- Spuštění výchozího koncového bodu streamování.
- Pomocí Programu Azure Media Player můžete zobrazit živý přenos a výstup na vyžádání.

Aby to bylo jednoduché, použijeme přednastavení kódování pro Azure Media Services v Wirecastu, předávací kódování v cloudu a RTMP.

## <a name="set-up-an-on-premises-encoder-by-using-wirecast"></a>Nastavení místního kodéru pomocí wirecastu

1. Stáhněte a nainstalujte Wirecast pro svůj operační systém na [webových stránkách Telestream](https://www.telestream.net).
1. Spusťte aplikaci a použijte svou oblíbenou e-mailovou adresu k registraci produktu. Udržujte aplikaci otevřenou.
1. V e-mailu, který obdržíte, ověřte svou e-mailovou adresu. Pak aplikace spustí bezplatnou zkušební verzi.
1. Doporučeno: Podívejte se na instruktážní video na úvodní obrazovce aplikace.

## <a name="set-up-an-azure-media-services-live-stream"></a>Nastavení živého datového proudu Mediálních služeb Azure

1. Přejděte na účet Azure Media Services na portálu a pak ze seznamu **Mediálních služeb** vyberte **Živé streamování.**

   ![Odkaz na živé vysílání](media/live-events-wirecast-quickstart/select-live-streaming.png)
1. Chcete-li vytvořit novou událost živého streamování, vyberte **Přidat živou událost.**

   ![Ikona Přidat živou událost](media/live-events-wirecast-quickstart/add-live-event.png)
1. Do pole **Název živé události** zadejte název nové události, například *TestLiveEvent*.

   ![Pole s názvem živé události](media/live-events-wirecast-quickstart/live-event-name.png)
1. Do pole **Popis** zadejte volitelný popis události.
1. Vyberte **možnost Předávací – bez možnosti kódování cloudu.**

   ![Možnost kódování za mrakem](media/live-events-wirecast-quickstart/cloud-encoding.png)
1. Vyberte možnost **RTMP.**
1. Ujistěte se, že je pro **spustit živou událost**vybraná možnost **Ne** , abyste se vyhnuli účtování za živou událost, než bude připravena. (Fakturace začne při spuštění živé události.)

   ![Možnost Spustit živou událost](media/live-events-wirecast-quickstart/start-live-event-no.png)
1. Chcete-li zkontrolovat nastavení, vyberte tlačítko **Revize + vytvořit.**
1. Chcete-li vytvořit živou událost, vyberte tlačítko **Vytvořit.** Poté se vrátíte do seznamu živých událostí.
1. Vyberte odkaz na živou událost, kterou jste právě vytvořili. Všimněte si, že vaše událost je zastavena.
1. Ponechte tuto stránku otevřenou v prohlížeči. Vrátíme se k tomu později.

## <a name="set-up-a-live-stream-by-using-wirecast-studio"></a>Nastavení živého přenosu pomocí aplikace Wirecast Studio

1. V aplikaci Drátové vysílání vyberte v hlavní nabídce **možnost Vytvořit prázdný dokument** a pak vyberte **Pokračovat**.

   ![Úvodní obrazovka wirecastu](media/live-events-wirecast-quickstart/open-empty-document.png)
1. Najeďte přes první vrstvu v oblasti **Vrstvy drátového vysílání.**  Vyberte ikonu **Přidat,** která se zobrazí, a vyberte vstup videa, který chcete streamovat.

   ![Ikona přidání wirecastu](media/live-events-wirecast-quickstart/add-icon.png)

   Otevře se dialogové okno **Hlavní vrstva 1.**
1. V nabídce vyberte **Snímání videa** a pak vyberte fotoaparát, který chcete použít.

   ![Oblast náhledu pro digitalizaci videa](media/live-events-wirecast-quickstart/video-shot-selection.png)

   Pohled z kamery se zobrazí v oblasti náhledu.
1. Najeďte přes druhou vrstvu v oblasti **Vrstvy drátového tažného vysílání.** Vyberte ikonu **Přidat,** která se zobrazí, a vyberte zvukový vstup, který chcete streamovat. Otevře se dialogové okno **Hlavní vrstva 2.**
1. V nabídce vyberte **Záznam zvuku** a pak vyberte zvukový vstup, který chcete použít.

   ![Vstupy pro snímání zvuku](media/live-events-wirecast-quickstart/audio-shot-select.png)
1. V hlavní nabídce vyberte **Nastavení výstupu**. Zobrazí se dialogové okno **Vybrat cíl výstupu.**
1. V rozevíracím seznamu **Cíl** vyberte **Mediální služby Azure.** Nastavení výstupu pro Azure Media Services automaticky naplní *většinu* nastavení výstupu.

   ![Obrazovka nastavení výstupu wirecast](media/live-events-wirecast-quickstart/azure-media-services.png)


V dalším postupu se vrátíte do Služby Azure Media Services ve vašem prohlížeči a zkopírujete vstupní adresu URL a zadejte do nastavení výstupu:

1. Na stránce Azure Media Services na portálu vyberte **Start** a spusťte událost živého přenosu. (Fakturace začíná právě teď.)

   ![Ikona Start](media/live-events-wirecast-quickstart/start.png)
2. Nastavte přepínač **Zabezpečený/nezabezpečený** na **Nezabezpečený**. Tento krok nastaví protokol rtmp místo RTMPS.
3. V poli **Vstupní adresa URL** zkopírujte adresu URL do schránky.

   ![Vstupní adresa URL](media/live-events-wirecast-quickstart/input-url.png)
4. Přepněte do aplikace Wirecast a vložte **vstupní adresu URL** do pole **Adresa** ve výstupním nastavení.

   ![Vstupní adresa URL drátového vysílání](media/live-events-wirecast-quickstart/input-url-wirecast.png)
5. Vyberte **OK**.

## <a name="set-up-outputs"></a>Nastavení výstupů

Tato část nastaví výstupy a umožní vám uložit záznam vašeho živého streamu.  

> [!NOTE]
> Chcete-li streamovat tento výstup, musí být spuštěn koncový bod streamování. Viz další [spuštění výchozího koncového bodu streamování.](#run-the-default-streaming-endpoint)

1. Pod prohlížečem **videa Výstupy vyberte** odkaz **Vytvořit výstupy.**
1. Pokud chcete, upravte název výstupu v poli **Název** na něco uživatelsky přívětivějšího, aby bylo možné ho později snadno najít.
   
   ![Pole výstupního názvu](media/live-events-wirecast-quickstart/output-name.png)
1. Nech všechny ty krabice na pokoji.
1. Výběrem **možnosti Další** přidáte lokátor streamování.
1. Změňte název lokátoru na něco uživatelsky přívětivějšího, pokud chcete.
   
   ![Pole s názvem lokátoru](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Všechno ostatní na téhle obrazovce zatím nech na pokoji.
1. Vyberte **Vytvořit**.

## <a name="start-the-broadcast"></a>Spuštění vysílání

1. V wirecastu vyberte **spuštění výstupu** > a zastavení vysílání spusťte**Start / Stop Broadcasting** > **mediální služby Azure: Mediální služby Azure** z hlavní nabídky.

   ![Spustit položky nabídky vysílání](media/live-events-wirecast-quickstart/start-broadcast.png)

   Po odeslání datového proudu do živé události se v přehrávači videa na stránce živé události ve službě Azure Media Services zobrazí okno **Živé** vysílání v wirecastu.

1. Vyberte tlačítko **Přejít** pod oknem náhledu a začněte vysílat video a zvuk, které jste vybrali pro vrstvy wirecast.

   ![Tlačítko Přejít wirecast](media/live-events-wirecast-quickstart/go-button.png)

   > [!TIP]
   > Pokud dojde k chybě, zkuste přehrávač znovu načíst tak, že nad hráčem vyberete odkaz **Znovu načíst přehrávač.**

## <a name="run-the-default-streaming-endpoint"></a>Spuštění výchozího koncového bodu streamování

1. V seznamu Media Services vyberte **koncové body streamování.**

   ![Položka nabídky Datový proud koncových bodů](media/live-events-wirecast-quickstart/streaming-endpoints.png)
1. Pokud je výchozí stav koncového bodu streamování zastavený, vyberte ho. Tento krok přejdete na stránku pro tento koncový bod.
1. Vyberte **Spustit**.
   
   ![Tlačítko Start pro koncový bod streamování](media/live-events-wirecast-quickstart/start.png)

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>Přehrání výstupního vysílání pomocí programu Azure Media Player

1. Zkopírujte adresu URL streamování do přehrávače **výstupního** videa.
1. Ve webovém prohlížeči otevřete [ukázku přehrávače Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html).
1. Vložte adresu URL streamování do pole **URL** v programu Azure Media Player.
1. Vyberte tlačítko **Aktualizovat přehrávač.**
1. Chcete-li zobrazit živý přenos, vyberte ve videu ikonu **Přehrát.**

## <a name="stop-the-broadcast"></a>Zastavení vysílání

Když si myslíte, že jste streamovali dostatek obsahu, zastavte vysílání.

1. V části Wirecast vyberte tlačítko **Vysílání.** Tento krok zastaví vysílání z Wirecast.
1. Na portálu vyberte **Zastavit**. Poté se zobrazí varovná zpráva, že živý přenos se zastaví, ale výstup se nyní stane aktivem na vyžádání.
1. V yarovné zprávě vyberte **Zastavit.** Azure Media Player teď zobrazuje chybu, protože živý přenos už není k dispozici.

## <a name="play-the-on-demand-output-by-using-azure-media-player"></a>Přehrání výstupu na vyžádání pomocí programu Azure Media Player

Výstup, který jste vytvořili, je teď k dispozici pro streamování na vyžádání, dokud je spuštěn koncový bod streamování.

1. Přejděte do seznamu Mediálních služeb a vyberte **položku Datové zdroje**.
1. Najděte výstup události, který jste vytvořili dříve, a vyberte odkaz na datový zdroj. Otevře se stránka výstupu datového zdroje.
1. Zkopírujte adresu URL streamování pod přehrávačem videa pro datový zdroj.
1. Vraťte se do Programu Azure Media Player v prohlížeči a vložte adresu URL streamování do pole URL.
1. Vyberte **možnost Aktualizovat přehrávač**.
1. Výběrem ikony **Přehrát** ve videu zobrazíte datový zdroj na vyžádání.

## <a name="clean-up-resources"></a>Vyčištění prostředků

> [!IMPORTANT]
> Zastavte služby! Po dokončení kroků v tomto rychlém startu nezapomeňte zastavit živou událost a koncový bod streamování, nebo se vám bude účtovat čas, kdy zůstanou spuštěny. Chcete-li zastavit živou událost, podívejte se na postup [Zastavení vysílání,](#stop-the-broadcast) kroky 2 a 3.

Zastavení koncového bodu streamování:

1. V seznamu Media Services vyberte **položku Koncové body streamování**.
2. Vyberte výchozí koncový bod streamování, který jste začali dříve. Tento krok otevře stránku koncového bodu.
3. Vyberte **Zastavit**.

> [!TIP]
> Pokud nechcete zachovat datové zdroje z této události, nezapomeňte je odstranit, abyste se neúčtovali úložiště.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Živé události a živé výstupy v Mediálních službách](./live-events-outputs-concept.md)
