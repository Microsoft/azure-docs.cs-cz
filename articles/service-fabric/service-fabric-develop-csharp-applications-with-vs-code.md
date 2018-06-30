---
title: Vývoj aplikací .NET Core Azure Service Fabric pomocí Visual Studio Code | Microsoft Docs
description: Tento článek ukazuje, jak vytvořit, nasadit a ladit aplikace .NET Core Service Fabric pomocí Visual Studio Code.
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
ms.openlocfilehash: 27c7c62125f3f559fb1764292729cbbfdc1c4e5f
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37115961"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>Vývoj aplikací C# Service Fabric pomocí Visual Studio Code

[Rozšíření Service Fabric spolehlivé služby pro VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) umožňuje snadno vytvářet aplikace .NET Core Service Fabric v operačních systémech Windows, Linux a systému macOS.

Tento článek ukazuje, jak vytvořit, nasadit a ladit aplikace .NET Core Service Fabric pomocí Visual Studio Code.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste již nainstalovali VS Code, Service Fabric spolehlivé služby rozšíření pro VS Code a všechny závislosti požadované pro vývojové prostředí. Další informace najdete v tématu [Začínáme](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Stažení ukázky
Tento článek používá CounterService aplikace [Service Fabric .NET Core Začínáme ukázky úložiště GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started). 

Klonovat úložiště na vývojovém počítači, spusťte následující příkaz z okna terminálu (příkazové okno v systému Windows):

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>Otevřete aplikaci v produktu VS Code

### <a name="windows"></a>Windows
Klikněte pravým tlačítkem na ikonu VS Code v nabídce Start a vyberte **spustit jako správce**. Chcete-li připojit ladicí program k vašim službám, VS Code spustit jako správce.

### <a name="linux"></a>Linux
Pomocí terminálu přejděte k cestě /service-fabric-dotnet-core-getting-started/Services/CounterService z adresáře, který se aplikace klonovat do místně.

Spusťte následující příkaz pro otevření VS Code jako kořenové uživatele tak, aby ladicí program můžete připojit k vašim službám.
```
sudo code . --user-data-dir='.'
```

Aplikace by se měla zobrazit v pracovním prostoru VS Code.

![Čítač aplikace služby v pracovním prostoru](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>Sestavení aplikace
1. Stiskněte klávesu (Ctrl + Shift + p) Chcete-li otevřít **příkaz palety** v produktu VS Code.
2. Vyhledejte a vyberte **Service Fabric: sestavení aplikace** příkaz. Výstup sestavení je odeslán integrované terminálu.

   ![Vytvoření příkazu aplikace v produktu VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Nasazení aplikace na místním clusteru
Po vytvoření aplikace můžete nasadit na místní cluster. 

1. Z **příkaz palety**, vyberte **Service Fabric: příkaz nasazení aplikace (Localhost)**. Výstup tohoto procesu instalace je odeslána integrované terminálu.

   ![Nasazení příkaz aplikace v produktu VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. Po dokončení nasazení se spustí prohlížeč a otevřete Service Fabric Explorer: http://localhost:19080/Explorer. Měli byste vidět, že je aplikace spuštěna. To může trvat delší dobu, tak buďte pacienta. 

   ![Čítač aplikace služby v Service Fabric Exploreru](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. Jakmile ověříte, že je aplikace spuštěna, spusťte prohlížeč a otevřete tuto stránku: http://localhost:31002. Toto je webové aplikace front-endu. Aktualizujte stránku chcete zobrazit aktuální hodnota čítače se zvýší, jak.

   ![Čítač aplikace služby v prohlížeči](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="debug-the-application"></a>Ladění aplikace
Při ladění aplikace v produktu VS Code, musí být aplikace spuštěna v místním clusteru. Zarážky můžete pak přidá do kódu.

Pokud chcete nastavit bod přerušení a ladění, proveďte následující kroky:
1. V Průzkumníku, otevřete */src/CounterServiceApplication/CounterService/CounterService.cs* souborů a nastavení boru přerušení na řádku 62 uvnitř `RunAsync` metoda.
3. Klikněte na ikonu ladění v **aktivity panelu** k otevření zobrazení ladicího programu v produktu VS Code. Klikněte na ikonu ozubené kolečko v horní části zobrazení ladicího programu a vyberte **.NET Core** z rozevírací nabídky prostředí. Soubor launch.json se otevře. Tento soubor můžete zavřít. Nyní byste měli vidět možnosti konfigurace v nabídce konfigurace ladění vedle tlačítko spustit (zelenou šipku).

   ![Ladění ikona v prostoru kód VS](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. Vyberte **.NET Core připojit** z nabídky konfigurace ladění.

   ![Ladění ikona v prostoru kód VS](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. Service Fabric Explorer, otevřete v prohlížeči: http://localhost:19080/Explorer. Klikněte na tlačítko **aplikace** a přejít k podrobnostem dowwn k určení primárního uzlu, který běží CounterService na. Na obrázku níže primární uzel CounterService je uzlu 0.

   ![Primární uzel pro CounterService](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. V produktu VS Code, klikněte na ikonu spuštění (zelenou šipku) vedle položky **.NET Core připojit** konfiguraci ladění. V dialogovém okně pro výběr v procesu vyberte CounterService proces, který běží na primárním uzlu, který jste identifikovali v kroku 4.

   ![Primární procesu](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. Zarážka v *CounterService.cs* soubor bude dosaženo velmi rychle. Pak můžete zkoumat hodnoty proměnných místní. Pomocí ladicích nástrojů v horní části VS Code pokračovat v provádění, krok přes řádky, krokování s vnořením metod, nebo krok mimo aktuální metoda. 

   ![Ladění hodnoty](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. Chcete-li ukončit relaci ladění, klikněte na ikonu moduly na ladění nástrojů v horní části VS Code...
   
   ![Odpojení od ladicí program](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. Pokud jste dokončili, ladění, můžete použít **Service Fabric: odebrat aplikace** příkazu CounterService aplikaci odebrat z vaší místní cluster. 

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [vyvíjet a ladit aplikace Java Service Fabric s VS Code](./service-fabric-develop-java-applications-with-vs-code.md).



