---
title: Konfigurovat kodér Haivision KB odesílat živý datový proud s jednou přenosovou rychlostí do Azure | Dokumentace Microsoftu
description: Toto téma ukazuje, jak nakonfigurovat živé kodér Haivision KB k odesílání datového proudu s jednou přenosovou rychlostí do AMS kanály, které jsou povolené kódování v reálném čase.
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
ms.date: 11/04/2018
ms.author: juliako;dbgeorge
ms.openlocfilehash: f6d4fcf5e6ec8029bb0e7c303849fdceef5bace7
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51012084"
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Použití živého kodér Haivision KB odesílat živý datový proud s jednou přenosovou rychlostí
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

Toto téma ukazuje, jak nakonfigurovat [kodér služby live Encoding Havision KB](https://www.haivision.com/products/kb-series/) kodér Odeslat datový proud s jednou přenosovou rychlostí do AMS kanály, které jsou povolené kódování v reálném čase. Další informace najdete v článku o [práci s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Tento kurz ukazuje, jak spravovat Azure Media Services (AMS) s nástrojem Azure Media Services Explorer (AMSE). Tento nástroj lze spustit pouze na počítač s Windows. Pokud jste v systému Mac nebo Linux, pomocí webu Azure portal k vytvoření [kanály](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) a [programy](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Požadavky
*   Přístup k kodér Haivision KB, Jihozápadní 5.01 nebo vyšší, s nebo vyšší.
* [Vytvoření účtu Azure Media Services](media-services-portal-create-account.md)
* Ujistěte se, je koncový bod streamování, spuštěná. Další informace najdete v tématu [spravovat koncové body streamování v účtu Media Services](media-services-portal-manage-streaming-endpoints.md)
* Nainstalujte nejnovější verzi [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) nástroj.
* Spusťte nástroj a připojte se ke svému účtu AMS.

## <a name="tips"></a>Tipy
* Kdykoli je to možné, použijte standardní kabelové internetové připojení.
* Základním pravidlem při určování požadavků na šířku pásma, je dvakrát streamování přenosových rychlostí. Protože toto není povinné požadavek, pomáhá zmírnit dopad zahlcení sítě.
* Při použití softwarových kodérů uzavřete všechny nepotřebné programy.

## <a name="create-a-channel"></a>Vytvoření kanálu
1. Nástroj AMSE, přejděte na **Live** kartu a klikněte pravým tlačítkem v rámci oblasti kanálu. Vyberte **vytvořit kanál...** v nabídce.
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Zadejte název kanálu, do pole Popis je volitelný. V části nastavení kanálu, vyberte **standardní** pro možnost Live Encoding s protokolem vstupu, nastavte na **RTMP**. Všechna ostatní nastavení je můžete nechat. Ujistěte se, že **spustit nový kanál** zaškrtnuto.
3. Klikněte na tlačítko **vytvořit kanál**.
[Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> Kanál může trvat až 20 minut.

## <a name="configure-the-haivision-kb-encoder"></a>Konfigurovat kodér Haivision KB
V tomto kurzu se používají následující nastavení výstupu. Zbytek tohoto oddílu popisuje jednotlivé kroky konfigurace v podrobněji.

Video:
-   Kodek: H.264
-   Profil: Vysoce (úroveň 4.0)
-   S přenosovou rychlostí: 5000 kB /
-   Klíčový snímek: 2 sekundy (60 snímků)
-   Frekvence snímků: 30

Zvuk:
-   Kodek: AAC (LC –)
-   S přenosovou rychlostí: 192 kb /
-   Vzorkovací frekvence: 44,1 kHz

## <a name="configuration-steps"></a>Postup konfigurace
1.  Přihlaste se k uživatelské rozhraní Haivision KB.
2.  Klikněte na **tlačítko nabídky** v control centeru kanálu a vyberte **přidat kanálu**  
    ![Snímek 2017-08-14 na 9.15.09 obrazovky AM.png](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Typ **název kanálu** v názvu pole a klikněte na tlačítko Další.  
    ![Snímek 2017-08-14 na 9.19.07 obrazovky AM.png](./media/media-services-configure-kb-live-encoder/step3.png)
4.  Vyberte **kanál vstupní zdroj** z **vstupní zdroj** rozevíracího seznamu a klikněte na tlačítko Další.
    ![Snímek 2017-08-14 na 9.20.44 obrazovky AM.png](./media/media-services-configure-kb-live-encoder/step4.png)
5.  Z **šablony Encoder** rozevíracího seznamu zvolte **H264 – 720-AAC-192** a klikněte na tlačítko Další.
    ![Snímek 2017-08-14 na 9.23.15 obrazovky AM.png](./media/media-services-configure-kb-live-encoder/step5.png)
6.  Z **vyberte nový výstup** rozevíracího seznamu zvolte **RTMP** a klikněte na tlačítko Další.  
    ![Snímek 2017-08-14 na 9.27.51 obrazovky AM.png](./media/media-services-configure-kb-live-encoder/step6.png)
7.  Z **výstupní kanál** okně naplnit informace o Azure stream. Vložit **RTMP** odkaz z nastavení počáteční kanál ve **Server** oblasti. V **název výstupního** oblasti zadejte název kanálu. V oblasti Stream název šablony použijte šablonu RTMPStreamName_ % video_bitrate % název datového proudu.
    ![Snímek 2017-08-14 na 9.33.17 obrazovky AM.png](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Klikněte na tlačítko Další a pak klikněte na Hotovo.
9.  Klikněte na tlačítko **tlačítko Přehrát** spuštění kanálu kodér.  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Přehrávání testů
Přejděte na nástroj AMSE a klikněte pravým tlačítkem na kanál, který má být testována. Z nabídky najeďte myší přehrávání na verzi Preview a vyberte pomocí Azure Media Playeru.

Pokud datový proud se zobrazí v přehrávači, pak kodér správně nakonfigurovaný pro připojení k AMS.

Pokud k chybě, kanál je potřeba resetovat a upravit nastavení kodéru. Najdete v článku řešení problémů s pokyny.

## <a name="create-a-program"></a>Vytvoření programu
1.  Jakmile přehrávání kanálu je potvrzen, vytvořte program. Na kartě živé nástroj AMSE klikněte pravým tlačítkem v rámci oblasti program a zvolte Vytvořit nový Program.
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Zadejte název programu a v případě potřeby upravte délka okna archivu, (kde je použit výchozí čtyři hodiny). Můžete také určit umístění úložiště nebo ponechte jako výchozí.
2.  Políčko počáteční Program nyní.
3.  Klikněte na vytvořit Program.
4.  Jakmile program běží, potvrďte přehrávání kliknutím pravým tlačítkem myši program a přejdete k přehrávání programech a následným výběrem pomocí Azure Media Playeru.
5.  Až potvrdí, klikněte pravým tlačítkem na program znovu a vyberte Kopírovat výstupní adresy URL do schránky (nebo tyto informace načíst informace o programu a možnosti nastavení v nabídce).

Datový proud je teď připravený k součástí přehrávač nebo distribuované na cílovou skupinou pro živé zobrazení.

> [!NOTE]
> Vytvoření programu trvá méně času než vytváření kanálů.
