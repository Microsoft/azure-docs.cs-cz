---
title: Vizualizace clusteru pomocí Azure Service Fabric Exploreru | Dokumentace Microsoftu
description: Service Fabric Explorer je aplikace pro kontrolu a správu cloudových aplikací a uzlů v clusteru s Microsoft Azure Service Fabric.
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
ms.date: 01/24/2019
ms.author: mikhegn
ms.openlocfilehash: 8a2a1b70290f48b3189bfae2f67dd19c5ef4c9f2
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55093253"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Vizualizujte cluster pomocí Service Fabric Exploreru

Service Fabric Exploreru (SFX) je opensourcový nástroj pro kontrolu a správu clusterů Azure Service Fabric. Service Fabric Explorer je desktopová aplikace pro Windows, macOS a Linux.

## <a name="service-fabric-explorer-download"></a>Stáhnout Service Fabric Exploreru

Chcete-li stáhnout Service Fabric Explorer, jako desktopová aplikace pomocí následujících odkazů:

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> Desktopová verze Service Fabric Explorer může mít více nebo méně funkcí než podpora clusteru. Vám může vrátit zpět k verzi Service Fabric Explorer nasadí do clusteru pro zajištění kompatibilitě úplné funkce.
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>Spuštění z clusteru Service Fabric Exploreru

Service Fabric Explorer je také hostovaná v clusteru Service Fabric koncový bod správy HTTP. SFX spustit ve webovém prohlížeči, přejděte na koncový bod správy HTTP clusteru z libovolného prohlížeče – například https://clusterFQDN:19080.

Pro nastavení pracovní stanici vývojáře, můžete spustit Service Fabric Explorer v místním clusteru tak, že přejdete do https://localhost:19080/Explorer. Podívejte se na tomto článku [Příprava vývojového prostředí](service-fabric-get-started.md).

> [!NOTE]
> Pokud je váš cluster zabezpečený pomocí certifikátu podepsaného svým držitelem, že zobrazí se chybová zpráva z webového prohlížeče "Tento web není zabezpečený". Většina moderních webových prohlížečů můžete jednoduše pokračujte tak, že přepíšete upozornění. V produkčním prostředí by měl být váš cluster zabezpečený pomocí běžné jméno a certifikátu autority vydaný certifikát. 
>
>

## <a name="connect-to-a-service-fabric-cluster"></a>Připojte se ke clusteru Service Fabric
K připojení ke clusteru Service Fabric, potřebujete koncový bod správy clusterů (plně kvalifikovaný název domény nebo IP) a protokolu HTTP port koncového bodu správy (clusteru 19080 ve výchozím nastavení). Například https://mysfcluster.westus.cloudapp.azure.com:19080. Zaškrtávací políčko "Připojit k místnímu hostiteli" slouží k připojení k místnímu clusteru na pracovní stanici.

### <a name="connect-to-a-secure-cluster"></a>Připojení k zabezpečenému clusteru
Řízení přístupu klientů ke clusteru Service Fabric pomocí certifikátů nebo pomocí Azure Active Directory (AAD).

Pokud se pokusíte připojit k zabezpečenému clusteru, pak v závislosti na konfiguraci clusteru budete se muset předložit certifikát klienta nebo přihlášení pomocí AAD.

## <a name="video-tutorial"></a>Videokurz

Naučte se používat Service Fabric Exploreru, podívejte se na následující video Microsoft Virtual Academy:

> [!NOTE]
> Toto video ukazuje, že Service Fabric Explorer hostovaný v clusteru Service Fabric, ne desktopová verze.
>
>

[<center><img src="./media/service-fabric-visualizing-your-cluster/SfxVideo.png" WIDTH="360" HEIGHT="244"></center>](https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=bBTFg46yC_9806218965)

## <a name="understand-the-service-fabric-explorer-layout"></a>Principy rozložení Service Fabric Exploreru
Service Fabric Explorer můžete procházet pomocí stromu na levé straně. V kořenovém adresáři stromu řídicí panel clusteru poskytuje přehled o clusteru včetně souhrnu stavu aplikací a uzlů.

