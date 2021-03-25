---
title: 'Kurz: publikování statického Web Apps Azure pomocí Azure DevOps'
description: Naučte se používat Azure DevOps k publikování statických Web Apps Azure.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: 4745cf9d60b58e01beb29a640b1282265c23b13c
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105051101"
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
   
2. Použijte existující úložiště nebo _importujte úložiště_ , jak je znázorněno níže.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="Úložiště DevOps":::

3. Vytvořte nový soubor pro webovou aplikaci front-end.
   
4. Zkopírujte a vložte následující kód HTML do nového souboru:

  ```HTML
  <!DOCTYPE html>
  <html lang="en">

  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="styles.css">
    <title>Hello World!</title>
  </head>

  <body>
    <main>
      <h1>Hello World!</h1>
    </main>
  </body>

  </html>
  ```

5. Soubor uložte.

## <a name="create-a-static-web-app"></a>Vytvoření statické webové aplikace

1. Přejděte na [Azure Portal](https://portal.azure.com).
   
2. Vyberte **Vytvořit prostředek**.
   
3. Vyhledejte **statické Web Apps**.
   
4. Vyberte možnost **statické Web Apps (Preview)**.
   
5. Vyberte **Vytvořit**.
   
6. V části _Podrobnosti nasazení_ se ujistěte, že jste vybrali možnost **jiné**. To vám umožní používat kód v úložišti Azure DevOps.
  > [!NOTE]
  > Funkce pro výběr _jiných_ funkcí je aktuálně vystaralá a ve všech předplatných Azure ještě nemusí být k dispozici.

  :::image type="content" source="media/publish-devops/create-resource.png" alt-text="Podrobnosti o nasazení – ostatní":::

7. Po úspěšném nasazení vyberte **Spravovat token nasazení**.

8. Zkopírujte **token nasazení** a vložte ho do textového editoru pro použití na jiné obrazovce.

  > [!NOTE]
  > Tato hodnota je pro tuto chvíli nastavená, protože do následujících kroků zkopírujete a vložíte další hodnoty.

  :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Token nasazení"::: 

## <a name="create-the-pipeline-task-in-azure-devops"></a>Vytvoření úlohy kanálu v Azure DevOps

1. Přejděte do projektu Azure DevOps, který jste vytvořili dříve.

2. Vytvořte nový **kanál sestavení** a vyberte **nastavit sestavení**.

  :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Kanál buildu"::: 

3. Zkopírujte a vložte následující YAML do svého kanálu.
> [!NOTE]
> Hodnoty zadané pro _app_location_, _api_location_ a _output_location_ se pro vaši aplikaci musí upravit.  

```YAML
trigger:
  - main

pool:
  vmImage: ubuntu-latest

steps:
  - task: AzureStaticWebApp@0
    inputs:
      app_location: frontend 
      api_location: api
      output_location: build
    env:
      azure_static_web_apps_api_token: $(deployment_token)
   ```
Nakonfigurujte vstupy _statických webových aplikací Azure_ podle struktury složek vaší aplikace.

| Vlastnost | Popis | Povinné |
|---|---|---|
| `app_location` | Umístění kódu aplikace<br><br>Například zadejte, `/` Pokud je zdrojový kód vaší aplikace v kořenu úložiště, nebo `/app` Pokud je kód aplikace v adresáři s názvem `app` . | Yes |
| `api_location` | Umístění kódu Azure Functions.<br><br>Zadejte například, `/api` Pokud je kód vaší aplikace ve složce s názvem `api` . Pokud ve složce není zjištěna žádná Azure Functions aplikace, sestavení neselže, pracovní postup předpokládá, že nechcete rozhraní API. | No |
| `output_location` | Umístění výstupního adresáře sestavení vzhledem k `app_location` .<br><br>Například pokud je zdrojový kód aplikace umístěn na `/app` , a skript sestavení výstupuje soubory do `/app/build` složky a pak nastaví `build` jako `output_location` hodnotu. | No |

`azure_static_web_apps_api_token`Hodnota je samostatně spravovaná a je nakonfigurována ručně.

4. Vyberte **proměnné**.

5. Vytvoří novou proměnnou.

6. Název proměnné **deployment_token** (bude odpovídat názvu v pracovním postupu).

7. Zkopírujte token nasazení, který jste předtím vložili do textového editoru.

8. Vložte do pole _hodnota_ token nasazení.

  :::image type="content" source="media/publish-devops/variable-token.png" alt-text="Variabilní token":::

9. Vyberte **OK**.

10. Vyberte **Uložit a spusťte** kanál.

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="Kanál":::
   
11. Po úspěšném nasazení přejděte do **přehledu** služby Azure static Web Apps, který obsahuje odkazy na konfiguraci nasazení.

12. Vyberte **adresu URL** pro zobrazení nově nasazeného webu. Všimněte si, jak odkaz _zdroj_ teď odkazuje na větev a umístění úložiště Azure DevOps.
   
    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="Umístění nasazení":::


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Konfigurace statického Web Apps Azure](./configuration.md)
