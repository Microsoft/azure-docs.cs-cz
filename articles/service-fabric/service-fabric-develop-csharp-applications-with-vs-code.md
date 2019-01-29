---
title: Vývoj aplikací Azure Service Fabric v .NET Core pomocí služby Visual Studio Code | Dokumentace Microsoftu
description: Tento článek ukazuje, jak vytvářet, nasazovat a ladit aplikace .NET Core pro Service Fabric pomocí Visual Studio Code.
services: service-fabric
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: v-jamebr
ms.openlocfilehash: d2e890110194b1fbe0528191fa645628cc3a1345
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55161355"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>Vývoj C# aplikace Service Fabric pomocí Visual Studio Code

[Service Fabric Reliable Services rozšíření pro VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) umožňuje snadno vytvářet aplikace Service Fabric v .NET Core v operačních systémech Windows, Linux a macOS.

Tento článek popisuje, jak vytvářet, nasazovat a ladit aplikace .NET Core pro Service Fabric pomocí Visual Studio Code.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste již nainstalovali VS Code, Service Fabric Reliable Services rozšíření pro VS Code a všechny závislosti, které vyžaduje pro vaše vývojové prostředí. Další informace najdete v tématu [Začínáme](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Stažení ukázky
Tento článek používá CounterService aplikaci [.NET Core v Service Fabric Začínáme ukázkové úložiště GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started). 

Naklonujte úložiště do svého vývojového počítače, spusťte následující příkaz z okna terminálu (příkazové okno na Windows):

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>Otevření aplikace v nástroji VS Code

### <a name="windows"></a>Windows
Klikněte pravým tlačítkem na ikonu VS Code, v nabídce Start a zvolte **spustit jako správce**. Připojení ladicího programu k vašim službám, budete muset spustit VS Code jako správce.

### <a name="linux"></a>Linux
Pomocí terminálu přejděte do /service-fabric-dotnet-core-getting-started/Services/CounterService cesta z adresáře, který byl do místně naklonované aplikace.

Spuštěním následujícího příkazu spusťte VS Code jako kořenové uživatele tak, aby ladicí program může připojit k vašim službám.
```
sudo code . --user-data-dir='.'
```

Aplikace by teď budou zobrazovat v pracovním prostoru VS Code.

![Čítač aplikace služby v pracovním prostoru](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>Sestavení aplikace
1. Stiskněte klávesu (kombinaci kláves Ctrl + Shift + p) Chcete-li otevřít **paletu příkazů** ve VS Code.
2. Vyhledání a výběr **Service Fabric: Sestavení aplikace** příkazu. Výstup sestavení je odeslán do integrovaného terminálu.

   ![Vytvoření příkazu aplikace v nástroji VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Nasazení aplikace do místního clusteru
Po vytvoření aplikace, nasadíte ho do místního clusteru. 

1. Z **paletu příkazů**, vyberte **Service Fabric: Nasazení aplikace (Localhost) příkaz**. Výstup procesu instalace je odeslán integrovaný terminál.

   ![Nasazení aplikace příkaz ve VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. Po dokončení nasazení spusťte prohlížeč a otevřete Service Fabric Explorer: http://localhost:19080/Explorer. Měli byste vidět, že je aplikace spuštěná. To může trvat nějakou dobu, tak buďte prosím trpěliví. 

   ![Čítač aplikace služby v Service Fabric Exploreru](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. Po ověření je aplikace spuštěna, spusťte prohlížeč a otevřete tuto stránku: http://localhost:31002. To je webový front-end aplikace. Aktualizujte stránku, aby zobrazil aktuální hodnotu čítače, jak zvýší.

   ![Čítač aplikace služby v prohlížeči](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="debug-the-application"></a>Ladění aplikace
Při ladění aplikací v nástroji VS Code, musí být aplikace spuštěná v místním clusteru. Zarážky je potom možné přidat do kódu.

Pokud chcete nastavit zarážky a ladit, proveďte následující kroky:
1. V Průzkumníku, otevřete */src/CounterServiceApplication/CounterService/CounterService.cs* souboru a nastavte zarážku na řádek 62 `RunAsync` metody.
3. Klikněte na tlačítko ikona ladit ve **aktivity panelu** otevřete zobrazení ladicího programu v nástroji VS Code. Klikněte na ikonu ozubeného kola v horní části zobrazení ladicího programu a vyberte **.NET Core** z rozevírací nabídky prostředí. Otevře se soubor Launch.js. Tento soubor můžete zavřít. Teď byste měli vidět možnosti konfigurace v nabídce konfigurace debug vedle tlačítka Spustit (zelená šipka).

   ![Ladění ikony v pracovním prostoru VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. Vyberte **.NET Core připojit** z nabídky ladění konfigurace.

   ![Ladění ikony v pracovním prostoru VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. Otevřete v prohlížeči Service Fabric Exploreru: http://localhost:19080/Explorer. Klikněte na tlačítko **aplikací** a zotavení po havárii dolů k určení primárního uzlu, na kterém běží CounterService na. Na obrázku níže primární uzel CounterService je uzel 0.

   ![Primární uzel CounterService](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. V nástroji VS Code, klikněte na ikonu spuštění (zelená šipka) vedle položky **.NET Core připojit** konfiguraci ladění. V dialogovém okně proces výběru vyberte CounterService proces, na kterém běží na primárním uzlu, který jste identifikovali v kroku 4.

   ![Primární procesu](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. Zarážka v *CounterService.cs* souboru docházet velmi rychle. Pak můžete zkoumat hodnoty místní proměnné. Pomocí panelu nástrojů ladění v horní části stránky VS Code můžete pokračovat v provádění, krok přes řádky, krokování s vnořením do metody, nebo krok mimo aktuální metoda. 

   ![Ladění hodnoty](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. Chcete-li ukončit relaci ladění, klikněte na ikonu moduly na panelu nástrojů ladění v horní části stránky VS Code...
   
   ![Odpojit od ladicího programu](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. Jakmile budete hotovi, ladění, můžete použít **Service Fabric: Odebrání aplikace** příkaz CounterService aplikaci odebrat z vašeho místního clusteru. 

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [vývoj a ladění aplikací Service Fabric v Javě s VS Code](./service-fabric-develop-java-applications-with-vs-code.md).



