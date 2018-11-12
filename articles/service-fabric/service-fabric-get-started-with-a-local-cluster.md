---
title: Nasazení a upgrade lokálně služby Azure Service Fabric | Dokumentace Microsoftu
description: Přečtěte si, jak vytvořit místní cluster Service Fabric, nasadit do něj existující aplikaci a potom tuto aplikaci upgradovat.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 60a1f6a5-5478-46c0-80a8-18fe62da17a8
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/03/2017
ms.author: ryanwi
ms.openlocfilehash: 639293a1811c35d87bd705800b8ecf62f667c0e9
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246714"
---
# <a name="get-started-with-deploying-and-upgrading-applications-on-your-local-cluster"></a>Začínáme s nasazením a upgradem aplikací v místním clusteru
Sada Azure Service Fabric SDK zahrnuje úplné místní vývojové prostředí, pomocí kterého můžete rychle začít nasazovat a spravovat aplikace v místním clusteru. V tomto článku vytvoříte místní cluster, nasadíte do něj existující aplikaci a potom ji upgradujete na novou verzi, to všechno z prostředí Windows PowerShell.

> [!NOTE]
> Tento článek vychází z předpokladu, že už máte [nastavené vývojové prostředí](service-fabric-get-started.md).
> 
> 

## <a name="create-a-local-cluster"></a>Vytvoření místního clusteru
Cluster Service Fabric tvoří sada hardwarových prostředků, na které můžete nasazovat aplikace. Většinou se cluster skládá z pěti až několika tisíc počítačů. Sada Service Fabric SDK ale obsahuje konfiguraci clusteru, která se dá spustit na jednom počítači.

Je důležité si uvědomit, že místní cluster Service Fabric není emulátor ani simulátor. Spouští stejný kód platformy, jaký najdete i v clusterech s víc počítači. Jediný rozdíl je v tom, že v něm na jednom počítači běží procesy platformy, které jsou normálně rozložené mezi pěti počítačů.

Sada SDK nabízí dva způsoby, jak vytvořit místní cluster: skript prostředí Windows PowerShell a aplikaci hlavního panelu systému Local Cluster Manager. V tomto kurzu používáme skript prostředí PowerShell.

> [!NOTE]
> Pokud jste už nasazením aplikace z Visual Studia vytvořili místní cluster, můžete tuto část přeskočit.
> 
> 

1. Jako správce spusťte nové okno prostředí PowerShell.
2. Spusťte instalační skript clusteru z této složky sady SDK:
   
    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
    ```
   
    Instalace clusteru bude chvíli trvat. Po dokončení instalace byste měli vidět výstup podobný tomuhle:
   
    ![Výstup po instalaci clusteru][cluster-setup-success]
   
    Teď můžete zkusit nasadit do clusteru aplikaci.

## <a name="deploy-an-application"></a>Nasazení aplikace
Sada Service Fabric SDK obsahuje celou řadu architektur a vývojářských nástrojů pro tvorbu aplikací. Pokud se chcete naučit vytvářet aplikace ve Visual Studiu, projděte si téma [Vytvoření první aplikace Service Fabric v sadě Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).

V tomto kurzu použijete existující ukázkovou aplikaci (s názvem WordCount), abyste se mohli zaměřit na aspekty správy platformy: nasazení, monitorování a upgrade.

1. Jako správce spusťte nové okno prostředí PowerShell.
2. Importujte modul Service Fabric SDK PowerShell.
   
    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```
3. Vytvořte adresář pro uložení aplikace, kterou stáhnete a nasadíte, třeba C:\ServiceFabric.
   
    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```
4. [Stáhněte aplikaci WordCount](https://aka.ms/servicefabric-wordcountapp) do umístění, které jste vytvořili.  Poznámka: Prohlížeč Microsoft Edge uloží soubor s příponou *.zip*.  Změňte příponu souboru na *.sfpkg*.
5. Připojte se k místnímu clusteru:
   
    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```
