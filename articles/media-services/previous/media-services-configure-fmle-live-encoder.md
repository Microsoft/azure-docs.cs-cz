---
title: Konfigurovat kodér FMLE odesílat živý datový proud s jednou přenosovou rychlostí | Dokumentace Microsoftu
description: Toto téma ukazuje, jak konfigurovat kodér rozhraní Flash Media Live Encoder (FMLE) Chcete-li odesílat datový proud s jednou přenosovou rychlostí do AMS kanálů, které jsou povolené kódování v reálném čase.
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
ms.openlocfilehash: 1a7cbd19b89663ab874fc5a7a86587e292b86f81
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665881"
---
# <a name="use-the-fmle-encoder-to-send-a-single-bitrate-live-stream"></a>Kodér FMLE můžete odesílat živý datový proud s jednou přenosovou rychlostí
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

Tento článek popisuje, jak nakonfigurovat [Flash Media Live Encoder](http://www.adobe.com/products/flash-media-encoder.html) encoder (FMLE) Odeslat datový proud s jednou přenosovou rychlostí do AMS kanály, které jsou povolené kódování v reálném čase. Další informace najdete v článku o [práci s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Tento kurz ukazuje, jak spravovat Azure Media Services (AMS) s nástrojem Azure Media Services Explorer (AMSE). Tento nástroj lze spustit pouze na počítač s Windows. Pokud jste v systému Mac nebo Linux, pomocí webu Azure portal k vytvoření [kanály](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) a [programy](media-services-portal-creating-live-encoder-enabled-channel.md).

Tento kurz popisuje použití AAC. Nicméně FMLE není ve výchozím nastavení podporuje AAC. Měl by si koupit modul plug-in AAC kódování, například kvůli Mainconceptu: [modulu plug-in AAC](http://www.mainconcept.com/products/plug-ins/plug-ins-for-adobe/aac-encoder-fmle.html)

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

    ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle1.png)

2. Zadejte název kanálu, do pole Popis je volitelný. V části nastavení kanálu, vyberte **standardní** pro možnost Live Encoding s protokolem vstupu, nastavte na **RTMP**. Všechna ostatní nastavení je můžete nechat.

    Ujistěte se, že **spustit nový kanál** zaškrtnuto.

3. Klikněte na tlačítko **vytvořit kanál**.

   ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle2.png)

> [!NOTE]
> Kanál může trvat až 20 minut.
>
>

Při spuštění kanálu můžete [nakonfigurovat kodér](media-services-configure-fmle-live-encoder.md#configure_fmle_rtmp).

> [!IMPORTANT]
> Všimněte si, že fakturace začne ihned poté, co kanál přejde do stavu Připraveno. Další informace najdete v tématu [kanálu stavy](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_fmle_rtmp></a>Konfigurovat kodér FMLE
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
1. Přejděte Flash Media Live Encoder společnosti (FMLE) rozhraní na počítač, který používáte.

    Rozhraní je jeden hlavní stránky nastavení. Poznamenejte si následující doporučené nastavení, abyste mohli začít se streamováním pomocí FMLE.

   * Formát: H.264 Snímková frekvence: 30,00
   * Velikost vstupu: 1280 × 720
   * Přenosová rychlost: 5000 kb/s (můžete třeba nastavit podle omezení sítě)  

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle3.png)

     Při použití prokládanou zdroje, prosím zaškrtnutí možnost "volby odstranit prokládání"
2. Vyberte ikonu klíče vedle formátu, by měla být tato další nastavení:

   * Profil: hlavní
   * Úroveň: 4.0
   * Klíčový snímek frekvence: 2 sekundy

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle4.png)
3. Nastavte následující důležité zvuku nastavení:

   * Formát: AAC
   * Vzorkovací frekvence: Hz 44100
   * S přenosovou rychlostí: 192 kb /

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle5.png)
4. Get kanál se uživatelovo zadání adresy URL, abyste mohli přiřadit k FMLE **koncový bod RTMP**.

    Přejděte zpět na nástroj AMSE a zkontrolovat stav dokončení kanálu. Jakmile stav změnil ze **počáteční** k **systémem**, vstupní adresu URL můžete získat.

    Při spuštění kanálu, klikněte pravým tlačítkem na název kanálu, přejděte dolů při najetí myší nad **adresa URL vstupu kopírování do schránky** a pak vyberte **primární adresy URL vstupu**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle6.png)
5. Vložte tyto informace **FMS URL** pole z výstupní sekce a přiřaďte název datového proudu.

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle7.png)

    Pro zajištění další redundance opakujte tyto kroky se sekundární adresa URL vstupu.
6. Vyberte **Connect** (Připojit).

> [!IMPORTANT]
> Před kliknutím na **připojit**, můžete **musí** Ujistěte se, že kanál je připravený.
> Také ujistěte se, že nechcete bez vstupní příspěvek kanálu po dobu delší než 15 minut > nechat kanál ve stavu Připraveno.
>
>

## <a name="test-playback"></a>Přehrávání testů

Přejděte na nástroj AMSE a klikněte pravým tlačítkem na kanál, který má být testována. Z nabídky, najeďte myší na **přehrávání Náhled** a vyberte **pomocí Azure Media Player**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle8.png)

Pokud datový proud se zobrazí v přehrávači, pak kodér správně nakonfigurovaný pro připojení k AMS.

Pokud k chybě, kanál je potřeba resetovat a upravit nastavení kodéru. Najdete v článku [řešení potíží s](media-services-troubleshooting-live-streaming.md) článku pokyny.  

## <a name="create-a-program"></a>Vytvoření programu
1. Jakmile přehrávání kanálu je potvrzen, vytvořte program. V části **Live** kartu nástroj AMSE klikněte pravým tlačítkem v rámci oblasti program a vyberte **vytvořit nový Program**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle9.png)
2. Název programu a v případě potřeby upravit **délka okna archivu** (která má výchozí hodnotu 4 hodiny). Můžete také určit umístění úložiště nebo ponechte jako výchozí.  
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
