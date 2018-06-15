---
title: Vizualizace vašeho clusteru pomocí Azure Service Fabric Explorer | Microsoft Docs
description: Service Fabric Explorer je aplikace pro kontroly a správa cloudových aplikací a uzly v clusteru s podporou Microsoft Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.assetid: c875b993-b4eb-494b-94b5-e02f5eddbd6a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2018
ms.author: mikhegn
ms.openlocfilehash: 916742d89447af4097d37b5d78e97ff86c12834c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210177"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Vizualizujte cluster pomocí Service Fabric Exploreru

Service Fabric Explorer (SFX) je nástroj open source pro kontroly a správě clusterů Azure Service Fabric. Service Fabric Explorer je aplikace pro Windows a Linux. Podpora systému MacOS tu bude brzo dostupná.

## <a name="service-fabric-explorer-download"></a>Stažení Service Fabric Exploreru

Chcete-li stáhnout Service Fabric Explorer jako desktopová aplikace pomocí následujících odkazů:

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> Verze aplikace Service Fabric Explorer může mít více nebo méně funkcí než podpora clusteru. Vám může vrátit zpět na verzi Service Fabric Explorer nasazené na clusteru pro zajištění kompatibility úplné funkce.
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>Spuštění z clusteru Service Fabric Exploreru

Service Fabric Explorer také hostovaná v clusteru Service Fabric koncový bod správy protokolu HTTP. Ke spuštění SFX ve webovém prohlížeči, přejděte do clusteru koncový bod správy protokolu HTTP z libovolného prohlížeče – například https://clusterFQDN:19080.

Pro vývojáře instalace pracovní stanice, můžete spustit Service Fabric Explorer v místním clusteru přechodem na https://localhost:19080/Explorer. Podívejte se na tomto článku [Příprava vývojového prostředí](service-fabric-get-started.md).

## <a name="connect-to-a-service-fabric-cluster"></a>Připojení ke clusteru Service Fabric
Pokud chcete připojit ke clusteru Service Fabric, budete potřebovat koncový bod správy clusterů (plně kvalifikovaný název domény nebo IP adresy) a port pro koncový bod správy protokolu HTTP (19080 ve výchozím nastavení). Například https://mysfcluster.westus.cloudapp.azure.com:19080. Zaškrtnutím políčka "Připojení k localhost" pomocí připojení k místnímu clusteru na pracovní stanici.

### <a name="connect-to-a-secure-cluster"></a>Připojení k zabezpečenému clusteru
Ke svému clusteru Service Fabric pomocí certifikátů nebo pomocí Azure Active Directory (AAD) můžete řídit přístup klienta.

Pokud se pokusíte připojit k zabezpečení clusteru, pak v závislosti na konfiguraci clusteru bude se budete muset certifikát klienta k dispozici nebo se přihlaste pomocí AAD.

## <a name="video-tutorial"></a>Videokurz

Informace o použití Service Fabric Explorer najdete v následujícím videu Microsoft Virtual Academy:

> [!NOTE]
> Toto video ukazuje, že Service Fabric Explorer hostovaný Cluster Service Fabric není verzi pro stolní počítače.
>
>

[<center><img src="./media/service-fabric-visualizing-your-cluster/SfxVideo.png" WIDTH="360" HEIGHT="244"></center>](https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=bBTFg46yC_9806218965)

## <a name="understand-the-service-fabric-explorer-layout"></a>Pochopení rozložení Service Fabric Exploreru
Pomocí stromové struktury nalevo můžete přejít pomocí Service Fabric Exploreru. Řídicí panel clusteru v kořenu stromu, obsahuje přehled clusteru, včetně shrnutí stavu uzlu a aplikace.

![Řídicí panel clusteru Service Fabric Exploreru][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>Zobrazit rozložení clusteru
Uzly v clusteru Service Fabric se umístí napříč dvourozměrná mřížku domén selhání a upgradu domény. Toto umístění zaručuje, že vaše aplikace zachovány k dispozici v případě selhání hardwaru a upgrady aplikací. Můžete zobrazit, jak aktuální cluster rozložená pomocí mapy clusteru.

![Mapa clusteru Service Fabric Exploreru][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Zobrazení aplikace a služby
Cluster obsahuje dva podstromy: jeden pro aplikace a druhý pro uzly.

Zobrazení aplikací můžete procházet logické hierarchie Service Fabric: aplikace, služby, oddíly a repliky.

V příkladu níže aplikace **Moje aplikace** se skládá ze dvou služeb **MyStatefulService** a **WebService**. Vzhledem k tomu **MyStatefulService** je stavová, obsahuje oddíl s jeden primární a dva sekundární repliky. Naopak WebSvcService je bezstavové a obsahuje jednu instanci.

![Zobrazení aplikace Service Fabric Exploreru][sfx-application-tree]

Na každé úrovni stromu hlavním podokně zobrazí příslušné informace o položce. Například se zobrazí stav a verze pro konkrétní službu.

![Podokno essentials Service Fabric Exploreru][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>Zobrazení uzlů clusteru
Zobrazení uzlu obsahuje fyzické rozložení clusteru. Pro daný uzel můžete zjistit, které aplikace mají v uzlu nasazený kód. Přesněji řečeno uvidíte, které repliky jsou aktuálně spuštěné existuje.

## <a name="actions"></a>Akce
Service Fabric Explorer nabízí rychlý způsob, jak vyvolání akce na uzly, aplikace a služby v rámci clusteru.

Chcete-li odstranit instanci aplikace, například ze stromu na levé straně zvolte aplikaci a poté zvolte **akce** > **odstranit aplikaci**.

![Odstranění aplikace v Service Fabric Exploreru][sfx-delete-application]

> [!TIP]
> Kliknutím na tlačítko se třemi tečkami vedle jednotlivých prvků můžete provádět stejné akce.
>
> Každou akci, která lze provádět pomocí Service Fabric Explorer můžete provést i pomocí prostředí PowerShell nebo rozhraní REST API, jak povolit automatizaci.
>
>

Můžete také Service Fabric Explorer pro vytvoření instancí aplikace daný typ a verze aplikace. Vyberte typ aplikace, v zobrazení stromu a pak klikněte na **vytvořit instanci aplikace** odkaz vedle verze byste chtěli v pravém podokně.

![Vytvoření instance aplikace v Service Fabric Exploreru][sfx-create-app-instance]

> [!NOTE]
> Service Fabric Explorer nepodporuje parametry, při vytváření instancí aplikace. Instance aplikace používají výchozí hodnoty parametrů.
>
>

## <a name="next-steps"></a>Další postup
* [Správu aplikací Service Fabric v sadě Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Nasazení aplikace Service Fabric pomocí prostředí PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/SfxCreateAppInstance.png