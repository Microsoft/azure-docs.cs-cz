---
title: 'Kurz: publikování statického Web Apps Azure pomocí Azure DevOps'
description: Naučte se používat Azure DevOps k publikování statických Web Apps Azure.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: 472cf7b69078b3247c393ff65139bc29e5683a32
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639377"
---
# <a name="tutorial-publish-azure-static-web-apps-with-azure-devops"></a>Kurz: publikování statického Web Apps Azure pomocí Azure DevOps

Tento článek ukazuje, jak nasadit do služby [Azure Static Web Apps](./overview.md) pomocí [Azure DevOps](https://dev.azure.com/).

V tomto kurzu se naučíte:

- Nastavení webu Azure static Web Apps
- Vytvoření kanálu Azure DevOps pro sestavování a publikování statické webové aplikace

## <a name="prerequisites"></a>Požadavky

- **Aktivní účet Azure:** Pokud ho nemáte, můžete si [účet zdarma vytvořit](https://azure.microsoft.com/free/).
- **Projekt Azure DevOps:** Pokud ho ještě nemáte, můžete si [projekt vytvořit zdarma](https://azure.microsoft.com/pricing/details/devops/azure-devops-services/).
- **Kanál Azure DevOps:** Pokud potřebujete pomáhat s začátkem, přečtěte si téma [Vytvoření prvního kanálu](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline?view=azure-devops&preserve-view=true).

## <a name="create-a-static-web-app-in-an-azure-devops-repository"></a>Vytvoření statické webové aplikace v úložišti Azure DevOps

  > [!NOTE]
  > Pokud máte ve svém úložišti existující aplikaci, můžete přejít k další části.

1. Přejděte do úložiště Azure DevOps.

1. Vyberte **importovat** a začněte importovat ukázkovou aplikaci.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="Úložiště DevOps":::

1. Do **adresy URL klonu** zadejte `https://github.com/staticwebdev/vanilla-api.git` .

1. Vyberte **Importovat**.

## <a name="create-a-static-web-app"></a>Vytvoření statické webové aplikace

1. Přejděte na [Azure Portal](https://portal.azure.com).

1. Vyberte **Vytvořit prostředek**.

1. Vyhledejte **statické Web Apps**.

1. Vyberte možnost **statické Web Apps (Preview)**.

1. Vyberte **Vytvořit**.

1. V části _Podrobnosti nasazení_ se ujistěte, že jste vybrali možnost **jiné**. To vám umožní používat kód v úložišti Azure DevOps.

    :::image type="content" source="media/publish-devops/create-resource.png" alt-text="Podrobnosti o nasazení – ostatní":::

1. Po úspěšném nasazení přejděte k novému prostředku statického Web Apps.

1. Vyberte **Spravovat token nasazení**.

1. Zkopírujte **token nasazení** a vložte ho do textového editoru pro použití na jiné obrazovce.

    > [!NOTE]
    > Tato hodnota je pro tuto chvíli nastavená, protože do následujících kroků zkopírujete a vložíte další hodnoty.

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Token nasazení":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>Vytvoření úlohy kanálu v Azure DevOps

1. Přejděte do úložiště Azure DevOps, které jste vytvořili dříve.

1. Vyberte **nastavit sestavení**.

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Kanál buildu":::

1. Na obrazovce *konfigurace vašeho kanálu* vyberte **Počáteční kanál**.

    :::image type="content" source="media/publish-devops/configure-pipeline.png" alt-text="Konfigurace kanálu":::

1. Zkopírujte a vložte následující YAML do svého kanálu.

    ```yaml
    trigger:
      - main
    
    pool:
      vmImage: ubuntu-latest
    
    steps:
      - task: AzureStaticWebApp@0
        inputs:
          app_location: "/" 
          api_location: "api"
          output_location: ""
        env:
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    > [!NOTE]
    > Pokud ukázkovou aplikaci nepoužíváte, hodnoty pro `app_location` , a se `api_location` `output_location` musí změnit tak, aby odpovídaly hodnotám ve vaší aplikaci.

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    `azure_static_web_apps_api_token`Hodnota je samostatně spravovaná a je nakonfigurována ručně.

1. Vyberte **proměnné**.

1. Vytvoří novou proměnnou.

1. Název proměnné **deployment_token** (bude odpovídat názvu v pracovním postupu).

1. Zkopírujte token nasazení, který jste předtím vložili do textového editoru.

1. Vložte do pole _hodnota_ token nasazení.

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="Variabilní token":::

1. Vyberte možnost **zachovat tuto hodnotu tajnou**.

1. Vyberte **OK**.

1. Vyberte **Uložit** a vraťte se k YAML kanálu.

1. Vyberte **Uložit a spustit** , aby se otevřel dialog _Uložit a spustit_ .

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="Kanál":::

1. Vyberte **Uložit a spustit** pro spuštění kanálu.

1. Po úspěšném nasazení přejděte do **přehledu** služby Azure static Web Apps, který obsahuje odkazy na konfiguraci nasazení. Všimněte si, jak _zdrojový_ odkaz teď odkazuje na větev a umístění úložiště Azure DevOps.

1. Vyberte **adresu URL** pro zobrazení nově nasazeného webu.

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="Umístění nasazení":::

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Konfigurace statického Web Apps Azure](./configuration.md)
