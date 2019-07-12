---
title: Jak funguje Visual Studio Code s Azure Dev prostory
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: conceptual
description: Jak funguje Visual Studio Code s Azure Dev prostory
keywords: Azure Dev mezery, Dev mezery, Docker, Kubernetes, Azure, AKS, služby Azure Kubernetes Service, kontejnery
ms.openlocfilehash: a7ec20908b75ae07532c16daab8950ace9cd67ae
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712147"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Jak funguje Visual Studio Code s Azure Dev prostory

Můžete použít Visual Studio Code a [rozšíření Azure Dev prostory][azds-extension] přípravy, spouštění a ladění vašich služeb s Azure Dev mezerami. Visual Studio Code a rozšíření Azure Dev mezery vám umožní:

* Generování prostředků pro spouštění a ladění služeb ve službě AKS
* Spuštění služby Java, Node.js a .NET Core v prostoru vývoj
* Přímo ladit vaše Java, Node.js a .NET Core služby spuštěné v prostoru vývoj

## <a name="generate-assets"></a>Generovat prostředky

Visual Studio Code a rozšíření Azure Dev prostory generovat následující prostředky pro váš projekt:

* Soubory Dockerfile pro aplikace Java pomocí nástroje Maven, aplikace .NET Core a aplikací Node.js
* Grafy Helm pro téměř libovolný jazyk s souboru Dockerfile
* `azds.yaml` Soubor, který se nachází [Azure Dev prostory konfigurační soubor][azds-yaml] pro váš projekt
* A `.vscode` složka s Visual Studio Code konfigurace spuštění projektu pro aplikace Java pomocí nástroje Maven, aplikace .NET Core a aplikací Node.js

Soubor Dockerfile, diagram helmu, a `azds.yaml` soubory jsou stejné prostředky, které jsou generovány při spuštění `azds prep`. Tyto soubory lze použít také mimo Visual Studio code pro spuštění projektu ve službě AKS, jako je například spuštění `azds up`. `.vscode` Složky pouze používá Visual Studio code pro váš projekt spustit ve službě AKS z Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Spuštění služby ve službě AKS

Jakmile vygenerujete prostředky pro váš projekt, můžete spustit služby Java, Node.js a .NET Core v existující prostor dev z Visual Studio Code. V *ladění* stránku sady Visual Studio Code, můžete vyvolat konfiguraci spuštění z `.vscode` adresář pro spouštění vašeho projektu.

Musíte vytvořit AKS cluster a povolení prostorů vývoj Azure ve vašem clusteru mimo Visual Studio Code. Například můžete použít rozhraní příkazového řádku Azure nebo na webu Azure portal provést tento instalační program. Můžete znovu použít existující soubory Dockerfile, grafům helmu a `azds.yaml` soubory vytvořené mimo Visual Studio Code, jako je například prostředky, které jsou generovány spuštěním `azds prep`. Pokud znovu použít prostředky, které jsou generovány mimo Visual Studio Code, stále musíte mít `.vscode` adresáře. To `.vscode` adresář může znovu vygenerovat. Visual Studio code a rozšíření Azure Dev mezery a nedojde k přepsání stávající prostředky.

Pro projekty .NET Core, musíte mít [ C# rozšíření][csharp-extension] installed to run your .NET service from Visual Studio Code. Also for Java projects using Maven, you must have the [Java Debugger for Azure Dev Spaces extension][java-extension] nainstalovaná také [Maven nainstalovanou a nakonfigurovanou][maven] ke spuštění vaší Java službu Visual Studio Code.

## <a name="debug-your-service-in-aks"></a>Ladění služby ve službě AKS

Po spuštění projektu, můžete ladit služby Java, Node.js a .NET Core spuštěnou v prostoru vývoj přímo z Visual Studio Code. Konfigurace spuštění v `.vscode` directory poskytuje další informace o ladění pro spouštění služby s povoleným v prostoru dev laděním. Visual Studio Code také připojí se k ladění procesu v kontejneru spuštěného v prostory vaší vývoj díky tomu můžete nastavit body přerušení, kontrolovat proměnné a provádět jiné operace ladění.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Visual Studio Code pomocí Azure Dev mezery

Zobrazí se Visual Studio Code a rozšíření Azure Dev prostory práce s Azure Dev mezery v následujících úvodních příručkách:

* [Vývoj v Javě][quickstart-java]
* [Vývoj s využitím .NET][quickstart-netcore]
* [Vývoj s využitím Node.js][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md