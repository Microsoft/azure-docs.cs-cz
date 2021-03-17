---
title: Vytvoření živého streamu Azure Media Services
description: Naučte se vytvářet Azure Media Services živý datový proud pomocí portálu a Wirecast
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 08/31/2020
ms.openlocfilehash: 76bbb980b6430f7cffc23ec078e2c932128dc637
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89265265"
---
# <a name="create-an-azure-media-services-live-stream"></a>Vytvoření živého streamu Azure Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Tento rychlý Start vám pomůže vytvořit Azure Media Services živý datový proud pomocí Azure Portal a Wirecast. Předpokládá, že máte předplatné Azure a máte vytvořený účet Media Services.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Otevřete webový prohlížeč a přejdete na [portál Microsoft Azure](https://portal.azure.com/). Zadejte přihlašovací údaje pro přihlášení k portálu. Výchozím zobrazením je váš řídicí panel služby.

V tomto rychlém startu pokryjeme:

- Nastavení místního kodéru pomocí bezplatné zkušební verze Wirecast.
- Nastavuje se živý datový proud.
- Nastavení výstupů živého streamu
- Spouští se výchozí koncový bod streamování.
- Použití Azure Media Player k zobrazení živého streamu a výstupu na vyžádání.

Abychom zachovali věci jednoduché, použijeme pro Azure Media Services v Wirecast, kódování předávacího cloudu a RTMP předdefinované kódování.

## <a name="set-up-an-on-premises-encoder-by-using-wirecast"></a>Nastavení místního kodéru pomocí Wirecast

