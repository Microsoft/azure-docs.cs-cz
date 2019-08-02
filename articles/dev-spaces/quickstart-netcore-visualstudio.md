---
title: Ladění a iterování pomocí sady Visual Studio a .NET Core v AKS s využitím Azure Dev Spaces
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 03/22/2019
ms.topic: quickstart
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s
manager: gwallace
ms.custom: vs-azure
ms.workload: azure-vs
ms.openlocfilehash: ece47c86fba6bc975a4146f596fa001014352a4f
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68725845"
---
# <a name="quickstart-debug-and-iterate-with-visual-studio-and-net-core-on-kubernetes-with-azure-dev-spaces"></a>Rychlý start: Ladění a iterování pomocí sady Visual Studio a .NET Core v Kubernetes s využitím Azure Dev Spaces

V tomto průvodci se naučíte:

- Nastavit Azure Dev Spaces se spravovaným clusterem Kubernetes v Azure
- Iterativně vyvíjet kód v kontejnerech pomocí sady Visual Studio
- Ladění kódu spuštěného v clusteru pomocí sady Visual Studio.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud žádné nemáte, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
- Visual Studio 2019 ve Windows s nainstalovanou úlohou vývoj pro Azure Můžete také použít Visual Studio 2017 ve Windows s úlohou vývoje webu a [Visual Studio Tools for Kubernetes](https://aka.ms/get-vsk8stools) nainstalovanou. Pokud nemáte nainstalované Visual Studio, Stáhněte si ho [tady](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Vytvoření clusteru služby Azure Kubernetes

Cluster AKS musíte vytvořit v [podporované oblasti][supported-regions]. Vytvoření clusteru:

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. Vyberte *+ vytvořit prostředek > Kubernetes službu*. 
1. Zadejte _předplatné_, _skupinu prostředků_, _název clusteru Kubernetes_, _oblast_, _verzi Kubernetes_a předponu _názvu DNS_.

    ![Vytvoření AKS v Azure Portal](media/get-started-netcore-visualstudio/create-aks-portal.png)

1. Klikněte na *Zkontrolovat a vytvořit*.
1. Klikněte na možnost *Vytvořit*.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Povolení Azure Dev Spaces v clusteru AKS

V Azure Portal přejděte na svůj cluster AKS a klikněte na *vývojové prostory*. Změňte *Povolit vývojové prostory* na *Ano* a klikněte na *Uložit*.

![Povolit vývojové prostory v Azure Portal](media/get-started-netcore-visualstudio/enable-dev-spaces-portal.png)

## <a name="create-a-new-aspnet-web-app"></a>Vytvoření nové webové aplikace v ASP.NET

1. Otevřít Visual Studio.
1. Vytvoří nový projekt.
1. Vyberte *ASP.NET Core webové aplikace* a pojmenujtesvůj projekt webendu.
1. Klikněte na *OK*.
1. Po zobrazení výzvy vyberte možnost *Webová aplikace (model-zobrazení-kontroler)* pro šablonu.
1. V horní části vyberte *.NET Core* a *ASP.NET Core 2,0* .
1. Klikněte na *OK*.

## <a name="connect-your-project-to-your-dev-space"></a>Připojte svůj projekt k prostoru pro vývoj

V projektu vyberte v rozevíracím seznamu nastavení spuštění možnost **Azure dev Spaces** , jak je znázorněno níže.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

V dialogovém okně Azure Dev Spaces vyberte své *předplatné* a *cluster Azure Kubernetes*. Ponechte nastavenou možnost *výchozí* a povolte zaškrtávací políčko *veřejně přístupné* . Klikněte na *OK*.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Tento proces nasadí vaši službu do *výchozího* vývojového prostoru s veřejně přístupným URL. Pokud zvolíte cluster, který není nakonfigurovaný pro práci s Azure Dev Spaces, zobrazí se zpráva s dotazem, jestli ho chcete nakonfigurovat. Klikněte na *OK*.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

V okně *výstup* se zobrazí veřejná adresa URL pro službu spuštěnou ve *výchozím* prostoru pro vývoj:

```cmd
Starting warmup for project 'webfrontend'.
Waiting for namespace to be provisioned.
Using dev space 'default' with target 'MyAKS'
...
Successfully built 1234567890ab
Successfully tagged webfrontend:devspaces-11122233344455566
Built container image in 39s
Waiting for container...
36s

Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:62266
Completed warmup for project 'webfrontend' in 125 seconds.
```

Ve výše uvedeném příkladu je http://webfrontend.1234567890abcdef1234.eus.azds.io/ veřejná adresa URL. Přejděte na veřejnou adresu URL vaší služby a pracujte se službou spuštěnou ve vývojovém prostoru.

Tento proces mohl mít zakázaný veřejný přístup k vaší službě. Pokud chcete povolit veřejný přístup, můžete aktualizovat [vstupní hodnotu v *Values. yaml*][ingress-update].

## <a name="update-code"></a>Aktualizace kódu

Pokud je Visual Studio stále připojené k vašemu vývojovému prostoru, klikněte na tlačítko Zastavit. Změňte řádek 20 v `Controllers/HomeController.cs` :
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

Uložte změny a spusťte službu pomocí **Azure dev Spaces** v rozevíracím seznamu nastavení spuštění. V prohlížeči otevřete veřejnou adresu URL vaší služby a klikněte na *o*aplikaci. Všimněte si, že se zobrazila aktualizovaná zpráva.

Místo opětovného sestavování a opětovného nasazení nové image kontejneru pokaždé, když jsou provedeny úpravy kódu, Azure Dev Spaces přírůstkově znovu zkompiluje kód v rámci existujícího kontejneru, aby byla zajištěna rychlejší smyčka úprav/ladění.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Nastavení a použití zarážek pro ladění

Pokud je Visual Studio stále připojené k vašemu vývojovému prostoru, klikněte na tlačítko Zastavit. Otevřete `Controllers/HomeController.cs` a klikněte někam na řádek 20 a umístěte kurzor tam. Chcete-li nastavit zarážku *F9* nebo klikněte na položku *ladit* a pak *Přepnout zarážku*. Pokud chcete službu spustit ve vývojovém prostoru v režimu ladění, stiskněte klávesu *F5* nebo klikněte na *ladění* a *Spusťte ladění*.

V prohlížeči otevřete službu a Všimněte si, že se nezobrazí žádná zpráva. Vraťte se do sady Visual Studio a sledujte řádek 20, který je zvýrazněný. Zarážka, kterou jste nastavili, pozastavila službu na řádku 20. Pokud chcete službu obnovit, stiskněte klávesu *F5* nebo klikněte na *ladit* a pak *pokračovat*. Vraťte se do prohlížeče a Všimněte si, že se teď zobrazí zpráva.

Při spouštění služby v Kubernetes s připojeným ladicím programem máte úplný přístup k ladicím informacím, jako je zásobník volání, místní proměnné a informace o výjimkách.

Odstraňte zarážku tak, že umístíte kurzor na řádek 20 `Controllers/HomeController.cs` v a zapnete *F9*.

## <a name="clean-up-your-azure-resources"></a>Vyčištění prostředků Azure

Přejděte do skupiny prostředků v Azure Portal a klikněte na *Odstranit skupinu prostředků*. Případně můžete použít příkaz [AZ AKS Delete](/cli/azure/aks#az-aks-delete) :

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Práce s více kontejnery a týmový vývoj](multi-service-netcore-visualstudio.md)

[ingress-update]: how-dev-spaces-works.md#how-running-your-code-is-configured
[supported-regions]: about.md#supported-regions-and-configurations
