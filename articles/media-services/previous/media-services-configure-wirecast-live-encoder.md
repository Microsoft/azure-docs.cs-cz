---
title: Konfigurace kodéru Telestream Wirecast pro odesílání jednoho datového proudu | Dokumenty společnosti Microsoft
description: 'Toto téma ukazuje, jak nakonfigurovat živý kodér Wirecast tak, aby odesílal jeden datový proud datového toku do kanálů AMS, které jsou povoleny pro živé kódování. '
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
ms.openlocfilehash: 8e3705aaecb0760513f0605aece89b7ffc0044a8
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641643"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Odeslání jednoho živého datového proudu pomocí kodéru Wirecast 
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>

Tento článek ukazuje, jak nakonfigurovat živý kodér [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) tak, aby odesílal jeden datový proud datového toku do kanálů AMS, které jsou povoleny pro živé kódování. Další informace najdete v článku o [práci s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Tento kurz ukazuje, jak spravovat Azure Media Services (AMS) pomocí nástroje Azure Media Services Explorer (AMSE). Tento nástroj běží pouze na Windows PC. Pokud používáte Mac nebo Linux, použijte portál Azure k vytváření [kanálů](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) a [programů](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> Kodéry musí při použití protokolů RTMPS podporovat protokoly TLS 1.2. Z důvodu požadavku TLS 1.2 použijte wirecast verzi 13.0.2 nebo vyšší.

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

    ![drátěné](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Zadejte název kanálu, pole popisu je volitelné. V části Nastavení kanálu vyberte **standardní** pro volbu živékódování s vstupním protokolem nastaveným na **RTMP**. Všechna ostatní nastavení můžete ponechat tak, jak jsou.

    Ujistěte se, že je nyní vybrán a vybrán **nový kanál.**

3. Klepněte na **tlačítko Vytvořit kanál**.

   ![drátěné](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> Spuštění kanálu může trvat až 20 minut.
>
>

Během spuštění kanálu můžete [kodér nakonfigurovat](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> Fakturace začíná, jakmile kanál přejde do stavu připravenosti. Další informace naleznete v [tématu Channel stavy](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="configure-the-telestream-wirecast-encoder"></a><a id="configure_wirecast_rtmp" />Konfigurace kodéru Telestream Wirecast
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
1. Otevřete aplikaci Telestream Wirecast na používaném počítači a nastavte pro streamování RTMP.
2. Nakonfigurujte výstup tak, že přejdete na kartu **Výstup** a vyberete **nastavení výstupu...**.

    Zkontrolujte, zda je **výstupní cíl** nastaven na **server RTMP**.
3. Klikněte na tlačítko **OK**.
4. Na stránce nastavení nastavte pole **Cíl** na **Azure Media Services**.

    Profil kódování je předem vybrán na **Azure H.264 720p 16:9 (1280x720).** Chcete-li tato nastavení přizpůsobit, vyberte ikonu ozubeného kola vpravo od rozevíracího panelu a pak zvolte **Nové přednastavení**.

    ![drátěné](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Nakonfigurujte přednastavení kodéru.

    Pojmenujte přednastavení a zkontrolujte následující doporučená nastavení:

    **Video**

   * Kodér: MainConcept H.264
   * Počet snímků za sekundu: 30
   * Průměrná přenosová rychlost: 5000 kbits/s (lze upravit na základě omezení sítě)
   * Profil: Hlavní
   * Klíčový snímek každý: 60 snímků

     **Zvuk**

   * Cílová přenosová rychlost: 192 kbit/s
   * Vzorkovací frekvence: 44.100 kHz

     ![drátěné](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Stiskněte **Uložit**.

    Pole Kódování má nyní nově vytvořený profil k dispozici pro výběr.

    Zkontrolujte, zda je vybrán nový profil.
7. Získejte vstupní adresu URL kanálu, abyste ji mohli přiřadit ke **koncovému bodu RTMP wirecast**.

    Přejděte zpět na nástroj AMSE a zkontrolujte stav dokončení kanálu. Jakmile se stát změní ze **spuštění** na **spuštěný**, můžete získat vstupní adresu URL.

    Když je kanál spuštěn, klikněte pravým tlačítkem myši na název kanálu, přejděte na jev myši na **kopírovat vstupní adresu URL do schránky** a pak vyberte Primární vstupní adresu **URL**.  

    ![drátěné](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. V okně **Nastavení výstupu** drátového vysílání vložte tyto informace do pole **Adresa** v části výstup a přiřaďte název datového proudu.

    ![drátěné](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Vyberte **OK**.
2. Na hlavní obrazovce **Wirecast** potvrďte, že vstupní zdroje pro video a zvuk jsou připraveny a pak stiskněte **stream** v levém horním rohu.

    ![drátěné](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Před klepnutím na tlačítko **Stream**je **nutné** zajistit, aby byl kanál připraven.
> Také se ujistěte, že nechcete opustit kanál v pohotovostním stavu bez vstupního příspěvku po dobu delší než > 15 minut.
>
>

## <a name="test-playback"></a>Přehrávání testů

Přejděte na nástroj AMSE a klepněte pravým tlačítkem myši na kanál, který chcete testovat. V nabídce najeďte na **Přehrát náhled** a vyberte pomocí **Programu Azure Media Player**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Pokud se datový proud zobrazí v přehrávači, je kodér správně nakonfigurován pro připojení k AMS.

Pokud je přijata chyba, je třeba kanál resetovat a upravit nastavení kodéru. Pokyny naleznete v článku [pro řešení potíží.](media-services-troubleshooting-live-streaming.md)  

## <a name="create-a-program"></a>Vytvoření programu
1. Po potvrzení přehrávání kanálu vytvořte program. Na kartě **Živé** v nástroji AMSE klepněte pravým tlačítkem myši do oblasti programu a vyberte **příkaz Vytvořit nový program**.  

    ![drátěné](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
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

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
