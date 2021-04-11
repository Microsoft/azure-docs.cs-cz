---
title: Vytvoření živého datového proudu pomocí OBS studia
description: Naučte se vytvářet Azure Media Services živý datový proud pomocí portálu a OBS Studio.
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/20/2021
ms.openlocfilehash: d52affbdc4dc433c40be687f2e56afae4bcf4c2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104949883"
---
# <a name="create-an-azure-media-services-live-stream-with-obs"></a>Vytvoření živého datového proudu Azure Media Services pomocí OBS

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Tento rychlý Start vám pomůže vytvořit Media Services živou událost pomocí Azure Portal a všesměrového vysílání pomocí programu Open Broadcast Studio (OBS). Předpokládá, že máte předplatné Azure a máte vytvořený účet Media Services.

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

   ![Položka nabídky koncových bodů streamování.](media/live-events-obs-quickstart/streaming-endpoints.png)
1. Pokud je stav výchozího koncového bodu streamu zastavený, vyberte ho. Tento krok vás přesměruje na stránku tohoto koncového bodu.
1. Vyberte **Spustit**.

   ![Tlačítko Start pro koncový bod streamování.](media/live-events-obs-quickstart/start.png)

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

   ![Možnost Spustit živou událost.](media/live-events-obs-quickstart/start-live-event-no.png)
1. Kliknutím na tlačítko **Revize + vytvořit** zkontrolujte nastavení.
1. Kliknutím na tlačítko **vytvořit** vytvořte živou událost. Pak jste vráceni do seznamu živých událostí.
1. Vyberte odkaz na živou událost, kterou jste vytvořili. Všimněte si, že vaše událost je zastavená.
1. Tuto stránku nechte otevřenou v prohlížeči. Později se k ní vrátíme.

## <a name="set-up-a-live-stream-by-using-obs-studio"></a>Nastavení živého datového proudu pomocí OBS studia

OBS začíná výchozí scénou, ale nemá vybrané žádné vstupy.

   ![Výchozí obrazovka OBS](media/live-events-obs-quickstart/live-event-obs-default-screen.png)

### <a name="add-a-video-source"></a>Přidat zdroj videa

1. Na panelu **zdroje** vyberte ikonu **Přidat** a vyberte nové zdrojové zařízení. Otevře se nabídka **zdroje** .

1. V nabídce zdrojového zařízení vyberte **zařízení pro digitalizaci videa** . Otevře se nabídka **vytvořit nebo vybrat zdroj** .

   ![Nabídka zdrojů OBS s vybraným video zařízením](media/live-events-obs-quickstart/live-event-obs-video-device-menu.png)

1. Zvolte přepínač **Přidat existující** a pak vyberte **OK**. Otevře se nabídka **vlastnosti pro video zařízení** .

   ![OBS novou nabídku zdroje videa s vybranou možnost Přidat existující.](media/live-events-obs-quickstart/live-event-obs-new-video-source.png)

1. V rozevíracím seznamu **zařízení** vyberte vstup videa, který chcete použít pro vysílání. Zbývající nastavení pro teď nechte beze změny a vyberte **OK**. Vstupní zdroj bude přidán na panel **zdroje** a zobrazení vstupu videa se zobrazí v oblasti **náhledu** .

   ![Nastavení kamery OBS](media/live-events-obs-quickstart/live-event-surface-camera.png)

### <a name="add-an-audio-source"></a>Přidat zdroj zvuku

1. Na panelu **zdroje** vyberte ikonu **Přidat** a vyberte nové zdrojové zařízení. Otevře se nabídka zdrojové zařízení.

1. V nabídce zdrojového zařízení vyberte **záznam zvukového vstupu** . Otevře se nabídka **vytvořit nebo vybrat zdroj** .

   ![V nabídce zdroje OBS se vybraným zvukovým zařízením.](media/live-events-obs-quickstart/live-event-obs-audio-device-menu.png)

