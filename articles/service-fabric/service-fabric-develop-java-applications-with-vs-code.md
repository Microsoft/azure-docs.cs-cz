---
title: Vývoj aplikací Azure Service Fabric v Javě pomocí služby Visual Studio Code | Dokumentace Microsoftu
description: Tento článek ukazuje, jak vytvářet, nasazovat a ladit aplikace Service Fabric v Javě pomocí nástroje Visual Studio Code.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 7f60371fb533526ef5bdb154d0c08dface9c0d1f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60393866"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>Vývoj aplikací Service Fabric v Javě pomocí služby Visual Studio Code

[Service Fabric Reliable Services rozšíření pro VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) umožňuje snadno vytvářet aplikace Service Fabric v Javě v operačních systémech Windows, Linux a macOS.

Tento článek popisuje, jak vytvářet, nasazovat a ladit aplikace Service Fabric v Javě pomocí nástroje Visual Studio Code.

> [!IMPORTANT]
> Aplikace Service Fabric Java mohou být vytvořeny na počítačích s Windows, ale je možné nasadit do pouze clustery Azure s Linuxem. Ladění aplikací v Javě nepodporuje Windows.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste již nainstalovali VS Code, Service Fabric Reliable Services rozšíření pro VS Code a všechny závislosti, které vyžaduje pro vaše vývojové prostředí. Další informace najdete v tématu [Začínáme](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Stažení ukázky
Hlasovací aplikace v tomto článku se používá [úložišti ukázek Githubu v Service Fabric v Javě aplikace rychlý Start](https://github.com/Azure-Samples/service-fabric-java-quickstart). 

Naklonujte úložiště do svého vývojového počítače, spusťte následující příkaz z okna terminálu (příkazové okno na Windows):

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>Otevření aplikace v nástroji VS Code

Otevřít VS Code.  Klikněte na ikonu Průzkumníka **aktivity panelu** a klikněte na tlačítko **otevřít složku**, nebo klikněte na tlačítko **soubor -> Otevřít složku**. Přejděte *./service-fabric-java-quickstart/Voting* klikněte na adresář, do složky, které jste naklonovali úložiště, pak **OK**. Pracovní prostor by měl obsahovat stejné soubory, které je znázorněno v následujícím snímku obrazovky.

![Java hlasovací aplikace vytvořené v pracovním prostoru](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>Sestavení aplikace

1. Stiskněte klávesu (kombinaci kláves Ctrl + Shift + p) Chcete-li otevřít **paletu příkazů** ve VS Code.
2. Vyhledání a výběr **Service Fabric: Sestavení aplikace** příkazu. Výstup sestavení je odeslán do integrovaného terminálu.

   ![Vytvoření příkazu aplikace v nástroji VS Code](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Nasazení aplikace do místního clusteru
Po vytvoření aplikace, nasadíte ho do místního clusteru. 

> [!IMPORTANT]
> Nasazení aplikace v Javě do místního clusteru se nepodporuje na počítačích s Windows.

1. Z **paletu příkazů**, vyberte **Service Fabric: Nasazení aplikace (Localhost) příkaz**. Výstup procesu instalace je odeslán integrovaný terminál.

   ![Nasazení aplikace příkaz ve VS Code](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. Po dokončení nasazení spusťte prohlížeč a otevřete Service Fabric Explorer: `http://localhost:19080/Explorer`. Měli byste vidět, že je aplikace spuštěná. To může trvat nějakou dobu, tak buďte prosím trpěliví. 

   ![Hlasovací aplikace v Service Fabric Exploreru](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. Po ověření, že je aplikace spuštěná, spusťte prohlížeč a otevřete tuto stránku: `http://localhost:8080`. To je webový front-end aplikace. Můžete přidat položky a kliknutím na návrhy si hlasování.

   ![Hlasovací aplikace v prohlížeči](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. Chcete-li odebrat aplikaci z clusteru, vyberte **Service Fabric: Odebrání aplikace** příkaz **paletu příkazů**. Výstup proces odinstalace je odeslán integrovaný terminál. Můžete ověřit, že aplikace byla odebrána z místního clusteru Service Fabric Explorer.

## <a name="debug-the-application"></a>Ladění aplikace
Při ladění aplikací v nástroji VS Code, musí být aplikace spuštěná v místním clusteru. Zarážky je potom možné přidat do kódu.

> [!IMPORTANT]
> Ladění aplikací v Javě není podporováno na počítačích s Windows.

Pokud chcete připravit VotingDataService a hlasovací aplikace pro ladění, proveďte následující kroky:

1. Aktualizace *Voting/VotingApplication/VotingDataServicePkg/Code/entryPoint.sh* souboru.
Okomentujte příkaz na řádku 6 (použijte '#') a na konec souboru přidejte následující příkaz:

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. Aktualizace *Voting/VotingApplication/ApplicationManifest.xml* souboru. Nastavte **MinReplicaSetSize** a **TargetReplicaSetSize** atributů na hodnotu "1" **StatefulService** element:
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. Klikněte na tlačítko ikona ladit ve **aktivity panelu** otevřete zobrazení ladicího programu v nástroji VS Code. Klikněte na ikonu ozubeného kola v horní části zobrazení ladicího programu a vyberte **Java** z rozevírací nabídky prostředí. Otevře se soubor Launch.js. 

   ![Ladění ikony v pracovním prostoru VS Code](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. V souboru launch.json, nastavte hodnotu portu v konfiguraci s názvem **ladění (připojit)** k **8001**. Uložte soubor.

   ![Konfigurace ladění pro souboru launch.json](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. Nasazení aplikace do místního clusteru pomocí **Service Fabric: Nasazení aplikace (Localhost)** příkazu. Ověřte, zda je aplikace spuštěna v Service Fabric Exploreru. Vaše aplikace je teď připravený k ladění.

Pokud chcete nastavit zarážku, proveďte následující kroky:

1. V Průzkumníku, otevřete */Voting/VotingDataService/src/statefulservice/VotingDataService.java* souboru. Nastavit zarážku na prvním řádku kódu v `try` blokovat `addItem` – metoda (řádek 80).
   
   ![Nastavení zarážky v hlasování datové služby](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > Zajistěte, aby že nastavení zarážek na spustitelný soubor řádků kódu. Například zarážky nastavené v deklaracích metod `try` příkazy, nebo `catch` příkazy budou chybět ladicím programem.
2. Ke spuštění, ladění, klikněte na tlačítko ikona ladit ve **aktivity panelu**, vyberte **ladění (připojit)** konfigurace z menu ladit a klikněte na tlačítko pro spuštění (zelená šipka).

   ![Ladění (připojit) konfigurace](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. Ve webovém prohlížeči přejděte na `http://localhost:8080`. Zadejte novou položku do textového pole a klikněte na tlačítko **+ přidat**. By měl dosažení vaše zarážky. Ladění nástrojů v horní části stránky VS Code můžete pokračovat v provádění, krok přes řádky, krokování s vnořením do metody, nebo krok mimo aktuální metoda. 
   
   ![Dosažení zarážky](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. Chcete-li ukončit relaci ladění, klikněte na ikonu moduly na panelu nástrojů ladění v horní části stránky VS Code.
   
   ![Odpojit od ladicího programu](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. Jakmile budete hotovi, ladění, můžete použít **Service Fabric: Odebrat aplikaci** příkazu odeberte hlasovací aplikace v místním clusteru. 

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [vyvíjet a ladit C# aplikace Service Fabric pomocí VS Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
