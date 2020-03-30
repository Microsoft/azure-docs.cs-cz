---
title: Nakonfigurujte kodér NewTek TriCaster tak, aby odesílal jeden datový proud | Dokumenty společnosti Microsoft
description: Toto téma ukazuje, jak nakonfigurovat kodér Trykastru živého připojení k odeslání jednoho datového toku do kanálů AMS, které jsou povoleny pro živé kódování.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: 8973181a-3059-471a-a6bb-ccda7d3ff297
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 7909fbb958a66d00616d4ed1b844d02bb47d997e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152495"
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Použití kodéru NewTek TriCaster k odeslání jednoho datového proudu  
> [!div class="op_single_selector"]
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

Tento článek ukazuje, jak nakonfigurovat živý kodér [NewTek TriCaster](https://newtek.com/products/tricaster-40.html) pro odeslání jednoho datového toku do kanálů AMS, které jsou povoleny pro živé kódování. Další informace najdete v článku o [práci s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Tento kurz ukazuje, jak spravovat Azure Media Services (AMS) pomocí nástroje Azure Media Services Explorer (AMSE). Tento nástroj běží pouze na Windows PC. Pokud používáte Mac nebo Linux, použijte portál Azure k vytváření [kanálů](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) a [programů](media-services-portal-creating-live-encoder-enabled-channel.md).

Při použití Tricaster pro odesílání příspěvku krmiva do Kanálů AMS, které jsou povoleny pro živé kódování, může být video / audio závady ve vaší živé události, pokud používáte určité funkce tricasteru, jako je rychlé řezání mezi kanály, nebo přepínání do / z břidlice. Tým AMS pracuje na řešení těchto problémů, do té doby se nedoporučuje používat tyto funkce.

> [!NOTE]
>  Zvažte přechod na TLS 1.2, předem nabízenou verzi TLS.

## <a name="prerequisites"></a>Požadavky

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

    ![tricastr](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Zadejte název kanálu, pole popisu je volitelné. V části Nastavení kanálu vyberte **standardní** pro volbu živékódování s vstupním protokolem nastaveným na **RTMP**. Všechna ostatní nastavení můžete ponechat tak, jak jsou.

    Ujistěte se, že je nyní vybrán a vybrán **nový kanál.**

3. Klepněte na **tlačítko Vytvořit kanál**.

   ![tricastr](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> Spuštění kanálu může trvat až 20 minut.
>
>

Během spuštění kanálu můžete [kodér nakonfigurovat](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

> [!IMPORTANT]
> Fakturace začíná, jakmile kanál přejde do stavu připravenosti. Další informace naleznete v [tématu Channel stavy](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="configure-the-newtek-tricaster-encoder"></a><a id="configure_tricaster_rtmp"/>Konfigurace kodéru NewTek TriCaster

V tomto kurzu se používají následující nastavení výstupu. Zbývající část této části popisuje kroky konfigurace podrobněji.

**Video**:

* Kodek: H.264
* Profil: vysoký (úroveň 4.0)
* Přenosová rychlost: 5000 kbps
* Klíčový snímek: 2 sekundy (60 sekund)
* Snímková frekvence: 30

**Zvuk**:

* Kodek: AAC (LC)
* Přenosová rychlost: 192 kb/s
* Vzorkovací frekvence: 44,1 kHz

### <a name="configuration-steps"></a>Postup konfigurace

1. Vytvořte nový projekt **NewTek TriCaster** v závislosti na tom, jaký zdroj vstupu videa se používá.
2. Jakmile v rámci tohoto projektu, najít **tlačítko Stream,** a klepněte na ikonu ozubeného kola vedle něj pro přístup k nabídce konfigurace datového proudu.

    ![tricastr](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. Po otevření nabídky klikněte pod nadpisem Připojení na **Nový.** Po zobrazení výzvy k zadání typu připojení vyberte **položku Adobe Flash**.

    ![tricastr](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. Klikněte na tlačítko **OK**.
5. Profil FMLE lze nyní importovat klepnutím na rozevírací šipku v části **Profil streamování** a přechodem na **Procházet**.

    ![tricastr](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. Přejděte na místo, kde byl uložen nakonfigurovaný profil FMLE.
7. Vyberte ji a stiskněte **ok**.

    Po nahrání profilu přejděte k dalšímu kroku.
8. Získejte vstupní adresu URL kanálu, abyste ji mohli přiřadit ke **koncovému bodu RTMP tricasteru**.

    Přejděte zpět na nástroj AMSE a zkontrolujte stav dokončení kanálu. Jakmile se stát změní ze **spuštění** na **spuštěný**, můžete získat vstupní adresu URL.

    Když je kanál spuštěn, klikněte pravým tlačítkem myši na název kanálu, přejděte na příkaz **přejděte** na příkaz Kopírovat vstupní adresu URL do schránky a vyberte primární vstupní adresu **URL**.  

    ![tricastr](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Tyto informace vložte do pole **Umístění** v části **Server Flash** v rámci projektu Tricaster. V poli **ID datového proudu** také přiřaďte název datového proudu.

    Pokud byly informace o datovém proudu přidány do profilu FMLE, lze je importovat také do této části klepnutím na tlačítko **Importovat nastavení**, přechodem do uloženého profilu FMLE a klepnutím na **tlačítko OK**. Příslušná pole serveru Flash by měla být vyplněna informacemi z FMLE.

    ![tricastr](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. Po dokončení klikněte na **OK** v dolní části obrazovky. Když jsou video a audio vstupy do trikastru připraveny, začněte streamovat do AMS kliknutím na tlačítko **Stream.**

     ![tricastr](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> Před klepnutím na tlačítko **Stream**je **nutné** zajistit, aby byl kanál připraven.
> Také se ujistěte, že nechcete opustit kanál v pohotovostním stavu bez vstupního příspěvku po dobu delší než > 15 minut.
>
>

## <a name="test-playback"></a>Přehrávání testů

Přejděte na nástroj AMSE a klepněte pravým tlačítkem myši na kanál, který chcete testovat. V nabídce najeďte na **Přehrát náhled** a vyberte pomocí **Programu Azure Media Player**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Pokud se datový proud zobrazí v přehrávači, je kodér správně nakonfigurován pro připojení k AMS.

Pokud je přijata chyba, kanál bude nutné obnovit a upravit nastavení kodéru. Pokyny naleznete v článku [pro řešení potíží.](media-services-troubleshooting-live-streaming.md)  

## <a name="create-a-program"></a>Vytvoření programu

1. Po potvrzení přehrávání kanálu vytvořte program. Na kartě **Živé** v nástroji AMSE klepněte pravým tlačítkem myši do oblasti programu a vyberte **příkaz Vytvořit nový program**.  

    ![tricastr](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
2. Pojmenujte program a v případě potřeby upravte **délku archivačního okna** (výchozí hodnota je čtyři hodiny). Můžete také zadat umístění úložiště nebo ponechat jako výchozí.  
3. Zaškrtněte **políčko Spustit program nyní.**
4. Klepněte na **tlačítko Vytvořit program**.  

    >[!NOTE]
    >Vytvoření programu trvá méně času než vytváření kanálu.
        
5. Jakmile je program spuštěn, potvrďte přehrávání kliknutím pravým tlačítkem myši na program a přechodem na **přehrávání programů** a výběrem pomocí programu Azure **Media Player**.  
6. Po potvrzení klepněte pravým tlačítkem myši na program znovu a vyberte **kopírovat výstupní adresu URL do schránky** (nebo načíst tyto informace z **možnosti Informace a nastavení programu** z nabídky).

Datový proud je nyní připraven k vložení do přehrávače nebo k distribuci publiku pro živé sledování.  

## <a name="troubleshooting"></a>Řešení potíží

Pokyny naleznete v článku [pro řešení potíží.](media-services-troubleshooting-live-streaming.md)

## <a name="next-step"></a>Další krok

Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
