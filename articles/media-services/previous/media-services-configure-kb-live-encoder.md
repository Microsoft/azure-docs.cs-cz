---
title: Konfigurace kodéru Haivision KB pro odeslání živého datového proudu s jednou přenosovou rychlostí do Azure | Microsoft Docs
description: V tomto tématu se dozvíte, jak nakonfigurovat kodér Haivision KB Live pro odeslání datového proudu s jednou přenosovou rychlostí do kanálů AMS, které mají povolené kódování v reálném čase.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: vsood
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 20c75672bc7198fb028278dc5432cc6a77e3766e
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016861"
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Použití kodéru Haivision KB Live k odeslání živého datového proudu s jednou přenosovou rychlostí

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

V tomto tématu se dozvíte, jak nakonfigurovat kodér kodéru [HAVISION KB](https://www.haivision.com/products/kb-series/) pro odesílání datových proudů s jednou přenosovou rychlostí do kanálů AMS, které mají povolené kódování v reálném čase. Další informace najdete v článku o [práci s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

V tomto kurzu se dozvíte, jak spravovat Azure Media Services (AMS) pomocí nástroje Azure Media Services Explorer (AMSE). Tento nástroj se spouští jenom na počítačích s Windows. Pokud používáte systém Mac nebo Linux, použijte Azure Portal k vytvoření [kanálů](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) a [programů](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Požadavky
*   Přístup k kodéru Haivision KB, na kterém běží software SW verze 5.01 nebo vyšší.
* [Vytvoření účtu Azure Media Services](media-services-portal-create-account.md)
* Ujistěte se, že je spuštěný koncový bod streamování. Další informace najdete v tématu [Správa koncových bodů streamování v účtu Media Services](media-services-portal-manage-streaming-endpoints.md) .
* Nainstalujte nejnovější verzi nástroje [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
* Spusťte nástroj a připojte se ke svému účtu AMS.

## <a name="tips"></a>Tipy
* Kdykoli je to možné, použijte kabelové internetové připojení.
* Vhodným pravidlem při určování požadavků na šířku pásma je zdvojnásobit přenosové rychlosti streamování. I když se nejedná o povinný požadavek, pomáhá zmírnit dopad zahlcení sítě.
* Při používání softwarových kodérů zavřete všechny nepotřebné programy.

## <a name="create-a-channel"></a>Vytvoření kanálu
1. V nástroji AMSE přejděte na kartu **Live** a klikněte pravým tlačítkem myši v oblasti kanálu. Vyberte **vytvořit kanál...** z nabídky.
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Zadejte název kanálu, pole Popis je volitelné. V části Nastavení kanálu vyberte **Standard** pro možnost Live Encoding a vstupní protokol nastavte na **RTMP**. Všechna ostatní nastavení můžete ponechat beze změny. Ujistěte se, že je vybraná možnost **Spustit nový kanál** .
3. Klikněte na **vytvořit kanál**.
[Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> Spuštění kanálu může trvat až 20 minut.

## <a name="configure-the-haivision-kb-encoder"></a>Konfigurace kodéru Haivision KB
V tomto kurzu se použijí následující nastavení výstupu. Zbývající část této části popisuje konfigurační kroky podrobněji.

Video:
-   Kodek: H.264
-   Profil: vysoký (úroveň 4.0)
-   Přenosová rychlost: 5000 KB/s
-   Klíčový snímek: 2 sekundy (60 snímků)
-   Snímková frekvence: 30

Kazet
-   Kodek: AAC (LC)
-   Přenosová rychlost: 192 kb/s
-   Vzorkovací frekvence: 44,1 kHz

## <a name="configuration-steps"></a>Postup konfigurace
1.  Přihlaste se k uživatelskému rozhraní Haivision KB.
2.  Klikněte na **tlačítko nabídky** v centru řízení kanálů a vyberte **Přidat kanál** .  
    ![Snímek obrazovky 2017-08-14 na adrese 9.15.09](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Do pole název zadejte **název kanálu** a klikněte na další.  
    ![Snímek obrazovky 2017-08-14 na adrese 9.19.07](./media/media-services-configure-kb-live-encoder/step3.png)
4.  V rozevíracím seznamu **vstupní zdroj** vyberte **zdroj vstupu kanálu** a klikněte na další.
    ![Snímek obrazovky 2017-08-14 na adrese 9.20.44](./media/media-services-configure-kb-live-encoder/step4.png)
5.  V rozevíracím seznamu **Šablona kodéru** vyberte **H264-720-AAC-192** a klikněte na další.
    ![Snímek obrazovky 2017-08-14 na adrese 9.23.15](./media/media-services-configure-kb-live-encoder/step5.png)
6.  V rozevíracím seznamu **vybrat nový výstup** zvolte **RTMP** a klikněte na další.  
    ![Snímek obrazovky 2017-08-14 na adrese 9.27.51](./media/media-services-configure-kb-live-encoder/step6.png)
7.  V okně **výstup kanálu** vyplňte informace o Azure streamu. Vložte odkaz **RTMP** z nastavení počátečního kanálu v oblasti **serveru** . V oblasti **Název výstupu** zadejte název kanálu. V oblasti šablona názvu streamu použijte šablonu RTMPStreamName_% video_bitrate% pro pojmenování datového proudu.
    ![Snímek obrazovky 2017-08-14 na adrese 9.33.17](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Klikněte na další a potom na Hotovo.
9.  Kliknutím na **tlačítko Přehrát** spusťte kanál kodéru.  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Přehrání testu
Přejděte do nástroje AMSE a klikněte pravým tlačítkem myši na kanál, který chcete testovat. V nabídce najeďte myší na přehrávání náhledu a vyberte možnost s Azure Media Player.

Pokud se datový proud objeví v přehrávači, kodér byl správně nakonfigurován pro připojení k AMS.

Pokud dojde k chybě, kanál se musí resetovat a nastavení kodéru se upraví. Pokyny najdete v článku věnovaném řešení potíží.

## <a name="create-a-program"></a>Vytvoření programu
1.  Po potvrzení přehrávání kanálu vytvořte program. Na kartě živá v nástroji AMSE klikněte pravým tlačítkem myši v oblasti programu a vyberte možnost vytvořit nový program.
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Pojmenujte program a v případě potřeby upravte délku okna archivu (výchozí nastavení je 4 hodiny). Můžete také zadat umístění úložiště nebo ponechat jako výchozí.
2.  Zaškrtněte políčko Spustit program nyní.
3.  Klikněte na vytvořit program.
4.  Po spuštění programu potvrďte přehrávání tak, že kliknete pravým tlačítkem myši na program a přejdete na přehrávání těchto programů a pak vyberete Azure Media Player.
5.  Po potvrzení klikněte znovu pravým tlačítkem myši na program a vyberte možnost Kopírovat výstupní adresu URL do schránky (nebo z nabídky informace o programu a nastavení načtěte tyto informace z nabídky).

Stream je teď připravený k vložení do přehrávače nebo k distribuci do cílové skupiny pro živé prohlížení.

> [!NOTE]
> Vytváření programu trvá méně času než vytvoření kanálu.
