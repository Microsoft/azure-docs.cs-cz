---
title: Azure Service Fabric pomocí VS Code Začínáme | Dokumentace Microsoftu
description: Tento článek představuje přehled vytváření aplikací Service Fabric pomocí Visual Studio Code.
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
ms.openlocfilehash: dc9c11e2c0d5642e31eace2a4dcb6065d990e25d
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50413651"
---
# <a name="service-fabric-for-visual-studio-code"></a>Service Fabric pro Visual Studio Code

[Service Fabric Reliable Services rozšíření pro VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) poskytuje nástroje potřebné k vytvoření, sestavení a ladění aplikace Service Fabric v operačních systémech Windows, Linux a macOS.

Tento článek obsahuje přehled požadavků a instalace rozšíření, a také použití různé příkazy, které jsou k dispozici v rozšíření. 

> [!IMPORTANT]
> Aplikace Service Fabric Java mohou být vytvořeny na počítačích s Windows, ale je možné nasadit do pouze clustery Azure s Linuxem. Ladění aplikací v Javě nepodporuje Windows.

## <a name="prerequisites"></a>Požadavky

Následující požadavky musí být nainstalován ve všech prostředích.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [Sada Service Fabric SDK](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)
* Generátorů yeoman – nainstalujte příslušné generátory pro vaši aplikaci

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Pro vývoj v Javě je nutné nainstalovat následující požadavky:

