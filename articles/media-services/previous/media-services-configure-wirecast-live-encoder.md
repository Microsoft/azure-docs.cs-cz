---
title: Konfigurovat kodér Telestream Wirecast odesílat živý datový proud s jednou přenosovou rychlostí | Dokumentace Microsoftu
description: 'Toto téma ukazuje, jak nakonfigurovat živé kodér Wirecast Odeslat datový proud s jednou přenosovou rychlostí do AMS kanálů, které jsou povolené kódování v reálném čase. '
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
ms.openlocfilehash: 6cc4c0b01511309766e48c3d671ee897e5d6f326
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669109"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Kodér Wirecast můžete odesílat živý datový proud s jednou přenosovou rychlostí
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

Tento článek popisuje, jak nakonfigurovat [Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm) kodér služby live Encoding Odeslat datový proud s jednou přenosovou rychlostí do AMS kanály, které jsou povolené kódování v reálném čase.  Další informace najdete v článku o [práci s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Tento kurz ukazuje, jak spravovat Azure Media Services (AMS) s nástrojem Azure Media Services Explorer (AMSE). Tento nástroj lze spustit pouze na počítač s Windows. Pokud jste v systému Mac nebo Linux, pomocí webu Azure portal k vytvoření [kanály](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) a [programy](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Požadavky
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

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Zadejte název kanálu, do pole Popis je volitelný. V části nastavení kanálu, vyberte **standardní** pro možnost Live Encoding s protokolem vstupu, nastavte na **RTMP**. Všechna ostatní nastavení je můžete nechat.

    Ujistěte se, že **spustit nový kanál** zaškrtnuto.

3. Klikněte na tlačítko **vytvořit kanál**.

   ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> Kanál může trvat až 20 minut.
>
>

Při spuštění kanálu můžete [nakonfigurovat kodér](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> Fakturace začíná, jakmile kanál přejde do stavu Připraveno. Další informace najdete v tématu [kanálu stavy](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="a-idconfigurewirecastrtmp-aconfigure-the-telestream-wirecast-encoder"></a>< id = "configure_wirecast_rtmp" /a > Konfigurovat kodér Telestream Wirecast
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
2. Nakonfigurovat výstup tak, že přejdete na **výstup** kartu a vyberete **výstup nastavení...** .

    Ujistěte se, že **cíl výstupu** je nastavena na **RTMP Server**.
3. Klikněte na **OK**.
4. Na stránce nastavení nastavit **cílové** pole bylo **Azure Media Services**.

    Profil kódování je předem vybraná pro **Azure H.264 720 p 16:9 (1280 x 720)**. Chcete-li nastavení upravit, vyberte ikonu ozubeného kola vedle rozevíracího seznamu a klikněte na tlačítko **nové přednastavení**.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Konfigurovat kodér předvolby.

    Pojmenujte přednastavený kontext a zkontrolujte následující doporučené nastavení:

    **Video**

   * Kodér: Mainconceptu H.264
   * Snímků za sekundu: 30
   * Průměrná přenosová rychlost: 5000 kbits za sekundu (můžete třeba nastavit podle omezení sítě)
   * Profil: hlavní
   * Klíčový snímek každých: 60 snímků

    **Zvuk**

   * Cíl přenosová rychlost: 192 kbits za sekundu
   * Vzorkovací frekvence: 44 100 kHz

     ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Stiskněte **Uložit**.

    Kódování pole má teď k dispozici pro výběr nově vytvořený profil.

    Ujistěte se, že je vybraný nový profil.
7. Get kanál se uživatelovo zadání adresy URL, abyste mohli přiřadit k Wirecast **koncový bod RTMP**.

    Přejděte zpět na nástroj AMSE a zkontrolovat stav dokončení kanálu. Jakmile stav změnil ze **počáteční** k **systémem**, vstupní adresu URL můžete získat.

    Při spuštění kanálu, klikněte pravým tlačítkem na název kanálu, přejděte dolů při najetí myší nad **adresa URL vstupu kopírování do schránky** a pak vyberte **primární adresy URL vstupu**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. V Wirecast **nastavení výstupní** okně vložte tyto informace **adresu** pole z výstupní sekce a přiřaďte název datového proudu.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Vyberte **OK**.
2. V hlavním **Wirecast** zkontrolujte vstupní zdroje pro připraveni videa a zvuku a pak klikněte na tlačítko **Stream** v levém horním rohu.

   ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Před kliknutím na **Stream**, můžete **musí** Ujistěte se, že kanál je připravený.
> Také ujistěte se, že nechcete bez vstupní příspěvek kanálu po dobu delší než 15 minut > nechat kanál ve stavu Připraveno.
>
>

## <a name="test-playback"></a>Přehrávání testů

Přejděte na nástroj AMSE a klikněte pravým tlačítkem na kanál, který má být testována. Z nabídky, najeďte myší na **přehrávání Náhled** a vyberte **pomocí Azure Media Player**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Pokud datový proud se zobrazí v přehrávači, pak kodér správně nakonfigurovaný pro připojení k AMS.

Pokud k chybě, kanál je potřeba resetovat a upravit nastavení kodéru. Najdete v článku [řešení potíží s](media-services-troubleshooting-live-streaming.md) článku pokyny.  

## <a name="create-a-program"></a>Vytvoření programu
1. Jakmile přehrávání kanálu je potvrzen, vytvořte program. V části **Live** kartu nástroj AMSE klikněte pravým tlačítkem v rámci oblasti program a vyberte **vytvořit nový Program**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
2. Název programu a v případě potřeby upravit **délka okna archivu** (která má výchozí hodnotu čtyři hodiny). Můžete také určit umístění úložiště nebo ponechte jako výchozí.  
3. Zkontrolujte **spustit Program nyní** pole.
4. Klikněte na tlačítko **vytvořit Program**.  

   >[!NOTE]
   >Vytvoření programu trvá méně času než vytváření kanálů.
       
5. Jakmile program běží, potvrďte přehrávání kliknutím pravým tlačítkem program a přejdete na **přehrávání programech** a následným výběrem **pomocí Azure Media Player**.  
6. Až potvrdí, klikněte pravým tlačítkem na program znovu a vyberte **zkopírujte adresu URL výstup do schránky** (nebo načtení těchto informací z **programu informace a nastavení** možnost z nabídky).

Datový proud je teď připravený k součástí přehrávač nebo distribuované na cílovou skupinou pro živé zobrazení.  

## <a name="troubleshooting"></a>Řešení potíží
Najdete v článku [řešení potíží s](media-services-troubleshooting-live-streaming.md) článku pokyny.

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
