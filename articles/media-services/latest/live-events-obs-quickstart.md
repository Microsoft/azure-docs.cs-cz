---
title: Vytvoření živého datového proudu Mediálních služeb Azure pomocí OBS Studia
description: Zjistěte, jak vytvořit živý přenos Mediálních služeb Azure pomocí portálu a STUDIA OBS
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 04/16/2020
ms.openlocfilehash: 74aa17fb17d682449bd817945c3b8bbf3f95363e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726610"
---
# <a name="create-an-azure-media-services-live-stream-with-obs"></a>Vytvoření živého datového proudu Mediálních služeb Azure pomocí služby OBS

Tento rychlý start vám pomůže vytvořit živý datový proud Azure Media Services pomocí portálu Azure a Open Broadcasting Studio (OBS). Předpokládá, že máte předplatné Azure a vytvořili účet mediálních služeb.

V tomto rychlém startu se budeme zabývat:

- Nastavení místního kodéru s OBS.
- Nastavení živého přenosu.
- Nastavení výstupů živého datového proudu.
- Spuštění výchozího koncového bodu streamování.
- Pomocí Programu Azure Media Player můžete zobrazit živý přenos a výstup na vyžádání.

## <a name="prerequisites"></a>Požadavky

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Otevřete svůj webový prohlížeč a přejděte na [portál Microsoft Azure](https://portal.azure.com/). Zadejte přihlašovací údaje pro přihlášení k portálu. Výchozím zobrazením je váš řídicí panel služby.

## <a name="set-up-an-on-premises-encoder-by-using-obs"></a>Nastavení místního kodéru pomocí OBS

1. Stáhněte a nainstalujte OBS pro svůj operační systém na [webových stránkách Open Broadcaster Software](https://obsproject.com/).
1. Spusťte aplikaci a nechte ji otevřenou.

## <a name="run-the-default-streaming-endpoint"></a>Spuštění výchozího koncového bodu streamování

1. V seznamu Media Services vyberte **koncové body streamování.**

   ![Položka nabídky Datový proud koncových bodů](media/live-events-obs-quickstart/streaming-endpoints.png)
1. Pokud je výchozí stav koncového bodu streamování zastavený, vyberte ho. Tento krok přejdete na stránku pro tento koncový bod.
1. Vyberte **Spustit**.

   ![Tlačítko Start pro koncový bod streamování](media/live-events-obs-quickstart/start.png)

## <a name="set-up-an-azure-media-services-live-stream"></a>Nastavení živého datového proudu Mediálních služeb Azure

1. Přejděte na účet Azure Media Services na portálu a pak ze seznamu **Mediálních služeb** vyberte **Živé streamování.**

   ![Odkaz na živé vysílání](media/live-events-obs-quickstart/select-live-streaming.png)
1. Chcete-li vytvořit novou událost živého streamování, vyberte **Přidat živou událost.**

   ![Ikona Přidat živou událost](media/live-events-obs-quickstart/add-live-event.png)
1. Do pole **Název živé události** zadejte název nové události, například *TestLiveEvent*.

   ![Pole s názvem živé události](media/live-events-obs-quickstart/live-event-name.png)
1. Do pole **Popis** zadejte volitelný popis události.
1. Vyberte **možnost Předávací – bez možnosti kódování cloudu.**

   ![Možnost kódování za mrakem](media/live-events-obs-quickstart/cloud-encoding.png)
1. Vyberte možnost **RTMP.**
1. Ujistěte se, že je pro **spustit živou událost**vybraná možnost **Ne** , abyste se vyhnuli účtování za živou událost, než bude připravena. (Fakturace začne při spuštění živé události.)

   ![Možnost Spustit živou událost](media/live-events-obs-quickstart/start-live-event-no.png)
1. Chcete-li zkontrolovat nastavení, vyberte tlačítko **Revize + vytvořit.**
1. Chcete-li vytvořit živou událost, vyberte tlačítko **Vytvořit.** Poté se vrátíte do seznamu živých událostí.
1. Vyberte odkaz na živou událost, kterou jste právě vytvořili. Všimněte si, že vaše událost je zastavena.
1. Ponechte tuto stránku otevřenou v prohlížeči. Vrátíme se k tomu později.

## <a name="set-up-a-live-stream-by-using-obs-studio"></a>Nastavení živého přenosu pomocí OBS Studio

OBS začíná výchozí scénou, ale bez vybraných vstupů.

   ![Výchozí obrazovka OBS](media/live-events-obs-quickstart/live-event-obs-default-screen.png)

### <a name="add-a-video-source"></a>Přidání zdroje videa

1. V panelu **Zdroje** klepněte na ikonu **Přidat** a vyberte nové zdrojové zařízení. Otevře se nabídka **Zdroje.**

1. Ze zdrojové nabídky zařízení vyberte **Zařízení pro digitalizaci** videa. Otevře se nabídka **Vytvořit/Vybrat zdroj.**

   ![Nabídka zdrojů OBS s vybraným videozařízením](media/live-events-obs-quickstart/live-event-obs-video-device-menu.png)

1. Vyberte tlačítko **Přidat existující** přepínací tlačítko a klepněte na **tlačítko OK**. Otevře se nabídka **Vlastnosti videozařízení.**

   ![OBS nová nabídka zdroje videa s přidáním existující vybrané](media/live-events-obs-quickstart/live-event-obs-new-video-source.png)

1. V rozevíracím seznamu **Zařízení** vyberte vstup videa, který chcete použít pro vysílání. Zbytek nastavení nechte prozatím na pokoji a klepněte na tlačítko **OK**. Vstupní zdroj bude přidán do panelu **Zdroje** a zobrazení pro vstup videa se zobrazí v oblasti **Náhled.**

   ![Nastavení kamery OBS](media/live-events-obs-quickstart/live-event-surface-camera.png)

### <a name="add-an-audio-source"></a>Přidání zdroje zvuku

1. V panelu **Zdroje** klepněte na ikonu **Přidat** a vyberte nové zdrojové zařízení. Otevře se nabídka Zdrojové zařízení.

1. Ze zdrojové nabídky zařízení vyberte **Záznam vstupu zvuku.** Otevře se nabídka **Vytvořit/Vybrat zdroj.**

   ![Nabídka zdrojů OBS s vybraným zvukovým zařízením](media/live-events-obs-quickstart/live-event-obs-audio-device-menu.png)

1. Vyberte tlačítko **Přidat existující** přepínací tlačítko a klepněte na **tlačítko OK**. Otevře se nabídka **Vlastnosti pro zachycení zvukového vstupu.**

   ![Zdroj zvuku OBS s přidáním existující vybrané ](media/live-events-obs-quickstart/live-event-obs-new-audio-source.png)

1. V rozevíracím seznamu **Zařízení** vyberte zařízení pro digitalizaci zvuku, které chcete použít pro vysílání. Zbytek nastavení nechte prozatím na pokoji a klikněte na OK. Zařízení pro digitalizaci zvuku bude přidáno do panelu zvukového mixážního panelu.

   ![Rozevírací seznam pro výběr zvukového zařízení OBS](media/live-events-obs-quickstart/live-event-select-audio-device.png)

### <a name="set-up-streaming-in-obs"></a>Nastavení streamování v OBS

V dalším postupu se vrátíte do Služby Azure Media Services ve vašem prohlížeči a zkopírujete vstupní adresu URL a zadejte do nastavení výstupu:

1. Na stránce Azure Media Services na portálu vyberte **Start** a spusťte událost živého přenosu. (Fakturace začíná právě teď.)

   ![Ikona Start](media/live-events-obs-quickstart/start.png)
1. Nastavte přepínač **RTMP** na **RTMPS**.
1. V poli **Vstupní adresa URL** zkopírujte adresu URL do schránky.

   ![Vstupní adresa URL](media/live-events-obs-quickstart/input-url.png)

1. Přepněte do aplikace OBS.

1. Klepněte na tlačítko **Nastavení** v panelu **Ovládací prvky.** Otevřou se možnosti Nastavení.

   ![Panel OBS Controls s vybraným nastavením](media/live-events-obs-quickstart/live-event-obs-settings.png)

1. Z nabídky **Nastavení** vyberte **Stream.**

1. V rozevíracím seznamu **Služba** vyberte Zobrazit vše a pak vyberte **Vlastní...**.

1. Do pole **Server** vložte adresu URL RTMPS, kterou jste zkopírovali do schránky.

1. Zadejte něco do pole **klíč streamu.**  Nezáleží na tom, co to je, ale musí mít hodnotu.

    ![Nastavení datového proudu OBS](media/live-events-obs-quickstart/live-event-obs-stream-settings.png)

1. Z nabídky **Nastavení** vyberte **Výstup.**

1. Do pole **Interval klíčového snímku** zadejte *hodnotu 2.* Tím se nastaví délka fragmentu na 2 sekundy. Pro nižší latenci živé doručení použijte hodnotu 1 sekundu.

1. VOLITELNÉ: Nastavte **přednastavení využití procesoru** na *velmi rychlé,* pokud používáte počítač, který má nízký výpočetní výkon. Volitelně můžete nastavit kbps na něco nižší, pokud existují nežádoucí síťové podmínky.

   ![Nastavení výstupu OBS](media/live-events-obs-quickstart/live-event-obs-advanced-output-settings.png)

1. Zbývající nastavení ponechejte bez šance a klepněte na tlačítko **OK**.

### <a name="start-streaming"></a>Spustit streamování

1. V panelu **Ovládací prvky** klepněte na **tlačítko Spustit streamování**.

    ![Tlačítko ZAHÁJENÍ streamování OBS](media/live-events-obs-quickstart/live-event-obs-start-streaming.png)

2. Přepněte v prohlížeči na obrazovku událostí Azure Media Services Live a klikněte na odkaz **Znovu načíst přehrávač.** Nyní byste měli vidět svůj stream v přehrávači Náhled.

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

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>Přehrání výstupního vysílání pomocí programu Azure Media Player

1. Zkopírujte adresu URL streamování do přehrávače **výstupního** videa.
1. Ve webovém prohlížeči otevřete [ukázku přehrávače Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html).
1. Vložte adresu URL streamování do pole **URL** v programu Azure Media Player.
1. Vyberte tlačítko **Aktualizovat přehrávač.**
1. Chcete-li zobrazit živý přenos, vyberte ve videu ikonu **Přehrát.**

## <a name="stop-the-broadcast"></a>Zastavení vysílání

Když si myslíte, že jste streamovali dostatek obsahu, zastavte vysílání.

1. Na portálu vyberte **Zastavit**.

1. V OBS vyberte v panelu **Ovládací prvky** tlačítko **Zastavit streamování.** Tento krok zastaví vysílání z OBS.

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
