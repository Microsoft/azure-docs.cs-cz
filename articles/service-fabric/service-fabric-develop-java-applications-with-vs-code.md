---
title: Vývoj aplikací Java Azure Service Fabric pomocí Visual Studio Code | Microsoft Docs
description: Tento článek ukazuje, jak pro vytváření, nasazení a ladění aplikací Java Service Fabric pomocí Visual Studio Code.
services: service-fabric
documentationcenter: .net
author: JimacoMS
manager: timlt
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: v-jamebr
ms.openlocfilehash: 54c94c50f6292694e947d97a10fd6976c14e19df
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37115924"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>Vývoj aplikací Java Service Fabric pomocí Visual Studio Code

[Rozšíření Service Fabric spolehlivé služby pro VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) usnadňuje vytváření aplikací Java Service Fabric v operačních systémech Windows, Linux a systému macOS.

Tento článek ukazuje, jak vytvořit, nasadit a ladit aplikace Java Service Fabric pomocí Visual Studio Code.

> [!IMPORTANT]
> Aplikace Service Fabric Java mohou být vytvořeny na počítače s Windows, ale lze nasadit do Azure Linux clusterů pouze. Ladění aplikací Java není podporována v systému Windows.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste již nainstalovali VS Code, Service Fabric spolehlivé služby rozšíření pro VS Code a všechny závislosti požadované pro vývojové prostředí. Další informace najdete v tématu [Začínáme](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Stažení ukázky
Tento článek používá aplikace Voting [úložiště GitHub ukázka rychlý Start aplikace Service Fabric Java](https://github.com/Azure-Samples/service-fabric-java-quickstart). 

Klonovat úložiště na vývojovém počítači, spusťte následující příkaz z okna terminálu (příkazové okno v systému Windows):

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>Otevřete aplikaci v produktu VS Code

Kód Open VS.  Klikněte na ikonu Explorer v **aktivity panelu** a klikněte na tlačítko **otevřít složku**, nebo klikněte na tlačítko **souboru -> Otevřít složku**. Přejděte na *./service-fabric-java-quickstart/Voting* adresář, do složky, které jste naklonovali úložiště, pak klikněte na tlačítko **OK**. Pracovní prostor by měl obsahovat stejné soubory, které ukazuje následující snímek obrazovky.

![Java hlasovací aplikace v pracovním prostoru](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>Sestavení aplikace

1. Stiskněte klávesu (Ctrl + Shift + p) Chcete-li otevřít **příkaz palety** v produktu VS Code.
2. Vyhledejte a vyberte **Service Fabric: sestavení aplikace** příkaz. Výstup sestavení je odeslán integrované terminálu.

   ![Vytvoření příkazu aplikace v produktu VS Code](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Nasazení aplikace na místním clusteru
Po vytvoření aplikace můžete nasadit na místní cluster. 

> [!IMPORTANT]
> Na počítače s Windows nepodporuje nasazení aplikací v jazyce Java k místnímu clusteru.

1. Z **příkaz palety**, vyberte **Service Fabric: příkaz nasazení aplikace (Localhost)**. Výstup tohoto procesu instalace je odeslána integrované terminálu.

   ![Nasazení příkaz aplikace v produktu VS Code](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. Po dokončení nasazení se spustí prohlížeč a otevřete Service Fabric Explorer: http://localhost:19080/Explorer. Měli byste vidět, že je aplikace spuštěna. To může trvat delší dobu, tak buďte pacienta. 

   ![Hlasovací aplikaci v Service Fabric Exploreru](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. Jakmile ověříte, že je aplikace spuštěna, spusťte prohlížeč a otevřete tuto stránku: http://localhost:8080. Toto je webové aplikace front-endu. Můžete přidat položky a klikněte na jejich ke hlasování.

   ![Hlasovací aplikaci v prohlížeči](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. Lze aplikaci odebrat z clusteru, vyberte **Service Fabric: Odeberte aplikaci** příkaz **příkaz palety**. Výstup proces odinstalace je odeslána integrované terminálu. Můžete ověřit, že aplikace má odebraná z místního clusteru Service Fabric Exploreru.

## <a name="debug-the-application"></a>Ladění aplikace
Při ladění aplikace v produktu VS Code, musí být aplikace spuštěna v místním clusteru. Zarážky můžete pak přidá do kódu.

> [!IMPORTANT]
> Na počítače s Windows nepodporuje ladění aplikací v jazyce Java.

Příprava VotingDataService a Voting aplikace pro ladění, proveďte následující kroky:

1. Aktualizace *Voting/VotingApplication/VotingDataServicePkg/Code/entryPoint.sh* souboru.
Komentář příkaz na řádku 6 (použijte '#') a přidejte do spodní části souboru následující příkaz:

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. Aktualizace *Voting/VotingApplication/ApplicationManifest.xml* souboru. Nastavte **MinReplicaSetSize** a **TargetReplicaSetSize** atributy na 1, v **StatefulService** element:
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. Klikněte na ikonu ladění v **aktivity panelu** k otevření zobrazení ladicího programu v produktu VS Code. Klikněte na ikonu ozubené kolečko v horní části zobrazení ladicího programu a vyberte **Java** z rozevírací nabídky prostředí. Soubor launch.json se otevře. 

   ![Ladění ikona v prostoru kód VS](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. V souboru launch.json, nastavte hodnotu portu v konfiguraci s názvem **ladění (připojit)** k **8001**. Uložte soubor.

   ![Konfiguraci ladění pro launch.json](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. Nasazení aplikace na místním clusteru pomocí **Service Fabric: nasazení aplikace (Localhost)** příkaz. Ověřte, že je aplikace spuštěna v Service Fabric Exploreru. Aplikace je nyní připraven k ladit.

Pokud chcete nastavit zarážky, proveďte následující kroky:

1. V Průzkumníku, otevřete */Voting/VotingDataService/src/statefulservice/VotingDataService.java* souboru. Nastavit zarážky první řádek kódu `try` blokovat `addItem` – metoda (řádku 80).
   
   ![Nastavení zarážek v hlasování Data Service](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > Ujistěte se, že můžete nastavit zarážky v spustitelné řádků kódu. Například zarážky nastavit na metoda deklarace `try` příkazy, nebo `catch` příkazy bude provedena pomocí ladicího programu.
2. Pokud chcete začít, ladění, klikněte na ikonu ladění v **aktivity panelu**, vyberte **ladění (připojit)** konfigurace z nabídky ladění a klikněte na tlačítko spustit (zelenou šipku).

   ![Ladění (připojení) konfigurace](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. Ve webovém prohlížeči, přejděte na http://localhost:8080. Zadejte novou položku do textového pole a klikněte na **+ přidat**. Vaší zarážce by měl být přístupů. Ladění nástrojů v horní části VS Code slouží k pokračování v provádění, krok přes řádky, krokování s vnořením metod, nebo krok mimo aktuální metoda. 
   
   ![Stiskněte tlačítko zarážek](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. Pokud chcete ukončit relaci ladění, klikněte na ikonu moduly na ladění nástrojů v horní části VS Code.
   
   ![Odpojení od ladicí program](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. Pokud jste dokončili, ladění, můžete použít **Service Fabric: odebrat aplikace** příkazu hlasovací aplikaci odebrat z vaší místní cluster. 

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [vývoji a ladění aplikací Service Fabric C# s VS Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
