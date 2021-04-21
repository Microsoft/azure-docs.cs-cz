---
title: 'Rychlý Start: Vytvoření webové aplikace ve statickém HTML'
description: Nasaďte první Hello World HTML do Azure App Service v řádu minut. Nasadíte pomocí Gitu, což je jedním z mnoha způsobů, jak nasadit do App Service.
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 603d8e642cd2e88beec6ae34094a2c6c43d179ee
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768894"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Vytvoření webové aplikace ve statickém HTML ve službě Azure

V tomto rychlém startu se dozvíte, jak nasadit základní web HTML + CSS do <abbr title="Služba založená na protokolu HTTP pro hostování webových aplikací, rozhraní REST API a mobilní back-endové aplikace.">Azure App Service</abbr>. Tento rychlý Start dokončíte v [Cloud Shell](../cloud-shell/overview.md), ale tyto příkazy můžete spustit i místně pomocí [Azure CLI](/cli/azure/install-azure-cli).

## <a name="1-prepare-your-environment"></a>1. Příprava prostředí

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

V [Cloud Shell](../cloud-shell/overview.md)vytvořte adresář pro rychlý Start a pak na něj přejděte.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Potom spusťte následující příkaz, pomocí kterého do tohoto adresáře naklonujete úložiště ukázkové aplikace.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```
<hr/>

## <a name="2-create-a-web-app"></a>2. vytvoření webové aplikace

Přejděte do adresáře, který obsahuje vzorový kód a spusťte příkaz `az webapp up`. **Nahradit** `<app-name>` globálně jedinečný název.

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name> --html
```

<details>
<summary>Řešení potíží</summary>
<ul>
<li>Pokud <code>az</code> příkaz není rozpoznaný, ujistěte se, že máte nainstalované rozhraní příkazového řádku Azure, jak je popsané v tématu <a href="#1-prepare-your-environment">Příprava prostředí</a>.</li>
<li>Nahraďte <code>&lt;app-name&gt;</code> názvem, který je jedinečný v rámci všech Azure ( <em> platné znaky jsou <code>a-z</code> , <code>0-9</code> a <code>-</code> </em> ). Dobrým vzorem je použití kombinace názvu vaší společnosti a identifikátoru aplikace.</li>
<li><code>--sku F1</code>Argument vytvoří webovou aplikaci na cenové úrovni Free. Vynechejte tento argument pro použití rychlejší úrovně Premium, což stojí za hodinu.</li>
<li><code>--html</code>Argument říká, že má být obsah složky považován za statický obsah a zakáže automatizaci sestavení.</li>
<li>Volitelně můžete zahrnout argument <code>--location &lt;location-name&gt;</code> , kde <code>&lt;location-name&gt;</code> je dostupná oblast Azure. Seznam povolených oblastí pro váš účet Azure můžete načíst spuštěním <a href="/cli/azure/appservice#az_appservice_list_locations"> <code>az account list-locations</code> </a> příkazu.</li>
</ul>
</details>

Dokončení příkazu může trvat několik minut. 

<details>
<summary>Co dělat <code>az webapp up</code> ?</summary>
<p>Příkaz <code>az webapp up</code> provádí tyto akce:</p>
<ul>
<li>Vytvoření výchozí skupiny prostředků</li>
<li>Vytvořte výchozí plán App Service.</li>
<li><a href="/cli/azure/webapp#az_webapp_create">Vytvoří aplikaci App Service</a> se zadaným názvem.</li>
<li>Soubory <a href="/azure/app-service/deploy-zip">zip nasadí</a> z aktuálního pracovního adresáře do aplikace.</li>
<li>Při spuštění poskytuje zpráva o vytváření prostředků, protokolování a nasazení souboru ZIP.</li>
</ul>

Až se dokončí, zobrazí se podobné informace jako v následujícím příkladu:

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

</details>

Budete potřebovat `resourceGroup` hodnotu pro [vyčištění prostředků](#6-clean-up-resources) později.

<hr/>

## <a name="3-browse-to-the-app"></a>3. Přejděte do aplikace.

V prohlížeči přejdete na adresu URL aplikace: `http://<app_name>.azurewebsites.net` .

Stránka je spuštěná jako webová aplikace služby Azure App Service.

![Domovská stránka ukázkové aplikace](media/quickstart-html/hello-world-in-browser-az.png)

<hr/>

## <a name="4-update-and-redeploy-the-app"></a>4. aktualizujte a znovu nasaďte aplikaci.

Do Cloud Shell **Zadejte** `nano index.html` a otevřete textový editor nano. 

V `<h1>` tagu nadpisu změňte "Azure App Service-Sample static HTML web" na "Azure App Service".

![Nano index.html](media/quickstart-html/nano-index-html.png)

Změny **uložte** pomocí příkazu `^O` .

**Ukončete** nano pomocí příkazu `^X` .

Znovu nasaďte aplikaci pomocí `az webapp up` příkazu.

```bash
az webapp up --html
```

Přepněte zpátky do okna prohlížeče, které se otevřelo v kroku **přechod do aplikace** .

**Aktualizujte** stránku.

![Domovská stránka aktualizované ukázkové aplikace](media/quickstart-html/hello-azure-in-browser-az.png)

<hr/>

## <a name="5-manage-your-new-azure-app"></a>5. Správa nové aplikace Azure

**Přejděte** na [Azure Portal](https://portal.azure.com). 

**Vyhledejte** a **Vyberte** **App Services**.

![Vyberte App Services v Azure Portal](./media/quickstart-html/portal0.png)

**Vyberte** název aplikace Azure.

![Přechod do aplikace Azure na portálu](./media/quickstart-html/portal1.png)

Zobrazí se stránka s přehledem vaší webové aplikace. Tady můžete provádět základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění.

![Okno App Service na webu Azure Portal](./media/quickstart-html/portal2.png)

Levá nabídka obsahuje odkazy na různé stránky pro konfiguraci vaší aplikace.

<hr/>

## <a name="6-clean-up-resources"></a>6. vyčištění prostředků

V předchozích krocích jste vytvořili prostředky Azure ve skupině prostředků. Pokud předpokládáte, že už tyto prostředky nebudete potřebovat, odstraňte skupinu prostředků spuštěním následujícího příkazu ve službě Cloud Shell: Mějte na paměti, že název skupiny prostředků se pro vás automaticky vygeneroval v kroku [Vytvoření webové aplikace](#2-create-a-web-app).

```bash
az group delete --name appsvc_rg_Windows_westeurope
```

Spuštění tohoto příkazu může trvat přibližně minut.

<hr/>

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Mapování vlastní domény](app-service-web-tutorial-custom-domain-uiex.md)
