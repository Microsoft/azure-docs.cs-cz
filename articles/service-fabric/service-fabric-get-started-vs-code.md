---
title: Azure Service Fabric s kódem VS Začínáme
description: Tento článek je přehled vytváření aplikací Service Fabric pomocí visual studio kód.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 17bff459a0399d39c6bfdd772ad16d0b5b2f6771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258483"
---
# <a name="service-fabric-for-visual-studio-code"></a>Service Fabric pro kód sady Visual Studio

[Rozšíření Service Fabric Reliable Services pro Kód VS](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) poskytuje nástroje potřebné k vytváření, vytváření a ladění aplikací Service Fabric v operačních systémech Windows, Linux a macOS.

Tento článek obsahuje přehled požadavků a nastavení rozšíření, jakož i použití různých příkazů, které jsou k dispozici rozšíření. 

> [!IMPORTANT]
> Aplikace Service Fabric Java lze vyvíjet na počítačích s Windows, ale lze je nasadit jenom do clusterů Azure Linux. Ladění aplikací jazyka Java není v systému Windows podporováno.

## <a name="prerequisites"></a>Požadavky

Následující požadavky musí být nainstalovány ve všech prostředích.

* [Kód visual studia](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [Service Fabric SDK](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)
* Yeoman Generátory - nainstalujte příslušné generátory pro vaši aplikaci

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Pro vývoj v jazyce Java musí být nainstalovány následující požadavky:

* [Java SDK](https://aka.ms/azure-jdks) (verze 1.8)
* [Gradle](https://gradle.org/install/)
* [Ladicí program pro rozšíření kódu Java VS](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) Potřebné k ladění java služeb. Ladění služeb Java je podporováno pouze na Linuxu. Můžete nainstalovat buď kliknutím na ikonu Rozšíření na **panelu aktivit** v kódu VS a vyhledáním rozšíření, nebo na webu VS Code Marketplace.

Pro vývoj rozhraní .NET Core/C# je nutné nainstalovat následující požadavky:

* [Jádro .NET](https://www.microsoft.com/net/learn/get-started) (verze 2.0.0 nebo novější)
* [C# pro Visual Studio Code (powered by OmniSharp) Rozšíření kódu VS](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) Potřebné k ladění služeb Jazyka C#. Můžete nainstalovat buď kliknutím na ikonu Rozšíření na **panelu aktivit** v kódu VS a vyhledáním rozšíření, nebo na webu VS Code Marketplace.

## <a name="setup"></a>Nastavení

1. Otevřete vs kód.
2. Klikněte na ikonu Rozšíření na **panelu aktivit** na levé straně kódu VS. Vyhledejte "Service Fabric". Klepnutím na tlačítko **Nainstalovat** pro rozšíření Service Fabric Reliable Services.

## <a name="commands"></a>Příkazy
Rozšíření Service Fabric Reliable Services pro Kód VS poskytuje mnoho příkazů, které vývojářům pomáhají vytvářet a nasazovat projekty Service Fabric. Příkazy můžete volat z **palety příkazů** stisknutím klávesy `(Ctrl + Shift + p)`, zadáním názvu příkazu do vstupního panelu a výběrem požadovaného příkazu ze seznamu výzev. 

* Fabric služby: Vytvořit aplikaci 
* Service Fabric: Publikovat aplikaci 
* Infrastruktura služeb: Nasazení aplikace 
* Service Fabric: Odebrat aplikaci  
* Service Fabric: Sestavení aplikace 
* Servisní tkanina: Čistá aplikace 

### <a name="service-fabric-create-application"></a>Fabric služby: Vytvořit aplikaci

Service **Fabric: Vytvořit aplikaci** příkaz vytvoří novou aplikaci Service Fabric v aktuálním pracovním prostoru. V závislosti na tom, které generátory yeoman jsou nainstalovány ve vývojovém počítači, můžete vytvořit několik typů aplikace Service Fabric, včetně Java, C#, Container a Guest projekty. 

1.  Výběr **struktury infrastruktury služeb: Vytvořit příkaz Vytvořit aplikaci.**
2.  Vyberte typ nové aplikace Service Fabric. 
3.  Zadejte název aplikace, kterou chcete vytvořit.
3.  Vyberte typ služby, kterou chcete přidat do aplikace Service Fabric. 
4.  Podle pokynů službu pojmenujte. 
5.  Nová aplikace Service Fabric se zobrazí v pracovním prostoru.
6.  Otevřete novou složku aplikace tak, aby se stala kořenovou složkou v pracovním prostoru. Odtud můžete pokračovat ve spouštění příkazů.

### <a name="service-fabric-add-service"></a>Service Fabric: Přidat službu
Service **Fabric: Add Service** příkaz přidá novou službu existující aplikace Service Fabric. Aplikace, do které bude služba přidána, musí být kořenovým adresářem pracovního prostoru. 

1.  Vyberte **service fabric: Přidat službu** příkazu.
2.  Vyberte typ aktuální aplikace Service Fabric. 
3.  Vyberte typ služby, kterou chcete přidat do aplikace Service Fabric. 
4.  Podle pokynů službu pojmenujte. 
5.  Nová služba se zobrazí v adresáři projektu. 

### <a name="service-fabric-publish-application"></a>Service Fabric: Publikovat aplikaci
Service **Fabric: Publikovat aplikaci** příkaz nasadí aplikace Service Fabric ve vzdáleném clusteru. Cílový cluster může být zabezpečený nebo nezabezpečený cluster. Pokud parametry nejsou nastaveny v Cloud.json, aplikace se nasadí do místního clusteru.

1.  Při prvním vytvoření aplikace je v adresáři projektu vygenerován soubor Cloud.json.
2.  Zadejte hodnoty pro cluster, ke kterému se chcete připojit, do souboru Cloud.json.
3.  Vyberte příkaz **Service Fabric: Publish Application** .
4.  Zobrazte cílový cluster pomocí aplikace Service Fabric Explorer a ověřte, zda byla aplikace nainstalována. 

### <a name="service-fabric-deploy-application-localhost"></a>Service Fabric: Nasazení aplikace (Localhost)
Service **Fabric: Deploy Application** příkaz deploys your Service Fabric application to your Local cluster. Před použitím příkazu se ujistěte, že je spuštěn místní cluster. 

1. Vyberte service **fabric: nasadit aplikační** příkaz
2. Chcete-li ověřit, zda byla\/aplikace nainstalována, zkontrolujte místní cluster pomocí průzkumníka Service Fabric Explorer (http: /localhost:19080/Explorer). To může nějakou dobu trvat, takže buďte trpěliví.
3. Můžete také použít **Service Fabric: Publikovat příkaz aplikace** bez parametrů nastavených v souboru Cloud.json k nasazení do místního clusteru.

> [!NOTE]
> Nasazení aplikací Java do místního clusteru není v počítačích se systémem Windows podporováno.

### <a name="service-fabric-remove-application"></a>Service Fabric: Odebrat aplikaci
Service **Fabric: Odebrat aplikaci příkazu** odebrat aplikace Service Fabric z clusteru, který byl dříve nasazen y pomocí rozšíření Kód VS. 

1.  Vyberte service **fabric: odebrat příkaz Aplikace.**
2.  Chcete-li ověřit, zda byla aplikace odebrána, zobrazte cluster pomocí aplikace Service Fabric Explorer. To může nějakou dobu trvat, takže buďte trpěliví.

### <a name="service-fabric-build-application"></a>Service Fabric: Sestavení aplikace
Service **Fabric: Sestavení aplikace** příkaz můžete vytvářet java nebo C# Service Fabric aplikace. 

1.  Před spuštěním tohoto příkazu se ujistěte, že jste v kořenové složce aplikace. Příkaz identifikuje typ aplikace (C# nebo Java) a podle toho vytvoří aplikaci.
2.  Vyberte service **fabric: sestavení aplikace** příkazu.
3.  Výstup procesu sestavení je zapsán do integrovaného terminálu.

### <a name="service-fabric-clean-application"></a>Servisní tkanina: Čistá aplikace
**Service Fabric: Clean Application** příkaz odstraní všechny jar soubory a nativní knihovny, které byly generovány sestavení. Platí pouze pro aplikace java. 

1.  Před spuštěním tohoto příkazu se ujistěte, že jste v kořenové složce aplikace. 
2.  Vyberte příkaz **Service Fabric: Clean Application** .
3.  Výstup čistého procesu je zapsán do integrovaného terminálu.

## <a name="next-steps"></a>Další kroky

* Naučte se [vyvíjet a ladit aplikace Jazyka C# Service Fabric pomocí kódu VS](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Naučte se [vyvíjet a ladit java service fabric aplikace s VS Code](./service-fabric-develop-java-applications-with-vs-code.md).
