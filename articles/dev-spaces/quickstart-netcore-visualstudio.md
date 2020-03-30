---
title: 'Ladění a iteraci na Kubernetes: Visual Studio & .NET Core'
services: azure-dev-spaces
ms.date: 11/13/2019
ms.topic: quickstart
description: Tento úvodní příručka ukazuje, jak pomocí Azure Dev Spaces a Visual Studio ladit a rychle iteraci aplikace .NET Core ve službě Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery, Helm, síť služeb, směrování sítě služeb, kubectl, k8s
manager: gwallace
ms.custom: vs-azure
ms.workload: azure-vs
ms.openlocfilehash: 5d0d1541600f4c10b021d5d7f7f435f1aa5ae589
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80239724"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio--net-core---azure-dev-spaces"></a>Úvodní příručka: Ladění a iterace na Kubernetes: Visual Studio & .NET Core - Azure Dev Spaces

V tomto průvodci se naučíte:

- Nastavit Azure Dev Spaces se spravovaným clusterem Kubernetes v Azure
- Iterativně vyvíjet kód v kontejnerech pomocí sady Visual Studio
- Ladicí kód spuštěný v clusteru pomocí sady Visual Studio.

Azure Dev Spaces také umožňuje ladit a iterate pomocí:
- [Java a Visual Studio kód](quickstart-java.md)
- [Soubor Node.js a kód sady Visual Studio](quickstart-nodejs.md)
- [Základní a visual studio .NET](quickstart-netcore.md)

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud žádné nemáte, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
- Visual Studio 2019 v systému Windows s nainstalovanou úlohou vývoje Azure. Visual Studio 2017 můžete taky používat v systému Windows s nainstalovanou úlohou pro vývoj webu a [nástroji Visual Studio pro Kubernetes.](https://aka.ms/get-vsk8stools) Pokud nemáte nainstalovaný Visual Studio, stáhněte si ho [sem](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Vytvoření clusteru služby Azure Kubernetes

Je nutné vytvořit cluster AKS v [podporované oblasti][supported-regions]. Vytvoření clusteru:

1. Přihlášení k [portálu Azure](https://portal.azure.com)
1. Vyberte *+ Vytvořit zdroj > služby Kubernetes*. 
1. Zadejte _předčíslí Odběr_, Skupina _prostředků_, _Název clusteru Kubernetes_, _Region_, _Kubernetesova verze_a _PŘEDPOD DNS_.

    ![Vytvoření AKS na webu Azure Portal](media/get-started-netcore-visualstudio/create-aks-portal.png)

1. Klikněte na *Zkontrolovat a vytvořit*.
1. Klikněte na *Vytvořit*.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Povolení Azure Dev Spaces v clusteru AKS

Přejděte do clusteru AKS na webu Azure portal a klikněte na *Dev Spaces*. Změňte *možnost Použít dev spaces* na *ano* a klepněte na tlačítko *Uložit*.

![Povolení dev spaces na webu Azure Portal](media/get-started-netcore-visualstudio/enable-dev-spaces-portal.png)

## <a name="create-a-new-aspnet-web-app"></a>Vytvoření nové webové aplikace ASP.NET

1. Otevřete sadu Visual Studio.
1. Vytvoření nového projektu
1. Zvolte *ASP.NET Základní webová aplikace* a klepněte na tlačítko *Další*.
1. Pojmenujte *webfrontend* projektu a klepněte na *tlačítko Vytvořit*.
1. Po zobrazení výzvy zvolte pro šablonu *webovou aplikaci (Model-View-Controller).*
1. V horní části vyberte *.NET Core* a *ASP.NET Core 2.1.*
1. Klikněte na *Vytvořit*.

## <a name="connect-your-project-to-your-dev-space"></a>Připojení projektu k dev prostoru

V projektu vyberte **Azure Dev Spaces** z rozbalovacího seznamu nastavení spuštění, jak je znázorněno níže.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

V dialogovém okně Azure Dev Spaces vyberte *předplatné* a *cluster Azure Kubernetes*. Ponechejte *mezeru* nastavenou na *výchozí* a povolte zaškrtávací políčko *Veřejně přístupné.* Klikněte na tlačítko *OK*.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Tento proces nasazuje vaši službu do *výchozího* dev prostoru s veřejně přístupnou adresou URL. Pokud zvolíte cluster, který není nakonfigurovaný pro práci s Azure Dev Spaces, zobrazí se zpráva s dotazem, jestli ho chcete nakonfigurovat. Klikněte na tlačítko *OK*.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Veřejná adresa URL služby spuštěné ve *výchozím* prostoru pro spuštění se zobrazí v okně *Výstup:*

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

Service 'webfrontend' port 'http' is available at http://default.webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:62266
Completed warmup for project 'webfrontend' in 125 seconds.
```

Ve výše uvedeném příkladu http://default.webfrontend.1234567890abcdef1234.eus.azds.io/je veřejná adresa URL . 

Vyberte **ladění** a **potom spusťte ladění**. Po několika sekundách se spustí služba a Visual Studio otevře prohlížeč s veřejnou adresou URL služby. Pokud se prohlížeč neotevře automaticky, přejděte v prohlížeči na veřejnou adresu URL služby a pracujte se službou spuštěnou ve vašem dev prostoru.

Tento proces pravděpodobně zakázal veřejný přístup k vaší službě. Chcete-li povolit veřejný přístup, můžete aktualizovat [hodnotu příchozího přenosu dat v *souboru values.yaml*][ingress-update].

## <a name="update-code"></a>Aktualizace kódu

Pokud je Visual Studio stále připojené k dev prostoru, klikněte na tlačítko Stop. Změnit řádek 20 na: `Controllers/HomeController.cs`
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

Uložte změny a vyberte **ladění** a **potom spusťte ladění**. Po několika sekundách se spustí služba a Visual Studio otevře prohlížeč s veřejnou adresou URL služby. Pokud se prohlížeč neotevře automaticky, přejděte v prohlížeči na veřejnou adresu URL služby a klepněte na *tlačítko O aplikaci*. Sledujte, že se zobrazí aktualizovaná zpráva.

Namísto opětovného sestavení a opětovného nasazení nové image kontejneru při každém úpravě kódu Azure Dev Spaces postupně překompiluje kód v rámci existujícího kontejneru, aby poskytoval rychlejší smyčku pro úpravy a ladění.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Nastavení a použití zarážek pro ladění

Pokud je Visual Studio stále připojené k dev prostoru, klikněte na tlačítko Stop. Otevřete `Controllers/HomeController.cs` a klikněte někde na řádku 20, aby váš kurzor tam. Chcete-li nastavit zarážku, *stiskněte klávesu F9* nebo klepněte na tlačítko *Ladění* a potom *přepnout zarážku*. Chcete-li službu spustit v režimu ladění v prostoru pro spuštění, klepněte na tlačítko *F5* nebo klepněte na tlačítko *Ladění* a *potom spusťte ladění*.

Otevřete službu v prohlížeči a všimněte si, že se nezobrazí žádná zpráva. Návrat do sady Visual Studio a sledování řádku 20 je zvýrazněna. Nastavená zarážka pozastavila službu na řádku 20. Chcete-li službu obnovit, stiskněte klávesu *F5* nebo klepněte na tlačítko *Ladění* *a potom pokračujte*. Vraťte se do prohlížeče a všimněte si, že se nyní zobrazí zpráva.

Při spuštění služby v Kubernetes s připojeným ladicím programem máte úplný přístup k informacím o ladění, jako je zásobník volání, místní proměnné a informace o výjimkách.

Odstraňte zarážku tím, že `Controllers/HomeController.cs` kurzor na řádku 20 a bít *F9*.

## <a name="clean-up-your-azure-resources"></a>Vyčištění prostředků Azure

Přejděte do skupiny prostředků na webu Azure portal a klikněte na *Odstranit skupinu prostředků*. Případně můžete použít příkaz [az aks delete:](/cli/azure/aks#az-aks-delete)

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Práce s více kontejnery a týmový vývoj](multi-service-netcore-visualstudio.md)

[ingress-update]: how-dev-spaces-works-up.md#how-running-your-code-is-configured
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
