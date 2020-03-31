---
title: Konfigurace kodéru Haivision KB pro odesílání jednoho živého datového toku do Azure | Dokumenty společnosti Microsoft
description: Toto téma ukazuje, jak nakonfigurovat živý kodér Haivision KB tak, aby odesílal jeden datový proud datového toku do kanálů AMS, které jsou povoleny pro živé kódování.
services: media-services
documentationcenter: ''
author: dbgeorge
manager: vsood
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: afc0fcb6751a08b41010fa569c67a9827e0abec0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77131933"
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Použití živého kodéru Haivision KB k odeslání jednoho datového proudu  
> [!div class="op_single_selector"]
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

Toto téma ukazuje, jak nakonfigurovat [kodér živého kodéru Havision KB](https://www.haivision.com/products/kb-series/) tak, aby odesílá jeden datový proud datového toku do kanálů AMS, které jsou povoleny pro živé kódování. Další informace najdete v článku o [práci s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Tento kurz ukazuje, jak spravovat Azure Media Services (AMS) pomocí nástroje Azure Media Services Explorer (AMSE). Tento nástroj běží pouze na Windows PC. Pokud používáte Mac nebo Linux, použijte portál Azure k vytváření [kanálů](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) a [programů](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Požadavky
*   Přístup k kodéru Haivision KB se systémem SW v5.01 nebo vyšším.
* [Vytvoření účtu Azure Media Services](media-services-portal-create-account.md)
* Ujistěte se, že je spuštěn koncový bod streamování. Další informace naleznete v tématu [Správa koncových bodů streamování v účtu Mediálních služeb.](media-services-portal-manage-streaming-endpoints.md)
* Nainstalujte nejnovější verzi nástroje [AMSE.](https://github.com/Azure/Azure-Media-Services-Explorer)
* Spusťte nástroj a připojte se ke svému účtu AMS.

## <a name="tips"></a>Tipy
* Kdykoli je to možné, použijte kabelové internetové připojení.
* Vhodným pravidlem při určování požadavků na šířku pásma je zdvojnásobit přenosové rychlosti streamování. I když se nejedná o povinný požadavek, pomáhá zmírnit dopad přetížení sítě.
* Při použití softwarových kodérů uzavřete všechny nepotřebné programy.

## <a name="create-a-channel"></a>Vytvoření kanálu
1. V nástroji AMSE přejděte na kartu **Živé** a klikněte pravým tlačítkem myši v oblasti kanálu. Vybrat **Vytvořit kanál...** z nabídky.
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Zadejte název kanálu, pole popisu je volitelné. V části Nastavení kanálu vyberte **standardní** pro volbu živékódování s vstupním protokolem nastaveným na **RTMP**. Všechna ostatní nastavení můžete ponechat tak, jak jsou. Ujistěte se, že je nyní vybrán a vybrán **nový kanál.**
3. Klepněte na **tlačítko Vytvořit kanál**.
[Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> Spuštění kanálu může trvat až 20 minut.

## <a name="configure-the-haivision-kb-encoder"></a>Konfigurace kodéru Haivision KB
V tomto kurzu se používají následující nastavení výstupu. Zbývající část této části popisuje kroky konfigurace podrobněji.

Video:
-   Kodek: H.264
-   Profil: vysoký (úroveň 4.0)
-   Přenosová rychlost: 5000 kbps
-   Klíčový snímek: 2 sekundy (60 snímků)
-   Snímková frekvence: 30

Audio:
-   Kodek: AAC (LC)
-   Přenosová rychlost: 192 kb/s
-   Vzorkovací frekvence: 44,1 kHz

## <a name="configuration-steps"></a>Postup konfigurace
1.  Přihlaste se k uživatelskému rozhraní Haivision KB.
2.  Klikněte na **tlačítko Nabídky** v ovládacím centru kanálu a vyberte **Přidat kanál**  
    ![Snímek obrazovky 2017-08-14 v 9.15.09](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Do pole Název zadejte **název kanálu** a klepněte na tlačítko Další.  
    ![Snímek obrazovky 2017-08-14 v 9.19.07](./media/media-services-configure-kb-live-encoder/step3.png)
4.  V rozevíracím seznamu **Vstupní zdroj** vyberte vstupní **zdroj** kanálu a klepněte na další.
    ![Snímek obrazovky 2017-08-14 v 9.20.44](./media/media-services-configure-kb-live-encoder/step4.png)
5.  Z rozbalovacího souboru **Šablona kodéru** zvolte **H264-720-AAC-192** a klikněte na další.
    ![Snímek obrazovky 2017-08-14 v 9.23.15](./media/media-services-configure-kb-live-encoder/step5.png)
6.  V rozevíracím souboru **Vybrat nový výstup** zvolte **RTMP** a klepněte na další.  
    ![Snímek obrazovky 2017-08-14 v 9.27.51](./media/media-services-configure-kb-live-encoder/step6.png)
7.  Z okna **Výstup kanálu** naplnit informace o datovém proudu Azure. Vložte odkaz **RTMP** z počátečního nastavení kanálu v oblasti **Server.** V oblasti **Výstupní název** zadejte název kanálu. V oblasti Šablona názvu datového proudu použijte k pojmenování datového proudu šablonu RTMPStreamName_%video_bitrate %.
    ![Snímek obrazovky 2017-08-14 v 9.33.17](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Klikněte na další a potom klikněte na Hotovo.
9.  Kliknutím na **tlačítko Přehrát** spusťte kanál kodéru.  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Přehrávání testů
Přejděte na nástroj AMSE a klepněte pravým tlačítkem myši na kanál, který chcete testovat. V nabídce najeďte na Přehrát náhled a vyberte pomocí Programu Azure Media Player.

Pokud se datový proud zobrazí v přehrávači, je kodér správně nakonfigurován pro připojení k AMS.

Pokud je přijata chyba, je třeba kanál resetovat a upravit nastavení kodéru. Pokyny naleznete v článku pro řešení potíží.

## <a name="create-a-program"></a>Vytvoření programu
1.  Po potvrzení přehrávání kanálu vytvořte program. Na kartě Živé v nástroji AMSE klepněte pravým tlačítkem myši do oblasti programu a vyberte vytvořit nový program.
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Pojmenujte program a v případě potřeby upravte délku archivačního okna (výchozí hodnota je čtyři hodiny). Můžete také zadat umístění úložiště nebo ponechat jako výchozí.
2.  Zaškrtněte políčko Spustit program nyní.
3.  Klikněte na Vytvořit program.
4.  Jakmile je program spuštěn, potvrďte přehrávání kliknutím pravým tlačítkem myši na program a přechodem na přehrát program (y) a pak výběrem pomocí programu Azure Media Player.
5.  Po potvrzení klepněte pravým tlačítkem myši na program znovu a vyberte kopírovat výstupní adresu URL do schránky (nebo načíst tyto informace z možnosti Informace a nastavení programu z nabídky).

Datový proud je nyní připraven k vložení do přehrávače nebo k distribuci publiku pro živé sledování.

> [!NOTE]
> Vytvoření programu trvá méně času než vytváření kanálu.