6. Pomocí příkazu pro nasazení v sadě SDK vytvořte novou aplikaci s názvem a cestou k balíčku aplikace.
   
    ```powershell  
   Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```
   
    Pokud všechno proběhne správně, měl by se zobrazit následující výstup:
   
    ![Nasazení aplikace do místního clusteru][deploy-app-to-local-cluster]
7. Pokud chcete vidět aplikaci v akci, spusťte prohlížeč a přejděte na adresu [http://localhost:8081/wordcount/index.html](http://localhost:8081/wordcount/index.html). Měli byste vidět tohle:
   
    ![Uživatelské rozhraní nasazené aplikace][deployed-app-ui]
   
    Aplikace WordCount je jednoduchá. Obsahuje kód JavaScript na straně klienta, který generuje náhodná „slova“ o pěti znacích a ta potom předává do aplikace přes webové rozhraní API ASP.NET. Stavová služba uchovává informace o spočítaném počtu slov. Slova se podle prvního znaku dělí do oddílů. Zdrojový kód aplikace WordCount najdete mezi [úvodními ukázkami modelu Classic](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/WordCount).
   
    Aplikace, kterou jsme nasadili, obsahuje čtyři oddíly. Slova začínající písmeny A až G se ukládají do prvního oddílu, slova začínající písmeny H až N do druhého oddílu a tak dál.

## <a name="view-application-details-and-status"></a>Zobrazení podrobností a stavu aplikace
Nasadili jsme aplikaci a teď se v prostředí PowerShell podíváme na některé podrobnosti.

1. Zadejte dotaz na všechny nasazené aplikace v clusteru:
   
    ```powershell
    Get-ServiceFabricApplication
    ```
   
    Pokud jste nasadili jenom aplikaci WordCount, zobrazí se přibližně tohle:
   
    ![Dotaz na všechny nasazené aplikace v prostředí PowerShell][ps-getsfapp]
2. Přejděte na další úroveň a zadejte dotaz na sadu služeb zahrnutých v aplikaci WordCount.
   
    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```
   
    ![Výpis seznamu služeb aplikace v prostředí PowerShell][ps-getsfsvc]
   
    Aplikace se skládá ze dvou služeb – z webového prostředí front-end a stavové služby, která spravuje slova.
3. Nakonec se podíváme na seznam oddílů služby WordCountService:
   
    ```powershell
    Get-ServiceFabricPartition 'fabric:/WordCount/WordCountService'
    ```
   
    ![Zobrazení oddílů služby v prostředí PowerShell][ps-getsfpartitions]
   
    Sada příkazů, které jste použili, stejně jako všechny příkazy Service Fabric prostředí PowerShell, jsou dostupné pro každý místní i vzdálený cluster, ke kterému se připojíte.
   
    Pokud byste ocenili víc vizuální způsob práce s clusterem, můžete použít webový nástroj Service Fabric Explorer na adrese [http://localhost:19080/Explorer](http://localhost:19080/Explorer) v prohlížeči.
   
    ![Zobrazení podrobností aplikace v Service Fabric Exploreru][sfx-service-overview]
   
   > [!NOTE]
   > Další informace o nástroji Service Fabric Explorer najdete v tématu [Vizualizace vašeho clusteru v nástroji Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
   > 
   > 

## <a name="upgrade-an-application"></a>Upgrade aplikace
Service Fabric nabízí upgrady bez výpadků díky tomu, že během zavádění v clusteru monitoruje stav aplikací. Proveďte upgrade aplikace WordCount.

Nová verze aplikace teď bude počítat jenom slova, která začínají samohláskou. Po upgradu vidíme v chování aplikace dvě změny. Zaprvé by se měla zpomalit rychlost zvyšování hodnoty, protože se počítá míň slov. Zadruhé by měla hodnota prvního oddílu postupně začít růst rychleji než ostatní oddíly, protože první oddíl obsahuje dvě samohlásky (A a E) a ostatní oddíly jenom po jedné.

1. [Stáhněte balíček WordCount verze 2](https://aka.ms/servicefabric-wordcountappv2) do stejného umístění, do kterého jste stáhli balíček verze 1.
2. Vraťte se do prostředí PowerShell a pomocí příkazu pro upgrade v sadě SDK zaregistrujte novou verzi v clusteru. Pak začněte upgradovat aplikaci fabric:/WordCount.
   
    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```
   
    Po zahájení upgradu by se měl v prostředí PowerShell zobrazit následující výstup.
   
    ![Průběh upgradu v prostředí PowerShell][ps-appupgradeprogress]
3. Během upgradu může být snazší monitorovat jeho stav v Service Fabric Exploreru. Spusťte okno prohlížeče a přejděte na adresu [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Ve stromě vlevo rozbalte položku **Aplikace**, zvolte **WordCount** a nakonec **fabric:/WordCount**. Na kartě základních informací vidíte průběh upgradu v upgradovacích doménách clusteru.
   
    ![Průběh upgradu v Service Fabric Exploreru][sfx-upgradeprogress]
   
    Během upgradu v jednotlivých doménách se provádí kontroly stavu, které ověřují správné chování aplikace.
4. Pokud pro sadu služeb v aplikaci fabric:/WordCount znovu spustíte předchozí dotaz, zjistíte, že se změnila verze WordCountService, ale verze WordCountWebService ne:
   
    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```
   
    ![Dotaz na aplikační služby po upgradu][ps-getsfsvc-postupgrade]
   
    Tento příklad ukazuje, jak Service Fabric spravuje upgrady aplikací. Dotýká se jenom sady služeb (nebo balíčků kódu/konfigurace v těchto službách), které se změnily, a díky tomu má upgrade rychlejší a spolehlivější průběh.
5. Nakonec se vraťte do prohlížeče, abyste mohli pozorovat chování nové verze aplikace. Podle očekávání se hodnoty zvyšují pomaleji a první oddíl nakonec získá o něco větší objem slov.
   
    ![Zobrazení nové verze aplikace v prohlížeči][deployed-app-ui-v2]

## <a name="cleaning-up"></a>Čištění
Před zabalením je dobré si uvědomit, že místní cluster je skutečný. Aplikace dál běží na pozadí, dokud je neodeberete.  V závislosti na povaze vašich aplikací může spuštěná aplikace spotřebovávat značné množství prostředků vašeho počítače. Při správě aplikací a clusteru máte několik možností:

1. Pokud chcete odebrat jednotlivou aplikaci a všechna její data, spusťte následující příkaz:
   
    ```powershell
    Unpublish-ServiceFabricApplication -ApplicationName "fabric:/WordCount"
    ```
   
    Nebo aplikaci odstraňte z nabídky **AKCE** v Service Fabric Exploreru nebo z kontextové nabídky zobrazení seznamu aplikací na levé straně.
   
    ![Odstranění aplikace v Service Fabric Exploreru][sfe-delete-application]
2. Po odstranění aplikace z clusteru zrušte registraci verze 1.0.0 a 2.0.0 typu aplikace WordCount. Při odstranění se z úložiště imagí v clusteru odeberou balíčky aplikace, včetně kódu a konfigurace.
   
    ```powershell
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 2.0.0
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0
    ```
   
    Nebo v Service Fabric Exploreru vyberte pro aplikaci možnost **Unprovision Type** (Zrušit zřízení typu).
3. Pokud chcete cluster zastavit, ale ponechat si data aplikací a trasování, klikněte v aplikaci na hlavním panelu systému na **Stop Local Cluster** (Zastavit místní cluster).
4. Pokud chcete cluster úplně odstranit, klikněte v aplikaci na hlavním panelu systému na možnost **Remove Local Cluster** (Odebrat místní cluster). Pokud vyberete tuto možnost, další nasazení po příštím stisknutí klávesy F5 ve Visual Studiu bude zase pomalé. Místní cluster odeberte jenom v případě, že se ho nechystáte nějakou dobu používat nebo potřebujete uvolnit prostředky.

## <a name="one-node-and-five-node-cluster-mode"></a>Režim clusteru s jedním uzlem a s pěti uzly
Při vývoji aplikací často potřebujete rychle střídat psaní kódu, ladění, změny kódu a ladění. Abyste mohli pracovat optimálněji, místní cluster může běžet ve dvou režimech: s jedním uzlem nebo s pěti uzly. Oba režimy mají své výhody. V případě clusteru s pěti uzly pracujete se skutečným clusterem. Můžete testovat scénáře převzetí služeb při selhání, pracovat s více instancemi a replikami služeb. Režim clusteru s jedním uzlem je optimalizovaný pro rychlé nasazení a registraci služeb, abyste mohli rychle ověřovat kód pomocí modulu runtime Service Fabric.

Cluster s jedním uzlem ani cluster s pěti uzly není emulátor ani simulátor. Místní vývojový cluster spouští stejný kód platformy, jaký najdete i v clusterech s více počítači.

> [!WARNING]
> Při změně režimu clusteru se aktuální cluster odebere ze systému a vytvoří se cluster nový. Data uložená v clusteru budou při změně režimu clusteru odstraněna.
> 
> 

Pokud chcete změnit režim na cluster s jedním uzlem, vyberte v nástroji Service Fabric Local Cluster Manager možnost **Přepnout režim clusteru**.

![Přepnutí režimu clusteru][switch-cluster-mode]

Nebo změňte režim clusteru pomocí prostředí PowerShell:

1. Jako správce spusťte nové okno prostředí PowerShell.
2. Spusťte instalační skript clusteru z této složky sady SDK:
   
    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster
    ```
   
    Instalace clusteru bude chvíli trvat. Po dokončení instalace byste měli vidět výstup podobný tomuhle:
   
    ![Výstup po instalaci clusteru][cluster-setup-success-1-node]

## <a name="next-steps"></a>Další postup
* Provedli jste nasazení a upgrade některých předem sestavených aplikací a teď si můžete zkusit [sestavit vlastní aplikaci v sadě Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).
* Všechny akce, které jsme v tomto článku prováděli v místním clusteru, se dají provádět i v [Azure Clusteru](service-fabric-cluster-creation-via-portal.md).
* V tomto článku jsme popisovali jenom jednoduchý upgrade. Další informace o výkonu a flexibilitě upgradů Service Fabric najdete v [dokumentaci upgradů](service-fabric-application-upgrade.md).

<!-- Images -->

[cluster-setup-success]: ./media/service-fabric-get-started-with-a-local-cluster/LocalClusterSetup.png
[extracted-app-package]: ./media/service-fabric-get-started-with-a-local-cluster/ExtractedAppPackage.png
[deploy-app-to-local-cluster]: ./media/service-fabric-get-started-with-a-local-cluster/DeployAppToLocalCluster.png
[deployed-app-ui]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-v1.png
[deployed-app-ui-v2]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-PostUpgrade.png
[sfx-app-instance]: ./media/service-fabric-get-started-with-a-local-cluster/SfxAppInstance.png
[sfx-two-app-instances-different-partitions]: ./media/service-fabric-get-started-with-a-local-cluster/SfxTwoAppInstances-DifferentPartitionCount.png
[ps-getsfapp]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFApp.png
[ps-getsfsvc]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc.png
[ps-getsfpartitions]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFPartitions.png
[ps-appupgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/PS-AppUpgradeProgress.png
[ps-getsfsvc-postupgrade]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc-PostUpgrade.png
[sfx-upgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/SfxUpgradeOverview.png
[sfx-service-overview]: ./media/service-fabric-get-started-with-a-local-cluster/sfx-service-overview.png
[sfe-delete-application]: ./media/service-fabric-get-started-with-a-local-cluster/sfe-delete-application.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[switch-cluster-mode]: ./media/service-fabric-get-started-with-a-local-cluster/switch-cluster-mode.png