1. Zvolte přepínač **Přidat existující** a pak vyberte **OK**. Otevře se nabídka **vlastnosti pro záznam zvuku vstupu** .

   ![OBS zdroj zvuku s vybranou možnost Přidat existující.](media/live-events-obs-quickstart/live-event-obs-new-audio-source.png)

1. V rozevíracím seznamu **zařízení** vyberte zařízení pro záznam zvuku, které chcete použít pro vysílání. Zbývající nastavení pro teď nechte beze změny a vyberte OK. Zařízení pro záznam zvuku bude přidáno na panel zvukového směšovače.

   ![Rozevírací seznam pro výběr zvukového zařízení OBS](media/live-events-obs-quickstart/live-event-select-audio-device.png)

### <a name="set-up-streaming-and-advanced-encoding-settings-in-obs"></a>Nastavení streamování a pokročilého nastavení kódování v OBS

V dalším postupu se v prohlížeči vraťte do Azure Media Services a zkopírujte vstupní adresu URL, která se má zadat do nastavení výstupu:

1. Na stránce Azure Media Services na portálu vyberte **Spustit** , aby se spustila událost živého streamu. (Fakturace začíná nyní.)

   ![Ikona Start](media/live-events-obs-quickstart/start.png)
1. Nastavte přepínací tlačítko **RTMP** na **rtmps**.
1. V poli **vstupní adresa URL** zkopírujte adresu URL do schránky.

   ![Vstupní adresa URL](media/live-events-obs-quickstart/input-url.png)

1. Přepněte do aplikace OBS.

1. Na panelu **ovládací prvky** vyberte tlačítko **Nastavení** . Otevře se možnost nastavení.

   ![Panel ovládací prvky OBS s vybraným nastavením.](media/live-events-obs-quickstart/live-event-obs-settings.png)

1. V nabídce **Nastavení** vyberte **Stream** .

1. V rozevíracím seznamu **Služba** vyberte Zobrazit vše a pak vyberte **vlastní...**.

1. Do pole **Server** vložte adresu URL rtmps, kterou jste zkopírovali do schránky.

1. Do pole **klíč streamu** zadejte něco.  Nezáleží na tom, co je, ale musí mít hodnotu.

    ![Nastavení datového proudu OBS](media/live-events-obs-quickstart/live-event-obs-stream-settings.png)

1. V nabídce **Nastavení** vyberte **výstup** .

1. V horní části stránky vyberte rozevírací seznam **režim výstupu** a pro přístup ke všem dostupným nastavením kodéru zvolte **Upřesnit** .

1. Vyberte kartu **streamování** a nastavte kodér.

1. Vyberte pro svůj systém pravý kodér.  Pokud váš hardware podporuje akceleraci GPU, vyberte ze NVIDIA **NVENC** h. 264 nebo Intel **QuickSync** H. 264. Pokud váš systém nemá podporovaný grafický procesor, vyberte možnost **x264** software Encoder.

#### <a name="x264-encoder-settings"></a>Nastavení kodéru x264

1. Pokud jste vybrali možnost kódování **x264** , vyberte pole **výstup změny velikosti** . Pokud používáte 1080ou živou událost Standard (720P), vyberte buď, pokud používáte prémiovou živou událost v Media Services nebo 1280 × 720.  Pokud používáte předávací živou událost, můžete zvolit jakékoli dostupné řešení.

1. Nastavte **přenosovou rychlost** na odkudkoli mezi 1500 KB/s a 4000 KB/s. Pokud používáte standardní událost standardního kódování v 720P, doporučujeme 2500 kb/s. Pokud používáte akci 1080P Premium Live, doporučujeme 4000 KB/s. Možná budete chtít nastavit přenosovou rychlost na základě dostupných schopností procesoru a šířky pásma ve vaší síti, abyste dosáhli požadovaného nastavení kvality.