![Řídicí panel clusteru Service Fabric Exploreru][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>Zobrazit rozložení clusteru
Uzly v clusteru Service Fabric jsou umístěny v dvojrozměrné mřížky domén selhání a upgradovacími doménami. Toto umístění se zajistí, že vaše aplikace zůstanou dostupné i za přítomnosti selhání hardwaru a upgrady aplikací. Můžete zobrazit, jak se aktuální cluster rozloženy pomocí mapy clusterů.

![Mapa clusteru Service Fabric Exploreru][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Zobrazit aplikace a služby
Obsahuje dva podstromy clusteru: jeden pro aplikace a druhý pro uzly.

Zobrazení aplikací můžete použít k procházení mezi logické hierarchie Service Fabric: aplikace, služby, oddíly a repliky.

V příkladu níže použití **MyApp** se skládá ze dvou služeb **MyStatefulService** a **webová služba**. Protože **MyStatefulService** je stavový, obsahuje oddíl s jeden primární a dva sekundární repliky. Naopak WebSvcService je bezstavové a obsahuje jednu instanci.

![Zobrazení aplikace Service Fabric Exploreru][sfx-application-tree]

Jednotlivé úrovně stromu hlavním podokně se zobrazí relevantní informace o položce. Například vidíte stav a verze pro konkrétní službu.

![Podokno essentials Service Fabric Exploreru][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>Zobrazit uzly clusteru
Zobrazení uzlu obsahuje fyzické rozložení clusteru. Pro daný uzel můžete zjistit, které aplikace mají v uzlu nasazený kód. Přesněji řečeno zobrazí se replik, které jsou aktuálně spuštěné.

## <a name="actions"></a>Akce
Service Fabric Explorer nabízí rychlý způsob, jak vyvolat akce na uzly, aplikace a služby v rámci vašeho clusteru.

Odstranění instance aplikace, například aplikace vyberte ze stromu na levé straně a klikněte na tlačítko **akce** > **odstranit aplikaci**.

![Odstraňuje se aplikace v Service Fabric Exploreru][sfx-delete-application]

> [!TIP]
> Stejné akce můžete provést kliknutím na tři tečky vedle každého prvku.
>
> Každá akce, které lze provést pomocí Service Fabric Explorer lze provést také pomocí Powershellu nebo rozhraní REST API, jak povolit automatizaci.
>
>

Service Fabric Exploreru můžete také použít k vytvoření instance aplikace daný typ a verze aplikace. Vyberte typ aplikace ve stromovém zobrazení klikněte **instance aplikace vytvořit** odkaz vedle verzí byste chtěli v pravém podokně.

![Vytvoření instance aplikace v Service Fabric Exploreru][sfx-create-app-instance]

> [!NOTE]
> Service Fabric Explorer nepodporuje parametry při vytváření instancí aplikace. Instance aplikace použít výchozí hodnoty parametrů.
>
>

## <a name="event-store"></a>Událost Store
Eventstoru je funkce nabízených platformou, která poskytuje události platformy Service Fabric jsou k dispozici v Service Fabric Exploreru a prostřednictvím rozhraní REST API. Zobrazí se zobrazení snímku, co se děje ve vašem clusteru pro každou entitu například uzel, služby, aplikace a pro dotazy podle času události. Můžete také další informace o Eventstoru na [přehled Eventstoru](service-fabric-diagnostics-eventstore.md).   

![Eventstoru][sfx-eventstore]

>[!NOTE]
>Od verze 6.4 Service Fabric. Eventstoru není ve výchozím nastavení povolené a musí být povolená v šabloně resource Manageru

>[!NOTE]
>Od verze 6.4 Service Fabric. rozhraní API Eventstoru jsou dostupné jenom pro clustery Windows pouze běžící v Azure. Pracujeme na přenos tuto funkci pro Linux, stejně jako naše samostatné clustery.


## <a name="next-steps"></a>Další postup
* [Správa aplikací Service Fabric v sadě Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Nasazení aplikace Service Fabric pomocí Powershellu](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/SfxCreateAppInstance.png
[sfx-eventstore]: ./media/service-fabric-diagnostics-eventstore/eventstore.png
