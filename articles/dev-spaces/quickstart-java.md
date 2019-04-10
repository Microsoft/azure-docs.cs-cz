---
title: Vývoj v Javě na použití Azure Dev prostorů Kubernetes
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
ms.author: zarhoads
ms.date: 03/22/2019
ms.topic: quickstart
description: Rychlý vývoj Kubernetes s kontejnery, mikroslužby a Java v Azure
keywords: Docker, Kubernetes, Azure, AKS, službě Azure Kubernetes, kontejnery, Java, Helm, služby sítě, směrování sítě služby, kubectl, k8s
manager: jeconnoc
ms.openlocfilehash: c1c039ba8696baff11abed3930998983647f4356
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2019
ms.locfileid: "59425742"
---
# <a name="quickstart-develop-with-java-on-kubernetes-using-azure-dev-spaces"></a>Rychlý start: Vývoj v Javě na použití Azure Dev prostorů Kubernetes

V tomto průvodci se naučíte:

- Nastavit Azure Dev Spaces se spravovaným clusterem Kubernetes v Azure
- Iterativní vývoj kódu v kontejnerech pomocí Visual Studio Code a příkazový řádek.
- Ladění kódu v prostoru dev z Visual Studio Code.


## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud žádné nemáte, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
- [Nainstalovat Visual Studio Code](https://code.visualstudio.com/download).
- [Azure Dev prostory](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) a [Java ladicího programu pro Azure Dev prostory](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) rozšíření pro Visual Studio Code nainstalované.
- [Nainstalované rozhraní Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Maven nainstalovanou a nakonfigurovanou](https://maven.apache.org).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Vytvoření clusteru Azure Kubernetes Service

Je potřeba vytvořit v clusteru AKS [podporované oblasti](https://docs.microsoft.com/azure/dev-spaces/#a-rapid,-iterative-kubernetes-development-experience-for-teams). Následujících příkazů vytvořte skupinu prostředků s názvem *MyResourceGroup* a cluster AKS volá *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-count 1 --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Povolit Azure Dev mezery ve vašem clusteru AKS

Použití `use-dev-spaces` příkazu povolit prostory vývoj ve vašem clusteru AKS a postupujte podle zobrazených výzev. Následující příkaz povolí vývoj mezery na *MyAKS* cluster v *MyResourceGroup* seskupovat a vytvoří *výchozí* dev místa.

```cmd
$ az aks use-dev-spaces -g MyResourceGroup -n MyAKS

'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Získat kód ukázkové aplikace

V tomto článku budete používat [Azure Dev prostory ukázkovou aplikaci](https://github.com/Azure/dev-spaces) demonstruje použití Azure Dev mezery.

Klonování aplikace z Githubu a přejděte do *dev prostorů/ukázky/java/získávání spustit/webfrontend* adresáře:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/java/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Příprava aplikace

Generovat graf prostředků Dockeru a Helm pro spuštění aplikace v Kubernetes pomocí `azds prep` příkaz:

```cmd
azds prep --public
```

Je třeba spustit `prep` příkaz *dev prostorů/ukázky/java/získávání spustit/webfrontend* adresáře se správně Generovat graf prostředků Dockeru a Helm.

## <a name="build-and-run-code-in-kubernetes"></a>Sestavení a spuštění kódu v Kubernetes

Sestavte a spusťte váš kód v AKS pomocí `azds up` příkaz:

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...3s
Installing Helm chart...8s
Waiting for container image build...28s
Building container image...
Step 1/8 : FROM maven:3.5-jdk-8-slim
Step 2/8 : EXPOSE 8080
Step 3/8 : WORKDIR /usr/src/app
Step 4/8 : COPY pom.xml ./
Step 5/8 : RUN /usr/local/bin/mvn-entrypoint.sh     mvn package -Dmaven.test.skip=true -Dcheckstyle.skip=true -Dmaven.javadoc.skip=true --fail-never
Step 6/8 : COPY . .
Step 7/8 : RUN mvn package -Dmaven.test.skip=true -Dcheckstyle.skip=true -Dmaven.javadoc.skip=true
Step 8/8 : ENTRYPOINT ["java","-jar","target/webfrontend-0.1.0.jar"]
Built container image in 37s
Waiting for container...57s
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:54256
...
```

Zobrazí se tato služba spuštěna po otevření veřejnou adresu URL, které se zobrazí ve výstupu `azds up` příkazu. V tomto příkladu je veřejnou adresu URL *http://webfrontend.1234567890abcdef1234.eus.azds.io/*.

Chcete-li zrušit `azds up` příkazu *Ctrl + c*, služba bude pokračovat ve službě AKS a veřejná adresa URL bude stále dostupný.

## <a name="update-code"></a>Aktualizace kódu

Pokud chcete nasadit aktualizovanou verzi vaší služby, můžete aktualizovat všechny soubory ve vašem projektu a znovu spustit `azds up` příkazu. Příklad:

1. Pokud `azds up` je pořád spuštěný, stiskněte klávesu *Ctrl + c*.
1. Aktualizace [řádek 16 v `src/main/java/com/ms/sample/webfrontend/Application.java` ](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/webfrontend/src/main/java/com/ms/sample/webfrontend/Application.java#L16) na:
    
    ```java
    return "Hello from webfrontend in Azure!";
    ```

1. Uložte provedené změny.
1. Znovu spustit `azds up` příkaz:

    ```cmd
    $ azds up
    Using dev space 'default' with target 'MyAKS'
    Synchronizing files...1s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Přejděte na spuštěnou službu a sledujte změny.
1. Stisknutím klávesy *Ctrl + c* Zastavit `azds up` příkazu.

## <a name="enable-visual-studio-code-to-debug-in-kubernetes"></a>Povolit Visual Studio Code pro ladění v Kubernetes

Otevřít Visual Studio Code, klikněte na tlačítko *souboru* pak *otevřít...* , přejděte *dev prostorů/ukázky/java/získávání spustit/webfrontend* adresář a klikněte na tlačítko *otevřít*.

Teď máte *webfrontend* projekt otevřít ve Visual Studio Code, který je ve stejné službě jste spustili pomocí `azds up` příkazu. Chcete-li ladit tuto službu ve službě AKS pomocí Visual Studio Code, na rozdíl od použití `azds up` přímo, je nutné připravit tento projekt používat Visual Studio Code pro komunikaci s prostorem vývoj.

Ve Visual Studio Code otevřete paletu příkazů, klikněte na tlačítko *zobrazení* pak *paletu příkazů*. Začněte psát `Azure Dev Spaces` a klikněte na `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.

![Příprava Azure Dev prostory konfiguračních souborů](./media/common/command-palette.png)

Když Visual Studio Code také zobrazí výzvu ke konfiguraci základní Image a vystavené port, zvolte `Azul Zulu OpenJDK for Azure (Free LTS)` pro základní image a `8080` vystavené portu.

![Vyberte základní image](media/get-started-java/select-base-image.png)

![Vyberte vystavené portu](media/get-started-java/select-exposed-port.png)

Tento příkaz připraví projektu pro spuštění v Azure Dev prostory přímo z Visual Studio Code. Zároveň se vygeneruje *.vscode* adresáře s laděním konfigurace v kořenovém adresáři vašeho projektu.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio"></a>Sestavení a spuštění kódu v Kubernetes ze sady Visual Studio

Klikněte na *ladění* na levé straně a klikněte na ikonu *spusťte Program Java (AZDS)* v horní části.

![Spusťte Program v jazyce Java](media/get-started-java/debug-configuration.png)

Tento příkaz vytvoří a spustí vaši službu v Azure Dev mezery v režimu ladění. *Terminálu* v dolní části okna se zobrazí výstup sestavení a adresy URL pro vaši službu s Azure Dev prostory. *Ladění konzoly* zobrazuje výstup protokolu.

> [!Note]
> Pokud nevidíte žádné příkazy Azure Dev mezery v *paletu příkazů*, ujistěte se, že jste nainstalovali [rozšíření Visual Studio Code pro Azure Dev prostory](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Ověřte také, můžete otevřít *dev prostorů/ukázky/java/získávání spustit/webfrontend* ve Visual Studio Code.

Klikněte na tlačítko *ladění* pak *Zastavit ladění* zastavit ladicí program.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Nastavení a použití zarážky pro ladění

Spusťte svoji službu v režimu ladění *spusťte Program Java (AZDS)*.

Přejděte zpět na *Explorer* zobrazení kliknutím *zobrazení* pak *Explorer*. Otevřít `src/main/java/com/ms/sample/webfrontend/Application.java` a klikněte na tlačítko někam na řádek 16 umístěte kurzor existuje. Chcete-li nastavit zarážku přístupů *F9* nebo klikněte na tlačítko *ladění* pak *Přepnout zarážku*.

Otevřete svou službu v prohlížeči a Všimněte si, že se nezobrazí žádná zpráva. Vraťte se do Visual Studio Code a podívejte se, že se zvýrazní řádek 16. Nastavit zarážku bylo pozastaveno služby na řádku 16. A to obnovit ji, stiskněte *F5* nebo klikněte na tlačítko *ladění* pak *pokračovat*. Vraťte se do prohlížeče a Všimněte si, že se teď zobrazí zpráva.

Při spuštění služby v Kubernetes s připojen jiný ladicí program, máte plný přístup k ladění informace, jako je zásobník volání, místní proměnné a informace o výjimce.

Odeberte zarážku vložením kurzor na řádku 16 v `src/main/java/com/ms/sample/webfrontend/Application.java` a stisknout *F9*.

## <a name="update-code-from-visual-studio-code"></a>Aktualizace kódu ze sady Visual Studio Code

Zatímco služba je spuštěna v režimu ladění, aktualizujte řádek 16 v `src/main/java/com/ms/sample/webfrontend/Application.java`. Příklad:
```java
return "Hello from webfrontend in Azure while debugging!";
```

Uložte soubor. Klikněte na tlačítko *ladění* pak *restartujte ladění* nebo *ladění nástrojů*, klikněte na tlačítko *restartujte ladění* tlačítko.

![Aktualizovat ladění](media/get-started-java/debug-action-refresh.png)

Otevřete svou službu v prohlížeči a Všimněte si, že se zobrazí aktualizovaná zpráva.

Místo znovu sestavovat a nasazovat nové image kontejneru pokaždé, když jsou provedeny změny kódu, Azure Dev prostory postupně se znovu zkompiluje kód v rámci existující kontejner zajistit rychlejší smyčky úpravy a ladění.

## <a name="clean-up-your-azure-resources"></a>Vyčištění prostředků Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Další postup

Zjistěte, jak prostory vývoj Azure vám pomůže vytvořit složitější aplikace napříč více kontejnery, a jak můžete zjednodušit spolupráce na vývoji práce s různými verzemi nebo větve kódu v různých oborech.

> [!div class="nextstepaction"]
> [Práce s několika kontejnery a vývoj v týmu](multi-service-java.md)
