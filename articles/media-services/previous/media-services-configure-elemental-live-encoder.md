---
title: Konfigurace kodér elementární za provozu na odesílat živý datový proud s jednou přenosovou rychlostí | Microsoft Docs
description: Toto téma ukazuje, jak nakonfigurovat kodér elementární za provozu k odeslání datový proud s jednou přenosovou rychlostí do AMS kanály, které jsou povolené kódování v reálném čase.
services: media-services
documentationcenter: ''
author: cenkdin
manager: cfowler
editor: ''
ms.assetid: 9c6bf6a9-6273-4fdd-9477-f0e565280b5b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: cenkd;anilmur;juliako
ms.openlocfilehash: 35d8d0438d548e679405aefcbd1982fa1731874a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33789429"
---
# <a name="use-the-elemental-live-encoder-to-send-a-single-bitrate-live-stream"></a>Pomocí kodéru elementární Live odesílat živý datový proud s jednou přenosovou rychlostí
> [!div class="op_single_selector"]
> * [Elemental za provozu](media-services-configure-elemental-live-encoder.md)
> * [čase](media-services-configure-tricaster-live-encoder.md)
> * [wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

Toto téma ukazuje, jak nakonfigurovat [elementární Live](http://www.elementaltechnologies.com/products/elemental-live) ke odesílat datový proud s jednou přenosovou rychlostí do AMS kanály, které jsou povolené kódování v reálném čase.  Další informace najdete v článku o [práci s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Tento kurz ukazuje, jak spravovat Azure Media Services (AMS) s nástrojem Azure Media Services Explorer (AMSE). Tento nástroj lze spustit pouze na počítačích s Windows. Pokud jste na Mac nebo Linux, použijte portál Azure k vytvoření [kanály](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) a [programy](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Požadavky
* Musí mít praktické znalosti použití elementární Live webové rozhraní pro vytváření živé události.
* [Vytvoření účtu Azure Media Services](media-services-portal-create-account.md)
* Ujistěte se, je koncový bod streamování, spuštěná. Další informace najdete v tématu [spravovat koncové body streamování v účtu Media Services](media-services-portal-manage-streaming-endpoints.md).
* Nainstalujte nejnovější verzi [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) nástroj.
* Spusťte nástroj a připojte se ke svému účtu AMS.

## <a name="tips"></a>Tipy
* Pokud je to možné, použijte standardní kabelové internetové připojení.
* Obvykle při určování nároky na šířku pásma je dvakrát streamování přenosových rychlostí. Přestože není povinný požadavek, pomůže omezit účinek zahlcení sítě.
* Při použití softwaru na základě kodéry, zavřete se všechny nepotřebné programy.

## <a name="elemental-live-with-rtp-ingest"></a>Ingestování elementární živé s RTP
V této části ukazuje, jak nakonfigurovat kodéru elementární za provozu, který odešle datový proud s jednou přenosovou rychlostí za provozu přes protokol RTP.  Další informace najdete v tématu [stream MPEG-TS využívající RTP](media-services-manage-live-encoder-enabled-channels.md#channel).

### <a name="create-a-channel"></a>Vytvoření kanálu

1. V nástroj AMSE, přejděte na **živé** kartě a klikněte pravým tlačítkem v oblasti kanálu. Vyberte **vytvořit kanál...** v nabídce.

    ![Elementární](./media/media-services-elemental-live-encoder/media-services-elemental1.png)

2. Zadejte název kanálu, pole popisu je volitelné. V části Nastavení kanál, vyberte **standardní** pro Live Encoding možnost s protokolem vstup nastavena na **RTP (MPEG-TS)**. Všechna ostatní nastavení jako je můžete nechat.

    Zajistěte, aby **nyní spustit nový kanál** je vybrána.

3. Klikněte na tlačítko **vytvořit kanál**.

   ![Elementární](./media/media-services-elemental-live-encoder/media-services-elemental12.png)

> [!NOTE]
> Kanál může trvat až 20 minut před spuštěním.
>
>

Při spouštění kanál můžete [nakonfigurovat kodér](media-services-configure-elemental-live-encoder.md#configure_elemental_rtp).

> [!IMPORTANT]
> Všimněte si, že fakturace začne hned, jak kanál přejde do stavu Připraveno. Další informace najdete v tématu [kanálu stavy](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

### <a id=configure_elemental_rtp></a>Konfigurace kodér elementární za provozu
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

#### <a name="configuration-steps"></a>Kroky konfigurace
1. Přejděte na **elementární Live** webové rozhraní a nastavení kodéru pro **UDP/TS** streamování.
2. Jakmile dojde k vytvoření nové události, posuňte se dolů a výstup skupiny a přidat **UDP/TS** skupiny výstupu.
3. Vytvořit nový výstupní výběrem **nového datového proudu** a pak levým na **přidat výstup**.  

    ![Elementární](./media/media-services-elemental-live-encoder/media-services-elemental13.png)

   > [!NOTE]
   > Doporučuje se, že elementární událost má záznamy typu časového kódu nastavit na "Systémové hodiny" pomohou kodér znovu připojit v případě selhání datového proudu.
   >
   >
4. Teď, když byla vytvořena výstup, klikněte na tlačítko **přidat datový proud**. Můžete teď konfigurovat nastavení výstup.
5. Posuňte se dolů "Datový proud 1" kterou jste právě vytvořili, klikněte **Video** na levé straně a rozbalte **Upřesnit** v oddílu nastavení.

    ![Elementární](./media/media-services-elemental-live-encoder/media-services-elemental4.png)

    Zatímco elementární Live obsahuje širokou škálu dostupné přizpůsobení, doporučujeme následující nastavení pro zahájení práce s streamování AMS.

   * Řešení: 1280 × 720
   * Kmitočet snímků: 30
   * GOP velikost: 60 rámce
   * Prokládání režim: progresivní
   * Přenosovou rychlostí: 5000000 bitů/s (to se dá upravit podle omezení sítě)

    ![Elementární](./media/media-services-elemental-live-encoder/media-services-elemental5.png)

1. Získáte vstupní adresa URL kanálu.

    Přejděte zpět na nástroj AMSE a zkontrolovat stav dokončení kanálu. Jakmile se stav změnil ze **počáteční** k **systémem**, můžete získat vstupní adresa URL.

    Při spuštění je kanál, klikněte pravým tlačítkem na název kanálu, přejděte dolů hover přes **adresa URL vstupu kopírování do schránky** a pak vyberte **primární adresa URL vstupu**.  

    ![Elementární](./media/media-services-elemental-live-encoder/media-services-elemental6.png)
2. Tyto informace v vložit **primární cílové** pole z elementární. Všechna ostatní nastavení může zůstat výchozí.

    ![Elementární](./media/media-services-elemental-live-encoder/media-services-elemental14.png)

    Pro další redundanci opakujte tyto kroky s adresou URL sekundární vstup tak, že vytvoříte samostatné kartě "Výstupní" pro streamování UDP/TS.
3. Klikněte na tlačítko **vytvořit** (Pokud byla vytvořena novou událost) nebo **aktualizace** (Pokud úpravy existující událostí) a pak pokračujte spustit kodér.

> [!IMPORTANT]
> Před kliknutím na **spustit** na webové rozhraní elementární za provozu je **musí** Ujistěte se, že kanál je připravený.
> Ujistěte se také, nechcete ponechat kanál ve stavu Připraveno bez události po dobu delší než > 15 minut.
>
>

Po spuštění datového proudu pro 30 sekund, přejděte zpět na AMSE nástroj a testování přehrávání.  

### <a name="test-playback"></a>Přehrávání testu

Přejděte do nástroj AMSE, a klikněte pravým tlačítkem na kanál, který má být testována. V nabídce pozastavte ukazatel myši nad **přehrávání ve verzi Preview** a vyberte **s Azure Media Player**.  

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental8.png)

Pokud datový proud se zobrazí v přehrávači, pak kodér správně nakonfigurovaný pro připojení k AMS.

Je-li k chybě, kanál bude nutné resetovat a upravit nastavení kodéru. Podrobnosti najdete [řešení potíží s](media-services-troubleshooting-live-streaming.md) tématu pokyny.   

### <a name="create-a-program"></a>Vytvořit program
1. Po potvrzení kanálu přehrávání vytvořte program. V části **živé** v nástroj AMSE, klikněte v oblasti program pravým tlačítkem a vyberte **vytvořit nový Program**.  

    ![Elementární](./media/media-services-elemental-live-encoder/media-services-elemental9.png)
2. Název programu a v případě potřeby upravit **délka archivačního okna** (výchozí 4 hodiny). Můžete také určit umístění úložiště nebo ponechte jako výchozí.  
3. Zkontrolujte **nyní spustit Program** pole.
4. Klikněte na tlačítko **vytvořit Program**.  

    >[!NOTE]
    > Vytváření programu trvá kratší dobu, než vytvoření kanálu.   
      
5. Jakmile program běží, potvrďte přehrávání tak, že kliknete program pravým tlačítkem a přejdete na **přehrávání programech** a potom vyberete **s Azure Media Player**.  
6. Po potvrzení, klikněte pravým tlačítkem na program znovu a vyberte **zkopírujte adresu URL výstup do schránky** (nebo načtení těchto informací z **programu informace a nastavení** možnost v nabídce).

Datový proud je nyní připravena vložených v přehrávač, nebo distribuovány do cílovou skupinu pro zobrazení za provozu.  

## <a name="troubleshooting"></a>Řešení potíží
Podrobnosti najdete [řešení potíží s](media-services-troubleshooting-live-streaming.md) tématu pokyny.

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
