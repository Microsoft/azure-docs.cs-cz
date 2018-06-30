---
title: Azure Service Fabric kódem VS Začínáme | Microsoft Docs
description: Tento článek je přehled vytváření aplikací Service Fabric pomocí Visual Studio Code.
services: service-fabric
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: v-jamebr
ms.openlocfilehash: 367829c269bd1d96e6aa5fab1be008483a4ab5ab
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37115932"
---
# <a name="service-fabric-for-visual-studio-code"></a>Service Fabric pro Visual Studio Code

[Rozšíření Service Fabric spolehlivé služby pro VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) poskytuje nástroje, které jsou nezbytné pro vytvoření, vytváření a ladění aplikací Service Fabric v operačních systémech Windows, Linux a systému macOS.

Tento článek obsahuje přehled požadavků a instalace rozšíření, a také použití různých příkazy, které jsou k dispozici rozšíření. 

> [!IMPORTANT]
> Aplikace Service Fabric Java mohou být vytvořeny na počítače s Windows, ale lze nasadit do Azure Linux clusterů pouze. Ladění aplikací Java není podporována v systému Windows.

## <a name="prerequisites"></a>Požadavky

Následující požadavky musí být nainstalován na všechna prostředí.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [Service Fabric SDK](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)
* Yeoman generátory--instalace příslušné generátory pro vaši aplikaci

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Pro vývoj Java, je nutné nainstalovat následující požadavky:

