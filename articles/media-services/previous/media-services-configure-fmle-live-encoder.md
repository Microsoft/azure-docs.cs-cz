---
title: Konfigurace kodér FMLE odeslat živý datový proud s jednou přenosovou rychlostí | Microsoft Docs
description: Toto téma ukazuje, jak nakonfigurovat kodér rozhraní Flash média Live Encoder (FMLE) k odeslání datový proud s jednou přenosovou rychlostí do AMS kanály, které jsou povolené kódování v reálném čase.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 3113f333-517a-47a1-a1b3-57e200c6b2a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako;cenkdin;anilmur
ms.openlocfilehash: 055decd689e260a69651c5a3e1ce3f3f78b67340
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="use-the-fmle-encoder-to-send-a-single-bitrate-live-stream"></a>Pomocí kodéru FMLE odesílat živý datový proud s jednou přenosovou rychlostí
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Elemental za provozu](media-services-configure-elemental-live-encoder.md)
> * [čase](media-services-configure-tricaster-live-encoder.md)
> * [wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

Tento článek ukazuje, jak nakonfigurovat [Flash Live kodér médií](http://www.adobe.com/products/flash-media-encoder.html) encoder (FMLE) k odeslání datový proud s jednou přenosovou rychlostí do AMS kanály, které jsou povolené kódování v reálném čase. Další informace najdete v článku o [práci s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Tento kurz ukazuje, jak spravovat Azure Media Services (AMS) s nástrojem Azure Media Services Explorer (AMSE). Tento nástroj lze spustit pouze na počítačích s Windows. Pokud jste na Mac nebo Linux, použijte portál Azure k vytvoření [kanály](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) a [programy](media-services-portal-creating-live-encoder-enabled-channel.md).

Tento kurz popisuje použití AAC. Však nepodporuje FMLE podporuje AAC ve výchozím nastavení. Budete muset zakoupit modulu plug-in pro AAC kódování, například kvůli MainConcept: [AAC modulu plug-in](http://www.mainconcept.com/products/plug-ins/plug-ins-for-adobe/aac-encoder-fmle.html)

## <a name="prerequisites"></a>Požadavky
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

    ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle1.png)

2. Zadejte název kanálu, pole popisu je volitelné. V části Nastavení kanál, vyberte **standardní** pro Live Encoding možnost s protokolem vstup nastavena na **RTMP**. Všechna ostatní nastavení jako je můžete nechat.

    Zajistěte, aby **nyní spustit nový kanál** je vybrána.

3. Klikněte na tlačítko **vytvořit kanál**.

   ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle2.png)

> [!NOTE]
> Kanál může trvat až 20 minut před spuštěním.
>
>

Při spouštění kanál můžete [nakonfigurovat kodér](media-services-configure-fmle-live-encoder.md#configure_fmle_rtmp).

> [!IMPORTANT]
> Všimněte si, že fakturace začne hned, jak kanál přejde do stavu Připraveno. Další informace najdete v tématu [kanálu stavy](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_fmle_rtmp></a>Konfigurace kodér FMLE
V tomto kurzu se používají následující nastavení výstup. Zbývající část tohoto oddílu popisuje kroky konfigurace podrobněji.

**Video**:

* Kodeků: H.264
* Profil: Vysoká (úroveň 4.0)
* Přenosovou rychlostí: 5000 kb/s
* Klíčový snímek: 2 sekund (60 sekund)
* Míra s rámečkem: 30

**Zvuk**:

* Kodeků: AAC (LC)
* Přenosovou rychlostí: 192 kb /
* Vzorkovací frekvence: 44,1 kHz

### <a name="configuration-steps"></a>Kroky konfigurace
1. Přejděte na rozhraní Flash Live kodér médií na (FMLE) v počítači používán.

    Rozhraní je jeden hlavní stránce nastavení. Poznamenejte si následující doporučené nastavení začít pracovat s streamování využívající FMLE.

   * Formát: H.264 obnovovací frekvence: 30,00
   * Vstupní velikost: 1280 × 720
   * Přenosová rychlost: 5000 kb/s (lze upravit podle omezení sítě)  

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle3.png)

     Při použití prokládaných zdroje, prosím zaškrtnout možnost "Volby Odstranit prokládání"
2. Vyberte ikonu klíče vedle formátu, tyto další nastavení by mělo být:

   * Profil: Main
   * Úroveň: 4.0
   * Frekvence @keyframe, které určuje: 2 sekund

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle4.png)
3. Nastavte následující důležité zvuk nastavení:

   * Formát: AAC
   * Vzorkovací frekvence: 44100 Hz
   * Přenosovou rychlostí: 192 kb /

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle5.png)
4. Get kanál vstup URL aby bylo možné ho přiřadit FMLE **koncový bod RTMP**.

    Přejděte zpět na nástroj AMSE a zkontrolovat stav dokončení kanálu. Jakmile se stav změnil ze **počáteční** k **systémem**, můžete získat vstupní adresa URL.

    Při spuštění je kanál, klikněte pravým tlačítkem na název kanálu, přejděte dolů hover přes **adresa URL vstupu kopírování do schránky** a pak vyberte **primární adresa URL vstupu**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle6.png)
