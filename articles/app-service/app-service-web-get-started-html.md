---
title: 'Úvodní příručka: Vytvoření statické webové aplikace HTML'
description: Nasaďte svůj první HTML Hello World do služby Azure App Service během několika minut. Nasazujete pomocí Gitu, což je jeden z mnoha způsobů nasazení do služby App Service.
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 159b38962fe91cedfc8d313bef943dbc74e9974e
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520256"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Vytvoření webové aplikace ve statickém HTML ve službě Azure

[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů. Tento rychlý start ukazuje, jak nasadit základní web HTML+CSS do služby Azure App Service. Tento rychlý start dokončíte v [prostředí Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), ale tyto příkazy můžete spustit také místně pomocí azure [CLI](/cli/azure/install-azure-cli).

![Domovská stránka ukázkové aplikace](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Stažení ukázky

Ve službě Cloud Shell vytvořte adresář rychlého startu a přejděte do něj.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Potom spusťte následující příkaz, pomocí kterého do tohoto adresáře naklonujete úložiště ukázkové aplikace.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

Přejděte do adresáře, který obsahuje vzorový kód a spusťte příkaz `az webapp up`.

V následujícím příkladu nahraďte <app_name > jedinečným názvem aplikace.

```bash
cd html-docs-hello-world
```

```azurecli
az webapp up --location westeurope --name <app_name> 
```

Příkaz `az webapp up` provádí tyto akce:

- Vytvoření výchozí skupiny prostředků

- Vytvoření výchozího plánu služby App Service

- Vytvoření aplikace se zadaným názvem

- [Nasazení souborů ZIP](https://docs.microsoft.com/azure/app-service/deploy-zip) z aktuálního pracovního adresáře do webové aplikace

Spuštění tohoto příkazu může trvat několik minut. Při spuštění příkaz zobrazí podobné informace jako v následujícím příkladu:

```json
{
  "app_url": "https://<app_name>.azurewebsites.net",
  "location": "westeurope",
  "name": "<app_name>",
  "os": "Windows",
  "resourcegroup": "appsvc_rg_Windows_westeurope",
  "serverfarm": "appsvc_asp_Windows_westeurope",
  "sku": "FREE",
  "src_path": "/home/<username>/quickstart/html-docs-hello-world ",
  < JSON data removed for brevity. >
}
```

Poznamenejte si hodnotu `resourceGroup`. Budete ji potřebovat v části [Vyčištění prostředků](#clean-up-resources).

## <a name="browse-to-the-app"></a>Přechod do aplikace

V prohlížeči přejděte na `http://<app_name>.azurewebsites.net`adresu URL aplikace: .

Stránka je spuštěná jako webová aplikace služby Azure App Service.

![Domovská stránka ukázkové aplikace](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

**Blahopřejeme!** Nasadili jste svoji první aplikaci v HTML do služby App Service.

## <a name="update-and-redeploy-the-app"></a>Aktualizace a opětovné nasazení aplikace

Ve službě Cloud Shell otevřete textový editor nano zadáním příkazu `nano index.html`. Ve značce nadpisu `<h1>` změňte text z „Azure App Service - Sample Static HTML Site“ na „Azure App Service“, jak je znázorněno níže.

![Nano index.html](media/app-service-web-get-started-html/nano-index-html.png)

Uložte změny a editor nano zavřete. K uložení použijte příkaz `^O` a k zavření příkaz `^X`.

Teď aplikaci znovu nasadíte pomocí stejného příkazu `az webapp up`.

```azurecli
az webapp up --location westeurope --name <app_name> --html
```

Po dokončení nasazení se vraťte do okna prohlížeče, které se otevřelo v kroku **Přechod do aplikace**, a aktualizujte zobrazení stránky.

![Domovská stránka aktualizované ukázkové aplikace](media/app-service-web-get-started-html/hello-azure-in-browser-az.png)

## <a name="manage-your-new-azure-app"></a>Správa nové aplikace Azure

Pokud chcete spravovat webovou aplikaci, kterou jste [vytvořili,](https://portal.azure.com)vyhledejte a vyberte **App Services**. 

![Výběr služby App Services na webu Azure Portal](./media/app-service-web-get-started-html/portal0.png)

Na stránce **App Services** vyberte název aplikace Azure.

![Přechod do aplikace Azure na portálu](./media/app-service-web-get-started-html/portal1.png)

Zobrazí se stránka s přehledem vaší webové aplikace. Tady můžete provádět základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění.

![Okno App Service na webu Azure Portal](./media/app-service-web-get-started-html/portal2.png)

Levá nabídka obsahuje odkazy na různé stránky pro konfiguraci vaší aplikace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste vytvořili prostředky Azure ve skupině prostředků. Pokud předpokládáte, že už tyto prostředky nebudete potřebovat, odstraňte skupinu prostředků spuštěním následujícího příkazu ve službě Cloud Shell: Mějte na paměti, že název skupiny prostředků se pro vás automaticky vygeneroval v kroku [Vytvoření webové aplikace](#create-a-web-app).

```azurecli
az group delete --name appsvc_rg_Windows_westeurope
```

Spuštění tohoto příkazu může trvat přibližně minut.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Mapování vlastní domény](app-service-web-tutorial-custom-domain.md)