* [Java SDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) (verze 1.8)
* [Gradle](https://gradle.org/install/)
* [Ladicí program k rozšíření Java VS Code](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) potřebné k ladění služby Java. Ladění služeb Java je podporovaná v systému Linux jenom. Můžete nainstalovat buď kliknutím na ikonu rozšíření v **aktivity panelu** v VS Code a hledání pro rozšíření, nebo z Marketplace kód VS.

Pro .NET Core musí být nainstalováno následující požadavky nebo vývoj C#:

* [.NET core](https://www.microsoft.com/net/learn/get-started) (verze 2.0.0 nebo novější)
* [C# pro Visual Studio Code (používá technologii OmniSharp) VS Code rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) potřebné k ladění jazyka C# služby. Můžete nainstalovat buď kliknutím na ikonu rozšíření v **aktivity panelu** v VS Code a hledání pro rozšíření, nebo z Marketplace kód VS.

## <a name="setup"></a>Nastavení

1. Kód Open VS.
2. Klikněte na ikonu rozšíření v **aktivity panelu** na levé straně VS Code. Vyhledejte "Service Fabric". Klikněte na tlačítko **nainstalovat** rozšíření Service Fabric spolehlivé služby.

## <a name="commands"></a>Příkazy
Spolehlivé služby Service Fabric rozšíření pro VS Code poskytuje mnoho příkazů, což vývojářům vytvářet a nasazovat projekty Service Fabric. Můžete volat příkazy z **příkaz palety** stisknutím `(Ctrl + Shift + p)`, zadejte název příkazu do vstupního panelu a výběrem požadovaného příkazu ze seznamu výzva. 

* Service Fabric: Vytvoření aplikace 
* Service Fabric: Publikování aplikace 
* Service Fabric: Nasazení aplikace 
* Service Fabric: Odebrání aplikace  
* Service Fabric: Vytvoření aplikace 
* Service Fabric: Vyčištění aplikace 

### <a name="service-fabric-create-application"></a>Service Fabric: Vytvoření aplikace

**Service Fabric: vytvoření aplikace** příkaz vytvoří novou aplikaci Service Fabric v aktuálním pracovním prostoru. V závislosti na tom, které yeoman generátory jsou nainstalovány na vývojovém počítači můžete vytvořit několik typů aplikace Service Fabric, včetně Java, C#, kontejneru a hosta projekty. 

1.  Vyberte **Service Fabric: Přidat službu** příkaz
2.  Vyberte typ pro novou aplikaci Service Fabric. 
3.  Zadejte název aplikace, kterou chcete vytvořit
3.  Vyberte typ služby, který chcete přidat do vaší aplikace Service Fabric. 
4.  Postupujte podle výzev a název služby. 
5.  Nová aplikace Service Fabric se zobrazí v pracovním prostoru.
6.  Otevřete novou složku aplikace, aby se stal kořenovou složku v pracovním prostoru. Můžete pokračovat v provádění příkazů z tohoto umístění.

### <a name="service-fabric-add-service"></a>Service Fabric: Přidat službu
**Service Fabric: Přidat službu** příkaz přidá novou službu do existující aplikace Service Fabric. Aplikace, která přidá službu musí být kořenový adresář pracovního prostoru. 

1.  Vyberte **Service Fabric: Přidat službu** příkaz.
2.  Vyberte typ vaše aktuální aplikace Service Fabric. 
3.  Vyberte typ služby, který chcete přidat do vaší aplikace Service Fabric. 
4.  Postupujte podle výzev a název služby. 
5.  Nové služby se zobrazí v adresáři projektu. 

### <a name="service-fabric-publish-application"></a>Service Fabric: Publikování aplikace
**Service Fabric: publikování aplikace** příkaz nasadí aplikaci Service Fabric na vzdálený cluster. Cílový cluster může být zabezpečený nebo nezabezpečená clusteru. Pokud parametry nejsou nastavené v Cloud.json, je aplikace nasazená na místní cluster.

1.  Prvním je integrovaná aplikace, soubor Cloud.json je vygenerována v adresáři projektu.
2.  Zadejte hodnoty pro cluster, který chcete připojit v souboru Cloud.json.
3.  Vyberte **Service Fabric: publikování aplikace** příkaz.
4.  Zobrazte cílový cluster pomocí Service Fabric Exploreru potvrďte, že byla nainstalována aplikace. 

### <a name="service-fabric-deploy-application-localhost"></a>Service Fabric: Nasazení aplikace (Localhost)
**Service Fabric: nasazení aplikace** příkaz nasadí aplikaci Service Fabric k místnímu clusteru. Ujistěte se, že místní cluster běží před použitím příkazu. 

1.  Vyberte **Service Fabric: nasazení aplikace** příkaz
2.  Zobrazení místní cluster pomocí Service Fabric Exploreru (http://localhost:19080/Explorer) potvrďte, že byla nainstalována aplikace. To může trvat delší dobu, tak buďte pacienta.
3.  Můžete také použít **Service Fabric: publikování aplikace** příkaz bez parametrů, nastavte v souboru Cloud.json k nasazení na místní cluster.

> [!NOTE]
> Na počítače s Windows nepodporuje nasazení aplikací v jazyce Java k místnímu clusteru.

### <a name="service-fabric-remove-application"></a>Service Fabric: Odebrání aplikace
**Service Fabric: Odeberte aplikaci** příkaz odebere z clusteru, aby byl dříve nasazen do pomocí rozšíření VS Code aplikace Service Fabric. 

1.  Vyberte **Service Fabric: Odeberte aplikaci** příkaz.
2.  Zobrazte clusteru pomocí Service Fabric Exploreru potvrďte, že aplikace byla odebrána. To může trvat delší dobu, tak buďte pacienta.

### <a name="service-fabric-build-application"></a>Service Fabric: Vytvoření aplikace
**Service Fabric: Odeberte aplikaci** příkaz mohou vytvářet aplikace Java nebo C# Service Fabric. 

1.  Ujistěte se, že jste v kořenové složce aplikace před spuštěním tohoto příkazu. Příkaz identifikuje typu aplikace (C# nebo Java) a odpovídajícím způsobem sestavení aplikace.
2.  Vyberte **Service Fabric: sestavení aplikace** příkaz.
3.  Výstup tohoto procesu sestavení se zapíše na integrované terminálu.

### <a name="service-fabric-clean-application"></a>Service Fabric: Vyčištění aplikace
**Service Fabric: Vyčištění aplikace** příkaz odstraní všechny soubory jar a nativní knihovny, které byly vygenerovány sestavení. Platné aplikací v jazyce Java. 

1.  Ujistěte se, že jste v kořenové složce aplikace před spuštěním tohoto příkazu. 
2.  Vyberte **Service Fabric: Vyčištění aplikace** příkaz.
3.  Výstup proces čištění se zapíše na integrované terminálu.

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [vývoji a ladění aplikací Service Fabric C# s VS Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Zjistěte, jak [vyvíjet a ladit aplikace Java Service Fabric s VS Code](./service-fabric-develop-java-applications-with-vs-code.md).
