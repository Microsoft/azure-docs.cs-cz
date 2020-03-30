---
title: Vytvoření živého datového proudu Mediálních služeb Azure s portálem a drátovým vysíláním
description: Přečtěte si, jak vytvořit živý stream Služby Azure Media Service.
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/25/2020
ms.openlocfilehash: e5bdd75ca61d53a64f003633d74e3d8f7992a98b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80336392"
---
# <a name="create-a-azure-media-services-live-stream-with-the-portal-and-wirecast"></a>Vytvoření živého datového proudu Mediálních služeb Azure s portálem a drátovým vysíláním

Tento průvodce začínáme předpokládá, že máte předplatné Azure a vytvořili účet Azure Media Services.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet, než začnete.

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Otevřete svůj webový prohlížeč a přejděte na [portál Microsoft Azure Portal](https://portal.azure.com/). Zadejte přihlašovací údaje pro přihlášení k portálu. Výchozím zobrazením je váš řídicí panel služby.

V tomto rychlém startu se budeme zabývat:

- Nastavení místního kodéru s bezplatnou zkušební verzí telestreamového wirecastu
- Nastavení živého přenosu
- Nastavení výstupů živého přenosu
- Spuštění výchozího koncového bodu streamování
- Zobrazení živého datového proudu a výstupu na vyžádání pomocí programu Azure Media Player

Aby to bylo jednoduché, použijeme přednastavení kódování pro Azure Media Services v Wirecastu, předávací kódování v cloudu a RTMP.

## <a name="setting-up-an-on-premises-encoder-with-wirecast"></a>Nastavení místního kodéru pomocí wirecastu

1. Stáhněte a nainstalujte Wirecast pro váš operační systém nahttps://www.telestream.net
1. Spusťte aplikaci a použijte svou oblíbenou e-mailovou adresu k registraci produktu.  Udržujte aplikaci otevřenou.
1. Obdržíte e-mail s žádostí o ověření vaší e-mailové adresy, pak aplikace spustí bezplatnou zkušební verzi.
1. DOPORUČENO: Podívejte se na instruktážní video na úvodní obrazovce aplikace.

## <a name="setting-up-an-azure-media-services-live-stream"></a>Nastavení živého datového proudu Mediálních služeb Azure

1. Po přechodu na účet Azure Media Services v rámci portálu vyberte **živé streamování** ze seznamu Mediálních služeb.<br/>
![Vybrat odkaz Živé streamování](media/live-events-wirecast-quickstart/select-live-streaming.png)<br/>
1. Kliknutím na **Přidat živou událost** vytvořte novou událost živého streamování.<br/>
![Ikona Přidat živou událost](media/live-events-wirecast-quickstart/add-live-event.png)<br/>
1. Zadejte název nové události, například *TestLiveEvent,* do pole **Název** živé události.<br/>
![Textové pole s názvem živé události](media/live-events-wirecast-quickstart/live-event-name.png)<br/>
1. Do pole **Popis** zadejte volitelný popis události.
1. Vyberte **předávací – žádné přepínací tlačítko kódování cloudu.**<br/>
![Přepínací tlačítko kódování mraků](media/live-events-wirecast-quickstart/cloud-encoding.png)
1. Vyberte přepínací tlačítko **RTMP.**
1. Ujistěte se, že je pro spustit živou událost vybráno žádné **No** přepínací tlačítko, abyste zabránili účtování za živou událost před tím, než je připravena.  (Fakturace začne po spuštění živé události.) ![Spustit přepínací tlačítko živé události](media/live-events-wirecast-quickstart/start-live-event-no.png)<br/>
1. Kliknutím na tlačítko **Revize + vytvořit** zkontrolujte nastavení.
1. Kliknutím na tlačítko **Vytvořit** vytvořte živou událost. Poté budete vráceni do zobrazení živého zobrazení seznamu událostí.
1. Klikněte na **odkaz na živou událost,** kterou jste právě vytvořili. Všimněte si, že vaše událost je zastavena.
1. Ponechte tuto stránku otevřenou v prohlížeči.  Vrátíme se k tomu později.

## <a name="setting-up-a-live-stream-with-wirecast-studio"></a>Nastavení živého přenosu pomocí wirecast studia

1. Za předpokladu, že máte stále otevřenou aplikaci Wirecast, vyberte v hlavní nabídce **možnost Vytvořit prázdný dokument** a klepněte na tlačítko **Pokračovat**.
![Úvodní obrazovka wirecastu](media/live-events-wirecast-quickstart/open-empty-document.png)
1. Najeďte přes první vrstvu v oblasti Vrstvy drátového vysílání.  Klikněte na ikonu **Přidat,** která se zobrazí, a vyberte vstup videa, který chcete streamovat.  Otevře se dialogové okno Hlavní vrstva 1.<br/>
![Ikona přidání wirecastu](media/live-events-wirecast-quickstart/add-icon.png)
1. V nabídce vyberte **Snímání videa** a pak vyberte fotoaparát, který chcete použít. Pokud vyberete kameru, zobrazí se pohled z kamery v oblasti Náhled.
![Obrazovka pro výběr videa s drátovým videam](media/live-events-wirecast-quickstart/video-shot-selection.png)
1. Najeďte přes druhou vrstvu v oblasti Vrstvy drátového tažného vysílání. Klikněte na ikonu **Přidat,** která se zobrazí, a vyberte zvukový vstup, který chcete streamovat.  Otevře se dialogové okno Hlavní vrstva 2.
1. V nabídce vyberte **Záznam zvuku** a pak vyberte zvukový vstup, který chcete použít.
![Obrazovka pro výběr zvukového snímku wirecast](media/live-events-wirecast-quickstart/audio-shot-select.png)
1. V hlavní nabídce vyberte **Nastavení výstupu**.  Zobrazí se dialogové okno Výstup.
1. V rozevíracím souboru výstupu vyberte **Mediální služby Azure.**  Nastavení výstupu pro Azure Media Services automaticky naplní *většinu* nastavení výstupu.<br/>
![Obrazovka nastavení výstupu wirecast](media/live-events-wirecast-quickstart/azure-media-services.png)
1. V další části se vrátíte do Služby Azure Media Services ve vašem prohlížeči a zkopírujete *vstupní adresu URL* a zadejte do nastavení výstupu.

### <a name="copy-and-paste-the-input-url"></a>Zkopírování a vložení vstupní adresy URL

1. Na stránce Azure Media Services na portálu klikněte na **Start** a spusťte událost živého přenosu. (Fakturace začíná právě teď.)<br/>
![Ikona Start](media/live-events-wirecast-quickstart/start.png)
2. Kliknutím na přepínač **Zabezpečit/Není zabezpečený** nastavte možnost **Není zabezpečený**.  Tím se nastaví protokol rtmp místo RTMPS.
3. Zkopírujte **vstupní adresu URL** do schránky.
![Vstupní adresa URL](media/live-events-wirecast-quickstart/input-url.png)
4. Přepněte do aplikace Wirecast a vložte **vstupní adresu URL** do pole **Adresa** v nastavení výstupu.<br/>
![Vstupní adresa URL drátového vysílání](media/live-events-wirecast-quickstart/input-url-wirecast.png)
5. Klepněte na **tlačítko V pořádku**.

## <a name="setting-up-outputs"></a>Nastavení výstupů

Tato část nastaví výstupy a umožní vám uložit záznam vašeho živého streamu.  

> [!NOTE]
> Chcete-li streamovat tento výstup, musí být spuštěn koncový bod streamování.  Viz Spuštění výchozího koncového bodu streamování níže.

1. Klikněte na odkaz **Vytvořit výstupy** pod prohlížečem videa Výstupy.
1. Pokud chcete, upravte název výstupu v poli **Název** na něco uživatelsky přívětivějšího, aby bylo možné jej později snadno najít.
![Pole výstupního názvu](media/live-events-wirecast-quickstart/output-name.png)
1. Nech teď všechna pole na pokoji.
1. Klikněte na **Další** přidat vyhledávač datových proudů.
1. Změňte název lokátoru na něco uživatelsky přívětivějšího, pokud chcete.
![Pole názvu lokátoru](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Všechno ostatní na téhle obrazovce zatím nech na pokoji.
1. Klikněte na **Vytvořit**.

## <a name="starting-the-broadcast"></a>Spuštění vysílání

1. V wirecastu vyberte **Výstup > Start / Stop vysílání > Spuštění mediálních služeb Azure : Mediální služby Azure** v hlavní nabídce.  Po odeslání datového proudu do živé události se okno Live v wirecastu zobrazí v přehrávači živého videa událostí na stránce živé události ve službě Azure Media Services.

   ![Spustit položky nabídky vysílání](media/live-events-wirecast-quickstart/start-broadcast.png)

1. Klepnutím na tlačítko **Přejít** pod oknem náhledu zahájíte vysílání videa a zvuku, které jste vybrali pro vrstvy wirecastu.

   ![Tlačítko Přejít wirecast](media/live-events-wirecast-quickstart/go-button.png)

   > [!TIP]
   > Pokud dojde k chybě, zkuste přehrávač znovu načíst kliknutím na odkaz Znovu načíst přehrávač nad hráčem.

## <a name="running-the-default-streaming-endpoint"></a>Spuštění výchozího koncového bodu streamování

1. Ujistěte se, že koncový bod streamování běží výběrem **koncových bodů streamování** v seznamu Media Services. Budete převedena na stránku koncových bodů streamování.<br/>
![Položka nabídky koncového bodu streamování](media/live-events-wirecast-quickstart/streaming-endpoints.png)
1. Pokud je výchozí stav koncového bodu streamování zastavený, klikněte na **výchozí** koncový bod streamování. Tím přejdete na stránku pro tento koncový bod.
1. Klepněte na tlačítko **Start**.  Tím se spustí koncový bod streamování.<br/>
![Položka nabídky koncového bodu streamování](media/live-events-wirecast-quickstart/start.png)

## <a name="play-the-output-broadcast-with-azure-media-player"></a>Přehrání výstupního vysílání pomocí programu Azure Media Player

1. Zkopírujte **adresu URL streamování** pod přehrávačem výstupního videa.
1. Ve webovém prohlížeči otevřete ukázku přehrávače Azure Media Player.https://ampdemo.azureedge.net/azuremediaplayer.html
1. Vložte **adresu URL streamování** do pole URL v přehrávači Azure Media Player.
1. Klepněte na tlačítko **Aktualizovat přehrávač.**
1. Kliknutím na ikonu **přehrávání** ve videu zobrazíte živý přenos.

## <a name="stopping-the-broadcast"></a>Zastavení vysílání

Pokud si myslíte, že jste streamovali dostatek obsahu, zastavte vysílání.

1. V wirecastu klikněte na tlačítko **vysílání.**  Tím se zastaví vysílání z Wirecast.
1. Na portálu klikněte na **Stop**. Zobrazí se varovná zpráva, že zastavení živého datového proudu, ale výstup se nyní stane aktivem na vyžádání.
1. Klikněte na **Zastavit** ve varovné zprávě. Azure Media Player se také nyní zobrazí chyba jako živý datový proud již není k dispozici.

## <a name="play-the-on-demand-output-with-the-azure-media-player"></a>Přehrání výstupu na vyžádání pomocí přehrávače Azure Media Player

Vytvořený výstup je teď k dispozici pro streamování na vyžádání, dokud je spuštěn koncový bod streamování.

1. Přejděte na výpis mediálních služeb a vyberte **položku Datové zdroje**.
1. Najděte výstup události, který jste vytvořili dříve, a klikněte na **odkaz na datový zdroj**. Otevře se stránka výstupu datového zdroje.
1. Zkopírujte **adresu URL streamování** pod přehrávačem videa pro datový zdroj.
1. Vraťte se do programu Azure Media Player v prohlížeči a vložte **adresu URL streamování** do pole URL programu Azure Media Player.
1. Klepněte na tlačítko **Aktualizovat přehrávač**.
1. Kliknutím na ikonu **přehrávání** ve videu zobrazíte datový zdroj na vyžádání.

## <a name="clean-up-resources"></a>Vyčištění prostředků

> [!IMPORTANT]
> Zastavte služby! Po dokončení kroků v tomto rychlém startu, ujistěte se, že zastavit živé události a koncový bod streamování nebo budete i nadále účtovat za dobu, po kterou zůstávají spuštěny. Chcete-li zastavit živou událost, podívejte se na kroky 2 a 3 výše zastavení vysílání.

### <a name="stopping-the-streaming-endpoint"></a>Zastavení koncového bodu streamování

1. V seznamu Media Services vyberte **položku Koncové body streamování**.
2. Klikněte na **výchozí** koncový bod streamování, který jste začali dříve. Otevře se stránka koncového bodu.
3. Klikněte na **Stop**.  Tím se zastaví koncový bod streamování.

> [!TIP]
> Pokud nechcete zachovat prostředky z této události, nezapomeňte je odstranit, aby se zabránilo účtování za úložiště.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Jaký článek je další v pořadí](./live-events-outputs-concept.md)
