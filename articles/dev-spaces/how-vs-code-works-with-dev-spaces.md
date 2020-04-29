---
title: Jak Visual Studio Code pracuje s Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: Přečtěte si, jak Visual Studio Code a Azure Dev Spaces vám pomůžou ladit aplikace Kubernetes a rychle je iterovat.
keywords: Azure Dev Spaces, vývojářské prostory, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
ms.openlocfilehash: 91440e59fdb8c21579ef1f04e78e66f933221ba0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80240443"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Jak Visual Studio Code pracuje s Azure Dev Spaces

Visual Studio Code a [rozšíření Azure dev Spaces][azds-extension] můžete použít k přípravě, spuštění a ladění služeb pomocí Azure dev Spaces. Pomocí Visual Studio Code a rozšíření Azure Dev Spaces můžete:

* Generování prostředků pro spouštění a ladění služeb v AKS
* Spouštění služeb Java, Node. js a .NET Core ve vývojovém prostoru
* Přímo ladit služby Java, Node. js a .NET Core běžící ve vývojovém prostoru

## <a name="generate-assets"></a>Generovat prostředky

Visual Studio Code a rozšíření Azure Dev Spaces vygeneruje následující prostředky pro váš projekt:

* Fázemi pro aplikace v jazyce Java využívající Maven, aplikace Node. js a aplikace .NET Core
* Helm grafy pro skoro libovolný jazyk s souboru Dockerfile
* `azds.yaml` Soubor, což je [konfigurační soubor Azure dev Spaces][azds-yaml] pro váš projekt
* `.vscode` Složka, ve které je Visual Studio Code spustit konfiguraci projektu pro aplikace Java pomocí Maven, aplikací v Node. js a aplikací .NET Core

Souboru Dockerfile, Helm graf a `azds.yaml` soubory jsou stejné prostředky vygenerované při spuštění. `azds prep` Tyto soubory lze také použít mimo Visual Studio Code ke spuštění projektu v AKS, jako je například spuštění `azds up`. Tuto `.vscode` složku používá pouze Visual Studio Code ke spuštění projektu v AKS z Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Spuštění služby v AKS

Po vygenerování assetů pro projekt můžete spustit služby Java, Node. js a .NET Core v existujícím prostoru pro vývoj z Visual Studio Code. Na stránce *ladění* Visual Studio Code můžete vyvolat konfiguraci spuštění z `.vscode` adresáře ke spuštění projektu.

Musíte vytvořit cluster AKS a povolit Azure Dev Spaces v clusteru mimo Visual Studio Code. K tomuto nastavení můžete použít například Azure CLI nebo Azure Portal. Můžete znovu použít stávající fázemi, Helm grafy a `azds.yaml` soubory vytvořené mimo Visual Studio Code, jako jsou například prostředky vygenerované spuštěním. `azds prep` Pokud znovu použijete prostředky vygenerované mimo Visual Studio Code, stále potřebujete mít `.vscode` adresář. Tento `.vscode` adresář je možné znovu vygenerovat pomocí Visual Studio Code a rozšíření Azure dev Spaces a nepřepíše stávající prostředky.

Pro projekty .NET Core musíte mít nainstalované [rozšíření C#][csharp-extension] pro spuštění služby .net z Visual Studio Code. V případě projektů Java, které používají Maven, musíte mít nainstalované [rozšíření Java debugger pro Azure dev Spaces][java-extension] a [Maven nainstalované a nakonfigurované][maven] tak, aby spouštělo službu Java od Visual Studio Code.

## <a name="debug-your-service-in-aks"></a>Ladění služby v AKS

Po spuštění projektu můžete ladit služby Java, Node. js a .NET Core spouštěné ve vývojovém prostoru přímo z Visual Studio Code. Konfigurace spuštění v `.vscode` adresáři poskytuje další informace o ladění pro spuštění služby s povoleným laděním ve vývojovém prostoru. Visual Studio Code se také připojí k procesu ladění ve spuštěném kontejneru ve vývojových prostorech, což vám umožní nastavit body přerušení, kontrolovat proměnné a provádět jiné operace ladění.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Použití Visual Studio Code s Azure Dev Spaces

V následujících rychlých startech můžete vidět Visual Studio Code a rozšíření Azure Dev Spaces pracující s Azure Dev Spaces:

* [Rychlé iterování a ladění pomocí Visual Studio Code a Java][quickstart-java]
* [Rychlé iterování a ladění pomocí Visual Studio Code a .NET][quickstart-netcore]
* [Rychlé iterace a ladění pomocí Visual Studio Code a Node. js][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works-prep.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
