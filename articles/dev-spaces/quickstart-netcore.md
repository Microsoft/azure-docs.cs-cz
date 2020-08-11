---
title: 'Ladění a iterace na Kubernetes: Visual Studio Code & .NET Core'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: V tomto rychlém startu se dozvíte, jak používat Azure Dev Spaces a Visual Studio Code k ladění a rychlé iteraci aplikace .NET Core ve službě Azure Kubernetes.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 13c5aabfc5a737a516a407803e620f2b62490e3e
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080741"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio-code-and-net-core---azure-dev-spaces"></a>Rychlý Start: ladění a iterace na Kubernetes: Visual Studio Code a .NET Core – Azure Dev Spaces

V tomto průvodci se naučíte:

- Nastavit Azure Dev Spaces se spravovaným clusterem Kubernetes v Azure
- Iterativní vývoj kódu v kontejnerech pomocí Visual Studio Code.
- Ladit kód ve vývojovém prostoru z Visual Studio Code.

Azure Dev Spaces také umožňují ladit a iterovat pomocí:
- [Java a Visual Studio Code](quickstart-java.md)
- [Node.js a Visual Studio Code](quickstart-nodejs.md)
- [.NET Core a Visual Studio](quickstart-netcore-visualstudio.md)

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud žádné nemáte, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
- [Visual Studio Code nainstalován](https://code.visualstudio.com/download).
- Rozšíření [Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) a [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) pro Visual Studio Code nainstalovány.
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

## <a name="get-sample-application-code"></a>Získat ukázkový kód aplikace

V tomto článku pomocí [ukázkové aplikace Azure dev Spaces](https://github.com/Azure/dev-spaces) předvádíte použití Azure dev Spaces.

Naklonujte aplikaci z GitHubu.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>Příprava ukázkové aplikace v Visual Studio Code

Otevřete Visual Studio Code, klikněte na *soubor* a pak na otevřít *...*, přejděte do adresáře *dev-Spaces/Samples/dotnetcore/Začínáme/webendu* a klikněte na *otevřít*.

Nyní máte projekt *webendu* otevřený v Visual Studio Code. Chcete-li aplikaci spustit ve vývojovém prostoru, vygenerujte pomocí rozšíření Azure Dev Spaces v paletě příkazů prostředky Docker a Helm Chart.

Chcete-li otevřít paletu příkazů v Visual Studio Code, klikněte na tlačítko *Zobrazit* *paletu příkazů*. Začněte psát `Azure Dev Spaces` a klikněte na `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces` .

![Příprava konfiguračních souborů pro Azure Dev Spaces](./media/common/command-palette.png)

Když Visual Studio Code také vyzve ke konfiguraci veřejného koncového bodu, vyberte možnost `Yes` Povolit veřejný koncový bod.

![Vybrat veřejný koncový bod](media/common/select-public-endpoint.png)

Tento příkaz připraví projekt tak, aby běžel v Azure Dev Spaces generováním grafu typu souboru Dockerfile a Helm. Také generuje adresář *. VSCode* s konfigurací ladění v kořenovém adresáři projektu.

> [!TIP]
> [Graf souboru Dockerfile a Helm](how-dev-spaces-works-prep.md#prepare-your-code) pro váš projekt je používán Azure dev Spaces k sestavení a spuštění kódu, ale tyto soubory lze změnit, pokud chcete změnit způsob sestavení a spuštění projektu.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Sestavování a spouštění kódu v Kubernetes z Visual Studio Code

Klikněte na ikonu *ladění* vlevo a v horní části klikněte na možnost *spuštění .NET Core (AZDS)* .

![Snímek obrazovky je v levém horním rohu okna Visual Studio Code. Ikona ladění je zvýrazněna, levý panel má název "ladění" a rozevírací seznam napravo od názvu zobrazuje "tečka .NET Core Launch (A Z D S).](media/get-started-netcore/debug-configuration.png)

Tento příkaz vytvoří a spustí vaši službu v Azure Dev Spaces v režimu ladění. V dolní části okna *terminálu* se zobrazuje výstup sestavení a adresy URL pro vaši službu spuštěnou v Azure dev Spaces. *Konzola ladění* zobrazuje výstup protokolu.

> [!Note]
> Pokud nevidíte žádné příkazy Azure Dev Spaces v *paletě příkazů*, ujistěte se, že máte nainstalovanou [příponu Visual Studio Code pro Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Ověřte také, že jste v Visual Studio Code otevřeli adresář *dev-Spaces/Samples/dotnetcore/Začínáme/webendu* .

Službu spuštěnou můžete zobrazit otevřením veřejné adresy URL.

> [!Note]
> Na začátku se může u veřejné adresy URL zobrazit *chybná chyba brány* . Počkejte několik sekund, než se webová stránka aktualizuje, a měli byste vidět, že je vaše služba spuštěná.

Klikněte na *ladit* a pak *Zastavit ladění* , aby se ladicí program zastavil.

## <a name="update-code"></a>Aktualizace kódu

Pokud chcete nasadit aktualizovanou verzi služby, můžete aktualizovat libovolný soubor v projektu a znovu spustit *.NET Core Launch (AZDS)*. Například:

1. Pokud je vaše aplikace stále spuštěná, klikněte na tlačítko *ladění* a zastavte tak *ladění* .
1. Aktualizujte [řádek 22 `Controllers/HomeController.cs` v](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Controllers/HomeController.cs#L22) :
    
    ```csharp
    ViewData["Message"] = "Your application description page in Azure.";
    ```

1. Uložte provedené změny.
1. Znovu spusťte *rozhraní .NET Core Launch (AZDS)*.
1. Přejděte do spuštěné služby a klikněte na *o*aplikaci.
1. Sledujte své změny.
1. Klikněte na *ladit* a pak *Zastavit ladění* a zastavte aplikaci.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Nastavení a použití zarážek pro ladění

Spusťte službu v režimu ladění pomocí *.NET Core Launch (AZDS)*.

Kliknutím na tlačítko *Zobrazit* a *Průzkumník*přejděte zpět do zobrazení *Průzkumníka* . Otevřete `Controllers/HomeController.cs` a klikněte někam na řádek 22 a umístěte kurzor tam. Chcete-li nastavit zarážku *F9* nebo klikněte na položku *ladit* a pak *Přepnout zarážku*.

V prohlížeči otevřete službu a Všimněte si, že se nezobrazí žádná zpráva. Vraťte se na Visual Studio Code a sledujte, že řádek 20 je zvýrazněný. Zarážka, kterou jste nastavili, pozastavila službu na řádku 20. Pokud chcete službu obnovit, stiskněte klávesu *F5* nebo klikněte na *ladit* a pak *pokračovat*. Vraťte se do prohlížeče a Všimněte si, že se teď zobrazí zpráva.

Při spouštění služby v Kubernetes s připojeným ladicím programem máte úplný přístup k ladicím informacím, jako je zásobník volání, místní proměnné a informace o výjimkách.

Odstraňte zarážku tak, že umístíte kurzor na řádek 22 v a zapnete `Controllers/HomeController.cs` *F9*.

## <a name="update-code-from-visual-studio-code"></a>Aktualizace kódu z Visual Studio Code

Když je služba spuštěna v režimu ladění, aktualizujte řádek 22 v `Controllers/HomeController.cs` . Například:

```csharp
ViewData["Message"] = "Your application description page in Azure while debugging!";
```

Soubor uložte. Klikněte na *ladit* a pak *znovu spusťte ladění* nebo na *panelu nástrojů ladění*klikněte na tlačítko *restartovat ladění* .

![Panel nástrojů ladění je malé podokno v horním středu stránky (hned pod nadpisem stránky). Na tlačítku restartovat se zobrazí KRUHOVÁ ŠIPKA a zvýrazní se. Obrázek tlačítka myši pro tlačítko je "restartovat (CTRL + SHIFT + f 5)".](media/common/debug-action-refresh.png)

V prohlížeči otevřete službu a Všimněte si, že se zobrazí aktualizovaná zpráva.

Místo opětovného sestavování a opětovného nasazení nové image kontejneru pokaždé, když jsou provedeny úpravy kódu, Azure Dev Spaces přírůstkově znovu zkompiluje kód v rámci existujícího kontejneru, aby byla zajištěna rychlejší smyčka úprav/ladění.

## <a name="clean-up-your-azure-resources"></a>Vyčištění prostředků Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak Azure Dev Spaces funguje.

> [!div class="nextstepaction"]
> [Jak Azure Dev Spaces funguje](how-dev-spaces-works.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