1. Stáhněte a nainstalujte si Wirecast pro váš operační systém na [webu-Stream](https://www.telestream.net).
1. Spusťte aplikaci a k registraci produktu použijte svoji oblíbenou e-mailovou adresu. Nechejte aplikaci otevřenou.
1. V e-mailu, který jste obdrželi, ověřte svoji e-mailovou adresu. Aplikace potom spustí bezplatnou zkušební verzi.
1. Doporučené: Podívejte se na video kurz na obrazovce otevírání aplikace.

## <a name="set-up-an-azure-media-services-live-stream"></a>Nastavení Azure Media Services živého streamu

1. Na portálu klikněte na účet Azure Media Services a potom v seznamu **Media Services** vyberte **živé streamování** .

   ![Odkaz živého streamování](media/live-events-wirecast-quickstart/select-live-streaming.png)
1. Vyberte **Přidat živou událost** a vytvořte novou událost živého streamování.

   ![Přidat živou ikonu události](media/live-events-wirecast-quickstart/add-live-event.png)
1. Do pole **název živé události** zadejte název nové události, například *TestLiveEvent*.

   ![Pole název živé události](media/live-events-wirecast-quickstart/live-event-name.png)
1. Do pole **Popis** Zadejte nepovinný popis události.
1. Vyberte možnost **předávat – bez cloudového kódování** .

   ![Možnost kódování cloudu](media/live-events-wirecast-quickstart/cloud-encoding.png)
1. Vyberte možnost **RTMP** .
1. Ujistěte se, že není vybraná **žádná** možnost pro **spuštění živé události**, abyste se vyhnuli fakturaci za živou událost předtím, než bude připravená. (Fakturace bude zahájena při spuštění živé události.)

   ![Možnost spuštění živé události](media/live-events-wirecast-quickstart/start-live-event-no.png)
1. Kliknutím na tlačítko **Revize + vytvořit** zkontrolujte nastavení.
1. Kliknutím na tlačítko **vytvořit** vytvořte živou událost. Pak jste vráceni do seznamu živých událostí.
1. Vyberte odkaz na živou událost, kterou jste právě vytvořili. Všimněte si, že vaše událost je zastavená.
1. Tuto stránku nechte otevřenou v prohlížeči. Později se k ní vrátíme.

## <a name="set-up-a-live-stream-by-using-wirecast-studio"></a>Nastavení živého datového proudu pomocí Wirecast studia

1. V aplikaci Wirecast v hlavní nabídce vyberte **vytvořit prázdný dokument** a pak vyberte **pokračovat**.

   ![Úvodní obrazovka Wirecast](media/live-events-wirecast-quickstart/open-empty-document.png)
1. Najeďte myší na první vrstvu v oblasti **Wirecast vrstev** .  Vyberte ikonu **Přidat** , která se zobrazí, a vyberte vstup videa, který chcete streamovat.

   ![Wirecast přidat ikonu](media/live-events-wirecast-quickstart/add-icon.png)

   Otevře se dialogové okno **Hlavní vrstva 1** .
1. V nabídce vyberte možnost **zachytávání videa** a pak vyberte kameru, kterou chcete použít.

   ![Oblast náhledu pro zachytávání videa](media/live-events-wirecast-quickstart/video-shot-selection.png)

   Zobrazení z kamery se zobrazí v oblasti verze Preview.
1. Najeďte myší na druhou vrstvu v oblasti **Wirecast vrstev** . Vyberte ikonu **Přidat** , která se zobrazí, a vyberte zvukový vstup, který chcete streamovat. Otevře se dialogové okno **Hlavní vrstva 2** .
1. V nabídce vyberte **záznam zvuku** a pak vyberte zvukový vstup, který chcete použít.

   ![Vstupy pro záznam zvuku](media/live-events-wirecast-quickstart/audio-shot-select.png)
1. V hlavní nabídce vyberte **Nastavení výstupu**. Zobrazí se dialogové okno **vybrat cíl výstupu** .
1. V rozevíracím seznamu **cíl** vyberte **Azure Media Services** . Nastavení výstupu pro Azure Media Services automaticky vyplní *většinu* nastavení výstupu.

   ![Obrazovka nastavení výstupu Wirecast](media/live-events-wirecast-quickstart/azure-media-services.png)


V dalším postupu se v prohlížeči vraťte do Azure Media Services a zkopírujte vstupní adresu URL, která se má zadat do nastavení výstupu:

1. Na stránce Azure Media Services na portálu vyberte **Spustit** , aby se spustila událost živého streamu. (Fakturace začíná nyní.)

   ![Ikona Start](media/live-events-wirecast-quickstart/start.png)
2. Nastavte přepínač **zabezpečené/nezabezpečené** na hodnotu **nezabezpečené**. Tento krok nastavuje protokol pro RTMP místo RTMP.
3. V poli **vstupní adresa URL** zkopírujte adresu URL do schránky.

   ![Vstupní adresa URL](media/live-events-wirecast-quickstart/input-url.png)
4. Přepněte do aplikace Wirecast a vložte **vstupní adresu URL** do pole **adresa** v nastavení výstup.

   ![Vstupní adresa URL Wirecast](media/live-events-wirecast-quickstart/input-url-wirecast.png)
5. Vyberte **OK**.

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

## <a name="start-the-broadcast"></a>Spustit všesměrové vysílání

1. V Wirecast vyberte **výstup**  >  **Spustit/zastavit vysílání**  >  **Start Azure Media Services: Azure Media Services** z hlavní nabídky.

   ![Spustit položky nabídky vysílání](media/live-events-wirecast-quickstart/start-broadcast.png)

   Když se datový proud pošle do živé události, zobrazí se v přehrávači videa na stránce živá událost v Azure Media Services **aktivní** okno v Wirecast.

1. Kliknutím na tlačítko **Přejít** v okně náhledu spustíte vysílání videa a zvuku, které jste vybrali pro Wirecast vrstvy.

   ![Tlačítko Wirecast přejít](media/live-events-wirecast-quickstart/go-button.png)

   > [!TIP]
   > Pokud dojde k chybě, zkuste znovu načíst přehrávač výběrem odkazu **znovu načíst přehrávač** výše v přehrávači.

## <a name="run-the-default-streaming-endpoint"></a>Spustit výchozí koncový bod streamování

1. V seznamu Media Services vyberte **koncové body streamování** .

   ![Položka nabídky koncových bodů streamování](media/live-events-wirecast-quickstart/streaming-endpoints.png)
1. Pokud je stav výchozího koncového bodu streamu zastavený, vyberte ho. Tento krok vás přesměruje na stránku tohoto koncového bodu.
1. Vyberte **Spustit**.
   
   ![Tlačítko Start pro koncový bod streamování](media/live-events-wirecast-quickstart/start.png)

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>Přehrát výstupní všesměrové vysílání pomocí Azure Media Player

1. Zkopírujte adresu URL streamování do **výstupního** přehrávače videa.
1. Ve webovém prohlížeči otevřete [ukázku Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html).
1. Vložte adresu URL streamování do pole **url** Azure Media Player.
1. Vyberte tlačítko pro **aktualizaci přehrávače** .
1. Kliknutím na ikonu **Přehrát** na videu zobrazíte živý datový proud.

## <a name="stop-the-broadcast"></a>Zastavit vysílání

Pokud si myslíte, že máte k dispozici dostatek obsahu, zastavte všesměrové vysílání.

1. V Wirecast vyberte tlačítko **vysílání** . Tento krok zastaví všesměrové vysílání z Wirecast.
1. Na portálu vyberte **zastavit**. Pak se zobrazí zpráva upozorňující, že živý datový proud přestane fungovat, ale výstup se teď stane prostředkem na vyžádání.
1. V varovné zprávě vyberte **zastavit** . Azure Media Player nyní zobrazuje chybu, protože živý datový proud již není k dispozici.

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