* [Sada Java SDK](https://aka.ms/azure-jdks) (verze 1.8)
* [Gradle](https://gradle.org/install/)
* [Ladicí program pro rozšíření VS Codu Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) potřebné k ladění služeb v Javě. Ladění služeb v Javě je v Linuxu podporováno pouze. Můžete nainstalovat buď kliknutím na ikonu rozšíření **aktivity panelu** ve VS Code a vyhledávání rozšíření nebo z VS Code Marketplace.

Musí být nainstalován následující požadované součásti pro .NET Core /C# vývoj:

* [.NET core](https://www.microsoft.com/net/learn/get-started) (verze 2.0.0 nebo novější)
* [C#pro Visual Studio Code (využívající omnisharp) VS Code příponou](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) potřebné k ladění C# služby. Můžete nainstalovat buď kliknutím na ikonu rozšíření **aktivity panelu** ve VS Code a vyhledávání rozšíření nebo z VS Code Marketplace.

## <a name="setup"></a>Nastavení

1. Otevřít VS Code.
2. Klikněte na ikonu rozšíření **aktivity panelu** na levé straně VS Code. Vyhledejte "Service Fabric". Klikněte na tlačítko **nainstalovat** pro rozšíření Service Fabric Reliable Services.

## <a name="commands"></a>Příkazy
Service Fabric Reliable Services rozšíření pro VS Code obsahuje mnoho příkazů, což vývojářům umožňuje vytvářet a nasazovat projekty Service Fabricu. Můžete volat příkazy z **paletu příkazů** stisknutím kombinace kláves `(Ctrl + Shift + p)`, zadáte název příkazu do panelu vstupní a výběrem požadovaného příkazu ze seznamu nápovědy. 

* Service Fabric: Vytvoření aplikace 
* Service Fabric: Publikovat aplikaci 
* Service Fabric: Nasazení aplikace 
* Service Fabric: Odebrání aplikace  
* Service Fabric: Sestavení aplikace 
* Service Fabric: Vyčištění aplikace 

### <a name="service-fabric-create-application"></a>Service Fabric: Vytvoření aplikace

**Service Fabric: vytvoření aplikace** příkaz vytvoří novou aplikaci Service Fabric v aktuálním pracovním prostoru. V závislosti na tom, jaké generátorů yeoman jsou nainstalovány na vývojovém počítači, můžete vytvořit několik typů aplikace Service Fabric, včetně Javy, C#, kontejner a projekty hosta. 

1.  Vyberte **Service Fabric: Přidat službu** příkaz
2.  Vyberte typ pro novou aplikaci Service Fabric. 
3.  Zadejte název aplikace, kterou chcete vytvořit
3.  Vyberte typ služby, který chcete přidat do aplikace Service Fabric. 
4.  Postupujte podle výzev a pojmenujte službu. 
5.  Nová aplikace Service Fabric se zobrazí v pracovním prostoru.
6.  Otevřete novou složku aplikace, aby se stal kořenové složky v pracovním prostoru. Můžete pokračovat v provádění příkazů odsud.

### <a name="service-fabric-add-service"></a>Service Fabric: Přidat službu
**Service Fabric: Přidat službu** příkaz přidá nové služby do stávající aplikace Service Fabric. Aplikace, služby se přidají do musí být kořenový adresář pracovního prostoru. 

1.  Vyberte **Service Fabric: Přidat službu** příkazu.
2.  Vyberte typ stávající aplikaci Service Fabric. 
3.  Vyberte typ služby, který chcete přidat do aplikace Service Fabric. 
4.  Postupujte podle výzev a pojmenujte službu. 
5.  Nová služba, zobrazí se v adresáři projektu. 

### <a name="service-fabric-publish-application"></a>Service Fabric: Publikovat aplikaci
**Service Fabric: publikování aplikace** příkaz nasadí vaši aplikaci Service Fabric ve vzdáleném clusteru. Cílový cluster může být zabezpečený nebo nezabezpečenému clusteru. Pokud parametry nejsou nastavené v Cloud.json, aplikace je nasazená do místního clusteru.

1.  Prvním je aplikace sestavená, je vygenerován soubor Cloud.json v adresáři projektu.
2.  Vstupní hodnoty pro cluster, který chcete připojit k souboru Cloud.json.
3.  Vyberte **Service Fabric: publikování aplikace** příkazu.
4.  Zobrazte cílový cluster pomocí Service Fabric Exploreru pro potvrzení, že je nainstalovaná aplikace. 

### <a name="service-fabric-deploy-application-localhost"></a>Service Fabric: Nasazení aplikace (Localhost)
**Service Fabric: nasazení aplikace** příkaz nasadí vaši aplikaci Service Fabric na místní cluster. Ujistěte se, že místní cluster běží před použitím příkazu. 

1.  Vyberte **Service Fabric: nasazení aplikace** příkaz
2.  Zobrazit místní cluster pomocí Service Fabric Exploreru (http://localhost:19080/Explorer) potvrďte, že je nainstalovaná aplikace. To může trvat nějakou dobu, tak buďte prosím trpěliví.
3.  Můžete také použít **Service Fabric: publikování aplikace** příkaz bez parametrů, nastavte v souboru Cloud.json k nasazení do místního clusteru.

> [!NOTE]
> Nasazení aplikace v Javě do místního clusteru se nepodporuje na počítačích s Windows.

### <a name="service-fabric-remove-application"></a>Service Fabric: Odebrání aplikace
**Service Fabric: odebrání aplikace** příkaz odebere z clusteru, aby byl dříve nasazen do používání rozšíření VS Codu aplikace Service Fabric. 

1.  Vyberte **Service Fabric: odebrání aplikace** příkazu.
2.  Zobrazte clusteru pomocí Service Fabric Exploreru pro potvrzení, že aplikace byla odebrána. To může trvat nějakou dobu, tak buďte prosím trpěliví.

### <a name="service-fabric-build-application"></a>Service Fabric: Sestavení aplikace
**Service Fabric: odebrání aplikace** příkazu můžete vytvořit buď Java nebo C# aplikace Service Fabric. 

1.  Ujistěte se, že jsou v kořenové složce aplikace před spuštěním tohoto příkazu. Tento příkaz určuje typ aplikace (C# nebo Java) a odpovídajícím způsobem sestavení vaší aplikace.
2.  Vyberte **Service Fabric: sestavení aplikace** příkazu.
3.  Výstup z procesu sestavení je zapsán do integrovaného terminálu.

### <a name="service-fabric-clean-application"></a>Service Fabric: Vyčištění aplikace
**Service Fabric: Clean Application** příkaz odstraní všechny soubory jar a nativních knihoven, které byly generovány sestavením. Platí pro aplikace v Javě. 

1.  Ujistěte se, že jsou v kořenové složce aplikace před spuštěním tohoto příkazu. 
2.  Vyberte **Service Fabric: Clean Application** příkazu.
3.  Výstup procesu čištění zapsán do integrovaného terminálu.

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [vyvíjet a ladit C# aplikace Service Fabric pomocí VS Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Zjistěte, jak [vývoj a ladění aplikací Service Fabric v Javě s VS Code](./service-fabric-develop-java-applications-with-vs-code.md).
