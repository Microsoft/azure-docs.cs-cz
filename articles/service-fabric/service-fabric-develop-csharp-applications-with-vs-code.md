---
title: Vývoj aplikací .NET Core pomocí Visual Studio Code
description: Tento článek ukazuje, jak sestavovat, nasazovat a ladit aplikace .NET Core Service Fabric pomocí Visual Studio Code.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 1d7478e6b81ef2c53ca6194197336e91d3ff250b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75614519"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>Vývoj aplikací Service Fabric C# pomocí Visual Studio Code

[Rozšíření Service Fabric Reliable Services pro vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) usnadňuje sestavování aplikací .net Core Service Fabric v operačních systémech Windows, Linux a MacOS.

V tomto článku se dozvíte, jak sestavit, nasadit a ladit aplikaci Service Fabric .NET Core pomocí Visual Studio Code.

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že jste už nainstalovali VS Code, Service Fabric Reliable Services rozšíření pro VS Code a všechny závislosti, které jsou pro vaše vývojové prostředí potřeba. Další informace najdete v tématu [Začínáme](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Stažení ukázky
V tomto článku se používá aplikace CounterService v [úložišti GitHub ukázek začínáme Service Fabric s rozhraním .NET Core](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started). 

Pokud chcete klonovat úložiště do vývojového počítače, spusťte následující příkaz z okna terminálu (příkazové okno ve Windows):

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>Otevřete aplikaci v VS Code

### <a name="windows"></a>Windows
V nabídce Start klikněte pravým tlačítkem myši na ikonu VS Code a vyberte **Spustit jako správce**. Chcete-li připojit ladicí program k vašim službám, je třeba spustit VS Code jako správce.

### <a name="linux"></a>Linux
Pomocí terminálu přejděte do cesty/service-fabric-dotnet-core-getting-started/Services/CounterService z adresáře, do kterého byla aplikace naklonována místně.

Spuštěním následujícího příkazu otevřete VS Code jako kořenový uživatel, aby se ladicí program mohl připojit k vašim službám.
```
sudo code . --user-data-dir='.'
```

Aplikace by se teď měla zobrazit v pracovním prostoru VS Code.

![Aplikace služby čítačů v pracovním prostoru](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>Sestavení aplikace
1. Stisknutím klávesy (CTRL + SHIFT + p) otevřete **paletu příkazů** v vs Code.
2. Vyhledejte a vyberte příkaz **Service Fabric: sestavit aplikaci** . Výstup sestavení se odešle do integrovaného terminálu.

   ![Příkaz sestavit aplikaci v VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Nasazení aplikace do místního clusteru
Po vytvoření aplikace ji můžete nasadit do místního clusteru. 

1. Z **palety příkazů**vyberte **příkaz Service Fabric: nasadit aplikaci (localhost)**. Výstup procesu instalace se pošle do integrovaného terminálu.

   ![Příkaz nasadit aplikaci v VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. Po dokončení nasazení spusťte prohlížeč a otevřete Service Fabric Explorer: http: \/ /localhost: 19080/Explorer. Měli byste vidět, že aplikace běží. To může nějakou dobu trvat, tedy pacient. 

   ![Aplikace služby čítače v Service Fabric Explorer](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. Po ověření, že je aplikace spuštěná, spusťte prohlížeč a otevřete tuto stránku: http: \/ /localhost: 31002. Toto je webový front-end aplikace. Aktualizujte stránku, aby se zobrazila aktuální hodnota čítače při zvýšení.

   ![Aplikace služby čítačů v prohlížeči](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="publish-the-application-to-an-azure-service-fabric-cluster"></a>Publikování aplikace do clusteru Azure Service Fabric
Společně s nasazením aplikace do místního clusteru můžete také publikovat aplikaci do vzdáleného clusteru Azure Service Fabric. 

1. Ujistěte se, že jste sestavili aplikaci pomocí výše uvedených pokynů. Aktualizujte vygenerovaný konfigurační soubor `Cloud.json` s podrobnostmi o vzdáleném clusteru, do kterého chcete publikovat.

2. Z **palety příkazů**vyberte **příkaz Service Fabric: publikování aplikace**. Výstup procesu instalace se pošle do integrovaného terminálu.

   ![Příkaz publikování aplikace v VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-publish-application.png)

3. Po dokončení nasazení spusťte prohlížeč a otevřete Service Fabric Explorer: `https:<clusterurl>:19080/Explorer` . Měli byste vidět, že aplikace běží. To může nějakou dobu trvat, tedy pacient. 

## <a name="debug-the-application"></a>Ladění aplikace
Při ladění aplikací v VS Code musí být aplikace spuštěná v místním clusteru. Zarážky lze následně přidat do kódu.

Chcete-li nastavit zarážku a ladit, proveďte následující kroky:
1. V Průzkumníkovi otevřete soubor */Src/CounterServiceApplication/CounterService/CounterService.cs* a nastavte zarážku na řádku 62 uvnitř `RunAsync` metody.
3. Kliknutím na ikonu ladění na **řádku aktivity** otevřete zobrazení ladicího programu v vs Code. V horní části zobrazení ladicího programu klikněte na ikonu ozubeného kolečka a v nabídce rozevíracího prostředí vyberte **.NET Core** . Otevře se launch.jsv souboru. Tento soubor můžete zavřít. Nyní byste měli vidět možnosti konfigurace v nabídce konfigurace ladění, která se nachází vedle tlačítka Spustit (zelená šipka).

   ![Ikona ladění v pracovním prostoru VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. V nabídce konfigurace ladění vyberte **.NET Core připojit** .

   ![Ikona ladění v pracovním prostoru VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. Otevřete Service Fabric Explorer v prohlížeči: http: \/ /localhost: 19080/Explorer. Klikněte na **aplikace** a přejděte k podrobnostem a určete primární uzel, na kterém je spuštěný CounterService. Na obrázku pod primárním uzlem pro CounterService je uzel 0.

   ![Primární uzel pro CounterService](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. V VS Code klikněte na ikonu spustit (zelená šipka) vedle položky **.NET Core připojit** konfiguraci ladění. V dialogovém okně Výběr procesu vyberte proces CounterService, který běží na primárním uzlu, který jste identifikovali v kroku 4.

   ![Primární proces](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. Zarážka v souboru *CounterService.cs* bude velmi rychlá. Pak můžete prozkoumat hodnoty místních proměnných. Pomocí panelu nástrojů ladění v horní části VS Code můžete pokračovat v provádění, krokovat řádky, Krokovat s vnořením do metod nebo krokovat s aktuální metodou. 

   ![Hodnoty ladění](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. Chcete-li ukončit relaci ladění, klikněte na ikonu plug-in na panelu nástrojů ladění v horní části VS Code..
   
   ![Odpojit od ladicího programu](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. Po dokončení ladění můžete pomocí příkazu **Service Fabric: Remove Application** odebrat aplikaci CounterService z místního clusteru. 

## <a name="next-steps"></a>Další kroky

* Naučte [se vyvíjet a ladit aplikace Java Service Fabric pomocí vs Code](./service-fabric-develop-java-applications-with-vs-code.md).



