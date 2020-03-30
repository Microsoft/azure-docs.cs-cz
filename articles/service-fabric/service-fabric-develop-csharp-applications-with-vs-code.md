---
title: Vývoj aplikací .NET Core s visual studio kódem
description: Tento článek ukazuje, jak vytvářet, nasazovat a ladit aplikace .NET Core Service Fabric pomocí kódu Sady Visual Studio.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 1d7478e6b81ef2c53ca6194197336e91d3ff250b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614519"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>Vývoj aplikací C# Service Fabric s visual studio kód

Rozšíření [Service Fabric Reliable Services pro Kód VS](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) usnadňuje vytváření aplikací .NET Core Service Fabric v operačních systémech Windows, Linux a macOS.

Tento článek ukazuje, jak vytvořit, nasadit a ladit aplikaci .NET Core Service Fabric pomocí kódu sady Visual Studio.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste již nainstalovali VS Kód, service fabric spolehlivé služby rozšíření pro VS kód a všechny závislosti potřebné pro vývojové prostředí. Další informace najdete v [tématu Začínáme](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Stažení ukázky
Tento článek používá aplikaci CounterService v [service fabric .NET Core, jak začít ukázky úložiště GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started). 

Chcete-li naklonovat úložiště do vývojového počítače, spusťte následující příkaz z okna terminálu (příkazové okno v systému Windows):

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>Otevření aplikace v kódu VS

### <a name="windows"></a>Windows
Klepněte pravým tlačítkem myši na ikonu Kód VS v nabídce Start a zvolte **Spustit jako správce**. Chcete-li připojit ladicí program ke službám, musíte spustit Kód VS jako správce.

### <a name="linux"></a>Linux
Pomocí terminálu přejděte na cestu /service-fabric-dotnet-core-getting-started/Services/CounterService z adresáře, do kterého byla aplikace naklonována místně.

Spusťte následující příkaz a otevřete Kód VS jako kořenového uživatele, aby se ladicí program mohl připojit k vašim službám.
```
sudo code . --user-data-dir='.'
```

Aplikace by se nyní měla zobrazit v pracovním prostoru kódu VS.

![Aplikace služby čítače v pracovním prostoru](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>Sestavení aplikace
1. Stisknutím klávesy (Ctrl + Shift + p) otevřete **paletu příkazů** v kódu VS.
2. Vyhledejte a vyberte příkaz **Service Fabric: Build Application** . Výstup sestavení je odeslán do integrovaného terminálu.

   ![Příkaz Sestavit aplikaci v kódu VS](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Nasazení aplikace do místního clusteru
Po vytvoření aplikace ji můžete nasadit do místního clusteru. 

1. V **paletě příkazů**vyberte **příkaz Service Fabric: Deploy Application (Localhost).** Výstup instalačního procesu je odeslán do integrovaného terminálu.

   ![Příkaz Nasadit aplikaci v kódu VS](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. Po dokončení nasazení spusťte prohlížeč a otevřete\/Service Fabric Explorer: http: /localhost:19080/Explorer. Měli byste vidět, že aplikace je spuštěna. To může nějakou dobu trvat, takže buďte trpěliví. 

   ![Aplikace služby čítače v aplikaci Service Fabric Explorer](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. Po ověření aplikace je spuštěna, spusťte prohlížeč a\/otevřete tuto stránku: http: /localhost:31002. Toto je webový front-end aplikace. Aktualizujte stránku, abyste viděli aktuální hodnotu čítače, jak se přírůstky.

   ![Aplikace služby čítače v prohlížeči](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="publish-the-application-to-an-azure-service-fabric-cluster"></a>Publikování aplikace do clusteru Azure Service Fabric
Spolu s nasazením aplikace do místního clusteru můžete také publikovat aplikaci do vzdáleného clusteru Azure Service Fabric. 

1. Ujistěte se, že jste vytvořili aplikaci podle výše uvedených pokynů. Aktualizujte vygenerovaný konfigurační soubor `Cloud.json` podrobnostmi o vzdáleném clusteru, do kterého chcete publikovat.

2. V **paletě příkazů**vyberte **příkaz Service Fabric: Publish Application**. Výstup instalačního procesu je odeslán do integrovaného terminálu.

   ![Příkaz Publikovat aplikaci v kódu VS](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-publish-application.png)

3. Po dokončení nasazení spusťte prohlížeč a otevřete Service Fabric Explorer: `https:<clusterurl>:19080/Explorer`. Měli byste vidět, že aplikace je spuštěna. To může nějakou dobu trvat, takže buďte trpěliví. 

## <a name="debug-the-application"></a>Ladění aplikace
Při ladění aplikací v kódu VS musí být aplikace spuštěna v místním clusteru. Zarážky pak mohou být přidány do kódu.

Chcete-li nastavit zarážku a ladění, proveďte následující kroky:
1. V Průzkumníkovi otevřete soubor */src/CounterServiceApplication/CounterService/CounterService.cs* a nastavte zarážku na řádku 62 uvnitř `RunAsync` metody.
3. Kliknutím na ikonu Ladění na **panelu aktivit** otevřete zobrazení ladicího programu v kódu VS. Klikněte na ikonu ozubeného kola v horní části zobrazení ladicího programu a v rozbalovací nabídce prostředí vyberte **.NET Core.** Otevře se soubor launch.json. Tento soubor můžete zavřít. Nyní byste měli vidět možnosti konfigurace v nabídce konfigurace ladění umístěné vedle tlačítka spustit (zelená šipka).

   ![Ikona ladění v pracovním prostoru kódu VS](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. V nabídce konfigurace ladění vyberte **možnost Připojit jádro .NET.**

   ![Ikona ladění v pracovním prostoru kódu VS](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. Otevřete service fabric explorer v\/prohlížeči: http: /localhost:19080/Explorer. Klikněte na **aplikace** a přejít k podrobnostem a určete primární uzel, na který je spuštěna Služba CounterService. Na obrázku pod primární uzel pro CounterService je uzel 0.

   ![Primární uzel pro službu CounterService](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. V kódu VS klikněte na ikonu spuštění (zelená šipka) vedle konfigurace ladění **.NET Core Attach.** V dialogovém okně výběru procesu vyberte proces CounterService, který je spuštěn na primárním uzlu, který jste identifikovali v kroku 4.

   ![Primární proces](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. Zarážka v souboru *CounterService.cs* bude velmi rychle přístupná. Potom můžete prozkoumat hodnoty místních proměnných. Pomocí panelu nástrojů Ladění v horní části kódu VS pokračujte v provádění, krokování řádků, krokování do metod nebo krokování z aktuální metody. 

   ![Ladicí hodnoty](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. Chcete-li ukončit relaci ladění, klepněte na ikonu plug na panelu nástrojů Ladění v horní části kódu VS..
   
   ![Odpojit od ladicího programu](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. Po dokončení ladění můžete pomocí příkazu Service **Fabric: Remove Application** odebrat aplikaci CounterService z místního clusteru. 

## <a name="next-steps"></a>Další kroky

* Naučte se [vyvíjet a ladit java service fabric aplikace s VS Code](./service-fabric-develop-java-applications-with-vs-code.md).



