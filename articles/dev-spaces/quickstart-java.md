---
title: 'Ladění a iterace na Kubernetes: Visual Studio Code & Java'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: V tomto rychlém startu se dozvíte, jak pomocí Azure Dev Spaces a Visual Studio Code ladit a rychle iterovat aplikaci Java ve službě Azure Kubernetes.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Java, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s
manager: gwallace
ms.openlocfilehash: ac7a1b37b565f3589b7c049a3c1ed2a84972ded0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80239737"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-with-visual-studio-code-and-java---azure-dev-spaces"></a>Rychlý Start: ladění a iterace v Kubernetes pomocí Visual Studio Code a Java-Azure Dev Spaces

V tomto rychlém startu jste nastavili Azure Dev Spaces pomocí spravovaného clusteru Kubernetes a pomocí aplikace Java v Visual Studio Code mohli iterativní vývoj a ladění kódu v kontejnerech. Azure Dev Spaces umožňuje ladit a testovat všechny komponenty vaší aplikace ve službě Azure Kubernetes Service (AKS) s minimálním nastavením vývojového počítače. 

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Java Development Kit (JDK) 1.8.0 +](https://aka.ms/azure-jdks).
- [Maven 3.5.0 +](https://maven.apache.org/download.cgi).
- [Visual Studio Code](https://code.visualstudio.com/download).
- Ladicí program [Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) a [Java pro rozšíření Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) pro Visual Studio Code.
- Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Vytvoření clusteru služby Azure Kubernetes

Cluster AKS je potřeba vytvořit v [podporované oblasti][supported-regions]. Následující příkazy vytvoří skupinu prostředků s názvem *MyResourceGroup* a cluster AKS s názvem *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Povolení Azure Dev Spaces v clusteru AKS

Pomocí `use-dev-spaces` příkazu povolte v clusteru AKS vývojářské prostory a postupujte podle pokynů. Následující příkaz povolí vývojové prostory v clusteru *MyAKS* ve skupině *MyResourceGroup* a vytvoří *výchozí* prostor pro vývoj.

> [!NOTE]
> `use-dev-spaces` Příkaz nainstaluje taky Azure dev Spaces CLI, pokud ještě není nainstalovaný. Azure Dev Spaces CLI nemůžete nainstalovat do Azure Cloud Shell.

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

Otevřete Visual Studio Code, vyberte **soubor** a pak **otevřít**, přejděte do adresáře *dev-Spaces/Samples/Java/Začínáme/webfront-Endu* a vyberte **otevřít**.

Nyní máte projekt *webendu* otevřený v Visual Studio Code. Chcete-li aplikaci spustit ve vývojovém prostoru, vygenerujte pomocí rozšíření Azure Dev Spaces v paletě příkazů prostředky Docker a Helm Chart.

Chcete-li otevřít paletu příkazů v Visual Studio Code, vyberte **Zobrazit** **paleta příkazů**. Začněte psát `Azure Dev Spaces` a vyberte **Azure dev Spaces: Připravte konfigurační soubory pro Azure dev Spaces**.

![Příprava konfiguračních souborů pro Azure Dev Spaces](./media/common/command-palette.png)

Když Visual Studio Code také vyzve k nakonfigurování základních imagí, vystaveného portu a veřejného koncového bodu `Azul Zulu OpenJDK for Azure (Free LTS)` , vyberte pro základní bitovou kopii, `8080` pro vystavený `Yes` port a pro povolení veřejného koncového bodu.

![Vybrat základní image](media/get-started-java/select-base-image.png)

![Vybrat vystavený port](media/get-started-java/select-exposed-port.png)

![Vybrat veřejný koncový bod](media/get-started-java/select-public-endpoint.png)

Tento příkaz připraví projekt tak, aby běžel v Azure Dev Spaces generováním grafu typu souboru Dockerfile a Helm. Také generuje adresář *. VSCode* s konfigurací ladění v kořenovém adresáři projektu.

> [!TIP]
> [Graf souboru Dockerfile a Helm](how-dev-spaces-works-prep.md#prepare-your-code) pro váš projekt je používán Azure dev Spaces k sestavení a spuštění kódu, ale tyto soubory lze změnit, pokud chcete změnit způsob sestavení a spuštění projektu.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Sestavování a spouštění kódu v Kubernetes z Visual Studio Code

Vyberte ikonu **ladění** vlevo a v horní části vyberte **Spustit program Java (AZDS)** .

![Spustit program Java](media/get-started-java/debug-configuration.png)

Tento příkaz vytvoří a spustí vaši službu v Azure Dev Spaces. V dolní části okna **terminálu** se zobrazuje výstup sestavení a adresy URL pro vaši službu běžící Azure dev Spaces. **Konzola ladění** zobrazuje výstup protokolu.

> [!Note]
> Pokud nevidíte žádné příkazy Azure Dev Spaces v **paletě příkazů**, ujistěte se, že máte nainstalovanou [příponu Visual Studio Code pro Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Ověřte také, že jste v Visual Studio Code otevřeli adresář *dev-Spaces/Samples/Java/Začínáme/webfront-Endu* .

Službu spuštěnou můžete zobrazit otevřením veřejné adresy URL.

Vyberte **ladit** a pak **Zastavit ladění** , aby se ladicí program zastavil.

## <a name="update-code"></a>Aktualizace kódu

Chcete-li nasadit aktualizovanou verzi služby, můžete aktualizovat libovolný soubor v projektu a znovu spustit **program Java (AZDS)**. Příklad:

1. Pokud je vaše aplikace pořád spuštěná, **Debug** vyberte ladění **a zastavte** ji.
1. Aktualizujte [řádek 19 `src/main/java/com/ms/sample/webfrontend/Application.java` v](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/webfrontend/src/main/java/com/ms/sample/webfrontend/Application.java#L19) :
    
    ```java
    return "Hello from webfrontend in Azure!";
    ```

1. Uložte provedené změny.
1. Spusťte znovu **spuštění programu Java (AZDS)**.
1. Přejděte do spuštěné služby a sledujte své změny.
1. Vyberte **ladit** a pak **Zastavit ladění** a zastavte aplikaci.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Nastavení a použití zarážek pro ladění

Spusťte službu pomocí **spuštění programu Java (AZDS)**. Tím se také spustí vaše služba v režimu ladění.

Přejděte zpět do zobrazení **Průzkumníka** výběrem možnosti **Zobrazit** a **Průzkumník**. Otevřete *Src/Main/Java/com/MS/Sample/webend/Application. Java* a Kliknutím kamkoli na řádku 19 umístěte kurzor do umístění. Pokud chcete nastavit zarážku, stiskněte **F9** nebo vyberte **ladit** a pak **přepínací zarážku**.

V prohlížeči otevřete službu a Všimněte si, že se nezobrazí žádná zpráva. Vraťte se na Visual Studio Code a sledujte, že je zvýrazněna řádková 19. Zarážka, kterou jste nastavili, pozastavila službu na řádku 19. Pokud chcete službu obnovit, stiskněte klávesu **F5** nebo vyberte **ladit** a pak **pokračovat**. Vraťte se do prohlížeče a Všimněte si, že se teď zobrazí zpráva.

Při spouštění služby v Kubernetes s připojeným ladicím programem máte úplný přístup k ladicím informacím, jako je zásobník volání, místní proměnné a informace o výjimkách.

Odstraňte zarážku tak, že umístíte kurzor na řádek 19 v *Src/Main/Java/com/MS/Sample/webendu/webendu/Application. Java* a stisknete klávesu **F9**.

## <a name="update-code-from-visual-studio-code"></a>Aktualizace kódu z Visual Studio Code

I když je služba spuštěna v režimu ladění, aktualizujte řádek 19 v *Src/Main/Java/com/MS/Sample/webendu/webend/Application. Java*. Příklad:
```java
return "Hello from webfrontend in Azure while debugging!";
```

Uložte soubor. Vyberte **ladit** a pak **znovu spusťte ladění** nebo na **panelu nástrojů ladění**vyberte tlačítko pro **restartování ladění** .

![Aktualizovat ladění](media/common/debug-action-refresh.png)

V prohlížeči otevřete službu a Všimněte si, že se zobrazí aktualizovaná zpráva.

Místo opětovného sestavování a opětovného nasazení nové image kontejneru pokaždé, když jsou provedeny úpravy kódu, Azure Dev Spaces přírůstkově znovu zkompiluje kód v rámci existujícího kontejneru, aby byla zajištěna rychlejší smyčka úprav/ladění.

## <a name="clean-up-your-azure-resources"></a>Vyčištění prostředků Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak Azure Dev Spaces pomáhá vyvíjet složitější aplikace napříč více kontejnery a jak zjednodušit vývoj díky práci s různými verzemi nebo větvemi kódu v různých prostorech.

> [!div class="nextstepaction"]
> [Práce s více kontejnery a týmový vývoj](multi-service-java.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
