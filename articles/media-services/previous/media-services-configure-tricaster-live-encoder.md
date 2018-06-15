---
title: Konfigurace kodér NewTek čase k odesílání živý datový proud s jednou přenosovou rychlostí | Microsoft Docs
description: Toto téma ukazuje, jak nakonfigurovat za provozu kodér čase k odesílání datový proud s jednou přenosovou rychlostí do AMS kanály, které jsou povolené kódování v reálném čase.
services: media-services
documentationcenter: ''
author: cenkdin
manager: cfowler
editor: ''
ms.assetid: 8973181a-3059-471a-a6bb-ccda7d3ff297
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako;cenkd;anilmur
ms.openlocfilehash: 8084f32ac8cc2184d93796468ad66fb73398e876
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788911"
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Pomocí kodéru NewTek čase odesílat živý datový proud s jednou přenosovou rychlostí
> [!div class="op_single_selector"]
> * [čase](media-services-configure-tricaster-live-encoder.md)
> * [Elemental za provozu](media-services-configure-elemental-live-encoder.md)
> * [wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

Tento článek ukazuje, jak nakonfigurovat [čase NewTek](http://newtek.com/products/tricaster-40.html) za provozu kodér Odeslat datový proud s jednou přenosovou rychlostí do AMS kanály, které jsou povolené kódování v reálném čase. Další informace najdete v článku o [práci s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Tento kurz ukazuje, jak spravovat Azure Media Services (AMS) s nástrojem Azure Media Services Explorer (AMSE). Tento nástroj lze spustit pouze na počítačích s Windows. Pokud jste na Mac nebo Linux, použijte portál Azure k vytvoření [kanály](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) a [programy](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> Při použití čase pro odesílání v příspěvek informační kanál AMS kanály, které jsou povolené kódování v reálném čase, může být video nebo zvuk chyb v živé události Pokud určité funkce čase, jako je rychlé vyjímání mezi informační kanály nebo přepnutí z slaty. AMS tým pracuje na řešení těchto problémů do té doby, nedoporučujeme používat tyto funkce.
>
>

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

    ![čase](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Zadejte název kanálu, pole popisu je volitelné. V části Nastavení kanál, vyberte **standardní** pro Live Encoding možnost s protokolem vstup nastavena na **RTMP**. Všechna ostatní nastavení jako je můžete nechat.

    Zajistěte, aby **nyní spustit nový kanál** je vybrána.

3. Klikněte na tlačítko **vytvořit kanál**.

   ![čase](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> Kanál může trvat až 20 minut před spuštěním.
>
>

Při spouštění kanál můžete [nakonfigurovat kodér](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

> [!IMPORTANT]
> Jakmile kanál přejde do stavu Připraveno, začne běžet fakturace. Další informace najdete v tématu [kanálu stavy](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_tricaster_rtmp></a>Konfigurace kodéru NewTek čase
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
1. Vytvořte novou **čase NewTek** projektu v závislosti na tom, jaké vstupní zdroj videa se používá.
2. Jednou v rámci projektu, Najít **datového proudu** tlačítko a klikněte na ikonu ozubené kolečko vedle sebe pro přístup k nabídce konfiguraci datového proudu.

    ![čase](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. Jakmile nabídce otevře, klikněte na tlačítko **nový** v části připojení. Po zobrazení výzvy pro typ připojení, vyberte **Adobe Flash**.

    ![čase](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. Klikněte na **OK**.
5. Kliknutím šipku rozevíracího seznamu v části lze nyní importovat profilem FMLE **streamování profil** a přejdete na **Procházet**.

    ![čase](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. Přejděte k uložení nakonfigurované FMLE profilu.
7. Vyberte ji a stiskněte klávesu **OK**.

    Po nahrání profilu pokračujte k dalšímu kroku.
8. Get kanál vstup URL aby bylo možné ho přiřadit čase **koncový bod RTMP**.

    Přejděte zpět na nástroj AMSE a zkontrolovat stav dokončení kanálu. Jakmile se stav změnil ze **počáteční** k **systémem**, můžete získat vstupní adresa URL.

    Při spuštění je kanál, klikněte pravým tlačítkem na název kanálu, přejděte dolů hover přes **adresa URL vstupu kopírování do schránky** a pak vyberte **primární adresa URL vstupu**.  

    ![čase](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Tyto informace v vložit **umístění** pole v části **serveru Flash** v čase projektu. Název datového proudu v přiřadit také **ID datového proudu** pole.

    Pokud informace datový proud byl přidán do profilu FMLE, se můžete také naimportovat do této části kliknutím **importovat nastavení**, přejdete na uloženého profilu FMLE a kliknutím na **OK**. Příslušná pole Flash serveru by měl naplnění informací z FMLE.

    ![čase](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. Po dokončení klikněte na tlačítko **OK** v dolní části obrazovky. Jakmile jsou připravené videosoubory a zvukové vstupy do čase, začněte streamování AMS kliknutím **datového proudu** tlačítko.

     ![čase](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> Před kliknutím na **datového proudu**, můžete **musí** Ujistěte se, že kanál je připravený.
> Ujistěte se také, nechcete bez vstupní příspěvku kanálu po dobu delší než 15 minut > z kanál ve stavu Připraveno.
>
>

## <a name="test-playback"></a>Přehrávání testu
Přejděte na nástroj AMSE a klikněte pravým tlačítkem na kanál, který má být testována. V nabídce pozastavte ukazatel myši nad **přehrávání ve verzi Preview** a vyberte **s Azure Media Player**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Pokud datový proud se zobrazí v přehrávači, pak kodér správně nakonfigurovaný pro připojení k AMS.

Je-li k chybě, kanál bude nutné resetovat a upravit nastavení kodéru. Najdete v článku [řešení potíží s](media-services-troubleshooting-live-streaming.md) pokyny najdete v článku.  

## <a name="create-a-program"></a>Vytvořit program
1. Po potvrzení kanálu přehrávání vytvořte program. V části **živé** v nástroj AMSE, klikněte pravým tlačítkem myši v oblasti program a vybrat **vytvořit nový Program**.  

    ![čase](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
2. Název programu a v případě potřeby upravit **délka archivačního okna** (výchozí čtyři hodiny). Můžete také určit umístění úložiště nebo ponechte jako výchozí.  
3. Zkontrolujte **nyní spustit Program** pole.
4. Klikněte na tlačítko **vytvořit Program**.  

    >[!NOTE]
    >Vytváření programu trvá kratší dobu, než vytvoření kanálu.
        
5. Jakmile program běží, potvrďte přehrávání tak, že kliknete program pravým tlačítkem a přejdete na **přehrávání programech** a potom vyberete **s Azure Media Player**.  
6. Po potvrzení znovu klikněte program pravým tlačítkem a vyberte **zkopírujte adresu URL výstup do schránky** (nebo načtení těchto informací z **programu informace a nastavení** možnost v nabídce).

Datový proud je nyní připravena vložených v přehrávač, nebo distribuovány do cílovou skupinu pro zobrazení za provozu.  

## <a name="troubleshooting"></a>Řešení potíží
Najdete v článku [řešení potíží s](media-services-troubleshooting-live-streaming.md) pokyny najdete v článku.

## <a name="next-step"></a>Další krok
Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
