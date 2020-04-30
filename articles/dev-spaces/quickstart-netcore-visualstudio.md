---
title: 'Ladění a iterace na Kubernetes: Visual Studio & .NET Core'
services: azure-dev-spaces
ms.date: 11/13/2019
ms.topic: quickstart
description: V tomto rychlém startu se dozvíte, jak používat Azure Dev Spaces a Visual Studio k ladění a rychlé iteraci aplikace .NET Core ve službě Azure Kubernetes.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s
manager: gwallace
ms.custom: vs-azure
ms.workload: azure-vs
ms.openlocfilehash: e6fa490285582c827b8704496fb7a20a7eb9cfbc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82166015"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio--net-core---azure-dev-spaces"></a>Rychlý Start: ladění a iterace na Kubernetes: Visual Studio & .NET Core – Azure Dev Spaces

V tomto průvodci se naučíte:

- Nastavit Azure Dev Spaces se spravovaným clusterem Kubernetes v Azure
- Iterativně vyvíjet kód v kontejnerech pomocí sady Visual Studio
- Ladění kódu spuštěného v clusteru pomocí sady Visual Studio.

Azure Dev Spaces taky umožňuje ladění a iteraci pomocí:
- [Java a Visual Studio Code](quickstart-java.md)
- [Node. js a Visual Studio Code](quickstart-nodejs.md)
- [.NET Core a Visual Studio Code](quickstart-netcore.md)

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud žádné nemáte, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
- Visual Studio 2019 ve Windows s nainstalovanou úlohou vývoj pro Azure Můžete také použít Visual Studio 2017 ve Windows s úlohou vývoje webu a [Visual Studio Tools for Kubernetes](https://aka.ms/get-vsk8stools) nainstalovanou. Pokud nemáte nainstalované Visual Studio, Stáhněte si ho [tady](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Vytvoření clusteru služby Azure Kubernetes

Cluster AKS musíte vytvořit v [podporované oblasti][supported-regions]. Vytvoření clusteru:

1. Přihlaste se k [Azure Portal](https://portal.azure.com)
1. Vyberte *+ vytvořit prostředek > Kubernetes službu*. 
1. Zadejte _předplatné_, _skupinu prostředků_, _název clusteru Kubernetes_, _oblast_, _verzi Kubernetes_a _předponu názvu DNS_.

    ![Vytvoření AKS v Azure Portal](media/get-started-netcore-visualstudio/create-aks-portal.png)

1. Klikněte na *Zkontrolovat a vytvořit*.
1. Klikněte na *Vytvořit*.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Povolení Azure Dev Spaces v clusteru AKS

V Azure Portal přejděte na svůj cluster AKS a klikněte na *vývojové prostory*. Změňte možnost *použít prostory pro vývoj* na *Ano* a klikněte na *Uložit*.

![Povolit vývojové prostory v Azure Portal](media/get-started-netcore-visualstudio/enable-dev-spaces-portal.png)

## <a name="create-a-new-aspnet-web-app"></a>Vytvoření nové webové aplikace v ASP.NET

1. Otevřete sadu Visual Studio.
1. Vytvoření nového projektu
1. Vyberte možnost *ASP.NET Core webová aplikace* a klikněte na tlačítko *Další*.
1. Pojmenujte svůj projekt *webendu* a klikněte na *vytvořit*.
1. Po zobrazení výzvy vyberte možnost *Webová aplikace (model-zobrazení-kontroler)* pro šablonu.
1. V horní části vyberte *.NET Core* a *ASP.NET Core 2,1* .
1. Klikněte na *Vytvořit*.

## <a name="connect-your-project-to-your-dev-space"></a>Připojte svůj projekt k prostoru pro vývoj

V projektu vyberte v rozevíracím seznamu nastavení spuštění možnost **Azure dev Spaces** , jak je znázorněno níže.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

V dialogovém okně Azure Dev Spaces vyberte své *předplatné* a *cluster Azure Kubernetes*. Ponechte nastavenou možnost *výchozí* a povolte zaškrtávací políčko *veřejně přístupné* . *Space* Klikněte na tlačítko *OK*.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Tento proces nasadí vaši službu do *výchozího* vývojového prostoru s veřejně přístupným URL. Pokud zvolíte cluster, který není nakonfigurovaný pro práci s Azure Dev Spaces, zobrazí se zpráva s dotazem, jestli ho chcete nakonfigurovat. Klikněte na tlačítko *OK*.

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

Service 'webfrontend' port 'http' is available at `http://default.webfrontend.1234567890abcdef1234.eus.azds.io/`
Service 'webfrontend' port 80 (http) is available at http://localhost:62266
Completed warmup for project 'webfrontend' in 125 seconds.
```

Ve výše uvedeném příkladu je `http://default.webfrontend.1234567890abcdef1234.eus.azds.io/`veřejná adresa URL. 

Vyberte **ladit** a potom **Spusťte ladění**. Po několika sekundách se vaše služba spustí a Visual Studio otevře prohlížeč s veřejnou adresou URL služby. Pokud se prohlížeč automaticky neotevře, přejděte v prohlížeči do veřejné adresy URL vaší služby a nahlaste se se službou spuštěnou ve vývojovém prostoru.

Tento proces mohl mít zakázaný veřejný přístup k vaší službě. Pokud chcete povolit veřejný přístup, můžete aktualizovat [vstupní hodnotu v *Values. yaml*][ingress-update].

## <a name="update-code"></a>Aktualizace kódu

Pokud je Visual Studio stále připojené k vašemu vývojovému prostoru, klikněte na tlačítko Zastavit. Změňte řádek 20 v `Controllers/HomeController.cs` :
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

Uložte změny a vyberte **ladit** a potom **Spusťte ladění**. Po několika sekundách se vaše služba spustí a Visual Studio otevře prohlížeč s veřejnou adresou URL služby. Pokud se prohlížeč neotevře automaticky, přejděte v prohlížeči na veřejnou adresu URL vaší služby a klikněte na *o*. Všimněte si, že se zobrazila aktualizovaná zpráva.

Místo opětovného sestavování a opětovného nasazení nové image kontejneru pokaždé, když jsou provedeny úpravy kódu, Azure Dev Spaces přírůstkově znovu zkompiluje kód v rámci existujícího kontejneru, aby byla zajištěna rychlejší smyčka úprav/ladění.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Nastavení a použití zarážek pro ladění

Pokud je Visual Studio stále připojené k vašemu vývojovému prostoru, klikněte na tlačítko Zastavit. Otevřete `Controllers/HomeController.cs` a klikněte někam na řádek 20 a umístěte kurzor tam. Chcete-li nastavit zarážku *F9* nebo klikněte na položku *ladit* a pak *Přepnout zarážku*. Pokud chcete službu spustit ve vývojovém prostoru v režimu ladění, stiskněte klávesu *F5* nebo klikněte na *ladění* a *Spusťte ladění*.

V prohlížeči otevřete službu a Všimněte si, že se nezobrazí žádná zpráva. Vraťte se do sady Visual Studio a sledujte řádek 20, který je zvýrazněný. Zarážka, kterou jste nastavili, pozastavila službu na řádku 20. Pokud chcete službu obnovit, stiskněte klávesu *F5* nebo klikněte na *ladit* a pak *pokračovat*. Vraťte se do prohlížeče a Všimněte si, že se teď zobrazí zpráva.

Při spouštění služby v Kubernetes s připojeným ladicím programem máte úplný přístup k ladicím informacím, jako je zásobník volání, místní proměnné a informace o výjimkách.

Odstraňte zarážku tak, že umístíte kurzor na řádek 20 `Controllers/HomeController.cs` v a zapnete *F9*.

## <a name="clean-up-your-azure-resources"></a>Vyčištění prostředků Azure

Přejděte do skupiny prostředků v Azure Portal a klikněte na *Odstranit skupinu prostředků*. Případně můžete použít příkaz [AZ AKS Delete](/cli/azure/aks#az-aks-delete) :

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Práce s více kontejnery a týmový vývoj](multi-service-netcore-visualstudio.md)

[ingress-update]: how-dev-spaces-works-up.md#how-running-your-code-is-configured
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
