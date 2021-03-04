---
title: 'Rychlý Start: Vytvoření webové aplikace Node.js – Windows'
description: Nasaďte první Node.js Hello World do Azure App Service pro platformu Windows v řádu minut.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 324919c1d085302e675e0bc9a370ce35f52a0f1d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109168"
---
<!-- advanced for windows -->

## <a name="3-deploy-to-azure-app-service-from-visual-studio-code"></a>3. nasazení do Azure App Service z Visual Studio Code

1. Otevřete složku aplikace v Visual Studio Code.

    ```bash
    code .
    ```

1. V Průzkumníku **služby Azure App Service** vyberte **Přihlásit se k Azure...** a postupujte podle pokynů. Po přihlášení by měl Průzkumník Zobrazit název vašeho předplatného Azure.

    ![Přihlášení k Azure](../media/quickstart-nodejs/sign-in.png)

    <details>
    <summary>Řešení potíží s přihlášením do Azure</summary>
    
    Pokud se vám při přihlašování k Azure zobrazí chyba **"nelze najít předplatné s názvem [ID předplatného]"** , může to být způsobeno tím, že jste za proxy serverem a nemůžete získat přístup k rozhraním API Azure. `HTTP_PROXY` `HTTPS_PROXY` Pomocí použijte konfiguraci a proměnné prostředí s informacemi o proxy serveru v terminálu `export` .
    
    ```bash
    export HTTPS_PROXY=https://username:password@proxy:8080
    export HTTP_PROXY=http://username:password@proxy:8080
    ```

    [Nahlášení problému](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)


1. V Průzkumníku **Azure App Service** vyberte ikonu modré šipky nahoru a nasaďte aplikaci do Azure. 

    :::image type="content" source="../media/quickstart-nodejs/deploy.png" alt-text="Snímek obrazovky služby Azure App Service v VS Code zobrazující vybranou modrou ikonu šipky":::

1. Vyberte adresář, který aktuálně máte otevřený `nodejs-docs-hello-world` .

1. Zvolit **vytvořit novou webovou aplikaci... Rozšířené**, pro nasazení na App Service ve Windows.

1. Zadejte globálně jedinečný <abbr title="Platnými znaky pro název aplikace jsou "a-z", "0-9" a "-".">name</abbr> pro webovou aplikaci a stiskněte klávesu **ENTER**. 
1. Vyberte **vytvořit novou skupinu prostředků** a potom zadejte název skupiny prostředků, například `AppServiceQS-rg` .
1. Vyberte **Node.js verzi**, doporučujeme LTS.

    Kanál oznámení zobrazuje prostředky Azure, které se vytvářejí pro vaši aplikaci.
1. Pro operační systém vyberte **systém Windows** .
1. Vyberte **vytvořit nový App Service plán**, zadejte název plánu (například `AppServiceQS-plan` ) a pak pro cenovou úroveň vyberte **F1 zdarma** .
1. Po zobrazení výzvy k Application Insights **Nyní vyberte Přeskočit** .
1. Vyberte oblast poblíž nebo poblíž zdrojů, ke kterým chcete získat přístup.

1. Po zobrazení výzvy k aktualizaci pracovního prostoru klikněte na **Ano** , aby se v pozdějších nasazeních automaticky nacílena stejná App Service webová aplikace. 

    :::image type="content" source="../media/quickstart-nodejs/save-configuration.png" alt-text="Snímek obrazovky s výzvou, aby se pracovní prostor aktualizoval vybraným tlačítkem Ano":::

1. Klikněte pravým tlačítkem na uzel pro službu App Service a vyberte **nasadit do webové aplikace**.

1. Klikněte pravým tlačítkem na uzel pro službu App Service a vyberte **Procházet web**.

    [Nahlášení problému](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

1. Až se nasazení dokončí, vyberte **Procházet web** v příkazovém řádku a zobrazte svou čerstvou nasazenou webovou aplikaci.

<br/>
<details>
<summary><strong>Řešení potíží</strong></summary>

Pokud nemůžete dokončit tento postup, ověřte následující:

* Ujistěte se, že aplikace naslouchá na portu poskytnutém proměnnou prostředí portu: `process.env.PORT` .

* Pokud se zobrazí chyba **"nemáte oprávnění k zobrazení tohoto adresáře nebo stránky."**, aplikace se pravděpodobně nespustila správně. Zkontrolujte výstup protokolu a vyhledejte a opravte chybu. 

</details>

<br>

[Nahlášení problému](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br/>
<hr/>
