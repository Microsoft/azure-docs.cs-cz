---
title: Service Fabric Azure s VS Code Začínáme
description: Tento článek představuje přehled vytváření Service Fabric aplikací pomocí Visual Studio Code.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.custom: devx-track-js
ms.openlocfilehash: a2ac8a84b4df4a241665e7f4269803908733ca82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91249905"
---
# <a name="service-fabric-for-visual-studio-code"></a>Service Fabric pro Visual Studio Code

[Rozšíření Service Fabric Reliable Services pro vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) poskytuje nástroje potřebné k vytváření, sestavování a ladění aplikací Service Fabric v operačních systémech Windows, Linux a MacOS.

Tento článek poskytuje přehled požadavků a nastavení rozšíření a také použití různých příkazů, které jsou k dispozici v rozšíření. 

> [!IMPORTANT]
> Aplikace Service Fabric Java je možné vyvíjet na počítačích s Windows, ale dají se nasadit jenom do clusterů Azure Linux. Ladění aplikací Java není v systému Windows podporováno.

## <a name="prerequisites"></a>Požadavky

Ve všech prostředích musí být nainstalované následující požadavky.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [Sada Service Fabric SDK](./service-fabric-get-started.md)
* Yeoman generátory – nainstalujte vhodné generátory pro vaši aplikaci.

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Pro vývoj v jazyce Java je nutné nainstalovat následující požadavky:

