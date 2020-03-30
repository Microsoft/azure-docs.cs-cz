---
title: Jak kód Visual Studia funguje s Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: Zjistěte, jak vám visual studio code a Azure Dev Spaces pomáhají ladit a rychle iterovat aplikace Kubernetes
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
ms.openlocfilehash: 91440e59fdb8c21579ef1f04e78e66f933221ba0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240443"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Jak kód Visual Studia funguje s Azure Dev Spaces

Pomocí kódu Visual Studia a [rozšíření Azure Dev Spaces][azds-extension] můžete připravit, spustit a ladit své služby pomocí Azure Dev Spaces. Pomocí kódu Visual Studia a rozšíření Azure Dev Spaces můžete:

* Generování datových zdrojů pro spouštění a ladění služeb v AKS
* Spuštění služeb Java, Node.js a .NET Core v dev prostoru
* Přímé ladění java, node.js a .NET Core služby běžící v dev prostoru

## <a name="generate-assets"></a>Generovat datové zdroje

Visual Studio Code a rozšíření Azure Dev Spaces generují pro váš projekt následující prostředky:

* Dockerfiles pro java aplikace používající aplikace Maven, Node.js a .NET Core aplikace
* Grafy soutoku pro téměř jakýkoli jazyk s Dockerfile
* Soubor, `azds.yaml` což je [konfigurační soubor Azure Dev Spaces][azds-yaml] pro váš projekt
* Složka `.vscode` s konfigurací spuštění kódu Visual Studio projektu pro aplikace Java pomocí aplikací Maven, Node.js a .NET Core

Dockerfile, Helm graf `azds.yaml` a soubory jsou stejné datové `azds prep`zdroje generované při spuštění . Tyto soubory lze také použít mimo kód sady Visual Studio ke spuštění `azds up`projektu v AKS, jako je například spuštění . Složka `.vscode` se používá pouze kód sady Visual Studio ke spuštění projektu v AKS z Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Spusťte svou službu v AKS

Po vygenerování prostředků pro váš projekt můžete spustit java, node.js a .NET Core služby v existujícím dev prostoru z Visual Studio Code. Na stránce *Ladění* kódu sady Visual Studio můžete vyvolat `.vscode` konfiguraci spuštění z adresáře a spustit projekt.

Musíte vytvořit cluster AKS a povolit Azure Dev Spaces ve vašem clusteru mimo Visual Studio Code. Například můžete použít Azure CLI nebo portál Azure k tomuto nastavení. Můžete znovu použít existující Dockerfiles, `azds.yaml` Helm grafy a soubory vytvořené mimo Visual Studio `azds prep`Code, jako jsou například prostředky generované spuštěním . Pokud znovu použijete datové zdroje generované mimo kód sady Visual `.vscode` Studio, stále potřebujete mít adresář. Tento `.vscode` adresář lze obnovit pomocí kódu sady Visual Studio a rozšíření Azure Dev Spaces a nebude přepisovat vaše stávající prostředky.

Pro projekty .NET Core musíte mít nainstalované [rozšíření C#][csharp-extension] pro spuštění služby .NET z kódu sady Visual Studio. Také pro java projekty pomocí Maven, musíte mít nainstalovaný [java debugger pro rozšíření Azure Dev Spaces,][java-extension] stejně jako [Maven nainstalován a nakonfigurován][maven] tak, aby spouštět službu Java z Visual Studio Code.

## <a name="debug-your-service-in-aks"></a>Ladění služby v AKS

Po spuštění projektu můžete ladit java, node.js a služby .NET Core spuštěné v prostoru pro spuštění přímo z kódu sady Visual Studio. Konfigurace spuštění v `.vscode` adresáři poskytuje další informace o ladění pro spuštění služby s laděním povoleným v prostoru pro změnu. Visual Studio Code také připojí k procesu ladění v běžícím kontejneru ve vašich vývojových prostorech, což umožňuje nastavit body přerušení, zkontrolovat proměnné a provádět další operace ladění.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Použití kódu Visual Studia s Azure Dev Spaces

Kód Visual Studia a rozšíření Azure Dev Spaces, které spolupracuje s Azure Dev Spaces, najdete v následujících rychlých startech:

* [Rychlé itereje a ladění pomocí kódu Visual Studio a Javy][quickstart-java]
* [Rychlé itered a ladění pomocí kódu Sady Visual Studio a rozhraní .NET][quickstart-netcore]
* [Rychlé itered a ladění pomocí kódu sady Visual Studio a souboru Node.js][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works-prep.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
