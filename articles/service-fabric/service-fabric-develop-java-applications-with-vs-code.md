---
title: Vývoj aplikací v jazyce Java pomocí Visual Studio Code
description: Tento článek popisuje, jak vytvářet, nasazovat a ladit aplikace Java Service Fabric pomocí Visual Studio Code.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.custom: devx-track-java
ms.author: pepogors
ms.openlocfilehash: cc65deb924a9f3367c2ea1d7c71544743ccf2697
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87327357"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>Vývoj aplikací Service Fabric Java pomocí Visual Studio Code

[Rozšíření Service Fabric Reliable Services pro vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) usnadňuje vytváření aplikací Java Service Fabric v operačních systémech Windows, Linux a MacOS.

V tomto článku se dozvíte, jak sestavit, nasadit a ladit aplikaci Service Fabric Java pomocí Visual Studio Code.

> [!IMPORTANT]
> Aplikace Service Fabric Java je možné vyvíjet na počítačích s Windows, ale dají se nasadit jenom do clusterů Azure Linux. Ladění aplikací Java není v systému Windows podporováno.

## <a name="prerequisites"></a>Předpoklady

V tomto článku se předpokládá, že jste už nainstalovali VS Code, Service Fabric Reliable Services rozšíření pro VS Code a všechny závislosti, které jsou pro vaše vývojové prostředí potřeba. Další informace najdete v tématu [Začínáme](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Stažení ukázky
V tomto článku se používá hlasovací aplikace v [ukázkovém úložišti GitHub aplikace Service Fabric Java Application Starter](https://github.com/Azure-Samples/service-fabric-java-quickstart). 

Pokud chcete klonovat úložiště do vývojového počítače, spusťte následující příkaz z okna terminálu (příkazové okno ve Windows):

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>Otevřete aplikaci v VS Code

Otevřete VS Code.  Klikněte na ikonu Průzkumníka na **řádku aktivity** a klikněte na **Otevřít složku**, nebo na  **soubor – > otevřít složku**. Přejděte do adresáře *./Service-Fabric-Java-Quickstart/voting* ve složce, kam jste naklonováni úložiště, a pak klikněte na **OK**. Pracovní prostor by měl obsahovat stejné soubory zobrazené na snímku obrazovky níže.

![Hlasovací aplikace v jazyce Java v pracovním prostoru](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>Sestavení aplikace

1. Stisknutím klávesy (CTRL + SHIFT + p) otevřete **paletu příkazů** v vs Code.
2. Vyhledejte a vyberte příkaz **Service Fabric: sestavit aplikaci** . Výstup sestavení se odešle do integrovaného terminálu.

   ![Příkaz sestavit aplikaci v VS Code](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Nasazení aplikace do místního clusteru
Po vytvoření aplikace ji můžete nasadit do místního clusteru. 

> [!IMPORTANT]
> V počítačích s Windows není podporováno nasazení aplikací Java do místního clusteru.

1. Z **palety příkazů** vyberte **příkaz Service Fabric: nasadit aplikaci (localhost)**. Výstup procesu instalace se pošle do integrovaného terminálu.

   ![Příkaz nasadit aplikaci v VS Code](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. Po dokončení nasazení spusťte prohlížeč a otevřete Service Fabric Explorer: `http://localhost:19080/Explorer` . Měli byste vidět, že aplikace běží. To může nějakou dobu trvat, tedy pacient. 

   ![Hlasovací aplikace v Service Fabric Explorer](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. Po ověření, že je aplikace spuštěná, spusťte prohlížeč a otevřete tuto stránku: `http://localhost:8080` . Toto je webový front-end aplikace. Můžete přidat položky a kliknout na ně, abyste mohli hlasovat.

   ![Hlasovací aplikace v prohlížeči](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. Chcete-li odebrat aplikaci z clusteru, vyberte příkaz **Service Fabric: odebrat aplikaci** z **palety příkazů**. Výstup procesu odinstalace se pošle do integrovaného terminálu. Pomocí nástroje Service Fabric Explorer lze ověřit, zda byla aplikace odebrána z místního clusteru.

## <a name="debug-the-application"></a>Ladění aplikace
Při ladění aplikací v VS Code musí být aplikace spuštěná v místním clusteru. Zarážky lze následně přidat do kódu.

> [!IMPORTANT]
> Ladění aplikací Java není v počítačích s Windows podporováno.

Chcete-li připravit VotingDataService a hlasovací aplikaci pro ladění, proveďte následující kroky:

1. Aktualizujte soubor *hlasovacího/VotingApplication/VotingDataServicePkg/Code/EntryPoint. sh* .
Přidejte komentář na řádek 6 (použijte ' # ') a přidejte do dolní části souboru následující příkaz:

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. Aktualizujte soubor *hlasovacího/VotingApplication/ApplicationManifest.xml* . V elementu **StatefulService** nastavte atributy **MinReplicaSetSize** a **TargetReplicaSetSize** na hodnotu "1":
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. Kliknutím na ikonu ladění na **řádku aktivity** otevřete zobrazení ladicího programu v vs Code. V horní části zobrazení ladicího programu klikněte na ikonu ozubeného kolečka a v nabídce rozevíracího prostředí vyberte **Java** . Otevře se launch.jsv souboru. 

   ![Ikona ladění v pracovním prostoru VS Code](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. V launch.jsv souboru nastavte hodnotu portu v konfiguraci s názvem **Debug (připojit)** na **8001**. Soubor uložte.

   ![Konfigurace ladění pro launch.jsv](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. Nasaďte aplikaci do místního clusteru pomocí příkazu **Service Fabric: nasadit aplikaci (localhost)** . Ověřte, zda je aplikace spuštěna v Service Fabric Explorer. Vaše aplikace je teď připravená k ladění.

Chcete-li nastavit zarážku, proveďte následující kroky:

1. V Průzkumníkovi otevřete soubor */voting/VotingDataService/src/statefulservice/VotingDataService.Java* . Nastavte zarážku na prvním řádku kódu v `try` bloku v `addItem` metodě (řádek 80).
   
   ![Nastavit zarážku ve službě hlasovacích dat](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > Ujistěte se, že jste nastavili zarážky na spustitelných řádcích kódu. Například zarážky nastavené v deklaracích metod, `try` příkazy nebo `catch` příkazy budou vynechány ladicím programem.
2. Ladění spustíte tak, že kliknete na ikonu ladění na **řádku aktivity**, vyberete konfiguraci **ladění (připojit)** z nabídky ladění a kliknete na tlačítko spustit (zelená šipka).

   ![Ladit (připojit) konfiguraci](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. Ve webovém prohlížeči přejděte na `http://localhost:8080`. Do textového pole zadejte novou položku a klikněte na **+ Přidat**. Měla by být vybrána vaše zarážka. Pomocí panelu nástrojů ladění v horní části VS Code můžete pokračovat v provádění, krokovat přes řádky, Krokovat s vnořením do metod nebo krokovat s aktuální metodou. 
   
   ![Zarážka volání](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. Chcete-li ukončit relaci ladění, klikněte na ikonu plug-in na panelu nástrojů ladění v horní části VS Code.
   
   ![Odpojit od ladicího programu](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. Po dokončení ladění můžete použít příkaz **Service Fabric: Remove Application** k odebrání hlasovací aplikace z místního clusteru. 

## <a name="next-steps"></a>Další kroky

* Naučte [se vyvíjet a ladit C# Service Fabric aplikace pomocí vs Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
