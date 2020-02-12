---
title: Konfigurovat kodér Telestream Wirecast odesílat živý datový proud s jednou přenosovou rychlostí | Dokumentace Microsoftu
description: 'Toto téma ukazuje, jak nakonfigurovat živé kodér Wirecast Odeslat datový proud s jednou přenosovou rychlostí do AMS kanálů, které jsou povolené kódování v reálném čase. '
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: cenkdin;anilmur
ms.openlocfilehash: 1d9d63aa6b3da1b8d8389722bd5af0eeed585d03
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134980"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Kodér Wirecast můžete odesílat živý datový proud s jednou přenosovou rychlostí 
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [Transkodéry](media-services-configure-tricaster-live-encoder.md)
>
>

Tento článek ukazuje, jak nakonfigurovat [Wirecast](https://www.telestream.net/wirecast/overview.htm) Live Encoder pro odesílání datových proudů s jednou přenosovou rychlostí do kanálů AMS, které mají povolené kódování v reálném čase. Další informace najdete v článku o [práci s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Tento kurz ukazuje, jak spravovat Azure Media Services (AMS) s nástrojem Azure Media Services Explorer (AMSE). Tento nástroj lze spustit pouze na počítač s Windows. Pokud používáte systém Mac nebo Linux, použijte Azure Portal k vytvoření [kanálů](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) a [programů](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> Kodéry musí podporovat protokol TLS 1,2 při použití protokolů RTMP. Použijte Wirecast verze 13.0.2 nebo vyšší z důvodu požadavku TLS 1,2.

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

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Zadejte název kanálu, do pole Popis je volitelný. V části Nastavení kanálu vyberte **Standard** pro možnost Live Encoding a vstupní protokol nastavte na **RTMP**. Všechna ostatní nastavení je můžete nechat.

    Ujistěte se, že je vybraná možnost **Spustit nový kanál** .

3. Klikněte na **vytvořit kanál**.

   ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> Kanál může trvat až 20 minut.
>
>

Při spuštění kanálu můžete [nakonfigurovat kodér](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> Fakturace začíná, jakmile kanál přejde do stavu Připraveno. Další informace najdete v tématu [stavy kanálu](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="a-idconfigure_wirecast_rtmp-configure-the-telestream-wirecast-encoder"></a><a id="configure_wirecast_rtmp" />konfigurace kodéru Wirecast streamování
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
1. Otevřete aplikaci Telestream Wirecast na počítač se používá a nastavit pro streamování RTMP.
2. Nakonfigurujte výstup tak, že přejdete na kartu **výstup** a vyberete **výstupní nastavení...** .

    Ujistěte se, že **cíl výstupu** je nastavený na **RTMP Server**.
3. Klikněte na tlačítko **OK**.
4. Na stránce nastavení nastavte **cílové** pole na **Azure Media Services**.

    Profil kódování je předem vybraný do **Azure H. 264 720p 16:9 (1280 × 720)** . Chcete-li tato nastavení přizpůsobit, vyberte ikonu ozubeného kolečka napravo od rozevírací nabídky a pak zvolte možnost **Nová předvolba**.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Konfigurovat kodér předvolby.

    Pojmenujte přednastavený kontext a zkontrolujte následující doporučené nastavení:

    **Obrazový**

   * Kodér: Mainconceptu H.264
   * Snímků za sekundu: 30
   * Průměrná přenosová rychlost: 5000 kbits za sekundu (můžete třeba nastavit podle omezení sítě)
   * Profil: hlavní
   * Klíčový snímek každých: 60 snímků

     **Kazet**

   * Cíl přenosová rychlost: 192 kbits za sekundu
   * Vzorkovací frekvence: 44 100 kHz

     ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Stiskněte **Uložit**.

    Kódování pole má teď k dispozici pro výběr nově vytvořený profil.

    Ujistěte se, že je vybraný nový profil.
7. Získejte vstupní adresu URL kanálu, aby ji bylo možné přiřadit ke **koncovému bodu Wirecast RTMP**.

    Přejděte zpět na nástroj AMSE a zkontrolovat stav dokončení kanálu. Jakmile se stav změní z **počáteční** na **spuštěno**, můžete získat vstupní adresu URL.

    Když je kanál spuštěný, klikněte pravým tlačítkem myši na název kanálu, přejděte dolů na **Kopírovat vstupní adresu URL do schránky** a pak vyberte **primární vstupní adresu URL**.  

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. V okně **Nastavení výstupu** Wirecast vložte tyto informace do pole **adresa** výstupní části a přiřaďte název streamu.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Vyberte **OK**.
2. Na hlavní obrazovce **Wirecast** potvrďte, že vstupní zdroje pro video a zvuk jsou připravené a potom stiskněte **Stream** v levém horním rohu.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Než kliknete na **Stream**, **musíte** zajistit, aby byl kanál připravený.
> Také ujistěte se, že nechcete bez vstupní příspěvek kanálu po dobu delší než 15 minut > nechat kanál ve stavu Připraveno.
>
>

## <a name="test-playback"></a>Přehrávání testů

Přejděte na nástroj AMSE a klikněte pravým tlačítkem na kanál, který má být testována. V nabídce najeďte myší **na přehrávání náhledu** a vyberte možnost **s Azure Media Player**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Pokud datový proud se zobrazí v přehrávači, pak kodér správně nakonfigurovaný pro připojení k AMS.

Pokud k chybě, kanál je potřeba resetovat a upravit nastavení kodéru. Pokyny najdete v článku [věnovaném řešení potíží](media-services-troubleshooting-live-streaming.md) .  

## <a name="create-a-program"></a>Vytvoření programu
1. Jakmile přehrávání kanálu je potvrzen, vytvořte program. Na kartě **živá** v nástroji AMSE klikněte pravým tlačítkem myši v oblasti programu a vyberte možnost **vytvořit nový program**.  

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
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

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
