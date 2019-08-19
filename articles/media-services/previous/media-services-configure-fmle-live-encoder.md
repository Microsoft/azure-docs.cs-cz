---
title: Konfigurace kodéru FMLE pro odesílání živého streamu s jednou přenosovou rychlostí | Microsoft Docs
description: V tomto tématu se dozvíte, jak nakonfigurovat kodér Flash Media Live Encoder (FMLE), který odešle datový proud s jednou přenosovou rychlostí do kanálů AMS, které mají povolené kódování v reálném čase.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 3113f333-517a-47a1-a1b3-57e200c6b2a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: cenkdin;anilmur
ms.openlocfilehash: 09d9bdffefe9204e9f58b8f07af5b21228269f6c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "69016758"
---
# <a name="use-the-fmle-encoder-to-send-a-single-bitrate-live-stream"></a>Použití kodéru FMLE k odeslání živého streamu s jednou přenosovou rychlostí 
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

Tento článek ukazuje, jak nakonfigurovat kodér [Flash Media Live Encoder](https://www.adobe.com/products/flash-media-encoder.html) (FMLE) pro odeslání datového proudu s jednou přenosovou rychlostí do kanálů AMS, které mají povolené kódování v reálném čase. Další informace najdete v článku o [práci s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Tento kurz ukazuje, jak spravovat Azure Media Services (AMS) s nástrojem Azure Media Services Explorer (AMSE). Tento nástroj lze spustit pouze na počítač s Windows. Pokud jste v systému Mac nebo Linux, pomocí webu Azure portal k vytvoření [kanály](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) a [programy](media-services-portal-creating-live-encoder-enabled-channel.md).

Tento kurz popisuje použití AAC. FMLE ale ve výchozím nastavení nepodporuje AAC. Museli byste si koupit modul plug-in pro kódování AAC, jako je třeba z MainConcept: [Modul plug-in AAC](https://www.mainconcept.com/products/plug-ins/plug-ins-for-adobe/aac-encoder-fmle.html)

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

Při spuštění kanálu můžete [nakonfigurovat kodér](media-services-configure-fmle-live-encoder.md).

> [!IMPORTANT]
> Všimněte si, že fakturace začne hned po přechodu kanálu do stavu připraveno. Další informace najdete v tématu [kanálu stavy](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_fmle_rtmp></a>Konfigurace kodéru FMLE
V tomto kurzu se používají následující nastavení výstupu. Zbytek tohoto oddílu popisuje jednotlivé kroky konfigurace v podrobněji.

**Video**:

* Kodek H. 264
* Profilu Vysoká (úroveň 4,0)
* Rychlostí 5000 KB/s
* Klíč 2 sekundy (60 sekund)
* Snímková frekvence: 30

**Zvuk**:

* Kodek AAC (LC)
* Rychlostí 192 KB/s
* Vzorkovací frekvence: 44,1 kHz

### <a name="configuration-steps"></a>Postup konfigurace
1. Přejděte na rozhraní FMLE (Flash Media Live Encoder) na používaném počítači.

    Rozhraní je jedna hlavní stránka nastavení. Poznamenejte si následující doporučené nastavení, abyste mohli začít s streamování pomocí FMLE.

   * Formát: H. 264 snímková frekvence: 30,00
   * Velikost vstupu: 1280 x 720
   * Přenosová rychlost: 5000 KB/s (dá se upravit na základě omezení sítě)  

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle3.png)

     Při použití prokládaných zdrojů zaškrtněte možnost Zrušit prokládání.
2. Vyberte ikonu klíče vedle možnosti formátovat, tato další nastavení by měla být:

   * Profilu Hlavní
   * Úroveň: 4.0
   * Frekvence klíčových snímků: 2 sekundy

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle4.png)
3. Nastavte následující důležité nastavení zvuku:

   * Formát: AAC
   * Vzorkovací frekvence: 44100 Hz
   * Rychlostí 192 KB/s

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle5.png)
4. Získejte vstupní adresu URL kanálu, aby ji bylo možné přiřadit ke FMLEmu **koncovému bodu RTMP**.

    Přejděte zpět na nástroj AMSE a zkontrolovat stav dokončení kanálu. Jakmile stav změnil ze **počáteční** k **systémem**, vstupní adresu URL můžete získat.

    Při spuštění kanálu, klikněte pravým tlačítkem na název kanálu, přejděte dolů při najetí myší nad **adresa URL vstupu kopírování do schránky** a pak vyberte **primární adresy URL vstupu**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle6.png)
5. Tyto informace vložte do pole **Adresa URL FMS** v části výstup a přiřaďte název streamu.

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle7.png)

    Pro dodatečnou redundanci opakujte tyto kroky se sekundární vstupní adresou URL.
6. Vyberte **Connect** (Připojit).

> [!IMPORTANT]
> Než kliknete na **připojit**, **musíte** zajistit, aby byl kanál připravený.
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
2. Pojmenujte program a v případě potřeby upravte **délku okna archivu** (výchozí nastavení je 4 hodiny). Můžete také určit umístění úložiště nebo ponechte jako výchozí.  
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
