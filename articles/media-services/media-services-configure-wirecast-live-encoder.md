---
title: Konfigurace kodér Telestream wirecast odeslat živý datový proud s jednou přenosovou rychlostí | Microsoft Docs
description: 'Toto téma ukazuje, jak nakonfigurovat za provozu kodér Wirecast k odeslání datový proud s jednou přenosovou rychlostí do AMS kanály, které jsou povolené kódování v reálném čase. '
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako;cenkdin;anilmur
ms.openlocfilehash: 0e4fb0b7c915969da1760eaccc77aa399030752e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Pomocí kodéru Wirecast odesílat živý datový proud s jednou přenosovou rychlostí
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [Elemental za provozu](media-services-configure-elemental-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

Tento článek ukazuje, jak nakonfigurovat [Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm) za provozu kodér Odeslat datový proud s jednou přenosovou rychlostí do AMS kanály, které jsou povolené kódování v reálném čase.  Další informace najdete v článku o [práci s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Tento kurz ukazuje, jak spravovat Azure Media Services (AMS) s nástrojem Azure Media Services Explorer (AMSE). Tento nástroj lze spustit pouze na počítačích s Windows. Pokud jste na Mac nebo Linux, použijte portál Azure k vytvoření [kanály](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) a [programy](media-services-portal-creating-live-encoder-enabled-channel.md).

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

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Zadejte název kanálu, pole popisu je volitelné. V části Nastavení kanál, vyberte **standardní** pro Live Encoding možnost s protokolem vstup nastavena na **RTMP**. Všechna ostatní nastavení jako je můžete nechat.

    Zajistěte, aby **nyní spustit nový kanál** je vybrána.

3. Klikněte na tlačítko **vytvořit kanál**.

   ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> Kanál může trvat až 20 minut před spuštěním.
>
>

Při spouštění kanál můžete [nakonfigurovat kodér](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> Jakmile kanál přejde do stavu Připraveno, začne běžet fakturace. Další informace najdete v tématu [kanálu stavy](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_wirecast_rtmp></a>Konfigurace kodér Telestream wirecast
V tomto kurzu se používají následující nastavení výstup. Zbývající část tohoto oddílu popisuje kroky konfigurace podrobněji.

**Video**:

* Codec: H.264
* Profil: Vysoká (úroveň 4.0)
* Přenosovou rychlostí: 5000 kb/s
* Klíčový snímek: 2 sekund (60 sekund)
* Míra s rámečkem: 30

**Zvuk**:

* Kodeků: AAC (LC)
* Přenosovou rychlostí: 192 kb /
* Vzorkovací frekvence: 44,1 kHz

### <a name="configuration-steps"></a>Kroky konfigurace
1. Otevřete aplikaci Telestream Wirecast na počítač se používá a nastavte pro streamování RTMP.
2. Konfigurace výstup přechodem na **výstup** kartě a výběrem **nastavení výstupní...** .

    Zajistěte, aby **cíl výstupu** je nastaven na **RTMP serveru**.
3. Klikněte na **OK**.
4. Na stránce nastavení nastavit **cílové** pole, které chcete být **Azure Media Services**.

    Profil kódování je předem vybraný k **Azure H.264 720 p 16:9 (1280 × 720)**. Chcete-li nastavení upravit, vyberte ikonu ozubené kolečko napravo od rozevíracího seznamu a poté zvolte **nové přednastavení**.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Nakonfigurujte kodér přednastavení.

    Název přednastavení a vyhledejte následující doporučené nastavení:

    **Video**

   * Kodér: MainConcept H.264
   * Počet snímků za sekundu: 30
   * Průměrná přenosová rychlost: 5000 kbits za sekundu (lze upravit podle omezení sítě)
   * Profil: Main
   * Klíče rámce každých: 60 rámce

    **zvuk**

   * Cílová přenosová rychlost: 192 kbits za sekundu
   * Vzorkovací frekvence: 44 100 kHz

     ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Stiskněte klávesu **Uložit**.

    Pole Encoding má nyní k dispozici pro výběr nově vytvořený profil.

    Ujistěte se, že je vybraný nový profil.
7. Get kanál vstup URL aby bylo možné ho přiřadit Wirecast **koncový bod RTMP**.

    Přejděte zpět na nástroj AMSE a zkontrolovat stav dokončení kanálu. Jakmile se stav změnil ze **počáteční** k **systémem**, můžete získat vstupní adresa URL.

    Při spuštění je kanál, klikněte pravým tlačítkem na název kanálu, přejděte dolů hover přes **adresa URL vstupu kopírování do schránky** a pak vyberte **primární adresa URL vstupu**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. V Wirecast **nastavení výstupní** okně Vložit tyto informace **adresu** pole výstup oddílu, a přiřadit název datového proudu.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Vyberte **OK**.
2. V hlavním **Wirecast** zkontrolujte vstupní zdroje pro připravení videa a zvuku a pak klikněte na tlačítko **datového proudu** v levém horním rohu.

   ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Před kliknutím na **datového proudu**, můžete **musí** Ujistěte se, že kanál je připravený.
> Ujistěte se také, nechcete bez vstupní příspěvku kanálu po dobu delší než 15 minut > z kanál ve stavu Připraveno.
>
>

## <a name="test-playback"></a>Přehrávání testu

Přejděte na nástroj AMSE a klikněte pravým tlačítkem na kanál, který má být testována. V nabídce pozastavte ukazatel myši nad **přehrávání ve verzi Preview** a vyberte **s Azure Media Player**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Pokud datový proud se zobrazí v přehrávači, pak kodér správně nakonfigurovaný pro připojení k AMS.

Je-li k chybě, kanál je třeba obnovit a upravit nastavení kodéru. Najdete v článku [řešení potíží s](media-services-troubleshooting-live-streaming.md) pokyny najdete v článku.  

## <a name="create-a-program"></a>Vytvořit program
1. Po potvrzení kanálu přehrávání vytvořte program. V části **živé** v nástroj AMSE, klikněte pravým tlačítkem myši v oblasti program a vybrat **vytvořit nový Program**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
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

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
