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
ms.openlocfilehash: 1855fd201d7963fdf2f1b3fa292643908bbb05a0
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2020
ms.locfileid: "88079599"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio--net-core---azure-dev-spaces"></a>Rychlý Start: ladění a iterace na Kubernetes: Visual Studio & .NET Core – Azure Dev Spaces

V tomto průvodci se naučíte:

- Nastavit Azure Dev Spaces se spravovaným clusterem Kubernetes v Azure
- Iterativně vyvíjet kód v kontejnerech pomocí sady Visual Studio
- Ladění kódu spuštěného v clusteru pomocí sady Visual Studio.

Azure Dev Spaces taky umožňuje ladění a iteraci pomocí:
- [Java a Visual Studio Code](quickstart-java.md)
- [Node.js a Visual Studio Code](quickstart-nodejs.md)
- [.NET Core a Visual Studio Code](quickstart-netcore.md)

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud žádné nemáte, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
- Visual Studio 2019 ve Windows s nainstalovanou úlohou vývoj pro Azure Pokud nemáte nainstalované Visual Studio, Stáhněte si ho [tady](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- [Nainstalované rozhraní Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-an-azure-kubernetes-service-cluster"></a>Vytvoření clusteru služby Azure Kubernetes Service

Cluster AKS je potřeba vytvořit v [podporované oblasti][supported-regions]. Níže uvedené příkazy vytvoří skupinu prostředků s názvem *MyResourceGroup* a cluster AKS s názvem *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Povolení Azure Dev Spaces v clusteru AKS

Pomocí `use-dev-spaces` příkazu povolte v clusteru AKS vývojářské prostory a postupujte podle pokynů. Následující příkaz povolí v *MyAKS* ve skupině *MyResourceGroup* vývojářské prostory a vytvoří *výchozí* místo pro vývoj.

> [!NOTE]
> `use-dev-spaces`Příkaz nainstaluje taky Azure dev Spaces CLI, pokud ještě není nainstalovaný. Azure Dev Spaces CLI nelze nainstalovat do Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

```output
'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="create-a-new-aspnet-web-app"></a>Vytvoření nové webové aplikace v ASP.NET

1. Otevřete sadu Visual Studio.
1. Vytvoření nového projektu
1. Vyberte možnost *ASP.NET Core webová aplikace* a klikněte na tlačítko *Další*.
1. Pojmenujte svůj projekt *webendu* a klikněte na *vytvořit*.
1. Po zobrazení výzvy vyberte možnost *Webová aplikace (model-zobrazení-kontroler)* pro šablonu.
1. V horní části vyberte *.NET Core* a *ASP.NET Core 2,1* .
1. Klikněte na možnost *Vytvořit*.

## <a name="connect-your-project-to-your-dev-space"></a>Připojte svůj projekt k prostoru pro vývoj

V projektu vyberte v rozevíracím seznamu nastavení spuštění možnost **Azure dev Spaces** , jak je znázorněno níže.

![Snímek obrazovky uživatelského rozhraní sady Visual Studio s zvýrazněnou a vybranou možností IIS Express a možnost Azure Dev Spaces zvýrazněna.](media/get-started-netcore-visualstudio/LaunchSettings.png)

V dialogovém okně Azure Dev Spaces vyberte své *předplatné* a *cluster Azure Kubernetes*. Ponechte nastavenou možnost *výchozí* a povolte zaškrtávací políčko *veřejně přístupné* . *Space* Klikněte na *OK*.

![Snímek obrazovky dialogového okna Azure Dev Spaces.](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Tento proces nasadí vaši službu do *výchozího* vývojového prostoru s veřejně přístupným URL. Pokud zvolíte cluster, který není nakonfigurovaný pro práci s Azure Dev Spaces, zobrazí se zpráva s dotazem, jestli ho chcete nakonfigurovat. Klikněte na *OK*.

![Snímek obrazovky dialogového okna Přidat prostředek prostorů Azure](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

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

Ve výše uvedeném příkladu je veřejná adresa URL `http://default.webfrontend.1234567890abcdef1234.eus.azds.io/` . 

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

Odstraňte zarážku tak, že umístíte kurzor na řádek 20 v a zapnete `Controllers/HomeController.cs` *F9*.

## <a name="clean-up-your-azure-resources"></a>Vyčištění prostředků Azure

Přejděte do skupiny prostředků v Azure Portal a klikněte na *Odstranit skupinu prostředků*. Případně můžete použít příkaz [AZ AKS Delete](/cli/azure/aks#az-aks-delete) :

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak Azure Dev Spaces funguje.

> [!div class="nextstepaction"]
> [Jak Azure Dev Spaces funguje](how-dev-spaces-works.md)

[ingress-update]: how-dev-spaces-works-up.md#how-running-your-code-is-configured
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
