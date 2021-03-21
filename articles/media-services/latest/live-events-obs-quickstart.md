---
title: Vytvoření živého datového proudu pomocí OBS studia
description: Naučte se vytvářet Azure Media Services živý datový proud pomocí portálu a OBS Studio.
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 08/31/2020
ms.openlocfilehash: 83f072b67f5aa162137a55d2b311dccf0daf7f53
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98956068"
---
# <a name="create-an-azure-media-services-live-stream-with-obs"></a>Vytvoření živého datového proudu Azure Media Services pomocí OBS

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Tento rychlý Start vám pomůže vytvořit Azure Media Services živý datový proud pomocí Azure Portal a otevřít OBS (Open pro vysílání studia). Předpokládá, že máte předplatné Azure a máte vytvořený účet Media Services.

V tomto rychlém startu pokryjeme:

- Nastavení místního kodéru pomocí OBS.
- Nastavuje se živý datový proud.
- Nastavení výstupů živého streamu
- Spouští se výchozí koncový bod streamování.
- Použití Azure Media Player k zobrazení živého streamu a výstupu na vyžádání.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Otevřete webový prohlížeč a přejdete na [portál Microsoft Azure](https://portal.azure.com/). Zadejte přihlašovací údaje pro přihlášení k portálu. Výchozím zobrazením je váš řídicí panel služby.

## <a name="set-up-an-on-premises-encoder-by-using-obs"></a>Nastavení místního kodéru pomocí OBS

1. Stáhněte a nainstalujte si OBS pro váš operační systém na [webu Open Broadcast software](https://obsproject.com/).
1. Spusťte aplikaci a nechte ji otevřenou.

## <a name="run-the-default-streaming-endpoint"></a>Spustit výchozí koncový bod streamování

1. V seznamu Media Services vyberte **koncové body streamování** .

   ![Položka nabídky koncových bodů streamování](media/live-events-obs-quickstart/streaming-endpoints.png)
1. Pokud je stav výchozího koncového bodu streamu zastavený, vyberte ho. Tento krok vás přesměruje na stránku tohoto koncového bodu.
1. Vyberte **Spustit**.

   ![Tlačítko Start pro koncový bod streamování](media/live-events-obs-quickstart/start.png)

## <a name="set-up-an-azure-media-services-live-stream"></a>Nastavení Azure Media Services živého streamu

1. Na portálu klikněte na účet Azure Media Services a potom v seznamu **Media Services** vyberte **živé streamování** .

   ![Odkaz živého streamování](media/live-events-obs-quickstart/select-live-streaming.png)
1. Vyberte **Přidat živou událost** a vytvořte novou událost živého streamování.

   ![Přidat živou ikonu události](media/live-events-obs-quickstart/add-live-event.png)
1. Do pole **název živé události** zadejte název nové události, například *TestLiveEvent*.

   ![Pole název živé události](media/live-events-obs-quickstart/live-event-name.png)
1. Do pole **Popis** Zadejte nepovinný popis události.
1. Vyberte možnost **předávat – bez cloudového kódování** .

   ![Možnost kódování cloudu](media/live-events-obs-quickstart/cloud-encoding.png)
1. Vyberte možnost **RTMP** .
1. Ujistěte se, že není vybraná **žádná** možnost pro **spuštění živé události**, abyste se vyhnuli fakturaci za živou událost předtím, než bude připravená. (Fakturace bude zahájena při spuštění živé události.)

   ![Možnost spuštění živé události](media/live-events-obs-quickstart/start-live-event-no.png)
1. Kliknutím na tlačítko **Revize + vytvořit** zkontrolujte nastavení.
1. Kliknutím na tlačítko **vytvořit** vytvořte živou událost. Pak jste vráceni do seznamu živých událostí.
1. Vyberte odkaz na živou událost, kterou jste právě vytvořili. Všimněte si, že vaše událost je zastavená.
1. Tuto stránku nechte otevřenou v prohlížeči. Později se k ní vrátíme.

## <a name="set-up-a-live-stream-by-using-obs-studio"></a>Nastavení živého datového proudu pomocí OBS studia

OBS začíná výchozí scénou, ale nemá vybrané žádné vstupy.

   ![Výchozí obrazovka OBS](media/live-events-obs-quickstart/live-event-obs-default-screen.png)

### <a name="add-a-video-source"></a>Přidat zdroj videa

1. Na panelu **zdroje** klikněte na ikonu **Přidat** a vyberte nové zdrojové zařízení. Otevře se nabídka **zdroje** .

1. V nabídce zdrojového zařízení vyberte **zařízení pro digitalizaci videa** . Otevře se nabídka **vytvořit nebo vybrat zdroj** .

   ![Nabídka zdrojů OBS s vybraným video zařízením](media/live-events-obs-quickstart/live-event-obs-video-device-menu.png)

1. Vyberte přepínač **Přidat existující** a pak klikněte na **OK**. Otevře se nabídka **vlastnosti pro video zařízení** .

   ![OBS novou nabídku zdroje videa s vybranou možnost Přidat existující](media/live-events-obs-quickstart/live-event-obs-new-video-source.png)

1. V rozevíracím seznamu **zařízení** vyberte vstup videa, který chcete použít pro vysílání. Zbývající nastavení pro teď nechte beze změny a klikněte na **OK**. Vstupní zdroj bude přidán na panel **zdroje** a zobrazení vstupu videa se zobrazí v oblasti **náhledu** .

   ![Nastavení kamery OBS](media/live-events-obs-quickstart/live-event-surface-camera.png)

### <a name="add-an-audio-source"></a>Přidat zdroj zvuku

1. Na panelu **zdroje** klikněte na ikonu **Přidat** a vyberte nové zdrojové zařízení. Otevře se nabídka zdrojové zařízení.

1. V nabídce zdrojového zařízení vyberte **záznam zvukového vstupu** . Otevře se nabídka **vytvořit nebo vybrat zdroj** .

   ![Nabídka zdrojů OBS se zvoleným zvukovým zařízením](media/live-events-obs-quickstart/live-event-obs-audio-device-menu.png)

1. Vyberte přepínač **Přidat existující** a pak klikněte na **OK**. Otevře se nabídka **vlastnosti pro záznam zvuku vstupu** .

   ![OBS zdroj zvuku s vybranou možnost Přidat existující ](media/live-events-obs-quickstart/live-event-obs-new-audio-source.png)

1. V rozevíracím seznamu **zařízení** vyberte zařízení pro záznam zvuku, které chcete použít pro vysílání. Zbývající nastavení pro teď nechte beze změny a klikněte na OK. Zařízení pro záznam zvuku bude přidáno na panel zvukového směšovače.

   ![Rozevírací seznam pro výběr zvukového zařízení OBS](media/live-events-obs-quickstart/live-event-select-audio-device.png)

### <a name="set-up-streaming-in-obs"></a>Nastavení streamování v OBS

V dalším postupu se v prohlížeči vraťte do Azure Media Services a zkopírujte vstupní adresu URL, která se má zadat do nastavení výstupu:

1. Na stránce Azure Media Services na portálu vyberte **Spustit** , aby se spustila událost živého streamu. (Fakturace začíná nyní.)

   ![Ikona Start](media/live-events-obs-quickstart/start.png)
1. Nastavte přepínací tlačítko **RTMP** na **rtmps**.
1. V poli **vstupní adresa URL** zkopírujte adresu URL do schránky.

   ![Vstupní adresa URL](media/live-events-obs-quickstart/input-url.png)

1. Přepněte do aplikace OBS.

1. Klikněte na tlačítko **Nastavení** na panelu **ovládací prvky** . Otevře se možnost nastavení.

   ![Panel ovládací prvky OBS s vybraným nastavením](media/live-events-obs-quickstart/live-event-obs-settings.png)

1. V nabídce **Nastavení** vyberte **Stream** .

1. V rozevíracím seznamu **Služba** vyberte Zobrazit vše a pak vyberte **vlastní...**.

1. Do pole **Server** vložte adresu URL rtmps, kterou jste zkopírovali do schránky.

1. Do pole **klíč streamu** zadejte něco.  Nezáleží na tom, co je, ale musí mít hodnotu.

    ![Nastavení datového proudu OBS](media/live-events-obs-quickstart/live-event-obs-stream-settings.png)

1. V nabídce **Nastavení** vyberte **výstup** .

1. Do pole **interval klíčového snímku** zadejte *2* . Tím se délka fragmentu nastaví na 2 sekundy. Pro zajištění nižší latence při živém doručování použijte hodnotu 1 sekundu.

1. Volitelné: nastavte **Předvolby využití CPU** na *veryfast* , pokud používáte počítač, který má nízké nároky na výpočetní výkon. V případě potřeby můžete v případě, že jsou nežádoucí síťové podmínky, nastavit KB/s na něco níže.

   ![Nastavení výstupu OBS](media/live-events-obs-quickstart/live-event-obs-advanced-output-settings.png)

1. Zbývající nastavení ponechte beze změny a klikněte na **OK**.

### <a name="start-streaming"></a>Spustit streamování

1. Na panelu **ovládací prvky** klikněte na **Spustit streamování**.

    ![OBS spustit streamování – tlačítko](media/live-events-obs-quickstart/live-event-obs-start-streaming.png)

2. V prohlížeči přepněte na obrazovku Azure Media Services živá událost a klikněte na odkaz **znovu načíst přehrávač** . V přehrávači Preview by se teď měl zobrazit váš datový proud.

## <a name="set-up-outputs"></a>Nastavení výstupů

Tato část nastaví vaše výstupy a umožní vám uložit záznam živého streamu.  

> [!NOTE]
> Pro streamování tohoto výstupu musí běžet koncový bod streamování. Podívejte se na pozdější [spuštění části výchozí koncový bod streamování](#run-the-default-streaming-endpoint) .

1. Vyberte odkaz **vytvořit výstupy** pod **výstupem** prohlížeče videa.
1. Pokud chcete, upravte název výstupu v poli **název** na více uživatelsky přívětivější, aby bylo možné ho později snadno najít.

   ![Pole Název výstupu](media/live-events-wirecast-quickstart/output-name.png)
1. Ponechte všechna ostatní pole v současnosti samostatně.
1. Vyberte **Další** a přidejte Lokátor streamování.
1. Pokud chcete, změňte název lokátoru na něco více uživatelsky přívětivého.

   ![Pole název lokátoru](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Všechno ostatní na této obrazovce teď ponechte samostatně.
1. Vyberte **Vytvořit**.

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>Přehrát výstupní všesměrové vysílání pomocí Azure Media Player

1. Zkopírujte adresu URL streamování do **výstupního** přehrávače videa.
1. Ve webovém prohlížeči otevřete [ukázku Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html).
1. Vložte adresu URL streamování do pole **url** Azure Media Player.
1. Vyberte tlačítko pro **aktualizaci přehrávače** .
1. Kliknutím na ikonu **Přehrát** na videu zobrazíte živý datový proud.

## <a name="stop-the-broadcast"></a>Zastavit vysílání

Pokud si myslíte, že máte k dispozici dostatek obsahu, zastavte všesměrové vysílání.

1. Na portálu vyberte **zastavit**.

1. V OBS na panelu **ovládací prvky** vyberte tlačítko **zastavit streamování** . Tento krok zastaví všesměrové vysílání z OBS.

## <a name="play-the-on-demand-output-by-using-azure-media-player"></a>Přehrání výstupu na vyžádání pomocí Azure Media Player

Výstup, který jste vytvořili, je teď dostupný pro streamování na vyžádání, pokud je spuštěný koncový bod streamování.

1. Přejít na seznam Media Services a vyberte **prostředky**.
1. Vyhledejte výstup události, který jste vytvořili dříve, a vyberte odkaz na prostředek. Otevře se stránka výstup prostředku.
1. Zkopírujte adresu URL streamování v přehrávači videa pro daný Asset.
1. V prohlížeči se vraťte do Azure Media Player a vložte adresu URL streamování do pole URL.
1. Vyberte **aktualizovat přehrávač**.
1. Kliknutím na ikonu **Přehrát** na videu zobrazíte prostředek na vyžádání.

## <a name="clean-up-resources"></a>Vyčištění prostředků

> [!IMPORTANT]
> Zastavte služby. Po dokončení kroků v tomto rychlém startu nezapomeňte zastavit živou událost a koncový bod streamování, nebo se vám bude účtovat čas, kdy je spuštěný. Pokud chcete zastavit živou událost, přečtěte si téma [zastavení postupu vysílání](#stop-the-broadcast) , kroky 2 a 3.

Zastavení koncového bodu streamování:

1. V seznamu Media Services vyberte **koncové body streamování**.
2. Vyberte výchozí koncový bod streamování, který jste spustili dříve. Tento krok otevře stránku koncového bodu.
3. Vyberte **zastavit**.

> [!TIP]
> Pokud nechcete, aby se prostředky z této události udržovaly, nezapomeňte je odstranit, takže se vám neúčtují poplatky za úložiště.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Živé události a živé výstupy v Media Services](./live-events-outputs-concept.md)
