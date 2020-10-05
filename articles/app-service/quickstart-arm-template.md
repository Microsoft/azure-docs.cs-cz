---
title: Vytvoření aplikace App Service pomocí šablony Azure Resource Manager
description: Vytvořte svou první aplikaci pro Azure App Service v řádu sekund pomocí Azure Resource Manager šablony, což je jedním z mnoha způsobů, jak nasadit do App Service.
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 05/25/2020
ms.custom: subject-armqs
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: e577616e0976ca050a55c8524e68129545ed1912
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89653568"
---
# <a name="create-app-service-app-using-an-azure-resource-manager-template"></a>Vytvoření aplikace App Service pomocí šablony Azure Resource Manager

Začněte s [Azure App Service](overview.md) nasazením aplikace do cloudu pomocí Azure Resource Manager šablony a [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) v Cloud Shell. Vzhledem k tomu, že používáte bezplatnou App Service úroveň, nebudete mít k dokončení tohoto rychlého startu žádné náklady.

 [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-azure-app-service-app"></a>Vytvoření aplikace Azure App Service

### <a name="review-the-template"></a>Kontrola šablony

::: zone pivot="platform-windows"
Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://github.com/Azure/azure-quickstart-templates/). Nasadí App Service plán a App Service aplikace ve Windows. Je kompatibilní s aplikacemi pro .NET Core, .NET Framework, PHP, Node.js a statickými HTML. Informace pro Java najdete v tématu [Vytvoření aplikace Java](app-service-web-get-started-java.md). 

[!code-json[<Azure Resource Manager template App Service Windows app>](~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json)]

V šabloně jsou definované dva prostředky Azure:

* [**Microsoft. Web/serverových farem**](/azure/templates/microsoft.web/serverfarms): vytvořte plán App Service.
* [**Microsoft. Web/weby**](/azure/templates/microsoft.web/sites): vytvoření aplikace App Service.

Tato šablona obsahuje několik parametrů, které jsou předdefinované pro vaše pohodlí. V následující tabulce najdete výchozí hodnoty parametrů a jejich popis:

| Parametry | Typ    | Výchozí hodnota                | Popis |
|------------|---------|------------------------------|-------------|
| webAppName | řetězec  | "webApp- **[`<uniqueString>`](/azure/azure-resource-manager/templates/template-functions-string#uniquestring)** " | Název aplikace |
| location   | řetězec  | "[[resourceName (). Location](/azure/azure-resource-manager/templates/template-functions-resource#resourcegroup)]" | Oblast aplikace |
| skladové        | řetězec  | Kláves                         | Velikost instance (F1 = Free úrovně) |
| language   | řetězec  | platformy                       | Sada programovacích jazyků (.NET, php, Node, HTML) |
| Hell | boolean | Nepravda                        | True = nasadit aplikaci Hello World |
| relijte    | řetězec  | " "                          | Externí úložiště Git (volitelné) |
::: zone-end
::: zone pivot="platform-linux"
Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://github.com/Azure/azure-quickstart-templates/). Nasadí App Service plán a App Service aplikaci v systému Linux. Je kompatibilní se všemi podporovanými programovacími jazyky na App Service.

[!code-json[<Azure Resource Manager template App Service Linux app>](~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json)]

V šabloně jsou definované dva prostředky Azure:

* [**Microsoft. Web/serverových farem**](/azure/templates/microsoft.web/serverfarms): vytvořte plán App Service.
* [**Microsoft. Web/weby**](/azure/templates/microsoft.web/sites): vytvoření aplikace App Service.

Tato šablona obsahuje několik parametrů, které jsou předdefinované pro vaše pohodlí. V následující tabulce najdete výchozí hodnoty parametrů a jejich popis:

| Parametry | Typ    | Výchozí hodnota                | Popis |
|------------|---------|------------------------------|-------------|
| webAppName | řetězec  | "webApp- **[`<uniqueString>`](/azure/azure-resource-manager/templates/template-functions-string#uniquestring)** " | Název aplikace |
| location   | řetězec  | "[[resourceName (). Location](/azure/azure-resource-manager/templates/template-functions-resource#resourcegroup)]" | Oblast aplikace |
| skladové        | řetězec  | Kláves                         | Velikost instance (F1 = Free úrovně) |
| linuxFxVersion   | řetězec  | "DOTNETCORE&#124;3,0        | "&#124; verze zásobníku programovacího jazyka" |
| relijte    | řetězec  | " "                          | Externí úložiště Git (volitelné) |

---
::: zone-end


### <a name="deploy-the-template"></a>Nasazení šablony

Azure CLI se tady používá k nasazení šablony. Můžete také použít Azure Portal, Azure PowerShell a REST API. Další informace o dalších metodách nasazení najdete v tématu [Nasazení šablon](../azure-resource-manager/templates/deploy-powershell.md). 

Následující kód vytvoří skupinu prostředků, plán App Service a webovou aplikaci. Pro vás byla nastavena výchozí Skupina prostředků, plán App Service a umístění. Nahraďte `<app-name>` globálně jedinečným názvem aplikace (platné znaky jsou `a-z` , `0-9` a `-` ).

::: zone pivot="platform-windows"
Spusťte následující kód pro nasazení aplikace rozhraní .NET Framework v systému Windows.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" sample="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-windows/azuredeploy.json"
::: zone-end
::: zone pivot="platform-linux"
Run the code below to create a Python app on Linux. 

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-linux/azuredeploy.json"
```

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
::: zone-end

> [!NOTE]
> Další [Azure App Service ukázek šablon najdete tady](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites).


## <a name="validate-the-deployment"></a>Ověření nasazení

Vyhledejte `http://<app_name>.azurewebsites.net/` a ověřte, zda byl vytvořen.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, [odstraňte skupinu prostředků](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nasazení z místního Gitu](deploy-local-git.md)

> [!div class="nextstepaction"]
> [ASP.NET Core s SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Python s Postgresem](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [PHP s MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Připojení ke službě Azure SQL Database pomocí jazyka Java](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Mapování vlastní domény](app-service-web-tutorial-custom-domain.md)