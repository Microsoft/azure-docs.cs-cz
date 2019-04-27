---
title: Vývoj s .NET Core v AKS s Azure Dev prostory a sady Visual Studio 2017
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
ms.author: zarhoads
ms.date: 03/22/2019
ms.topic: quickstart
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, službě Azure Kubernetes, kontejnery, Helm, služby sítě, směrování sítě služby, kubectl, k8s
manager: jeconnoc
ms.custom: vs-azure
ms.workload: azure-vs
ms.openlocfilehash: 9afca253bd188556ad6a3f6e081fb2eccc4c81cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60707167"
---
# <a name="quickstart-develop-with-net-core-on-kubernetes-with-azure-dev-spaces-visual-studio-2017"></a>Rychlý start: Vývoj s využitím .NET Core v Kubernetes se službou Azure Dev mezery (Visual Studio 2017)

V tomto průvodci se naučíte:

- Nastavit Azure Dev Spaces se spravovaným clusterem Kubernetes v Azure
- Iterativní vývoj kódu v kontejnerech pomocí sady Visual Studio 2017.
- Ladění kódu spuštěného v clusteru pomocí sady Visual Studio 2017.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud žádné nemáte, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
- Visual Studio 2017 na Windows s nainstalovaná úloha vývoj pro Web. Pokud ji nemáte nainstalovanou, stáhněte si ji [odtud](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- [Visual Studio Tools pro systém Kubernetes](https://aka.ms/get-vsk8stools) nainstalované.

## <a name="create-an-azure-kubernetes-service-cluster"></a>Vytvoření clusteru Azure Kubernetes Service

Je nutné vytvořit v clusteru AKS [podporované oblasti](https://docs.microsoft.com/azure/dev-spaces/#a-rapid,-iterative-kubernetes-development-experience-for-teams). Pokud chcete vytvořit cluster:

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. Vyberte *+ vytvořit prostředek > Služba Kubernetes*. 
1. Zadejte _předplatné_, _skupiny prostředků_, _název clusteru Kubernetes_, _oblasti_, _verze Kubernetes_, a _předpona názvu DNS_.

    ![Vytvoření AKS na portálu Azure portal](media/get-started-netcore-visualstudio/create-aks-portal.png)

1. Klikněte na *Zkontrolovat a vytvořit*.
1. Klikněte na možnost *Vytvořit*.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Povolit Azure Dev mezery ve vašem clusteru AKS

Přejděte ke svému clusteru AKS na portálu Azure portal a klikněte na tlačítko *Dev prostory*. Změna *povolit prostory Dev* k *Ano* a klikněte na tlačítko *Uložit*.

![Povolit prostory pro vývoj na webu Azure Portal](media/get-started-netcore-visualstudio/enable-dev-spaces-portal.png)

## <a name="create-a-new-aspnet-web-app"></a>Vytvořit novou webovou aplikaci ASP.NET

1. Otevřete sadu Visual Studio 2017.
1. Vytvoření nového projektu
1. Zvolte *webové aplikace ASP.NET Core* a pojmenujte svůj projekt *webfrontend*.
1. Klikněte na *OK*.
1. Po zobrazení výzvy zvolte *webové aplikace (Model-View-Controller)* šablony.
1. Vyberte *.NET Core* a *ASP.NET Core 2.0* v horní části.
1. Klikněte na *OK*.

## <a name="connect-your-project-to-your-dev-space"></a>Připojení projektu prostorem vývoj

Ve vašem projektu, vyberte **Azure Dev prostory** z rozevíracího seznamu k nastavení spuštění uvedeno dále.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

V dialogovém okně Azure Dev prostorů vyberte váš *předplatné* a *Azure Kubernetes Cluster*. Ponechte *místo* nastavena na *výchozí* a povolte *veřejně přístupné* zaškrtávací políčko. Klikněte na *OK*.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Tento proces nasadí vaše služba *výchozí* dev prostoru se adresa URL veřejně dostupná. Pokud zvolíte cluster, který není nakonfigurovaný pro práci s Azure Dev Spaces, zobrazí se zpráva s dotazem, jestli ho chcete nakonfigurovat. Klikněte na *OK*.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Veřejná adresa URL služby spuštěné v *výchozí* dev místa se zobrazí v *výstup* okno:

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

V předchozím příkladu je veřejnou adresu URL http://webfrontend.1234567890abcdef1234.eus.azds.io/. Přejděte na veřejnou adresu URL vaší služby a interakci se službou spuštění v prostoru vývoj.

## <a name="update-code"></a>Aktualizace kódu

Pokud Visual Studio 2017 je pořád připojený prostorem dev, kliknutím na tlačítko Zastavit. Změňte řádek 20 v `Controllers/HomeController.cs` na:
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

Změny uložte a spusťte službu pomocí **Azure Dev prostory** z rozevíracího seznamu nastavení spuštění. Otevřete prohlížeč a klikněte na veřejnou adresu URL vaší služby *o*. Podívejte se, že se zobrazí aktualizovaná zprávy.

Místo znovu sestavovat a nasazovat nové image kontejneru pokaždé, když jsou provedeny změny kódu, Azure Dev prostory postupně se znovu zkompiluje kód v rámci existující kontejner zajistit rychlejší smyčky úpravy a ladění.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Nastavení a použití zarážky pro ladění

Pokud Visual Studio 2017 je pořád připojený prostorem dev, kliknutím na tlačítko Zastavit. Otevřít `Controllers/HomeController.cs` a klikněte na tlačítko někam na řádek 20 umístěte kurzor existuje. Chcete-li nastavit zarážku přístupů *F9* nebo klikněte na tlačítko *ladění* pak *Přepnout zarážku*. Pro spuštění služby v režimu ladění v prostoru dev, stiskněte *F5* nebo klikněte na tlačítko *ladění* pak *spustit ladění*.

Otevřete svou službu v prohlížeči a Všimněte si, že se nezobrazí žádná zpráva. Vraťte se do sady Visual Studio 2017 a podívejte se, že se zvýrazní řádek 20. Nastavit zarážku bylo pozastaveno služby na řádku 20. A to obnovit ji, stiskněte *F5* nebo klikněte na tlačítko *ladění* pak *pokračovat*. Vraťte se do prohlížeče a Všimněte si, že se teď zobrazí zpráva.

Při spuštění služby v Kubernetes s připojen jiný ladicí program, máte plný přístup k ladění informace, jako je zásobník volání, místní proměnné a informace o výjimce.

Odeberte zarážku vložením kurzor na řádku 20 `Controllers/HomeController.cs` a stisknout *F9*.

## <a name="clean-up-your-azure-resources"></a>Vyčištění prostředků Azure

Přejděte do vaší skupiny prostředků na webu Azure Portal a klikněte na tlačítko *odstranit skupinu prostředků*. Alternativně můžete použít [az aks odstranit](/cli/azure/aks#az-aks-delete) příkaz:

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Práce s více kontejnery a týmový vývoj](multi-service-netcore-visualstudio.md)
