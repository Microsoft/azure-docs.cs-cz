---
title: Vytvoření spolehlivé služby Azure Service Fabric pomocí jazyka C#
description: Vytvoření, nasazení a ladění aplikace Reliable Services postavené na Azure Service Fabric pomocí sady Visual Studio.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: c3655b7b-de78-4eac-99eb-012f8e042109
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/14/2018
ms.author: ryanwi
ms.openlocfilehash: 7e64bc34f5c39edaf87cc732d7c4702655df0e3e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212666"
---
# <a name="create-your-first-c-service-fabric-stateful-reliable-services-application"></a>Vytvoření první stavové aplikace Reliable Services pro Service Fabric v jazyce C#

Zjistěte, jak nasadit první aplikaci Azure Service Fabric pro .NET v systému Windows během několika minut. Jakmile budete hotovi, budete mít místní cluster se spuštěnou aplikací Reliable Services.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte [nastavené vývojové prostředí](service-fabric-get-started.md). Tento proces zahrnuje instalaci sady Service Fabric SDK a sady Visual Studio 2017 nebo 2015.

## <a name="create-the-application"></a>Vytvoření aplikace

1. Spusťte sadu Visual Studio jako správce.

2. Vytvořte nový projekt stisknutím Ctrl+Shift+N.

3. V dialogovém okně **Nový projekt** vyberte **Cloud** > **Aplikace Service Fabric**.

4. Pojmenujte aplikaci **MyApplication**. Pak vyberte **OK**.

   ![Dialogové okno Nový projekt v sadě Visual Studio][1]

5. V dalším dialogovém okně můžete vytvořit jakýkoli typ aplikace Service Fabric. Pro účely tohoto rychlého startu zvolte **.Net Core 2.0** > **Stavová služba**.

6. Pojmenujte službu **MyStatefulService**. Pak vyberte **OK**.

    ![Dialogové okno Nová služba v sadě Visual Studio][2]

    Sada Visual Studio vytvoří projekt aplikace a projekt stavové služby. Pak je zobrazí v Průzkumníku řešení.

    ![Průzkumník řešení po vytvoření aplikace se stavovou službou][3]

    Projekt aplikace (**MyApplication**) neobsahuje žádný kód. Odkazuje ale na sadu projektů služeb. Obsahuje také tři další typy obsahu:

    * **Profily publikování**  
    Profily pro nasazování do různých prostředí.

    * **Skripty**  
    Skripty PowerShellu pro nasazení nebo upgrade vaší aplikace.

    * **Definice aplikace**  
Zahrnuje soubor ApplicationManifest.xml ve složce *ApplicationPackageRoot*, který popisuje složení aplikace. Ve složce *ApplicationParameters* se nachází související soubory parametrů aplikace, které můžete použít k zadání parametrů specifických pro prostředí. Sada Visual Studio vybere soubor parametrů aplikace, který je zadaný v přidruženém profilu publikování.
    
Přehled obsahu projektu služby najdete v tématu [Začínáme se službami Reliable Services](service-fabric-reliable-services-quick-start.md).

## <a name="deploy-and-debug-the-application"></a>Nasazení a ladění aplikace

Když teď máte aplikaci, můžete ji pomocí následujících kroků spustit, nasadit a ladit.

1. Stisknutím klávesy F5 v sadě Visual Studio aplikaci nasadíte pro ladění.

    >[!NOTE]
    >Při prvním místním spuštění a nasazení aplikace sada Visual Studio vytvoří místní cluster pro účely ladění. To může nějakou dobu trvat. Stav vytváření clusteru se zobrazí v okně výstupu sady Visual Studio.

    Až bude cluster připravený, aplikace pro správu na hlavním panelu systému místního clusteru, která je součástí sady SDK, zobrazí upozornění.
    
    ![Upozornění na hlavním panelu systému místního clusteru][4]

    Po spuštění aplikace sada Visual Studio automaticky zobrazí Prohlížeč diagnostických událostí, ve kterém uvidíte výstup trasování ze služeb.
    
    ![Prohlížeč diagnostických událostí][5]

    >[!NOTE]
    >Události by se měly v Prohlížeči diagnostických událostí začít sledovat automaticky. Pokud potřebujete Prohlížeč diagnostických událostí nakonfigurovat ručně, otevřete nejprve soubor `ServiceEventSource.cs`, který se nachází v projektu **MyStatefulService**. Zkopírujte hodnotu atributu `EventSource` v horní části třídy `ServiceEventSource`. V následujícím příkladu má zdroj událostí název `"MyCompany-MyApplication-MyStatefulService"`, který ve vaší situaci může být jiný.
>
    >![Vyhledání názvu zdroje události služby][service-event-source-name]


2. Dále otevřete dialogové okno **Zprostředkovatelé Trasování událostí pro Windows**. Pak vyberte ikonu ozubeného kola na kartě **Diagnostické události**. Vložte zkopírovaný název zdroje událostí do vstupního pole **Zprostředkovatelé Trasování událostí pro Windows**. Pak vyberte tlačítko **Použít**. Tím automaticky spustí trasování událostí.

    ![Nastavení názvu zdroje diagnostických událostí][setting-event-source-name]

    Nyní by se události měly zobrazovat v okně Diagnostické události.

    Šablona stavové služby uvádí hodnotu čítače, která se zvyšuje v metodě `RunAsync` v souboru **MyStatefulService.cs**.

