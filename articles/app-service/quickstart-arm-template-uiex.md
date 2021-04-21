---
title: Vytvoření aplikace App Service pomocí šablony Azure Resource Manager
description: Vytvořte svou první aplikaci pro Azure App Service v řádu sekund pomocí Azure Resource Manager šablony (šablony ARM), což je jedním z mnoha způsobů, jak nasadit do App Service.
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 10/16/2020
ms.custom: subject-armqs, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: bce6bfb61eb59d1fa66c550a133ac8b6f8d7f2c5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768997"
---
# <a name="quickstart-create-app-service-app-using-an-arm-template"></a>Rychlý Start: Vytvoření aplikace App Service pomocí šablony ARM

Začněte s [Azure App Service](overview.md) nasazením aplikace do cloudu pomocí <abbr title="Soubor JSON, který deklarativně definuje jeden nebo víc prostředků a závislostí Azure mezi nasazenými prostředky. Šablony lze použít k nasazení prostředků konzistentně a opakovaně.">Šablona ARM</abbr> a [Azure CLI](/cli/azure/get-started-with-azure-cli) v Cloud Shell. Vzhledem k tomu, že používáte bezplatnou App Service úroveň, nebudete mít k dokončení tohoto rychlého startu žádné náklady. <abbr title="V deklarativní syntaxi popíšete zamýšlené nasazení, aniž byste museli psát sekvenci programovacích příkazů pro vytvoření tohoto nasazení.">Tato šablona používá deklarativní syntaxi.</abbr>

 Pokud vaše prostředí splňuje požadavky a Vy jste obeznámeni s používáním [šablon ARM](../azure-resource-manager/templates/overview.md), vyberte tlačítko **nasadit do Azure** . Šablona se otevře v prostředí Azure Portal.

::: zone pivot="platform-windows"
[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-windows%2Fazuredeploy.json)
::: zone-end

::: zone pivot="platform-linux"
[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-linux%2Fazuredeploy.json)
::: zone-end

<hr/>

## <a name="1-prepare-your-environment"></a>1. Příprava prostředí

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<hr/>

## <a name="2-review-the-template"></a>2. Zkontrolujte šablonu.

::: zone pivot="platform-windows"
Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-app-service-docs-windows). Nasadí App Service plán a App Service aplikace ve Windows.

:::code language="json" source="~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json":::

<details>
<summary>Jaké prostředky a parametry jsou definované v šabloně?</summary>

V šabloně jsou definované dva prostředky Azure:

* [**Microsoft. Web/serverových farem**](/azure/templates/microsoft.web/serverfarms): vytvořte plán App Service.
* [**Microsoft. Web/weby**](/azure/templates/microsoft.web/sites): vytvoření aplikace App Service.

Následující tabulka popisuje parametry výchozích hodnot a jejich popis:

| Parametry | Typ    | Výchozí hodnota                | Description |
|------------|---------|------------------------------|-------------|
| webAppName | řetězec  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Název aplikace |
| location   | řetězec  | "[[resourceName (). Location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Oblast aplikace |
| skladové        | řetězec  | Kláves                         | Velikost instance (F1 = Free úrovně) |
| language   | řetězec  | platformy                       | Sada programovacích jazyků (.NET, php, Node, HTML) |
| Hell | boolean | Ne                        | True = nasadit aplikaci Hello World |
| relijte    | řetězec  | " "                          | Externí úložiště Git (volitelné) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-app-service-docs-linux). Nasadí App Service plán a App Service aplikace ve Windows.

:::code language="json" source="~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json":::

Tato šablona zahrnuje prostředky a parametry Azure, které jsou definované pro vaše pohodlí.

<details>
<summary>Jaké prostředky a parametry jsou definované v šabloně?</summary>

V šabloně jsou definované dva prostředky Azure:

* [**Microsoft. Web/serverových farem**](/azure/templates/microsoft.web/serverfarms): vytvořte plán App Service.
* [**Microsoft. Web/weby**](/azure/templates/microsoft.web/sites): vytvoření aplikace App Service.

Následující tabulka popisuje parametry výchozích hodnot a jejich popis:

| Parametry | Typ    | Výchozí hodnota                | Description |
|------------|---------|------------------------------|-------------|
| webAppName | řetězec  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Název aplikace |
| location   | řetězec  | "[[resourceName (). Location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Oblast aplikace |
| skladové        | řetězec  | Kláves                         | Velikost instance (F1 = Free úrovně) |
| linuxFxVersion   | řetězec  | "DOTNETCORE&#124;3,0        | "&#124; verze zásobníku programovacího jazyka" |
| relijte    | řetězec  | " "                          | Externí úložiště Git (volitelné) |

---

</details>

::: zone-end

<hr/>

## <a name="3-deploy-the-template"></a>3. nasazení šablony

