---
title: 'Rychlý Start: Vytvoření webové aplikace ve statickém HTML'
description: Nasaďte první Hello World HTML do Azure App Service v řádu minut. Nasadíte pomocí Gitu, což je jedním z mnoha způsobů, jak nasadit do App Service.
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-html-uiex
ms.openlocfilehash: 1a179f30b4004eba105780ee73e25394e6a569a6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708981"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Vytvoření webové aplikace ve statickém HTML ve službě Azure

[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů. V tomto rychlém startu se dozvíte, jak nasadit základní web HTML + CSS do Azure App Service. Tento rychlý Start dokončíte v [Cloud Shell](../cloud-shell/overview.md), ale tyto příkazy můžete spustit i místně pomocí [Azure CLI](/cli/azure/install-azure-cli).

![Domovská stránka ukázkové aplikace](media/quickstart-html/hello-world-in-browser-az.png)

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

Přejděte do adresáře, který obsahuje vzorový kód a spusťte příkaz `az webapp up`. V následujícím příkladu nahraďte <app_name > jedinečným názvem aplikace. Statický obsah je označen `--html` příznakem.

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name> --html
```

Příkaz `az webapp up` provádí tyto akce:

- Vytvoření výchozí skupiny prostředků

- Vytvoření výchozího plánu služby App Service

- Vytvoření aplikace se zadaným názvem

- [Nasazení souborů ZIP](./deploy-zip.md) z aktuálního pracovního adresáře do webové aplikace

Spuštění tohoto příkazu může trvat několik minut. Při spuštění příkaz zobrazí podobné informace jako v následujícím příkladu:

```output
{
  "app_url": "https://&lt;app_name&gt;.azurewebsites.net",
  "location": "westeurope",
  "name": "&lt;app_name&gt;",
  "os": "Windows",
  "resourcegroup": "appsvc_rg_Windows_westeurope",
  "serverfarm": "appsvc_asp_Windows_westeurope",
  "sku": "FREE",
  "src_path": "/home/&lt;username&gt;/quickstart/html-docs-hello-world ",
  &lt; JSON data removed for brevity. &gt;
}
```

Poznamenejte si hodnotu `resourceGroup`. Budete ji potřebovat v části [Vyčištění prostředků](#clean-up-resources).

## <a name="browse-to-the-app"></a>Přechod do aplikace

V prohlížeči přejdete na adresu URL aplikace: `http://<app_name>.azurewebsites.net` .

Stránka je spuštěná jako webová aplikace služby Azure App Service.

![Domovská stránka ukázkové aplikace](media/quickstart-html/hello-world-in-browser-az.png)

**Blahopřejeme!** Nasadili jste svoji první aplikaci v HTML do služby App Service.

## <a name="update-and-redeploy-the-app"></a>Aktualizace a opětovné nasazení aplikace

Ve službě Cloud Shell otevřete textový editor nano zadáním příkazu `nano index.html`. Ve značce nadpisu `<h1>` změňte text z „Azure App Service - Sample Static HTML Site“ na „Azure App Service“, jak je znázorněno níže.

![Nano index.html](media/quickstart-html/nano-index-html.png)

Uložte změny a editor nano zavřete. K uložení použijte příkaz `^O` a k zavření příkaz `^X`.

Teď aplikaci znovu nasadíte pomocí stejného příkazu `az webapp up`.

```bash
az webapp up --location westeurope --name <app_name> --html
```

Po dokončení nasazení se vraťte do okna prohlížeče, které se otevřelo v kroku **Přechod do aplikace**, a aktualizujte zobrazení stránky.

![Domovská stránka aktualizované ukázkové aplikace](media/quickstart-html/hello-azure-in-browser-az.png)

## <a name="manage-your-new-azure-app"></a>Správa nové aplikace Azure

Pokud chcete spravovat webovou aplikaci, kterou jste vytvořili, v [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **App Services**. 

![Vyberte App Services v Azure Portal](./media/quickstart-html/portal0.png)

Na stránce **App Services** vyberte název aplikace Azure.

![Přechod do aplikace Azure na portálu](./media/quickstart-html/portal1.png)

Zobrazí se stránka s přehledem vaší webové aplikace. Tady můžete provádět základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění.

![Okno App Service na webu Azure Portal](./media/quickstart-html/portal2.png)

Levá nabídka obsahuje odkazy na různé stránky pro konfiguraci vaší aplikace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste vytvořili prostředky Azure ve skupině prostředků. Pokud předpokládáte, že už tyto prostředky nebudete potřebovat, odstraňte skupinu prostředků spuštěním následujícího příkazu ve službě Cloud Shell: Mějte na paměti, že název skupiny prostředků se pro vás automaticky vygeneroval v kroku [Vytvoření webové aplikace](#create-a-web-app).

```bash
az group delete --name appsvc_rg_Windows_westeurope
```

Spuštění tohoto příkazu může trvat přibližně minut.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Mapování vlastní domény](app-service-web-tutorial-custom-domain.md)