3. Pokud některou událost rozbalíte, zobrazí se další podrobnosti, včetně uzlu, na kterém kód běží. V tomto případě je to uzel **\_Node\_0**, to se ale může na vašem počítači lišit.
   
    ![Podrobnosti v prohlížeči diagnostických událostí][6]

4. Místní cluster obsahuje pět uzlů hostovaných na jednom počítači. V produkčním prostředí je každý uzel hostovaný na odlišném fyzickém nebo virtuálním počítači. Abyste napodobili situaci, kdy počítač přestane pracovat, zatímco na něm spouštíte ladící program sady Visual Studio, zastavte jeden z uzlů místního clusteru.

5. V okně **Průzkumník řešení** otevřete soubor **MyStatefulService.cs**. 

6. Vyhledejte metodu `RunAsync` a pak na prvním řádku metody nastavte zarážku.

    ![Zarážka v metodě RunAsync stavové služby][7]

7. Spusťte nástroj Service Fabric Explorer kliknutím pravým tlačítkem na aplikaci **Local Cluster Manager** na hlavním panelu systému a výběrem možnosti **Manage Local Cluster** (Správa místního clusteru).

    ![Spuštění nástroje Service Fabric Explorer z aplikace Local Cluster Manager][systray-launch-sfx]

    [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) nabízí vizuální znázornění clusteru. To zahrnujte sadu aplikací, které jsou v něm nasazené, a sadu fyzických uzlů, které ho tvoří.

8. V levém podokně rozbalte **Cluster** > **Uzly** a vyhledejte uzel, na kterém běží váš kód. Pak simulujte restartování počítače kliknutím na **Akce** > **Deaktivovat (restartovat)**.

    ![Zastavení uzlu v Service Fabric Exploreru][sfx-stop-node]

    Na okamžik byste měli zahlédnout, jak Visual Studio dojde k vaší zarážce a výpočet probíhající na jednom uzlu plynule převezme jiný uzel.

9. Dále se vraťte do prohlížeče diagnostických událostí a podívejte se na zprávy. Hodnota čítače pořád roste, i když události ve skutečnosti přicházejí z jiného uzlu.

    ![Prohlížeč diagnostických událostí po převzetí služeb při selhání][diagnostic-events-viewer-detail-post-failover]

## <a name="clean-up-the-local-cluster-optional"></a>Vyčištění místního clusteru (volitelné)

Pamatujte, že tento místní cluster je skutečný. Při zastavení ladicího programu se odebere instance aplikace a zruší se registrace typu aplikace. Cluster ale dál běží na pozadí. Až budete připraveni zastavit místní cluster, máte několik možností.

### <a name="keep-application-and-trace-data"></a>Zachování dat aplikace a trasování

Vypněte cluster kliknutím pravým tlačítkem na aplikaci **Local Cluster Manager** na hlavním panelu systému a výběrem možnosti **Stop Local Cluster** (Zastavit místní cluster).

### <a name="delete-the-cluster-and-all-data"></a>Odstranění clusteru a veškerých dat

Odeberte cluster kliknutím pravým tlačítkem na aplikaci **Local Cluster Manager** na hlavním panelu systému. Pak zvolte **Remove Local Cluster** (Odebrat místní cluster). 

Pokud zvolíte tuto možnost, sada Visual Studio cluster znovu nasadí při příštím spuštění aplikace. Zvolte tuto možnost v případě, že se místní cluster nechystáte nějakou dobu používat nebo potřebujete uvolnit prostředky.

## <a name="next-steps"></a>Další kroky
Další informace o [Reliable Services](service-fabric-reliable-services-introduction.md).
<!-- Image References -->

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog.png
[2]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
[3]: ./media/service-fabric-create-your-first-application-in-visual-studio/solution-explorer-stateful-service-template.png
[4]: ./media/service-fabric-create-your-first-application-in-visual-studio/local-cluster-manager-notification.png
[5]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer.png
[6]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail.png
[7]: ./media/service-fabric-create-your-first-application-in-visual-studio/runasync-breakpoint.png
[sfx-stop-node]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-deactivate-node.png
[systray-launch-sfx]: ./media/service-fabric-create-your-first-application-in-visual-studio/launch-sfx.png
[diagnostic-events-viewer-detail-post-failover]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail-post-failover.png
[sfe-delete-application]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-delete-application.png
[switch-cluster-mode]: ./media/service-fabric-create-your-first-application-in-visual-studio/switch-cluster-mode.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[service-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/event-source-attribute-value.png
[setting-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/setting-event-source-name.png
[switch-cluster-mode]: ./media/service-fabric-create-your-first-application-in-visual-studio/switch-cluster-mode.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[service-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/event-source-attribute-value.png
[setting-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/setting-event-source-name.png
