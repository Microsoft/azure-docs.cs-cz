---
title: Konfigurace kodéru NewTek transkodéry pro odesílání živého streamu s jednou přenosovou rychlostí | Microsoft Docs
description: V tomto tématu se dozvíte, jak nakonfigurovat kodér transkodéry Live pro odeslání datového proudu s jednou přenosovou rychlostí do kanálů AMS, které mají povolené kódování v reálném čase.
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
ms.openlocfilehash: 11ee8f52a8fd4db2d052eeaeef1387b011d23050
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77131556"
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Použití kodéru NewTek transkodéry k odeslání živého datového proudu s jednou přenosovou rychlostí  
> [!div class="op_single_selector"]
> * [Transkodéry](media-services-configure-tricaster-live-encoder.md)
> * [Aktivní element](media-services-configure-elemental-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

Tento článek ukazuje, jak nakonfigurovat kodér [NewTek transkodéry](https://newtek.com/products/tricaster-40.html) Live pro odeslání datového proudu s jednou přenosovou rychlostí do kanálů AMS, které mají povolené kódování v reálném čase. Další informace najdete v článku o [práci s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Tento kurz ukazuje, jak spravovat Azure Media Services (AMS) s nástrojem Azure Media Services Explorer (AMSE). Tento nástroj lze spustit pouze na počítač s Windows. Pokud používáte systém Mac nebo Linux, použijte Azure Portal k vytvoření [kanálů](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) a [programů](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> Při použití transkodéry pro odesílání v informačním kanálu o příspěvcích do kanálů AMS, které mají povolené kódování v reálném čase, může být video/audio histogramu v živé události, pokud používáte určité funkce transkodéry, jako je rychlé rozřezání mezi informačními kanály nebo přechod na/z SLAT. Tým AMS pracuje na řešení těchto problémů, a to až do té doby, nedoporučujeme tyto funkce používat.
>
>

## <a name="prerequisites"></a>Předpoklady

* [Vytvoření účtu Azure Media Services](media-services-portal-create-account.md)
* Ujistěte se, je koncový bod streamování, spuštěná. Další informace najdete v tématu [Správa koncových bodů streamování v účtu Media Services](media-services-portal-manage-streaming-endpoints.md) .
* Nainstalujte nejnovější verzi nástroje [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
* Spusťte nástroj a připojte se ke svému účtu AMS.

## <a name="tips"></a>Tipy

* Kdykoli je to možné, použijte standardní kabelové internetové připojení.
* Základním pravidlem při určování požadavků na šířku pásma, je dvakrát streamování přenosových rychlostí. Protože toto není povinné požadavek, pomáhá zmírnit dopad zahlcení sítě.
* Při použití softwarových kodérů uzavřete všechny nepotřebné programy.

## <a name="create-a-channel"></a>Vytvoření kanálu

1. V nástroji AMSE přejděte na kartu **Live** a klikněte pravým tlačítkem myši v oblasti kanálu. Vyberte **vytvořit kanál...** v nabídce.

    ![transkodéry](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Zadejte název kanálu, do pole Popis je volitelný. V části Nastavení kanálu vyberte **Standard** pro možnost Live Encoding a vstupní protokol nastavte na **RTMP**. Všechna ostatní nastavení je můžete nechat.

    Ujistěte se, že je vybraná možnost **Spustit nový kanál** .

3. Klikněte na **vytvořit kanál**.

   ![transkodéry](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> Kanál může trvat až 20 minut.
>
>

Při spuštění kanálu můžete [nakonfigurovat kodér](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

> [!IMPORTANT]
> Fakturace začíná, jakmile kanál přejde do stavu Připraveno. Další informace najdete v tématu [stavy kanálu](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="a-idconfigure_tricaster_rtmpconfigure-the-newtek-tricaster-encoder"></a><a id="configure_tricaster_rtmp"/>konfigurace kodéru Transkodéryu NewTek

V tomto kurzu se používají následující nastavení výstupu. Zbytek tohoto oddílu popisuje jednotlivé kroky konfigurace v podrobněji.

**Video**:

* Kodek: H.264
* Profil: Vysoce (úroveň 4.0)
* S přenosovou rychlostí: 5000 kB /
* Klíčový snímek: 2 sekundy (60 sekund)
* Frekvence snímků: 30

**Zvuk**:

* Kodek: AAC (LC –)
* S přenosovou rychlostí: 192 kb /
* Vzorkovací frekvence: 44,1 kHz

### <a name="configuration-steps"></a>Postup konfigurace

1. Vytvořte nový projekt **transkodéry NewTek** v závislosti na tom, jaký zdroj vstupu videa se používá.
2. V rámci tohoto projektu najděte tlačítko **Stream** a kliknutím na ikonu ozubeného kolečka vedle něho přejděte do nabídky konfigurace streamu.

    ![transkodéry](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. Po otevření nabídky klikněte na **Nový** pod nadpisem připojení. Po zobrazení výzvy k zadání typu připojení vyberte **Adobe Flash**.

    ![transkodéry](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. Klikněte na tlačítko **OK**.
5. Profil FMLE se teď dá importovat tak, že kliknete na šipku rozevíracího seznamu v části **profil streamování** a přejdete na **Procházet**.

    ![transkodéry](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. Přejděte do umístění, kde byl uložen nakonfigurovaný profil FMLE.
7. Vyberte ji a stiskněte **OK**.

    Po nahrání profilu přejděte k dalšímu kroku.
8. Získejte vstupní adresu URL kanálu, aby ji bylo možné přiřadit ke **koncovému bodu transkodéry RTMP**.

    Přejděte zpět na nástroj AMSE a zkontrolovat stav dokončení kanálu. Jakmile se stav změní z **počáteční** na **spuštěno**, můžete získat vstupní adresu URL.

    Když je kanál spuštěný, klikněte pravým tlačítkem myši na název kanálu, přejděte dolů na **Kopírovat vstupní adresu URL do schránky** a pak vyberte **primární vstupní adresu URL**.  

    ![transkodéry](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Tyto informace vložte do pole **umístění** v rámci projektu transkodéry v části **Server Flash** . Také v poli **ID streamu** přiřaďte název datového proudu.

    Pokud se informace o datovém proudu přidaly do profilu FMLE, můžete je do této části naimportovat i tak, že kliknete na **importovat nastavení**, přejdete na uložený profil FMLE a kliknete na **OK**. Odpovídající pole serveru Flash by se měla naplnit informacemi z FMLE.

    ![transkodéry](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. Po dokončení klikněte na **OK** v dolní části obrazovky. Až budou video a zvukové vstupy do transkodéry připravené, začněte streamovat do AMS kliknutím na tlačítko **Stream** .

     ![transkodéry](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> Než kliknete na **Stream**, **musíte** zajistit, aby byl kanál připravený.
> Také ujistěte se, že nechcete bez vstupní příspěvek kanálu po dobu delší než 15 minut > nechat kanál ve stavu Připraveno.
>
>

## <a name="test-playback"></a>Přehrávání testů

Přejděte na nástroj AMSE a klikněte pravým tlačítkem na kanál, který má být testována. V nabídce najeďte myší **na přehrávání náhledu** a vyberte možnost **s Azure Media Player**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Pokud datový proud se zobrazí v přehrávači, pak kodér správně nakonfigurovaný pro připojení k AMS.

Pokud dojde k chybě, kanál se bude muset resetovat a nastavení kodéru se upraví. Pokyny najdete v článku [věnovaném řešení potíží](media-services-troubleshooting-live-streaming.md) .  

## <a name="create-a-program"></a>Vytvoření programu

1. Jakmile přehrávání kanálu je potvrzen, vytvořte program. Na kartě **živá** v nástroji AMSE klikněte pravým tlačítkem myši v oblasti programu a vyberte možnost **vytvořit nový program**.  

    ![transkodéry](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
2. Pojmenujte program a v případě potřeby upravte **délku okna archivu** (výchozí nastavení je 4 hodiny). Můžete také určit umístění úložiště nebo ponechte jako výchozí.  
3. Zaškrtněte políčko **Spustit program nyní** .
4. Klikněte na **vytvořit program**.  

    >[!NOTE]
    >Vytvoření programu trvá méně času než vytváření kanálů.
        
5. Po spuštění programu potvrďte přehrávání kliknutím pravým tlačítkem myši na program a přechodem k **přehrávání programu** a následným výběrem možnosti **Azure Media Player**.  
6. Po potvrzení klikněte znovu pravým tlačítkem myši na program a vyberte možnost **Kopírovat výstupní adresu URL do schránky** (nebo z nabídky **informace o programu a nastavení** načtěte tyto informace z nabídky).

Datový proud je teď připravený k součástí přehrávač nebo distribuované na cílovou skupinou pro živé zobrazení.  

## <a name="troubleshooting"></a>Řešení potíží

Pokyny najdete v článku [věnovaném řešení potíží](media-services-troubleshooting-live-streaming.md) .

## <a name="next-step"></a>Další krok

Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