::: zone pivot="platform-windows"
Spusťte následující kód pro nasazení aplikace rozhraní .NET Framework v systému Windows pomocí rozhraní příkazového řádku Azure CLI. 

Nahrazení <abbr title="Platné znaky jsou znaky `a-z` , `0-9` a `-` .">`<app-name>`</abbr> s globálně jedinečným názvem aplikace. Další informace <abbr title="Můžete také použít Azure Portal, Azure PowerShell a REST API.">metody nasazení</abbr>najdete v tématu [Nasazení šablon](../azure-resource-manager/templates/deploy-powershell.md). Další [Azure App Service ukázek šablon najdete tady](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites).

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" helloWorld="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-windows/azuredeploy.json"
```
::: zone-end
::: zone pivot="platform-linux"
Pro vytvoření aplikace v Pythonu na platformě Linux spusťte následující kód. 

Nahrazení <abbr title="Platné znaky jsou znaky `a-z` , `0-9` a `-` .">`<app-name>`</abbr> s globálně jedinečným názvem aplikace.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-linux/azuredeploy.json"
```
::: zone-end

<details>
<summary>Jaký je kód?</summary>
<p>Příkazy provedou následující akce:</p>
<ul>
<li>Vytvořit výchozí <abbr title="Logický kontejner pro související prostředky Azure, které můžete spravovat jako jednotku.">skupina prostředků</abbr>.</li>
<li>Vytvořit výchozí <abbr title="Plán, který určuje umístění, velikost a funkce webové serverové farmy, která je hostitelem vaší aplikace.">Plán služby App Service</abbr>.</li>
<li><a href="/cli/azure/webapp#az_webapp_create">Vytvořit <abbr title="Reprezentace vaší webové aplikace, která obsahuje kód vaší aplikace, názvy hostitelů DNS, certifikáty a související prostředky. "> Aplikace App Service</abbr></a> se zadaným názvem.</li>
</ul>
</details>

::: zone pivot="platform-windows"
<details>
<summary>Návody nasadit jinou sadu jazyků?</summary>
Pokud chcete nasadit jiný jazykový zásobník, aktualizujte <abbr title="Tato šablona je kompatibilní s aplikacemi .NET Core, .NET Framework, PHP, Node.js a statickými HTML. "> parametr jazyka</abbr> s příslušnými hodnotami. Informace pro Java najdete v tématu <a href="/azure/app-service/quickstart-java-uiex">Vytvoření aplikace Java</a>.

| Parametry | Typ    | Výchozí hodnota                | Description |
|------------|---------|------------------------------|-------------|
| language   | řetězec  | platformy                       | Sada programovacích jazyků (.NET, php, Node, HTML) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
<details>
<summary>Návody nasadit jinou sadu jazyků?</summary>
Pokud chcete nasadit jinou sadu jazyků, aktualizujte je `linuxFxVersion` pomocí příslušných hodnot. Níže jsou uvedené ukázky. Chcete-li zobrazit aktuální verze, spusťte následující příkaz v Cloud Shell: `az webapp config show --resource-group myResourceGroup --name <app-name> --query linuxFxVersion`

| Jazyk    | Příklad                                              |
|-------------|------------------------------------------------------|
| **.NET**    | linuxFxVersion = "DOTNETCORE&#124;3,0"                 |
| **PHP**     | linuxFxVersion = "PHP&#124;7,4"                        |
| **Node.js** | linuxFxVersion = "NODE&#124;10,15"                     |
| **Java**    | linuxFxVersion = "JAVA&#124;1,8 &#124;TOMCAT&#124;9,0" |
| **Python**  | linuxFxVersion = "PYTHON&#124;3,7"                     |
| **Ruby**    | linuxFxVersion = "RUBY&#124;2,6"                       |

---

</details>
::: zone-end

<hr/>

## <a name="4-validate-the-deployment"></a>4. Ověření nasazení

Vyhledejte `http://<app_name>.azurewebsites.net/` a ověřte, zda byl vytvořen.

<hr/>

## <a name="5-clean-up-resources"></a>5. vyčištění prostředků

Pokud už je nepotřebujete, [odstraňte skupinu prostředků](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group).

<hr/>

## <a name="next-steps"></a>Další kroky

- [Nasazení z místního Gitu](deploy-local-git.md)
- [ASP.NET Core s SQL Database](tutorial-dotnetcore-sqldb-app.md)
- [Python s Postgresem](tutorial-python-postgresql-app.md)
- [PHP s MySQL](tutorial-php-mysql-app.md)
- [Připojení ke službě Azure SQL Database pomocí jazyka Java](../azure-sql/database/connect-query-java.md?toc=%2fazure%2fjava%2ftoc.json)
- [Mapování vlastní domény](app-service-web-tutorial-custom-domain-uiex.md)
