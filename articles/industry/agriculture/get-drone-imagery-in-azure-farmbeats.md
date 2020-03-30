---
title: Získání snímků z dronů
description: Tento článek popisuje, jak získat snímky drone od partnerů.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 3e452cd548738e5f211899d3a6a676f883d800ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132048"
---
# <a name="get-drone-imagery-from-drone-partners"></a>Získejte snímky dronů od partnerů drone

Tento článek popisuje, jak můžete přenést ortomosaic data z vašich partnerů drone snímků azure FarmBeats Datahub. Ortomosaic je letecká ilustrace nebo obraz, který je geometricky korigován a sešit z dat shromážděných dronem.

V současné době jsou podporovány následující snímky partnerů.

  ![Partneři pro snímky dronů FarmBeats](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

Integrace dat snímků dronů s Azure FarmBeats vám pomůže získat ortomozaiková data z letů drone, které provádíte na své farmě, do datového hubu. Poté, co jsou data k dispozici, můžete je zobrazit v akcelerátoru FarmBeats. Data lze použít pro fúzi dat a umělou inteligenci a vytváření modelů strojového učení.

## <a name="before-you-begin"></a>Než začnete

  - Ujistěte se, že jste nainstalovali Azure FarmBeats. Informace o instalaci FarmBeats najdete [v tématu Instalace Azure FarmBeats](install-azure-farmbeats.md).
  - Ujistěte se, že máte farmu, pro kterou chcete drone snímky definované ve vašem systému FarmBeats.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>Povolte integraci snímků dronů s FarmBeats

Poskytněte svému poskytovateli zařízení následující informace, které umožní integraci s FarmBeats:
 - Koncový bod rozhraní API
 - ID tenanta
 - ID klienta
 - Tajný klíč klienta

Postupujte následovně.

1. Stáhněte si tento [skript](https://aka.ms/farmbeatspartnerscript)a extrahujte jej na místní disk. Dva soubory jsou uvnitř souboru zip.
2. Přihlaste se k [portálu Azure](https://portal.azure.com/) a otevřete Azure Cloud Shell. Tato možnost je k dispozici na panelu nástrojů v pravém horním rohu portálu.

    ![Otevření prostředí Azure Cloud Shell na pravém horním panelu portálu](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Ujistěte se, že je prostředí nastaveno na **Prostředí PowerShell**.

    ![Nastavení prostředí PowerShell](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Nahrajte dva soubory, které jste stáhli z kroku 1 v instanci Cloud Shell.

    ![Nahrání souborů](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. Přejděte do adresáře, do kterého byly soubory nahrány. Ve výchozím nastavení jsou odeslány do domovského adresáře pod uživatelským jménem.
6. Spusťte tento skript:

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```

7. Postupujte podle pokynů na obrazovce k zachycení hodnot koncového bodu rozhraní API, ID klienta, ID klienta, tajný klíč klienta a eventhub spojovací řetězec.

    Po zadání požadovaných přihlašovacích údajů do softwarového systému dronů partnera můžete importovat všechny farmy ze systému FarmBeats. Pak můžete použít podrobnosti o farmě k plánování vaší dráhy letu a shromažďování obrázků drone.

    Poté, co jsou nezpracované obrázky zpracovány softwarem poskytovatelů dronů, softwarový systém drone nahraje šitou ortomozaiku a další zpracované obrázky do datahubu.

## <a name="view-drone-imagery"></a>Zobrazit snímky drone

Po odeslání dat do datahubu FarmBeats můžete dotazovat úložiště scén pomocí api FarmBeats Datahub.

Případně můžete zobrazit nejnovější obrázek drone na stránce **Podrobnosti farmy.** Chcete-li obrázek zobrazit, postupujte podle pokynů.

1. Vyberte farmu, do které byly vaše snímky nahrány. Zobrazí se stránka **Podrobnosti farmy.**
2. Přejděte dolů k nejnovější části **Přesné mapy.**
3. Prohlédněte si obrázek v části **Snímky dronu.**

    ![Sekce snímků drone](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>Stáhněte si snímky dronů

Když vyberete sekci Snímky drone, otevře se vyskakovací okno, které zobrazí obraz ortomozaiky drone ve vysokém rozlišení.

![Ortomozaika s vysokým rozlišením](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>Zobrazit všechny mapy dronů

Soubory a obrázky nahrané poskytovatelem dronu se zobrazí v sekci **Mapy.** Vyberte oddíl **Mapy,** filtrujte podle **farmy**a vyberte příslušné soubory, které chcete zobrazit a stáhnout.

  ![Sekce Mapy](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>Další kroky

Naučte se používat farmbeats Datahub [API,](rest-api-in-azure-farmbeats.md) abyste získali snímky drone.
