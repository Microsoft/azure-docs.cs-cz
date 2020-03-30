---
title: Vizualizace clusteru pomocí Průzkumníka azure service fabric
description: Service Fabric Explorer je aplikace pro kontrolu a správu cloudových aplikací a uzlů v clusteru Microsoft Azure Service Fabric.
author: mikkelhegn
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: mikhegn
ms.openlocfilehash: 80e3d990b6e8026c57ffff0048d0447a95529564
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258184"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Vizualizujte cluster pomocí Service Fabric Exploreru

Service Fabric Explorer (SFX) je open source nástroj pro kontrolu a správu clusterů Azure Service Fabric. Service Fabric Explorer je desktopová aplikace pro Windows, macOS a Linux.

## <a name="service-fabric-explorer-download"></a>Aplikace Service Fabric Explorer ke stažení

Pomocí následujících odkazů stáhněte aplikaci Service Fabric Explorer jako desktopovou aplikaci:

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> Desktopová verze aplikace Service Fabric Explorer může mít více nebo méně funkcí než podpora clusteru. Můžete se vrátit k verzi Aplikace Service Fabric Explorer nasazené do clusteru, abyste zajistili úplnou kompatibilitu funkcí.
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>Spuštění aplikace Service Fabric Explorer z clusteru

Aplikace Service Fabric Explorer je také hostována v koncovém bodě správy http clusteru Service Fabric. Chcete-li spustit SFX ve webovém prohlížeči, přejděte na koncový bod\/správy HTTP clusteru z libovolného prohlížeče – například https: /clusterFQDN:19080.

Pro nastavení pracovní stanice pro vývojáře můžete spustit Service Fabric https://localhost:19080/ExplorerExplorer v místním clusteru přechodem na . Podívejte se na tento článek [připravit vývojové prostředí](service-fabric-get-started.md).

> [!NOTE]
> Pokud je cluster zabezpečen certifikátem podepsaným svým držitelem, zobrazí se z webového prohlížeče chybová zpráva "Tento web není zabezpečený". Můžete jednoduše pokračovat přes většinu moderních webových prohlížečů přepsáním varování. V provozním prostředí by měl být cluster zabezpečen běžným názvem a certifikátem vydaným certifikační autoritou. 
>
>

## <a name="connect-to-a-service-fabric-cluster"></a>Připojení ke clusteru Service Fabric
Chcete-li se připojit ke clusteru Service Fabric, potřebujete koncový bod pro správu clusterů (FQDN/IP) a port koncového bodu správy HTTP (ve výchozím nastavení 19080). Například\:https //mysfcluster.westus.cloudapp.azure.com:19080. Pomocí zaškrtávacího políčka Připojit k místnímu hostiteli se můžete připojit k místnímu clusteru na pracovní stanici.

### <a name="connect-to-a-secure-cluster"></a>Připojení k zabezpečenému clusteru
Přístup klientů ke clusteru Service Fabric můžete řídit pomocí certifikátů nebo pomocí služby Azure Active Directory (AAD).

Pokud se pokusíte připojit k zabezpečenému clusteru, budete muset v závislosti na konfiguraci clusteru předložit klientský certifikát nebo se přihlásit pomocí služby AAD.

## <a name="understand-the-service-fabric-explorer-layout"></a>Principy rozložení Průzkumníka prostředků infrastruktury služeb
Můžete procházet Service Fabric Explorer pomocí stromu na levé straně. V kořenovém adresáři stromu řídicí panel clusteru poskytuje přehled clusteru, včetně souhrnu stavu aplikace a uzlu.

