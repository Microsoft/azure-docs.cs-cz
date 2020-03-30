---
title: Vývoj aplikací Java s visual studio kód
description: Tento článek ukazuje, jak vytvářet, nasazovat a ladit java service fabric aplikace pomocí visual studio kód.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 999dbb8c36c4e0413f287b2a73cf39ab4acd15f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610042"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>Vývoj aplikací Java Service Fabric pomocí kódu Visual Studia

Rozšíření [Service Fabric Reliable Services pro Kód VS](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) usnadňuje vytváření aplikací Java Service Fabric v operačních systémech Windows, Linux a macOS.

Tento článek ukazuje, jak vytvořit, nasadit a ladit java service fabric aplikace pomocí visual studio kód.

> [!IMPORTANT]
> Aplikace Service Fabric Java lze vyvíjet na počítačích s Windows, ale lze je nasadit jenom do clusterů Azure Linux. Ladění aplikací jazyka Java není v systému Windows podporováno.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste již nainstalovali VS Kód, service fabric spolehlivé služby rozšíření pro VS kód a všechny závislosti potřebné pro vývojové prostředí. Další informace najdete v [tématu Začínáme](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Stažení ukázky
Tento článek používá hlasovací aplikaci v [aplikaci Service Fabric Java rychlý start ukázkový úložiště GitHub](https://github.com/Azure-Samples/service-fabric-java-quickstart). 

Chcete-li naklonovat úložiště do vývojového počítače, spusťte následující příkaz z okna terminálu (příkazové okno v systému Windows):

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>Otevření aplikace v kódu VS

Otevřete vs kód.  Klepněte na ikonu Průzkumníka na **panelu aktivit** a klepněte na **příkaz Otevřít složku**nebo klepněte na příkaz Soubor **-> Otevřít složku**. Přejděte do adresáře *./service-fabric-java-quickstart/Voting* ve složce, do které jste úložiště naklonovali, a pak klepněte na tlačítko **OK**. Pracovní prostor by měl obsahovat stejné soubory zobrazené na následujícím snímku obrazovky.

![Java hlasovací aplikace v pracovním prostoru](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>Sestavení aplikace

1. Stisknutím klávesy (Ctrl + Shift + p) otevřete **paletu příkazů** v kódu VS.
2. Vyhledejte a vyberte příkaz **Service Fabric: Build Application** . Výstup sestavení je odeslán do integrovaného terminálu.

   ![Příkaz k sestavení aplikace v kódu VS](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Nasazení aplikace do místního clusteru
Po vytvoření aplikace ji můžete nasadit do místního clusteru. 

> [!IMPORTANT]
> Nasazení aplikací Java do místního clusteru není v počítačích se systémem Windows podporováno.

1. V **paletě příkazů**vyberte **příkaz Service Fabric: Deploy Application (Localhost).** Výstup instalačního procesu je odeslán do integrovaného terminálu.

   ![Příkaz nasazení aplikace v kódu VS](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. Po dokončení nasazení spusťte prohlížeč a otevřete Service Fabric Explorer: `http://localhost:19080/Explorer`. Měli byste vidět, že aplikace je spuštěna. To může nějakou dobu trvat, takže buďte trpěliví. 

   ![Aplikace pro hlasování v průzkumníku infrastruktury služby](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. Po ověření, že je aplikace spuštěná, spusťte `http://localhost:8080`prohlížeč a otevřete tuto stránku: . Toto je webový front-end aplikace. Můžete přidat položky a klikněte na ně hlasovat.

   ![Hlasovací aplikace v prohlížeči](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. Chcete-li aplikaci odebrat z clusteru, vyberte příkaz **Service Fabric: Remove Application** z **palety příkazů**. Výstup procesu odinstalace je odeslán do integrovaného terminálu. Pomocí aplikace Service Fabric Explorer můžete ověřit, zda byla aplikace odebrána z místního clusteru.

## <a name="debug-the-application"></a>Ladění aplikace
Při ladění aplikací v kódu VS musí být aplikace spuštěna v místním clusteru. Zarážky pak mohou být přidány do kódu.

> [!IMPORTANT]
> Ladění aplikací jazyka Java není v počítačích se systémem Windows podporováno.

Chcete-li připravit službu VotingDataService a aplikaci Hlasování pro ladění, proveďte následující kroky:

1. Aktualizujte soubor *Hlasování/HlasováníApplication/VotingDataServicePkg/Code/entryPoint.sh.*
Zakomentujte příkaz na řádku 6 (použijte '#') a přidejte následující příkaz do dolní části souboru:

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. Aktualizujte soubor *Hlasování/HlasováníAplikace/ApplicationManifest.xml.* Nastavte atributy **MinReplicaSetSize** a **TargetReplicaSetSize** na "1" v elementu **StavfulService:**
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. Kliknutím na ikonu Ladění na **panelu aktivit** otevřete zobrazení ladicího programu v kódu VS. Klikněte na ikonu ozubeného kola v horní části zobrazení ladicího programu a v rozbalovací nabídce prostředí vyberte **Javu.** Otevře se soubor launch.json. 

   ![Ikona ladění v pracovním prostoru kódu VS](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. V souboru launch.json nastavte hodnotu portu v konfiguraci s názvem **Ladění (Připojit)** na **8001**. Uložte soubor.

   ![Konfigurace ladění pro soubor launch.json](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. Nasaďte aplikaci do místního clusteru pomocí příkazu **Service Fabric: Deploy Application (Localhost).** Ověřte, zda je aplikace spuštěna v aplikaci Service Fabric Explorer. Aplikace je nyní připravena k odladění.

Chcete-li nastavit zarážku, proveďte následující kroky:

1. V Průzkumníkovi otevřete soubor */Voting/VotingDataService/src/stavfulservice/VotingDataService.java.* Nastavte zarážku na prvním `try` řádku kódu `addItem` v bloku v metodě (řádek 80).
   
   ![Nastavení zarážky ve službě Data Hlasování](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > Ujistěte se, že jste nastavili zarážky na spustitelných řádcích kódu. Například zarážky nastavené na `try` deklaraci `catch` metody, příkazy nebo příkazy budou vynechány ladicím programem.
2. Chcete-li začít ladit, klepněte na ikonu Ladění na **panelu aktivit**, vyberte konfiguraci **ladění (Připojit)** z nabídky ladění a klepněte na tlačítko spustit (zelená šipka).

   ![Konfigurace ladění (připojit)](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. Ve webovém prohlížeči přejděte na `http://localhost:8080`. Do textového pole zadejte novou položku a klepněte na **tlačítko + Přidat**. Vaše zarážka by měla být přístupů. Můžete použít ladicí panel nástrojů v horní části Kódu VS pokračovat v provádění, krok ování řádky, krok do metody nebo krok z aktuální metody. 
   
   ![Zarážka přístupu](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. Chcete-li ukončit relaci ladění, klepněte na ikonu plug na panelu nástrojů Ladění v horní části kódu VS.
   
   ![Odpojit od ladicího programu](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. Po dokončení ladění můžete pomocí příkazu Service **Fabric: Remove Application** odebrat aplikaci Hlasování z místního clusteru. 

## <a name="next-steps"></a>Další kroky

* Naučte se [vyvíjet a ladit aplikace Jazyka C# Service Fabric pomocí kódu VS](./service-fabric-develop-csharp-applications-with-vs-code.md).