1. Do pole **interval klíčového snímku** zadejte *2* . Hodnota nastavuje interval klíčových snímků na 2 sekundy, což určuje konečnou velikost fragmentů doručených přes HLS nebo POMLČKu od Media Services. Nikdy nenastavte interval klíčových snímků na více než 4 sekundy.  Pokud při vysílání vidíte vysokou latenci, měli byste vždy pokontrolovat nebo informovat uživatele aplikace, aby tuto hodnotu vždycky nastavili na 2 sekundy. Při pokusu o dosažení živého doručování s nižší latencí můžete zvolit nastavení této hodnoty na hodnotu nižší než 1 sekunda.

1. Volitelné: Nastavte předvolby využití CPU na **veryfast** a spusťte některé experimenty, abyste viděli, jestli váš místní procesor dokáže zvládnout kombinaci přenosové rychlosti a přednastavené dostatečné nároky. Snažte se vyhnout nastavením, která by výsledkem průměrného výkonu procesoru vyššího než 80%, aby se předešlo problémům během živého streamování. Pro zlepšení kvality můžete testovat pomocí **rychlejší** a **rychlé** předvolby nastavení, dokud nedosáhnete omezení procesoru.

   ![Nastavení kodéru OBS x264](media/live-events-obs-quickstart/live-event-obs-x264-settings.png)

1. Zbývající nastavení ponechte beze změny a vyberte **OK**.

#### <a name="nvidia-nvenc-encoder-settings"></a>Nastavení kodéru NVIDIA NVENC

1. Pokud jste vybrali možnost kódování GPU **NVENC** , zaškrtněte políčko **přeškálovat výstup** a vyberte buď 1080, pokud používáte prémiovou živou událost v Media Services, nebo 1280 × 720, pokud používáte standardní (720p) živou událost. Pokud používáte předávací živou událost, můžete zvolit jakékoli dostupné řešení.

1. Nastavte **řízení rychlosti** na hodnotu CBR pro řízení přenosů konstantních přenosů.

1. Nastavte **přenosovou rychlost** kdekoliv mezi 1500 KB/s a 4000 KB/s. Pokud používáte standardní událost standardního kódování v 720P, doporučujeme 2500 kb/s. Pokud používáte akci 1080P Premium Live, doporučujeme 4000 KB/s. Tuto možnost můžete upravit na základě dostupných schopností procesoru a šířky pásma sítě, abyste dosáhli požadovaného nastavení kvality.

1. Nastavte **interval klíčového snímku** na 2 sekundy, jak je uvedeno výše v možnostech x264. Nepřekračuje 4 sekundy, protože to může významně ovlivnit latenci vašeho živého vysílání.

1. Nastavte **Předvolby** na nízkou latenci, Low-Latency výkon nebo Low-Latency kvalitu v závislosti na rychlosti procesoru na místním počítači. Experimentujte s těmito nastaveními, abyste dosáhli nejlepšího vyrovnání kvality a využití procesoru na vlastním hardwaru.

1. Pokud používáte výkonnější hardwarovou konfiguraci, nastavte **profil** na "Main" nebo "High".

1. Ponechte nezaškrtnuté políčko **Hledat** . Pokud máte velmi výkonný počítač, můžete to ověřit.

1. Nechte nezaškrtnuté **ladění vizuálního psycho** . Pokud máte velmi výkonný počítač, můžete to ověřit.

1. Nastavte **GPU** na 0, aby se automaticky rozhodla, která GPU se má přidělit. V případě potřeby můžete omezit využití GPU.

1. Nastavte **Maximum B-snímků** na 2.

   ![OBS nastavení kodéru NVidia NVidia NVENC GPU](media/live-events-obs-quickstart/live-event-obs-nvidia-settings.png)

#### <a name="intel-quicksync-encoder-settings"></a>Nastavení kodéru Intel QuickSync

1. Pokud jste vybrali možnost kódování GPU Intel **QuickSync** , zaškrtněte políčko **přeškálovat výstup** a vyberte buď 1080, pokud používáte prémiovou živou událost v Media Services, nebo 1280 × 720, pokud používáte standardní (720p) živou událost. Pokud používáte předávací živou událost, můžete zvolit jakékoli dostupné řešení.