5. Vložte tyto informace **FMS URL** pole výstup oddílu, a přiřadit název datového proudu.

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle7.png)

    Pro další redundanci opakujte tyto kroky s adresou URL sekundární vstup.
6. Vyberte **Connect** (Připojit).

> [!IMPORTANT]
> Před kliknutím na **připojit**, můžete **musí** Ujistěte se, že kanál je připravený.
> Ujistěte se také, nechcete bez vstupní příspěvku kanálu po dobu delší než 15 minut > z kanál ve stavu Připraveno.
>
>

## <a name="test-playback"></a>Přehrávání testu

Přejděte na nástroj AMSE a klikněte pravým tlačítkem na kanál, který má být testována. V nabídce pozastavte ukazatel myši nad **přehrávání ve verzi Preview** a vyberte **s Azure Media Player**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle8.png)

Pokud datový proud se zobrazí v přehrávači, pak kodér správně nakonfigurovaný pro připojení k AMS.

Je-li k chybě, kanál je třeba obnovit a upravit nastavení kodéru. Najdete v článku [řešení potíží s](media-services-troubleshooting-live-streaming.md) pokyny najdete v článku.  

## <a name="create-a-program"></a>Vytvořit program
1. Po potvrzení kanálu přehrávání vytvořte program. V části **živé** v nástroj AMSE, klikněte pravým tlačítkem myši v oblasti program a vybrat **vytvořit nový Program**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle9.png)
2. Název programu a v případě potřeby upravit **délka archivačního okna** (výchozí 4 hodiny). Můžete také určit umístění úložiště nebo ponechte jako výchozí.  
3. Zkontrolujte **nyní spustit Program** pole.
4. Klikněte na tlačítko **vytvořit Program**.  

    >[!NOTE]
    >Vytváření programu trvá kratší dobu, než vytvoření kanálu.
        
5. Jakmile program běží, potvrďte přehrávání tak, že kliknete program pravým tlačítkem a přejdete na **přehrávání programech** a potom vyberete **s Azure Media Player**.  
6. Po potvrzení znovu klikněte program pravým tlačítkem a vyberte **zkopírujte adresu URL výstup do schránky** (nebo načtení těchto informací z **programu informace a nastavení** možnost v nabídce).

Datový proud je nyní připravena vložených v přehrávač, nebo distribuovány do cílovou skupinu pro zobrazení za provozu.  

## <a name="troubleshooting"></a>Řešení potíží
Najdete v článku [řešení potíží s](media-services-troubleshooting-live-streaming.md) pokyny najdete v článku.

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
