---
title: Získání pomocí dronů snímků
description: V této části najdete popis postupu získání pomocí dronů snímků z partnerů.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 19d709a2eb0af5bdb9d40a2bce96c5716ce3c6f6
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847414"
---
# <a name="get-drone-imagery-from-drone-partners"></a>Získání pomocí dronů snímků z partnerů pomocí dronů

Tento článek popisuje, jak můžete přenést data orthomosaic z vašich partnerů z pomocí dronůového úložiště do Azure FarmBeats data hub. Orthomosaic je letecká ilustrace nebo obrázek, který je geometrickě opravován a připojen z dat shromažďovaných pomocí pomocí dronů.

V současné době jsou podporovány následující partneři na základě snímků.

  ![Beats farmy projektu](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

Integrace dat pomocí dronů s použitím Azure FarmBeats vám pomůže získat orthomosaic data z pomocí dronůch letů, které ve farmě vykonáváte, do datového centra. Jakmile jsou data k dispozici, můžete je zobrazit v akcelerátoru FarmBeats a lze je použít pro vytváření modelů v datových Fusion a AI/ML.

## <a name="before-you-begin"></a>Než začnete

  - Ujistěte se, že jste nasadili Azure FarmBeats. Pro nasazení navštivte web [nasazení FarmBeats](prepare-for-deployment.md).
  - Ujistěte se, že máte farmu (pro kterou chcete pomocí dronůý obrázek) definovaný v FarmBeats systému.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>Povolit integraci pomocí dronůového snímků s FarmBeats   

Abyste mohli povolit integraci s FarmBeats, musíte poskytovateli zařízení zadat následující informace:  
 - Koncový bod rozhraní API  
 - ID tenanta  
 - ID klienta  
 - Tajný kód klienta  

Použijte k tomu následující postup:

1. Stáhněte si tento [skript](https://aka.ms/farmbeatspartnerscript) a extrahujte ho na místním disku. V tomto souboru ZIP se nacházejí dva soubory.  
2. Přihlaste se k [Azure Portal](https://portal.azure.com/) a otevřete Cloud Shell (Tato možnost je k dispozici na pravém horním panelu portálu).   

    ![Beats farmy projektu](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Ujistěte se, že je prostředí nastavené na **PowerShell** .

    ![Beats farmy projektu](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Nahrajte dva soubory, které jste stáhli (z kroku 1 výše), do Cloud Shell.  

    ![Beats farmy projektu](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. Přejít do adresáře, kam se nahrály soubory. (Ve výchozím nastavení se nahraje do domovského adresáře > uživatelské jméno.)  
6. Spusťte tento skript:

    ```azurepowershell-interactive 

    PS> ./generateCredentials.ps1   

    ```

7. Podle pokynů na obrazovce Zachyťte hodnoty koncového bodu rozhraní API, ID tenanta, ID klienta, tajný klíč klienta a připojovací řetězec EventHub.

    Po zadání požadovaných přihlašovacích údajů do pomocí dronů softwaru partnera budete moct importovat všechny farmy ze systému FarmBeats a použít podrobnosti farmy pro plánování letových cest a kolekci imagí pomocí dronů.

    Po zpracování nezpracovaných imagí softwarem pomocí dronů Providers nahraje sešitované orthomosaic a další zpracované image do datového centra pomocí dronů.

## <a name="view-drone-imagery"></a>Zobrazit pomocí dronůý obrázek

Jakmile se data odešlou do datového centra FarmBeats, měli byste být schopni dotazovat úložiště scény pomocí rozhraní API centra dat FarmBeats.

Alternativně byste měli být schopni zobrazit nejnovější Image pomocí dronů na stránce s **podrobnostmi o farmě** . Chcete-li zobrazit, postupujte podle následujících kroků:  

1. Vyberte farmu, do které se nahrály vaše dokumenty z vašich snímků. Zobrazí se stránka s podrobnostmi o **farmě** .
2. Přejděte dolů k části nejnovější **mapy přesnosti** .
3. Měli byste být schopni zobrazit obrázek v části **pomocí dronů snímků** .

    ![Beats farmy projektu](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>Stáhnout pomocí dronůý obrázek

Když vyberete část pomocí dronů, zobrazí se automaticky otevírané okno s obrázkem s vysokým rozlišením orthomosaicu pomocí dronů.

![Beats farmy projektu](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>Zobrazit všechny mapy pomocí dronů

Soubory a obrázky odeslané poskytovatelem pomocí dronů se zobrazí v oddílu Maps. Vyberte část **mapy** , filtr podle **farmy** a zvolte příslušné soubory pro zobrazení a stažení:

  ![Beats farmy projektu](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>Další kroky

Naučte se používat [rozhraní API](references-for-farmbeats.md#rest-api) centra dat FarmBeats k získání vašich pomocí dronů snímků.
