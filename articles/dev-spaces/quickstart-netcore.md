---
title: Vytvoření vývojového prostředí Kubernetes v cloudu | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: ghogen
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: quickstart
description: Rychlý vývoj v prostředí Kubernetes s kontejnery a mikroslužbami v Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: 279b7a8c20717668c0ff4be541e9168e2d8706fd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361573"
---
# <a name="quickstart-create-a-kubernetes-development-environment-with-azure-dev-spaces-net-core-and-vs-code"></a>Rychlý start: Vytvoření vývojového prostředí Kubernetes pomocí služby Azure Dev Spaces (.NET Core a VS Code)


[!INCLUDE[](includes/learning-objectives.md)]

[!INCLUDE[](includes/see-troubleshooting.md)]

Teď můžete v Azure vytvořit vývojové prostředí založené na systému Kubernetes.

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="install-the-azure-cli"></a>Instalace rozhraní příkazového řádku Azure CLI
Služba Azure Dev Spaces vyžaduje minimální nastavení místního počítače. Většina konfigurace vývojového prostředí se ukládá do cloudu, aby bylo možné ji sdílet s ostatními uživateli. Nejdřív si stáhněte a spusťte [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). 

> [!IMPORTANT]
> Pokud už máte rozhraní příkazového řádku Azure nainstalované, ujistěte se, že používáte verzi 2.0.32 nebo vyšší.

[!INCLUDE[](includes/sign-into-azure.md)]

[!INCLUDE[](includes/use-dev-spaces.md)]

[!INCLUDE[](includes/install-vscode-extension.md)]

Při čekání na vytvoření clusteru můžete začít vyvíjet kód.

## <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core
Pokud máte nainstalovanou architekturu [.NET Core](https://www.microsoft.com/net), můžete rychle vytvořit webovou aplikaci ASP.NET Core ve složce s názvem `webfrontend`.

```cmd
   dotnet new mvc --name webfrontend
```

Nebo si **stáhněte vzorový kód z GitHubu**. Přejděte na https://github.com/Azure/dev-spaces a vyberte **Clone or Download** (Klonovat nebo stáhnout), abyste úložiště GitHub stáhli do svého místního prostředí. Kód tohoto průvodce je tady: `samples/dotnetcore/getting-started/webfrontend`.

[!INCLUDE[](includes/azds-prep.md)]

[!INCLUDE[](includes/build-run-k8s-cli.md)]

## <a name="update-a-content-file"></a>Aktualizace souboru obsahu
Azure Dev Spaces neslouží jenom ke spuštění kódu v prostředí Kubernetes. Umožňuje také rychle opakovaně prohlížet změny kódu, ke kterým dochází v prostředí Kubernetes v cloudu.

1. Najděte soubor `./Views/Home/Index.cshtml` a upravte ho v HTML. Můžete třeba změnit řádek 70, na kterém je `<h2>Application uses</h2>`, třeba takto: `<h2>Hello k8s in Azure!</h2>`
1. Uložte soubor. Za chvíli se v okně terminálu zobrazí zpráva o aktualizaci souboru ve spuštěném kontejneru.
1. Přejděte do prohlížeče a aktualizujte stránku. Na webové stránce by se měl zobrazit aktualizovaný kód HTML.

Co se stalo? Úpravy obsahových souborů jako HTML a CSS nevyžadují rekompilaci ve webové aplikaci .NET Core, aktivní příkaz `azds up` totiž automaticky synchronizuje všechny upravené obsahové soubory do kontejneru běžícího v Azure, takže úpravy obsahu uvidíte okamžitě.

## <a name="update-a-code-file"></a>Aktualizace souboru s kódem
Aktualizace souborů s kódem je o něco pracnější, protože aplikace .NET Core musí znovu sestavit a vytvořit aktualizované binární soubory aplikace.

1. V okně terminálu stiskněte `Ctrl+C`, abyste zastavili `azds up`.
1. Otevřete soubor s kódem s názvem `Controllers/HomeController.cs` a upravte zprávu, která zobrazí na stránce O aplikaci: `ViewData["Message"] = "Your application description page.";`
1. Uložte soubor.
1. V okně terminálu spusťte `azds up`. 

Tento příkaz znovu sestaví image kontejneru a znovu nasadí Helm chart. Pokud chcete vidět, jak se změny kódu projevily v běžící aplikaci, přejděte do nabídky O aplikaci webové aplikace.


Existuje ještě *rychlejší způsob* vývoje kódu, který si ukážeme v další části. 

## <a name="debug-a-container-in-kubernetes"></a>Ladění kontejneru v prostředí Kubernetes

[!INCLUDE[](includes/debug-intro.md)]

[!INCLUDE[](includes/init-debug-assets-vscode.md)]


### <a name="select-the-azds-debug-configuration"></a>Výběr konfigurace AZDS pro ladění
1. Pokud chcete zobrazit ladění, klikněte na boku editoru VS Code na **panelu aktivit** na ikonu Ladění.
1. Jako aktivní konfiguraci ladění vyberte **.NET Core Launch (AZDS)**.

![](media/get-started-netcore/debug-configuration.png)

> [!Note]
> Pokud na paletě příkazů nevidíte příkazy Azure Dev Spaces, ověřte, že máte nainstalované rozšíření VS Code pro Azure Dev Spaces. Ujistěte se, že pracovní prostor, který jste otevřeli ve VS Code, je složka obsahující soubor azds.yaml.


### <a name="debug-the-container-in-kubernetes"></a>Ladění kontejneru v prostředí Kubernetes
Když chcete v prostředí Kubernetes ladit kód, stiskněte **F5**.

Stejně jako u příkazu `up` se synchronizuje kód s vývojovým prostředím a sestaví se kontejner, který se nasadí v prostředí Kubernetes. Tentokrát se ale ladicí program samozřejmě připojí ke vzdálenému kontejneru.

[!INCLUDE[](includes/tip-vscode-status-bar-url.md)]

V serverovém souboru s kódem nastavte zarážku, třeba ve funkci `Index()` ve zdrojovém souboru `Controllers/HomeController.cs`. Aktualizace stránky prohlížeče způsobí aktivaci zarážky.

Máte plný přístup k informacím o ladění, jako je zásobník volání, místní proměnné, informace o výjimkách apod., úplně stejně jako při lokálním spuštění kódu.

### <a name="edit-code-and-refresh"></a>Úprava kódu a aktualizace
S aktivním ladicím programem proveďte úpravu kódu. Změňte třeba zprávu na stránce O aplikaci v `Controllers/HomeController.cs`. 

```csharp
public IActionResult About()
{
    ViewData["Message"] = "My custom message in the About page.";
    return View();
}
```

Uložte soubor a v **podokně akcí ladicího programu** klikněte na tlačítko **Aktualizovat**. 

![](media/get-started-netcore/debug-action-refresh.png)

Místo opětovného sestavení a nasazení nové image kontejneru po každé provedené změně, což často dlouho trvá, služba Azure Dev Space provádí přírůstkovou rekompilaci kódu v existujícím kontejneru, takže zajišťuje kratší cyklus úprav/ladění.

Aktualizujte webovou aplikaci v prohlížeči a přejděte na stránku O aplikaci. V uživatelském rozhraní by se měla zobrazit vaše upravená zpráva.

**Teď znáte metodu, která umožňuje rychlou iteraci kódu a ladění přímo v prostředí Kubernetes.**

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Práce s více kontejnery a týmový vývoj](get-started-netcore.md#call-a-service-running-in-a-separate-container)