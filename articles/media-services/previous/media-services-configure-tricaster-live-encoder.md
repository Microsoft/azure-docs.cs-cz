---
title: Konfigurovat kodér NewTek tricaster odesílat živý datový proud s jednou přenosovou rychlostí | Dokumentace Microsoftu
description: Toto téma ukazuje, jak nakonfigurovat Tricaster kodér služby live Encoding Odeslat datový proud s jednou přenosovou rychlostí do AMS kanálů, které jsou povolené kódování v reálném čase.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: 8973181a-3059-471a-a6bb-ccda7d3ff297
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 02/08/2019
ms.author: juliako;cenkd;anilmur
ms.openlocfilehash: 64c38d6e7400d49903323159f23b395a8c2c334f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55998205"
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Kodér NewTek tricaster můžete odesílat živý datový proud s jednou přenosovou rychlostí  
> [!div class="op_single_selector"]
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

Tento článek popisuje, jak nakonfigurovat [kodér NewTek TriCaster](http://newtek.com/products/tricaster-40.html) kodér služby live Encoding Odeslat datový proud s jednou přenosovou rychlostí do AMS kanály, které jsou povolené kódování v reálném čase. Další informace najdete v článku o [práci s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Tento kurz ukazuje, jak spravovat Azure Media Services (AMS) s nástrojem Azure Media Services Explorer (AMSE). Tento nástroj lze spustit pouze na počítač s Windows. Pokud jste v systému Mac nebo Linux, pomocí webu Azure portal k vytvoření [kanály](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) a [programy](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> Při použití Tricaster k odeslání příspěvku do AMS kanály, které jsou povolené kódování v reálném čase, může být video nebo zvuk potížím s vykreslováním v svoji živou událost použití určitých funkcí Tricaster, jako je rychlé cutting mezi informační kanály nebo přechod z slaty. AMS tým pracuje na řešení těchto problémů, dokud to neuděláte, nedoporučujeme používat tyto funkce.
>
>

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

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Zadejte název kanálu, do pole Popis je volitelný. V části nastavení kanálu, vyberte **standardní** pro možnost Live Encoding s protokolem vstupu, nastavte na **RTMP**. Všechna ostatní nastavení je můžete nechat.

    Ujistěte se, že **spustit nový kanál** zaškrtnuto.

3. Klikněte na tlačítko **vytvořit kanál**.

   ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> Kanál může trvat až 20 minut.
>
>

Při spuštění kanálu můžete [nakonfigurovat kodér](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

> [!IMPORTANT]
> Fakturace začíná, jakmile kanál přejde do stavu Připraveno. Další informace najdete v tématu [kanálu stavy](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="a-idconfiguretricasterrtmpconfigure-the-newtek-tricaster-encoder"></a><a id="configure_tricaster_rtmp"/>Konfigurovat kodér NewTek tricaster

V tomto kurzu se používají následující nastavení výstupu. Zbytek tohoto oddílu popisuje jednotlivé kroky konfigurace v podrobněji.

**Video**:

* Kodek: H.264
* Profil: Vysoká (úroveň 4.0)
* S přenosovou rychlostí: 5000 kb/s
* Klíčový snímek: 2 sekundy (60 sekund)
* Snímková frekvence: 30

**Zvuk**:

* Kodek: AAC (LC)
* S přenosovou rychlostí: 192 kb/s
* Vzorkovací frekvence: 44,1 kHz

### <a name="configuration-steps"></a>Postup konfigurace

1. Vytvořte nový **kodér NewTek TriCaster** projektu v závislosti na tom, jaké vstupní zdroj videa se používá.
2. Jednou v rámci projektu, vyhledejte **Stream** tlačítko a klikněte na ikonu ozubeného kola vedle mu umožní přístup k nabídce konfiguraci datového proudu.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. Po otevření nabídky, klikněte na tlačítko **nový** pod záhlavím připojení. Po zobrazení výzvy pro typ připojení, vyberte **Adobe Flash**.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. Klikněte na **OK**.
5. Kliknutím šipku rozevíracího seznamu v části lze nyní importovat profil FMLE **streamování profilu** a přejdete do **Procházet**.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. Přejděte k uložení nakonfigurovaný profil FMLE.
7. Vyberte ji a stiskněte klávesu **OK**.

    Po nahrání profilu pokračujte k dalšímu kroku.
8. Get kanál se uživatelovo zadání adresy URL, abyste mohli přiřadit k Tricaster **koncový bod RTMP**.

    Přejděte zpět na nástroj AMSE a zkontrolovat stav dokončení kanálu. Jakmile stav změnil ze **počáteční** k **systémem**, vstupní adresu URL můžete získat.

    Při spuštění kanálu, klikněte pravým tlačítkem na název kanálu, přejděte dolů při najetí myší nad **adresa URL vstupu kopírování do schránky** a pak vyberte **primární adresy URL vstupu**.  

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Vložte tyto informace **umístění** pole **serveru Flash** v rámci projektu Tricaster. Také přiřadit název do datového proudu **Stream ID** pole.

    Informace o streamu přidal do profilu FMLE, ji můžete také importovat do této části kliknutím **nastavení importu**, že přejdete na uloženého profilu FMLE a kliknutím na **OK**. Do příslušných polí serveru Flash by měl vyplnit informacemi z FMLE.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. Až budete hotovi, klikněte na tlačítko **OK** v dolní části obrazovky. Jakmile jsou připravené video a audiostreamů vstupy do Tricaster, začít Streamovat do AMS po kliknutí **Stream** tlačítko.

     ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> Před kliknutím na **Stream**, můžete **musí** Ujistěte se, že kanál je připravený.
> Také ujistěte se, že nechcete bez vstupní příspěvek kanálu po dobu delší než 15 minut > nechat kanál ve stavu Připraveno.
>
>

## <a name="test-playback"></a>Přehrávání testů

Přejděte na nástroj AMSE a klikněte pravým tlačítkem na kanál, který má být testována. Z nabídky, najeďte myší na **přehrávání Náhled** a vyberte **pomocí Azure Media Player**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Pokud datový proud se zobrazí v přehrávači, pak kodér správně nakonfigurovaný pro připojení k AMS.

Pokud k chybě, kanál muset obnovit a upravit nastavení kodéru. Najdete v článku [řešení potíží s](media-services-troubleshooting-live-streaming.md) článku pokyny.  

## <a name="create-a-program"></a>Vytvoření programu

1. Jakmile přehrávání kanálu je potvrzen, vytvořte program. V části **Live** kartu nástroj AMSE klikněte pravým tlačítkem v rámci oblasti program a vyberte **vytvořit nový Program**.  

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
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

## <a name="next-step"></a>Další krok

Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