1. Nastavte **cílové použití** na "vyvážené", nebo podle potřeby upravte podle potřeby na základě zatížení procesoru a GPU. Upravte podle potřeby a Experimentujte, abyste dosáhli maximálního počtu 80% využití procesoru v průměru s kvalitou, kterou váš hardware dokáže vyrobit. Pokud máte více omezených hardwarových zařízení, otestujte pomocí možnosti "Rychlá" nebo "velmi rychle", pokud máte problémy s výkonem.

1. Pokud používáte výkonnější hardwarovou konfiguraci, nastavte **profil** na "Main" nebo "High".

1. Nastavte **interval klíčového snímku** na 2 sekundy, jak je uvedeno výše v možnostech x264. Nepřekračuje 4 sekundy, protože to může významně ovlivnit latenci vašeho živého vysílání.

1. Nastavte **řízení rychlosti** na hodnotu CBR pro řízení přenosů konstantních přenosů.

1. Nastavte **přenosovou rychlost** kdekoliv mezi 1500 a 4000 KB/s.  Pokud používáte standardní událost standardního kódování v 720P, doporučujeme 2500 kb/s. Pokud používáte akci 1080P Premium Live, doporučujeme 4000 KB/s. Tuto možnost můžete upravit na základě dostupných schopností procesoru a šířky pásma sítě, abyste dosáhli požadovaného nastavení kvality.

1. Nastavte **latenci** na nízká.

1. Nastavte **snímky B** na 2.

1. Ponechte nezaškrtnutá **vylepšení pro téma videa** .

   ![OBS nastavení kodéru Intel QuickSync GPU.](media/live-events-obs-quickstart/live-event-obs-intel-settings.png)

### <a name="set-audio-settings"></a>Nastavení zvuku

V dalším postupu budete upravovat nastavení kódování zvuku.

1. V nastavení vyberte kartu výstupní >zvuku.

1. Nastavte **přenosovou rychlost** stopy 1 až 128 kb/s.

   ![Nastavení zvukové rychlosti OBS](media/live-events-obs-quickstart/live-event-obs-audio-output-panel.png)

1. Vyberte kartu zvuk v nastavení.

1. Nastavte **vzorkovací frekvenci** na 44,1 kHz.

   ![OBS nastavení vzorkovací frekvence zvuku.](media/live-events-obs-quickstart/live-event-obs-audio-sample-rate-settings.png)

### <a name="start-streaming"></a>Spustit streamování

1. Na panelu **ovládací prvky** klikněte na **Spustit streamování**.

    ![OBS tlačítko spustit streamování.](media/live-events-obs-quickstart/live-event-obs-start-streaming.png)

2. V prohlížeči přepněte na obrazovku Azure Media Services živá událost a klikněte na odkaz **znovu načíst přehrávač** . V přehrávači Preview by se teď měl zobrazit váš datový proud.

## <a name="set-up-outputs"></a>Nastavení výstupů

Tato část nastaví vaše výstupy a umožní vám uložit záznam živého streamu.  

> [!NOTE]
> Pro streamování tohoto výstupu musí běžet koncový bod streamování. Podívejte se na pozdější [spuštění části výchozí koncový bod streamování](#run-the-default-streaming-endpoint) .

1. Vyberte odkaz **vytvořit výstupy** pod **výstupem** prohlížeče videa.
1. Pokud chcete, upravte název výstupu v poli **název** na více uživatelsky přívětivější, aby bylo možné ho později snadno najít.

   ![Název výstupního pole.](media/live-events-wirecast-quickstart/output-name.png)
1. Ponechte všechna ostatní pole v současnosti samostatně.
1. Vyberte **Další** a přidejte Lokátor streamování.
1. Pokud chcete, změňte název lokátoru na něco více uživatelsky přívětivého.

   ![Pole název lokátoru.](media/live-events-wirecast-quickstart/live-event-locator.png)
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