![Řídicí panel clusteru Aplikace Service Fabric Explorer][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>Zobrazení rozložení clusteru
Uzly v clusteru Service Fabric jsou umístěny přes dvourozměrnou mřížku domén selhání a upgradovacích domén. Toto umístění zajišťuje, že vaše aplikace zůstanou k dispozici v případě selhání hardwaru a upgradů aplikací. Pomocí mapy clusteru můžete zobrazit, jak je aktuální cluster rozložen.

![Mapa clusteru Aplikace Service Fabric Explorer][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Zobrazit aplikace a služby
Cluster obsahuje dva podstromy: jeden pro aplikace a druhý pro uzly.

Zobrazení aplikace můžete použít k procházení logické hierarchie Service Fabric: aplikace, služby, oddíly a repliky.

V níže uvedeném příkladu se aplikace **MyApp** skládá ze dvou služeb **MyStatefulService** a **WebService**. Vzhledem k tomu, **že MyStatefulService** je stavový, obsahuje oddíl s jednou primární a dvě sekundární repliky. Naproti tomu WebSvcService je bezstavová a obsahuje jednu instanci.

![Zobrazení aplikace Aplikace Service Fabric Explorer][sfx-application-tree]

Na každé úrovni stromu se v hlavním podokně zobrazují relevantní informace o položce. Můžete například zobrazit stav a verzi pro konkrétní službu.

![Podokno Základy aplikace Service Fabric Explorer][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>Zobrazení uzlů clusteru
Zobrazení uzlu obsahuje fyzické rozložení clusteru. Pro daný uzel můžete zjistit, které aplikace mají v uzlu nasazený kód. Přesněji řečeno, můžete vidět, které repliky jsou aktuálně spuštěny tam.

## <a name="actions"></a>Akce
Service Fabric Explorer nabízí rychlý způsob, jak vyvolat akce na uzly, aplikace a služby v rámci clusteru.

Chcete-li například odstranit instanci aplikace, zvolte ji ze stromu vlevo a pak zvolte **Akce** > **Odstranit aplikaci**.

![Odstranění aplikace v Aplikaci Service Fabric Explorer][sfx-delete-application]

> [!TIP]
> Stejné akce můžete provést kliknutím na tři tečky vedle každého prvku.
>
> Každou akci, kterou lze provést prostřednictvím aplikace Service Fabric Explorer, lze také provést prostřednictvím prostředí PowerShell nebo rozhraní REST API, které umožní automatizaci.
>
>

Průzkumník prostředků Service Fabric můžete také vytvořit instance aplikace pro daný typ aplikace a verzi. Ve stromovém zobrazení zvolte typ aplikace a klikněte na odkaz **Vytvořit instanci aplikace** vedle verze, kterou chcete použít, v pravém podokně.

![Vytvoření instance aplikace v Aplikaci Service Fabric Explorer][sfx-create-app-instance]

> [!NOTE]
> Aplikace Service Fabric Explorer nepodporuje parametry při vytváření instancí aplikace. Instance aplikace používají výchozí hodnoty parametrů.
>
>

## <a name="event-store"></a>Úložiště událostí
EventStore je funkce nabízená platformou, která poskytuje události platformy Service Fabric, které jsou k dispozici v aplikaci Service Fabric Explorer a prostřednictvím rozhraní REST API. Můžete zobrazit snímek zobrazení toho, co se děje ve vašem clusteru pro každou entitu, například uzel, služba, aplikace a dotaz na základě času události. Další informace o EventStore si můžete přečíst v [přehledu eventstore](service-fabric-diagnostics-eventstore.md).   

![Úložiště událostí][sfx-eventstore]

>[!NOTE]
>Od service fabric verze 6.4. EventStore není ve výchozím nastavení povolen a musí být povolen v šabloně správce prostředků.

>[!NOTE]
>Od service fabric verze 6.4. Rozhraní API úložiště událostí jsou dostupná jenom pro clustery Windows spuštěné jenom v Azure. Pracujeme na portování této funkce na Linux, stejně jako naše samostatné clustery.

## <a name="image-store-viewer"></a>Prohlížeč úložiště obrázků
Prohlížeč úložiště obrázků je funkce nabízená při použití nativního úložiště obrázků, která umožňuje zobrazit aktuální obsah úložiště obrázků a získat informace o souborech a složkách spolu s odstraněním souborů / složek.

![Mapa clusteru Aplikace Service Fabric Explorer][sfx-imagestore]

## <a name="backup-and-restore"></a>Zálohování a obnovení
Service Fabric Explorer nabízí možnost rozhraní s [zálohování a obnovení](./service-fabric-reliable-services-backup-restore.md). Chcete-li zobrazit funkce zálohování a obnovení v SFX, musí být povolen rozšířený režim.

![Povolení rozšířeného režimu][0]
 
Jsou možné následující operace:

* Vytvořte, upravte a odstraňte zásady zálohování.
* Povolte a zakažte zálohování pro aplikaci, službu nebo oddíl.
* Pozastavit a obnovit zálohování pro aplikaci, službu nebo oddíl.
* Aktivace a sledování zálohování oddílu.
* Aktivace a sledování obnovení oddílu.

Další informace o službě Zálohování a obnovení naleznete v [odkazu rozhraní REST API](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore).
## <a name="next-steps"></a>Další kroky
* [Správa aplikací Service Fabric v sadě Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Nasazení aplikace Service Fabric pomocí PowerShellu](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-dashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-map.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/sfx-application-tree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/sfx-service-essentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/sfx-delete-application.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/sfx-create-app-instance.png
[sfx-eventstore]: ./media/service-fabric-diagnostics-eventstore/eventstore.png
[sfx-imagestore]: ./media/service-fabric-visualizing-your-cluster/sfx-image-store.png
[0]: ./media/service-fabric-backuprestoreservice/advanced-mode.png