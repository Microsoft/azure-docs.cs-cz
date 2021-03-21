---
title: Získání snímků z dronů
description: Tento článek popisuje, jak z partnerů získat pomocí dronů.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: 18932d2ddb60242b4d7874dddf0349a62cd5c738
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102177602"
---
# <a name="get-drone-imagery-from-drone-partners"></a>Získání pomocí dronů snímků z partnerů pomocí dronů

Tento článek popisuje, jak můžete přenést orthomosaic data z vašich partnerů z pomocí dronůového úložiště do Azure FarmBeats DataHub. Orthomosaic je letecká ilustrace nebo obrázek, který je geometrickě opravený a sešitný z dat shromažďovaných pomocí pomocí dronů.

V současné době jsou podporovány následující partneři na základě snímků.

  ![Partneři FarmBeats pomocí dronů snímků](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

Integrace dat pomocí dronů s použitím Azure FarmBeats vám pomůže získat orthomosaic data z pomocí dronůch letů, které ve vaší farmě vykonáváte, do DataHub. Jakmile budou data k dispozici, můžete je zobrazit v akcelerátoru FarmBeats. Data se dají použít k tvorbě datových fúzí a umělých inteligentních funkcí a vytváření modelů strojového učení.

## <a name="before-you-begin"></a>Než začnete

  - Ujistěte se, že jste nainstalovali Azure FarmBeats. Informace o tom, jak nainstalovat FarmBeats, najdete v tématu [instalace Azure FarmBeats](install-azure-farmbeats.md).
  - Ujistěte se, že máte farmu, pro kterou chcete pomocí dronůý obrázek ve vašem FarmBeats systému definovat.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>Povolit integraci pomocí dronůového snímků s FarmBeats

Pokud chcete povolit integraci s FarmBeats, poskytněte poskytovateli zařízení následující informace:
 - Koncový bod rozhraní API
 - ID tenanta
 - ID klienta
 - Tajný klíč klienta

Postupujte takto:

1. Stáhněte si tento [skript](https://aka.ms/farmbeatspartnerscript)a extrahujte ho na místní disk. V souboru ZIP jsou dva soubory.
2. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) a otevřete Azure Cloud Shell. Tato možnost je k dispozici na panelu nástrojů v pravém horním rohu portálu.

    ![Otevřít Azure Cloud Shell na pravém horním panelu portálu](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Ujistěte se, že je prostředí nastavené na **PowerShell**.

    ![Nastavení PowerShellu](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Nahrajte dva soubory, které jste stáhli z kroku 1 v instanci Cloud Shell.

    ![Nahrání souborů](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. Přejít do adresáře, kam se nahrály soubory. Ve výchozím nastavení se odešlou do domovského adresáře pod uživatelským jménem.
6. Spusťte tento skript:

    ```azurepowershell-interactive

    ./generateCredentials.ps1

    ```

7. Podle pokynů na obrazovce Zachyťte hodnoty koncového bodu rozhraní API, ID tenanta, ID klienta, tajný klíč klienta a připojovací řetězec EventHub.

    Po zadání požadovaných přihlašovacích údajů do pomocí dronů softwarového systému partnera můžete importovat všechny farmy ze systému FarmBeats. Pak můžete použít podrobnosti o farmě pro plánování letových cest a kolekci imagí pomocí dronů.

    Po zpracování nezpracovaných imagí softwarem pomocí dronů Providers nahraje do DataHub software pomocí dronůed orthomosaic a další zpracované image.

## <a name="view-drone-imagery"></a>Zobrazit pomocí dronůý obrázek

Po odeslání dat do FarmBeats DataHub můžete zadat dotaz na úložiště scény pomocí rozhraní FarmBeats DataHub API.

Alternativně můžete zobrazit nejnovější Image pomocí dronů na stránce s **podrobnostmi o farmě** . Chcete-li zobrazit obrázek, postupujte podle pokynů.

1. Vyberte farmu, do které se nahrál váš obrázek. Zobrazí se stránka s podrobnostmi o **farmě** .
2. Přejděte dolů k části nejnovější **mapy přesnosti** .
3. Podívejte se na obrázek v části **pomocí dronů snímků** .

    ![Oddíl pomocí dronů snímků](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>Stáhnout pomocí dronůý obrázek

Když vyberete část pomocí dronů, zobrazí se automaticky otevírané okno s obrázkem s vysokým rozlišením orthomosaicu pomocí dronů.

![Orthomosaic s vysokým rozlišením](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>Zobrazit všechny mapy pomocí dronů

Soubory a obrázky odeslané poskytovatelem pomocí dronů se zobrazí v oddílu **Maps** . Vyberte část **mapy** , filtr podle **farmy** a vyberte příslušné soubory, které chcete zobrazit a stáhnout.

  ![Oddíl Maps](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>Další kroky

Naučte se používat rozhraní FarmBeats DataHub [API](rest-api-in-azure-farmbeats.md) k získání pomocí dronů snímků.
