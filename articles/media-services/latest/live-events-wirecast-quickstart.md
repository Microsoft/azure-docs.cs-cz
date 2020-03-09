---
title: Vytvoření živého streamu Azure Media Services s portálem a Wirecast
description: Naučte se vytvořit živý datový proud Azure Media Service
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/06/2020
ms.openlocfilehash: c0eaf3907cbfcd86424b1d2cbc03930a7af72786
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927623"
---
# <a name="create-a-azure-media-services-live-stream-with-the-portal-and-wirecast"></a>Vytvoření živého streamu Azure Media Services s portálem a Wirecast

Tato Začínáme příručka předpokládá, že máte předplatné Azure a máte vytvořený účet Azure Media Services.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal

Otevřete svůj webový prohlížeč a přejděte na [portál Microsoft Azure Portal](https://portal.azure.com/). Zadejte přihlašovací údaje pro přihlášení k portálu. Výchozím zobrazením je váš řídicí panel služby.

V tomto rychlém startu se pokryjeme:

- Nastavení místního kodéru pomocí bezplatné zkušební verze Wirecast pro odesílání dat
- Nastavení živého datového proudu
- Nastavení výstupů živého streamu
- Spuštění výchozího koncového bodu streamování
- Použití Azure Media Player k zobrazení živého streamu a výstupu na vyžádání

Abychom mohli něco zjednodušit, použijeme pro Azure Media Services v Wirecast, kódování předávacího cloudu a RTMP předdefinované kódování.

## <a name="setting-up-an-on-premises-encoder-with-wirecast"></a>Nastavení místního kodéru pomocí Wirecast

1. Stažení a instalace Wirecast pro váš operační systém na https://www.telestream.net
1. Spusťte aplikaci a k registraci produktu použijte svoji oblíbenou e-mailovou adresu.  Nechejte aplikaci otevřenou.
1. Obdržíte e-mail s výzvou k ověření vaší e-mailové adresy a aplikace spustí bezplatnou zkušební verzi.
1. Doporučené: Podívejte se na video kurz na obrazovce otevírání aplikace.

## <a name="setting-up-an-azure-media-services-live-stream"></a>Nastavení Azure Media Services živého streamu

1. Po přechodu na účet Azure Media Services v rámci portálu vyberte **živé streamování** ze seznamu Media Services.
1. Kliknutím na **Přidat živou událost** vytvořte novou událost živého streamování.
1. Do pole **název** živé události zadejte název nové události, například *TestLiveEvent* .
1. Do pole **Popis** Zadejte nepovinný popis události.
1. Vyberte přepínač **předávat – bez cloudového kódování** .
1. Vyberte přepínač **RTMP** . 
1. Ujistěte se, že přepínač **No** není vybraný pro možnost Spustit živou událost, aby se zabránilo fakturaci za živou událost předtím, než bude připravená.  (Po zahájení živé události se začne účtovat.)
1. Kliknutím na tlačítko **Revize + vytvořit** zkontrolujte nastavení.
1. Kliknutím na tlačítko **vytvořit** vytvořte živou událost. Pak se vrátíte do zobrazení seznamu živých událostí.
1. Klikněte na **odkaz na živou událost** , kterou jste právě vytvořili. Všimněte si, že vaše událost je zastavená.
1. Tuto stránku nechte otevřenou v prohlížeči.  Později se k ní vrátíme.

## <a name="setting-up-a-live-stream-with-wirecast-studio"></a>Nastavení živého datového proudu pomocí Wirecast studia

1. Za předpokladu, že máte stále otevřenou aplikaci Wirecast, v hlavní nabídce vyberte **vytvořit prázdný dokument** a pak klikněte na **pokračovat**.
1. Najeďte myší na první vrstvu v oblasti Wirecast vrstev.  Klikněte na ikonu **Přidat** , která se zobrazí, a vyberte vstup videa, který chcete streamovat.  Otevře se dialogové okno hlavní vrstva 1.
1. V nabídce vyberte možnost **zachytávání videa** a pak vyberte kameru, kterou chcete použít. Zobrazení z kamery se zobrazí v oblasti verze Preview.
1. Najeďte myší na druhou vrstvu v oblasti Wirecast vrstev. Klikněte na ikonu **Přidat** , která se zobrazí, a vyberte zvukový vstup, který chcete streamovat.  Otevře se dialogové okno hlavní vrstva 2.
1. V nabídce vyberte záznam **zvuku** a pak vyberte zvukový vstup, který chcete použít. 
1. V hlavní nabídce vyberte **Nastavení výstupu**.  Zobrazí se dialogové okno výstup.
1. V rozevíracím seznamu výstup vyberte **Azure Media Services** .  Nastavení výstupu pro Azure Media Services automaticky vyplní *většinu* nastavení výstupu.
1. V další části se můžete vrátit do Azure Media Services v prohlížeči a zkopírovat *vstupní adresu URL* a zadat do nich výstupní nastavení.

### <a name="copy-and-paste-the-input-url"></a>Zkopírování a vložení vstupní adresy URL

1. Zpátky na stránce Azure Media Services na portálu klikněte na **Spustit** , aby se spustila událost živého streamu. (Fakturace začíná nyní.)
2. Klikněte na přepínač **zabezpečeno/Nezabezpečeno** a nastavte jej na hodnotu **nezabezpečené**.  Tato akce nastaví protokol na RTMP místo RTMP.
3. Zkopírujte **vstupní adresu URL** do schránky.
4. Přepněte do aplikace Wirecast a vložte **vstupní adresu URL** do pole **adresa** v nastavení výstup.
5. Klikněte na tlačítko **OK**.

## <a name="setting-up-outputs"></a>Nastavení výstupů

Tato část nastaví vaše výstupy a umožní vám uložit záznam živého streamu.  

> [!NOTE]
> Aby bylo možné streamovat tento výstup, musí být spuštěn koncový bod streamování.  Další informace najdete v části spuštění výchozího koncového bodu streamování.

1. Klikněte na odkaz **vytvořit výstupy** pod výstupem prohlížeče videa.
1. Pokud chcete, upravte název výstupu v poli **název** na více uživatelsky přívětivé, aby bylo možné ho později snadno najít.
1. Ponechte všechna zbývající pole pouze teď.
1. Klikněte na **Další** přidat Lokátor streamování.
1. Změňte název lokátoru na více uživatelsky přívětivé, pokud je to žádoucí.
1. Všechno ostatní na této obrazovce teď ponechte samostatně.
1. Klikněte na možnost **Vytvořit**.

## <a name="starting-the-broadcast"></a>Spouští se všesměrové vysílání.

1. V Wirecast vyberte **výstup > spustit/zastavit vysílání > start Azure Media Services: Azure Media Services** z hlavní nabídky.  Až se datový proud pošle do živé události, zobrazí se aktivní okno v Wirecast v přehrávači videa živé události na stránce živá událost v Azure Media Services.

1. Kliknutím na tlačítko **Přejít** pod oknem Náhled spustíte vysílání videa a zvuku vybraného pro Wirecast vrstvy.

> [!TIP]
> Pokud dojde k chybě, zkuste znovu načíst přehrávač kliknutím na odkaz znovu načíst přehrávač výše v přehrávači.

## <a name="running-the-default-streaming-endpoint"></a>Spuštění výchozího koncového bodu streamování

1. Ujistěte se, že je koncový bod streamování spuštěný, a to výběrem **koncových bodů streamování** v seznamu Media Services. Přejdete na stránku koncových bodů streamování.
1. Pokud je stav výchozího koncového bodu streamu zastavený, klikněte na **výchozí** koncový bod streamování. Tím přejdete na stránku pro daný koncový bod.
1. Klikněte na **Start**.  Tím se spustí koncový bod streamování.

## <a name="play-the-output-broadcast-with-azure-media-player"></a>Přehrát výstup všesměrového vysílání s Azure Media Player

1. Zkopírujte **adresu URL streamování** do výstupního přehrávače videa. 
1. Ve webovém prohlížeči otevřete ukázku Azure Media Player https://ampdemo.azureedge.net/azuremediaplayer.html
1. Vložte **adresu URL streamování** do pole adresa URL Azure Media Player.
1. Klikněte na tlačítko **aktualizovat přehrávač** .
1. Kliknutím na ikonu **Přehrát** na videu zobrazíte živý datový proud.

## <a name="stopping-the-broadcast"></a>Zastavuje se všesměrové vysílání.

Když si myslíte, že máte k dispozici streamování obsahu, zastavte vysílání.

1. V Wirecast klikněte na tlačítko **vysílání** .  Tím se zastaví všesměrové vysílání z Wirecast.
1. Na portálu klikněte na **zastavit**. Zobrazí se zpráva s upozorněním, že živý datový proud skončí, ale výstup se teď stane prostředkem na vyžádání.
1. Ve zprávě upozornění klikněte na **zastavit** . V Azure Media Player se nyní zobrazí chyba, protože živý datový proud již není k dispozici.

## <a name="play-the-on-demand-output-with-the-azure-media-player"></a>Přehrání výstupu na vyžádání pomocí Azure Media Player

Vytvořený výstup je teď dostupný pro streamování na vyžádání, pokud je spuštěný koncový bod streamování.

1. Přejděte do seznamu Media Services a vyberte **prostředky**.
1. Vyhledejte výstup události, který jste vytvořili dříve, a klikněte na **odkaz na prostředek**. Otevře se stránka výstup assetu.
1. Zkopírujte **adresu URL streamování** v přehrávači videa pro daný Asset.
1. Vraťte se do Azure Media Player v prohlížeči a vložte **adresu URL streamování** do pole adresa URL Azure Media Player.
1. Klikněte na **aktualizovat přehrávač**.
1. Kliknutím na ikonu **Přehrát** na videu zobrazíte prostředek na vyžádání.

## <a name="clean-up-resources"></a>Vyčištění prostředků

  > [!IMPORTANT]
  > Zastavte služby. Po dokončení kroků v tomto rychlém startu se ujistěte, že se zastaví živá událost a koncový bod streamování, nebo se vám bude dál účtovat čas, kdy je spuštěný. Pokud chcete zastavit živou událost, přečtěte si téma zastavení vysílání, kroky 2 a 3 výše.

### <a name="stopping-the-streaming-endpoint"></a>Zastavování koncového bodu streamování

1. V seznamu Media Services vyberte **koncové body streamování**.
2. Klikněte na **výchozí** koncový bod streamování, který jste spustili dříve. Tím se otevře stránka koncového bodu.
3. Klikněte na **zastavit**.  Tím se zastaví koncový bod streamování.

>[!TIP]
>Pokud nechcete, aby se prostředky z této události udržovaly, nezapomeňte je odstranit, aby se zabránilo účtování úložiště.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Který článek je dál v posloupnosti](./live-events-outputs-concept.md)
