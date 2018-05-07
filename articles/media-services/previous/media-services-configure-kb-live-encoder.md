---
title: "Konfigurace kodér Haivision KB k odeslání živý datový proud s jednou přenosovou rychlostí do Azure | Microsoft Docs"
description: "Toto téma ukazuje, jak nakonfigurovat za provozu kodér Haivision KB k odeslání datový proud s jednou přenosovou rychlostí do AMS kanály, které jsou povolené kódování v reálném čase."
services: media-services
documentationcenter: 
author: dbgeorge
manager: vsood
editor: 
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 02/02/2018
ms.author: juliako;dbgeorge
ms.openlocfilehash: 25077cd9338a2764c6dff9e755812033685f6641
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2018
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Pomocí kodéru za provozu Haivision KB odesílat živý datový proud s jednou přenosovou rychlostí
> [!div class="op_single_selector"]
> * [Elemental za provozu](media-services-configure-elemental-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

Toto téma ukazuje, jak nakonfigurovat [za provozu kodér Havision KB](https://www.haivision.com/products/kb-series/) ke odesílat datový proud s jednou přenosovou rychlostí do AMS kanály, které jsou povolené kódování v reálném čase. Další informace najdete v článku o [práci s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Tento kurz ukazuje, jak spravovat Azure Media Services (AMS) s nástrojem Azure Media Services Explorer (AMSE). Tento nástroj lze spustit pouze na počítačích s Windows. Pokud jste na Mac nebo Linux, použijte portál Azure k vytvoření [kanály](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) a [programy](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Požadavky
*   Přístup k kodér Haivision KB, systémem SW 5.01 nebo vyšší, nebo vyšší.
* [Vytvoření účtu Azure Media Services](media-services-portal-create-account.md)
* Ujistěte se, je koncový bod streamování, spuštěná. Další informace najdete v tématu [spravovat koncové body streamování v účtu Media Services](media-services-portal-manage-streaming-endpoints.md)
* Nainstalujte nejnovější verzi [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) nástroj.
* Spusťte nástroj a připojte se ke svému účtu AMS.

## <a name="tips"></a>Tipy
* Pokud je to možné, použijte standardní kabelové internetové připojení.
* Obvykle při určování nároky na šířku pásma je dvakrát streamování přenosových rychlostí. Přestože není povinný požadavek, pomáhá omezit účinek zahlcení sítě.
* Při použití kodérů založeného na softwaru, zavřete se všechny nepotřebné programy.

## <a name="create-a-channel"></a>Vytvoření kanálu
1. V nástroj AMSE, přejděte na **živé** kartě a klikněte pravým tlačítkem myši v oblasti kanálu. Vyberte **vytvořit kanál...** v nabídce.
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Zadejte název kanálu, pole popisu je volitelné. V části Nastavení kanál, vyberte **standardní** pro Live Encoding možnost s protokolem vstup nastavena na **RTMP**. Všechna ostatní nastavení jako je můžete nechat. Zajistěte, aby **nyní spustit nový kanál** je vybrána.
3. Klikněte na tlačítko **vytvořit kanál**.
[Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> Kanál může trvat až 20 minut před spuštěním.

## <a name="configure-the-haivision-kb-encoder"></a>Konfigurace kodér Haivision KB
V tomto kurzu se používají následující nastavení výstup. Zbývající část tohoto oddílu popisuje kroky konfigurace podrobněji.

Video:
-   Codec: H.264
-   Profil: Vysoká (úroveň 4.0)
-   Přenosovou rychlostí: 5000 kb/s
-   Klíčový snímek: 2 sekund (60 sekund)
-   Míra s rámečkem: 30

Zvuk:
-   Kodeků: AAC (LC)
-   Přenosovou rychlostí: 192 kb /
-   Vzorkovací frekvence: 44,1 kHz

## <a name="configuration-steps"></a>Kroky konfigurace
1.  Přihlaste se k uživatelské rozhraní Haivision KB.
2.  Klikněte na **tlačítko nabídky** v kanálu řídicí centrum a vyberte **kanálu přidat**  
    ![Snímek 2017-08-14 na 9.15.09 obrazovky AM.png](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Typ **název kanálu** v názvu pole a klikněte na tlačítko Další.  
    ![Snímek 2017-08-14 na 9.19.07 obrazovky AM.png](./media/media-services-configure-kb-live-encoder/step3.png)
4.  Vyberte **kanál vstupní zdroj** z **vstupní zdroj** rozevíracího seznamu a klikněte na tlačítko Další.
    ![Snímek 2017-08-14 na 9.20.44 obrazovky AM.png](./media/media-services-configure-kb-live-encoder/step4.png)
5.  Z **kodér šablony** rozevíracího seznamu vyberte **H264 720 AAC 192** a klikněte na tlačítko Další.
    ![Snímek 2017-08-14 na 9.23.15 obrazovky AM.png](./media/media-services-configure-kb-live-encoder/step5.png)
6.  Z **vyberte nový výstupní** rozevíracího seznamu vyberte **RTMP** a klikněte na tlačítko Další.  
    ![Snímek 2017-08-14 na 9.27.51 obrazovky AM.png](./media/media-services-configure-kb-live-encoder/step6.png)
7.  Z **výstup kanál** okně naplnit informace o Azure datového proudu. Vložení **RTMP** odkaz z instalačního programu Počáteční kanál v **Server** oblasti. V **název výstupu** oblasti zadejte název kanálu. V oblasti šablony názvu datového proudu pomocí šablony RTMPStreamName_ % video_bitrate % název datového proudu.
    ![Snímek 2017-08-14 na 9.33.17 obrazovky AM.png](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Klikněte na tlačítko Další a pak kliknete na Hotovo.
9.  Klikněte **tlačítko Přehrát** zahájíte kodér kanál.  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Přehrávání testu
Přejděte na nástroj AMSE a klikněte pravým tlačítkem na kanál, který má být testována. V nabídce najeďte myší na přehrávání ve verzi Preview a vyberte pomocí Azure Media Player.

Pokud datový proud se zobrazí v přehrávači, pak kodér správně nakonfigurovaný pro připojení k AMS.

Je-li k chybě, kanál je třeba obnovit a upravit nastavení kodéru. Najdete v článku řešení problémů s pokyny.

## <a name="create-a-program"></a>Vytvořit program
1.  Po potvrzení kanálu přehrávání vytvořte program. Na kartě za provozu v nástroj AMSE klikněte pravým tlačítkem myši v oblasti program a vybrat vytvořit nový Program.
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Název programu a v případě potřeby upravte délka archivačního okna (s výchozím nastavením na čtyři hodiny). Můžete také určit umístění úložiště nebo ponechte jako výchozí.
2.  Políčko spuštění Program nyní.
3.  Kliknutím na vytvořit Program.
4.  Jakmile program běží, potvrďte přehrávání kliknutím pravým tlačítkem na program a přejdete na přehrání programech a pak vyberete s Azure Media Player.
5.  Po potvrzení znovu klikněte program pravým tlačítkem a vyberte Kopírovat výstup adresy URL do schránky (nebo načtení těchto informací z informace o programu a možnost nastavení v nabídce).

Datový proud je nyní připravena vložených v přehrávač, nebo distribuovány do cílovou skupinu pro zobrazení za provozu.

> [!NOTE]
> Vytváření programu trvá kratší dobu, než vytvoření kanálu.