* [Java SDK](https://aka.ms/azure-jdks) (verze 1,8)
* [Gradle](https://gradle.org/install/)
* [Ladicí program pro rozšíření Java vs Code](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) Nutné pro ladění služeb Java. Ladění služeb Java je podporováno pouze v systému Linux. Instalaci můžete provést kliknutím na ikonu rozšíření na **řádku aktivity** vs Code a hledáním rozšíření nebo na webu vs Code Marketplace.

Pro vývoj pro .NET Core/C# je nutné nainstalovat následující požadavky:

* [.NET Core](https://www.microsoft.com/net/learn/get-started) (verze 2.0.0 nebo novější)
* [Rozšíření jazyka C# pro Visual Studio Code (s technologií OmniSharp) vs Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) Nutné pro ladění služeb C#. Instalaci můžete provést kliknutím na ikonu rozšíření na **řádku aktivity** vs Code a hledáním rozšíření nebo na webu vs Code Marketplace.

## <a name="setup"></a>Nastavení

1. Otevřete VS Code.
2. Klikněte na ikonu rozšíření na **panelu aktivity** na levé straně vs Code. Vyhledejte "Service Fabric". Pro rozšíření Service Fabric Reliable Services klikněte na **instalovat** .

## <a name="commands"></a>Příkazy
Rozšíření Service Fabric Reliable Services pro VS Code poskytuje mnoho příkazů, které vývojářům pomůžou vytvořit a nasadit Service Fabric projekty. Příkazy lze volat z **palety příkazů** stisknutím klávesy `(Ctrl + Shift + p)` , zadáním názvu příkazu do vstupního panelu a výběrem požadovaného příkazu ze seznamu příkazů. 

* Service Fabric: Vytvoření aplikace 
* Service Fabric: publikování aplikace 
* Service Fabric: nasadit aplikaci 
* Service Fabric: odebrat aplikaci  
* Service Fabric: sestavování aplikace 
* Service Fabric: vyčištění aplikace 

### <a name="service-fabric-create-application"></a>Service Fabric: Vytvoření aplikace

Příkaz **Service Fabric: Create Application** vytvoří novou aplikaci Service Fabric ve vašem aktuálním pracovním prostoru. V závislosti na tom, které generátory Yeoman jsou nainstalovány na vašem vývojovém počítači, můžete vytvořit několik typů Service Fabric aplikace, včetně projektů Java, C#, Container a Host. 

1.  Výběr příkazu **Service Fabric: Create Application**
2.  Vyberte typ nové aplikace Service Fabric. 
3.  Zadejte název aplikace, kterou chcete vytvořit.
3.  Vyberte typ služby, kterou chcete přidat do aplikace Service Fabric. 
4.  Podle pokynů pojmenujte službu. 
5.  V pracovním prostoru se zobrazí nová aplikace Service Fabric.
6.  Otevřete složku nová aplikace tak, aby se stala kořenovou složkou v pracovním prostoru. V tomto případě můžete pokračovat ve spouštění příkazů.

### <a name="service-fabric-add-service"></a>Service Fabric: Přidání služby
Příkaz **Service Fabric: Add Service** přidá novou službu do existující aplikace Service Fabric. Aplikace, do které bude služba přidána, musí být kořenovým adresářem pracovního prostoru. 

1.  Vyberte příkaz **Service Fabric: Add Service** .
2.  Vyberte typ aktuální aplikace Service Fabric. 
3.  Vyberte typ služby, kterou chcete přidat do aplikace Service Fabric. 
4.  Podle pokynů pojmenujte službu. 
5.  Nová služba se zobrazí v adresáři projektu. 

### <a name="service-fabric-publish-application"></a>Service Fabric: publikování aplikace
Příkaz **Service Fabric: Publish aplikace** nasadí vaši aplikaci Service Fabric ve vzdáleném clusteru. Cílový cluster může být buď zabezpečený, nebo nezabezpečený cluster. Pokud nejsou v Cloud.jsnastaveny parametry, aplikace se nasadí do místního clusteru.

1.  Při prvním sestavení aplikace se v adresáři projektu vygeneruje Cloud.jsv souboru.
2.  Zadejte hodnoty pro cluster, ke kterému se chcete připojit, v Cloud.jssouboru.
3.  Vyberte příkaz **Service Fabric: publikovat aplikaci** .
4.  Zobrazte cílový cluster s Service Fabric Explorer a ověřte, že je aplikace nainstalovaná. 

### <a name="service-fabric-deploy-application-localhost"></a>Service Fabric: nasadit aplikaci (localhost)
Příkaz **Service Fabric: Deploy Application** nasadí vaši aplikaci Service Fabric do místního clusteru. Před použitím příkazu se ujistěte, že je váš místní cluster spuštěný. 

1. Vyberte **Service Fabric: nasadit aplikaci –** příkaz
2. Zobrazte místní cluster pomocí Service Fabric Explorer (http: \/ /localhost: 19080/Explorer) a potvrďte, že je aplikace nainstalovaná. To může nějakou dobu trvat, tedy pacient.
3. Můžete také použít příkaz **Service Fabric: Publish aplikace** bez parametrů nastavených v Cloud.jssouboru pro nasazení do místního clusteru.

> [!NOTE]
> V počítačích s Windows není podporováno nasazení aplikací Java do místního clusteru.

### <a name="service-fabric-remove-application"></a>Service Fabric: odebrat aplikaci
Příkaz **Service Fabric: Remove Application** odebere z clusteru Service Fabric aplikaci, kterou předtím nasadili na používání rozšíření vs Code. 

1.  Vyberte příkaz **Service Fabric: Remove Application** .
2.  Prohlédněte si cluster s Service Fabric Explorer a ověřte, že byla aplikace odebrána. To může nějakou dobu trvat, tedy pacient.

### <a name="service-fabric-build-application"></a>Service Fabric: sestavování aplikace
Příkaz **Service Fabric: Build Application** může sestavovat aplikace v jazyce Java nebo C# Service Fabric. 

1.  Před spuštěním tohoto příkazu se ujistěte, že jste v kořenové složce aplikace. Příkaz identifikuje typ aplikace (C# nebo Java) a odpovídajícím způsobem vytvoří aplikaci.
2.  Vyberte příkaz **Service Fabric: Build Application** .
3.  Výstup procesu sestavení je zapsán do integrovaného terminálu.

### <a name="service-fabric-clean-application"></a>Service Fabric: vyčištění aplikace
Příkaz **Service Fabric: Clean Application** odstraní všechny soubory jar a nativní knihovny, které byly vygenerovány sestavením. Platí pouze pro aplikace v jazyce Java. 

1.  Před spuštěním tohoto příkazu se ujistěte, že jste v kořenové složce aplikace. 
2.  Vyberte příkaz **Service Fabric: Clean Application** .
3.  Výstupem procesu čištění je zápis do integrovaného terminálu.

## <a name="next-steps"></a>Další kroky

* Naučte [se vyvíjet a ladit C# Service Fabric aplikace pomocí vs Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Naučte [se vyvíjet a ladit aplikace Java Service Fabric pomocí vs Code](./service-fabric-develop-java-applications-with-vs-code.md).
