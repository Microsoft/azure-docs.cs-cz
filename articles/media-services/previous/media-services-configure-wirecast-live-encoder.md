---
title: Konfigurace kodéru Wirecast streamování pro odesílání živého streamu s jednou přenosovou rychlostí | Microsoft Docs
description: 'V tomto tématu se dozvíte, jak nakonfigurovat kodér Wirecast Live pro odeslání datového proudu s jednou přenosovou rychlostí do kanálů AMS, které mají povolené kódování v reálném čase. '
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: cenkdin;anilmur
ms.openlocfilehash: a736270919f36cb7d4bbd4e686a98ddc57f402fa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016827"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Použití kodéru Wirecast k odeslání živého streamu s jednou přenosovou rychlostí

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>

Tento článek ukazuje, jak nakonfigurovat [Wirecast](https://www.telestream.net/wirecast/overview.htm) Live Encoder pro odesílání datových proudů s jednou přenosovou rychlostí do kanálů AMS, které mají povolené kódování v reálném čase. Další informace najdete v článku o [práci s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

V tomto kurzu se dozvíte, jak spravovat Azure Media Services (AMS) pomocí nástroje Azure Media Services Explorer (AMSE). Tento nástroj se spouští jenom na počítačích s Windows. Pokud používáte systém Mac nebo Linux, použijte Azure Portal k vytvoření [kanálů](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) a [programů](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> Kodéry musí podporovat protokol TLS 1,2 při použití protokolů RTMP. Použijte Wirecast verze 13.0.2 nebo vyšší z důvodu požadavku TLS 1,2.

## <a name="prerequisites"></a>Předpoklady
* [Vytvoření účtu Azure Media Services](media-services-portal-create-account.md)
* Ujistěte se, že je spuštěný koncový bod streamování. Další informace najdete v tématu [Správa koncových bodů streamování v účtu Media Services](media-services-portal-manage-streaming-endpoints.md) .
* Nainstalujte nejnovější verzi nástroje [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
* Spusťte nástroj a připojte se ke svému účtu AMS.

## <a name="tips"></a>Tipy
* Kdykoli je to možné, použijte kabelové internetové připojení.
* Vhodným pravidlem při určování požadavků na šířku pásma je zdvojnásobit přenosové rychlosti streamování. I když se nejedná o povinný požadavek, pomáhá zmírnit dopad zahlcení sítě.
* Při používání softwarových kodérů zavřete všechny nepotřebné programy.

## <a name="create-a-channel"></a>Vytvoření kanálu
1. V nástroji AMSE přejděte na kartu **Live** a klikněte pravým tlačítkem myši v oblasti kanálu. Vyberte **vytvořit kanál...** z nabídky.

    ![Snímek obrazovky ukazuje vytvoření kanálu vybraného z nabídky.](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Zadejte název kanálu, pole Popis je volitelné. V části Nastavení kanálu vyberte **Standard** pro možnost Live Encoding a vstupní protokol nastavte na **RTMP**. Všechna ostatní nastavení můžete ponechat beze změny.

    Ujistěte se, že je vybraná možnost **Spustit nový kanál** .

3. Klikněte na **vytvořit kanál**.

   ![Snímek obrazovky se zobrazí v dialogovém okně vytvořit živý kanál.](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> Spuštění kanálu může trvat až 20 minut.
>
>

Při spuštění kanálu můžete [nakonfigurovat kodér](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> Fakturace začne ihned po přechodu kanálu do stavu připraveno. Další informace najdete v tématu [stavy kanálu](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="configure-the-telestream-wirecast-encoder"></a><a id="configure_wirecast_rtmp" />Konfigurace kodéru Wirecast streamování
V tomto kurzu se použijí následující nastavení výstupu. Zbývající část této části popisuje konfigurační kroky podrobněji.

**Video**:

* Kodek: H.264
* Profil: vysoký (úroveň 4.0)
* Přenosová rychlost: 5000 KB/s
* Klíčový snímek: 2 sekundy (60 sekund)
* Snímková frekvence: 30

**Zvuk**:

* Kodek: AAC (LC)
* Přenosová rychlost: 192 kb/s
* Vzorkovací frekvence: 44,1 kHz

### <a name="configuration-steps"></a>Postup konfigurace
1. Otevřete aplikaci Wirecast Stream na používaném počítači a nastavte pro streamování RTMP.
2. Nakonfigurujte výstup tak, že přejdete na kartu **výstup** a vyberete **výstupní nastavení...**.

    Ujistěte se, že **cíl výstupu** je nastavený na **RTMP Server**.
3. Klikněte na **OK**.
4. Na stránce nastavení nastavte **cílové** pole na **Azure Media Services**.

    Profil kódování je předem vybraný do **Azure H. 264 720p 16:9 (1280 × 720)**. Chcete-li tato nastavení přizpůsobit, vyberte ikonu ozubeného kolečka napravo od rozevírací nabídky a pak zvolte možnost **Nová předvolba**.

    ![Snímek obrazovky se zobrazí dialogové okno Zvolit šablonu s vybraným BlobTrigger.](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Nakonfigurujte předvolby kodéru.

    Pojmenujte přednastavení a ověřte následující doporučené nastavení:

    **Video**

   * Kodér: MainConcept H. 264
   * Počet snímků za sekundu: 30
   * Průměrná přenosová rychlost: 5000 kbit/s (dá se upravit na základě omezení sítě)
   * Profil: hlavní
   * Klíčový snímek každých: 60 snímků

     **Zvuk**

   * Cílová bitová rychlost: 192 kbit/s
   * Vzorkovací frekvence: 44,100 kHz

     ![Snímek obrazovky zobrazuje předvolby kodéru pro AzureTest1.](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Stiskněte **Uložit**.

    Pole kódování má nyní nově vytvořený profil k dispozici pro výběr.

    Ujistěte se, že je vybraný nový profil.
7. Získejte vstupní adresu URL kanálu, aby ji bylo možné přiřadit ke **koncovému bodu Wirecast RTMP**.

    Přejděte zpět do nástroje AMSE a podívejte se na stav dokončení kanálu. Jakmile se stav změní z **počáteční** na **spuštěno**, můžete získat vstupní adresu URL.

    Když je kanál spuštěný, klikněte pravým tlačítkem myši na název kanálu, přejděte dolů na **Kopírovat vstupní adresu URL do schránky** a pak vyberte **primární vstupní adresu URL**.  

    ![Snímek obrazovky ukazuje možnost kopírování vstupu U R L do schránky pro primární vstup U R L.](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. V okně **Nastavení výstupu** Wirecast vložte tyto informace do pole **adresa** výstupní části a přiřaďte název streamu.

    ![Snímek obrazovky ukazuje nastavení výstupu.](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Vyberte **OK**.
2. Na hlavní obrazovce **Wirecast** potvrďte, že vstupní zdroje pro video a zvuk jsou připravené a potom stiskněte **Stream** v levém horním rohu.

    ![Snímek obrazovky se zobrazí na tlačítku Wirecast Stream.](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Než kliknete na **Stream**, **musíte** zajistit, aby byl kanál připravený.
> Také se ujistěte, že kanál neopouštíte v připraveném stavu, aniž byste museli mít vstupní kanál příspěvků po dobu delší než > 15 minut.
>
>

## <a name="test-playback"></a>Přehrání testu

Přejděte do nástroje AMSE a klikněte pravým tlačítkem myši na kanál, který chcete testovat. V nabídce najeďte myší **na přehrávání náhledu** a vyberte možnost **s Azure Media Player**.  

![Snímek obrazovky ukazuje, že je vybraná možnost Přehrát náhled s možností Azure Media Player.](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Pokud se datový proud objeví v přehrávači, kodér byl správně nakonfigurován pro připojení k AMS.

Pokud dojde k chybě, kanál se musí resetovat a nastavení kodéru se upraví. Pokyny najdete v článku [věnovaném řešení potíží](media-services-troubleshooting-live-streaming.md) .  

## <a name="create-a-program"></a>Vytvoření programu
1. Po potvrzení přehrávání kanálu vytvořte program. Na kartě **živá** v nástroji AMSE klikněte pravým tlačítkem myši v oblasti programu a vyberte možnost **vytvořit nový program**.  

    ![Snímek obrazovky zobrazuje vybranou možnost vytvořit program.](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
2. Pojmenujte program a v případě potřeby upravte **délku okna archivu** (výchozí nastavení je 4 hodiny). Můžete také zadat umístění úložiště nebo ponechat jako výchozí.  
3. Zaškrtněte políčko **Spustit program nyní** .
4. Klikněte na **vytvořit program**.  

   >[!NOTE]
   >Vytváření programu trvá méně času než vytvoření kanálu.
       
5. Po spuštění programu potvrďte přehrávání kliknutím pravým tlačítkem myši na program a přechodem k **přehrávání programu** a následným výběrem možnosti **Azure Media Player**.  
6. Po potvrzení klikněte znovu pravým tlačítkem myši na program a vyberte možnost **Kopírovat výstupní adresu URL do schránky** (nebo z nabídky **informace o programu a nastavení** načtěte tyto informace z nabídky).

Stream je teď připravený k vložení do přehrávače nebo k distribuci do cílové skupiny pro živé prohlížení.  

## <a name="troubleshooting"></a>Poradce při potížích
Pokyny najdete v článku [věnovaném řešení potíží](media-services-troubleshooting-live-streaming.md) .

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
