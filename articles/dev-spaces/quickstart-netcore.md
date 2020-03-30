---
title: 'Ladění a iteraci na Kubernetes: Visual Studio Code & .NET Core'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: Tento úvodní příručka ukazuje, jak pomocí Azure Dev Spaces a Visual Studio Code použít k ladění a rychlé iteraci aplikace .NET Core ve službě Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery, Helm, síť služeb, směrování sítě služeb, kubectl, k8s
manager: gwallace
ms.openlocfilehash: b38562879fa67d7ee82e3251ea2fcaa57a2075d6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80240214"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio-code-and-net-core---azure-dev-spaces"></a>Úvodní příručka: Ladění a iterace na Kubernetes: Visual Studio Code a .NET Core - Azure Dev Spaces

V tomto průvodci se naučíte:

- Nastavit Azure Dev Spaces se spravovaným clusterem Kubernetes v Azure
- Iterativně vyvíjet kód v kontejnerech pomocí Visual Studio Code.
- Ladění kódu v prostoru pro spuštění z kódu sady Visual Studio.

Azure Dev Spaces také umožňuje ladit a iterate pomocí:
- [Java a Visual Studio kód](quickstart-java.md)
- [Soubor Node.js a kód sady Visual Studio](quickstart-nodejs.md)
- [Jádro rozhraní .NET a sada Visual Studio](quickstart-netcore-visualstudio.md)

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud žádné nemáte, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
- [Nainstalován kód visual studio](https://code.visualstudio.com/download).
- [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) a [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) rozšíření pro Visual Studio kód nainstalován.
- [Nainstalované rozhraní Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-an-azure-kubernetes-service-cluster"></a>Vytvoření clusteru služby Azure Kubernetes

Je třeba vytvořit cluster AKS v [podporované oblasti][supported-regions]. Níže uvedené příkazy vytvoří skupinu prostředků nazvanou *MyResourceGroup* a cluster AKS s názvem *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Povolení Azure Dev Spaces v clusteru AKS

Pomocí `use-dev-spaces` příkazu povolte funkci Dev Spaces v clusteru AKS a postupujte podle pokynů. Níže uvedený příkaz povolí dev spaces v clusteru *MyAKS* ve skupině *MyResourceGroup* a vytvoří *výchozí* dev prostor.

> [!NOTE]
> Příkaz `use-dev-spaces` také nainstaluje příkaz CLI Azure Dev Spaces, pokud ještě není nainstalován. V prostředí Azure Cloud Shell nelze nainstalovat příkaz příkazpříkaz ový příkaz Azure Dev Spaces.

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

V tomto článku použijete [ukázkovou aplikaci Azure Dev Spaces](https://github.com/Azure/dev-spaces) k předvedení pomocí Azure Dev Spaces.

Klonovat aplikaci z GitHubu.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>Příprava ukázkové aplikace v kódu sady Visual Studio

Otevřete kód sady Visual Studio, klepněte na *položku Soubor* a potom *na otevřít...*, přejděte do *adresáře dev-spaces/samples/dotnetcore/getting-started/webfrontend* a klepněte na tlačítko *Otevřít*.

Nyní máte projekt *webfrontend* otevřený v kódu sady Visual Studio. Chcete-li spustit aplikaci ve vašem vývojovém prostoru, vygenerujte prostředky grafu Dockeru a Helmu pomocí rozšíření Azure Dev Spaces v paletě příkazů.

Chcete-li otevřít paletu příkazů v kódu sady Visual Studio, klepněte na tlačítko *Zobrazit* a potom na *příkazpaletu*. Začněte `Azure Dev Spaces` psát a `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`klikněte na .

![Příprava konfiguračních souborů pro Azure Dev Spaces](./media/common/command-palette.png)

Když visual studio kód také vyzve ke konfiguraci `Yes` veřejného koncového bodu, zvolte povolit veřejný koncový bod.

![Vybrat veřejný koncový bod](media/common/select-public-endpoint.png)

Tento příkaz připraví váš projekt ke spuštění v Azure Dev Spaces generováním Dockerfile a Helm grafu. Generuje také adresář *.vscode* s konfigurací ladění v kořenovém adresáři projektu.

> [!TIP]
> [Dockerfile a Helm graf](how-dev-spaces-works-prep.md#prepare-your-code) pro váš projekt se používá Azure Dev Spaces k sestavení a spuštění kódu, ale můžete upravit tyto soubory, pokud chcete změnit způsob sestavení a spuštění projektu.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Sestavení a spuštění kódu v Kubernetes z kódu Sady Visual Studio

Klikněte na ikonu *Ladění* vlevo a klikněte na *.NET Core Launch (AZDS)* v horní části.

![](media/get-started-netcore/debug-configuration.png)

Tento příkaz sestavuje a spouští službu v Azure Dev Spaces v režimu ladění. Okno *Terminál* v dolní části zobrazuje výstup sestavení a adresy URL pro vaši službu spuštěnou v Azure Dev Spaces. *Ladicí konzole* zobrazuje výstup protokolu.

> [!Note]
> Pokud v *paletě příkazů příkazy pro příkazy*nevidíte žádné příkazy Azure Dev Spaces , zkontrolujte, jestli jste nainstalovali [rozšíření kódu Visual Studia pro Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Také ověřte, zda jste otevřeli adresář *dev-spaces/samples/dotnetcore/getting-started/webfrontend* v kódu sady Visual Studio.

Službu spuštěnou můžete zobrazit otevřením veřejné adresy URL.

> [!Note]
> Zpočátku může veřejná adresa URL zobrazit *chybu chybné brány.* Před aktualizací webové stránky počkejte několik sekund a měli byste vidět, že je služba spuštěna.

*Chcete-li* ladicí program zastavit, klepněte na tlačítko Ladění a potom *zastavte ladění.*

## <a name="update-code"></a>Aktualizace kódu

Chcete-li nasadit aktualizovanou verzi služby, můžete aktualizovat libovolný soubor v projektu a znovu spustit *spuštění .NET Core Launch (AZDS).* Například:

1. Pokud je vaše aplikace stále spuštěná, klikněte na *ladění* a *zastavte ladění* a zastavte ji.
1. Aktualizovat [řádek 22 `Controllers/HomeController.cs` na:](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Controllers/HomeController.cs#L22)
    
    ```csharp
    ViewData["Message"] = "Your application description page in Azure.";
    ```

1. Uložte provedené změny.
1. Znovu spusťte *spuštění jádra .NET (AZDS)*.
1. Přejděte na spuštěnou službu a klepněte na *tlačítko O aplikaci*.
1. Sledujte své změny.
1. Chcete-li aplikaci zastavit, klepněte na tlačítko *Ladění* a *potom zastavte ladění.*

## <a name="setting-and-using-breakpoints-for-debugging"></a>Nastavení a použití zarážek pro ladění

Spusťte službu v režimu ladění pomocí *spuštění jádra .NET (AZDS).*

Klepnutím na tlačítko *Zobrazit* a potom na Zobrazit přejděte zpět do *zobrazení* *Průzkumníka*. Otevřete `Controllers/HomeController.cs` a klikněte někde na řádku 22, aby váš kurzor tam. Chcete-li nastavit zarážku, *stiskněte klávesu F9* nebo klepněte na tlačítko *Ladění* a potom *přepnout zarážku*.

Otevřete službu v prohlížeči a všimněte si, že se nezobrazí žádná zpráva. Návrat do visual studio kód a sledovat řádek 20 je zvýrazněna. Nastavená zarážka pozastavila službu na řádku 20. Chcete-li službu obnovit, stiskněte klávesu *F5* nebo klepněte na tlačítko *Ladění* *a potom pokračujte*. Vraťte se do prohlížeče a všimněte si, že se nyní zobrazí zpráva.

Při spuštění služby v Kubernetes s připojeným ladicím programem máte úplný přístup k informacím o ladění, jako je zásobník volání, místní proměnné a informace o výjimkách.

Odstraňte zarážku tím, že `Controllers/HomeController.cs` kurzor na řádku 22 a bít *F9*.

## <a name="update-code-from-visual-studio-code"></a>Aktualizace kódu z kódu sady Visual Studio

Pokud je služba spuštěna v režimu ladění, `Controllers/HomeController.cs`aktualizujte řádek 22 v . Například:

```csharp
ViewData["Message"] = "Your application description page in Azure while debugging!";
```

Uložte soubor. Klepněte na *tlačítko Ladění* a *potom na restartujte ladění* nebo na *panelu nástrojů Ladění*, klepněte na tlačítko *Restartovat ladění.*

![](media/common/debug-action-refresh.png)

Otevřete službu v prohlížeči a všimněte si, že se zobrazí aktualizovaná zpráva.

Namísto opětovného sestavení a opětovného nasazení nové image kontejneru při každém úpravě kódu Azure Dev Spaces postupně překompiluje kód v rámci existujícího kontejneru, aby poskytoval rychlejší smyčku pro úpravy a ladění.

## <a name="clean-up-your-azure-resources"></a>Vyčištění prostředků Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Další kroky

Zjistěte, jak Azure Dev Spaces pomáhá vyvíjet složitější aplikace napříč více kontejnery a jak můžete zjednodušit vývoj spolupráce pomocí práce s různými verzemi nebo větvemi kódu v různých prostorech. 

> [!div class="nextstepaction"]
> [Práce s více kontejnery a týmový vývoj](multi-service-netcore.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
