---
title: Vizualizace clusteru pomocí Azure Service Fabric Explorer
description: Service Fabric Explorer je aplikace pro kontrolu a správu cloudových aplikací a uzlů v clusteru Microsoft Azure Service Fabric.
author: mikkelhegn
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: mikhegn
ms.openlocfilehash: 5c1a7de386baeb4b89fd12bd89236ea2e0348a57
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91357212"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Vizualizujte cluster pomocí Service Fabric Exploreru

Service Fabric Explorer (SFX) je open source nástroj pro kontrolu a správu clusterů Azure Service Fabric. Service Fabric Explorer je desktopová aplikace pro Windows, macOS a Linux.

## <a name="service-fabric-explorer-download"></a>Stažení Service Fabric Explorer

Pomocí následujících odkazů stáhněte Service Fabric Explorer jako desktopovou aplikaci:

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> Verze Service Fabric Explorer pro stolní počítače může mít více nebo méně funkcí než podpora clusteru. K zajištění plné kompatibility funkcí se můžete vrátit k verzi Service Fabric Explorer nasazené do clusteru.
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>Spuštění Service Fabric Explorer z clusteru

Service Fabric Explorer se taky hostuje v koncovém bodě správy HTTP clusteru Service Fabric. Pokud chcete spustit SFX ve webovém prohlížeči, přejděte ke koncovému bodu správy HTTP clusteru z libovolného prohlížeče – například https: \/ /clusterFQDN: 19080.

Pro nastavení pracovní stanice pro vývojáře můžete Service Fabric Explorer spustit v místním clusteru, a to tak, že přejdete na https://localhost:19080/Explorer . V tomto článku si [Připravte vývojové prostředí](service-fabric-get-started.md).

> [!NOTE]
> Pokud je váš cluster zabezpečený certifikátem podepsaným svým držitelem, zobrazí se chybová zpráva z webového prohlížeče "Tato lokalita není zabezpečená". Můžete jednoduše pokračovat ve většině moderních webových prohlížečů, a to přepsáním upozornění. V produkčním prostředí by měl být cluster zabezpečený pomocí běžného názvu a certifikátu vystaveného certifikační autoritou. 
>
>

## <a name="connect-to-a-service-fabric-cluster"></a>Připojení ke clusteru Service Fabric
Pokud se chcete připojit ke clusteru Service Fabric, budete potřebovat koncový bod správy clusterů (FQDN/IP) a port HTTP Management Endpoint (standardně 19080). Například https \: //mysfcluster.westus.cloudapp.Azure.com:19080. Pomocí zaškrtávacího políčka připojit k localhost se můžete připojit k místnímu clusteru v pracovní stanici.

### <a name="connect-to-a-secure-cluster"></a>Připojení k zabezpečenému clusteru
Přístup klienta k vašemu Service Fabric clusteru můžete řídit pomocí certifikátů nebo pomocí Azure Active Directory (AAD).

Pokud se pokusíte připojit k zabezpečenému clusteru, pak v závislosti na konfiguraci clusteru budete muset předložit klientský certifikát nebo se přihlásit pomocí AAD.

## <a name="understand-the-service-fabric-explorer-layout"></a>Pochopení Service Fabric Explorerho rozložení
Můžete procházet Service Fabric Explorer pomocí stromu na levé straně. V kořenovém adresáři stromu poskytuje řídicí panel clusteru Přehled vašeho clusteru, včetně souhrnu stavu aplikace a uzlu.

![Řídicí panel clusteru Service Fabric Explorer][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>Zobrazit rozložení clusteru
Uzly v clusteru Service Fabric jsou umístěny v dvojrozměrné mřížce domén selhání a upgradovacích domén. Toto umístění zajišťuje, aby vaše aplikace zůstaly k dispozici v případě selhání hardwaru a upgradování aplikací. Způsob, jakým je aktuální cluster rozložen, můžete zobrazit pomocí mapy clusteru.

![Service Fabric Explorer mapa clusteru][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Zobrazit aplikace a služby
Cluster obsahuje dva podstromy: jeden pro aplikace a druhý pro uzly.

Zobrazení aplikace můžete použít k procházení logických hierarchií Service Fabric: aplikace, služby, oddíly a repliky.

V následujícím příkladu se aplikace **Mojeapl** skládá ze dvou služeb, **MyStatefulService** a **WebService**. Vzhledem k tomu, že **MyStatefulService** je stavový, zahrnuje oddíl s jednou primární a dvěma sekundárními replikami. Naproti tomu WebSvcService je Bezstavová a obsahuje jednu instanci.

![Zobrazení aplikace Service Fabric Explorer][sfx-application-tree]

Na každé úrovni stromu se v hlavním podokně zobrazují relevantní informace o položce. Můžete například zobrazit stav a verzi konkrétní služby.

![Podokno Essentials Service Fabric Explorer][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>Zobrazit uzly clusteru
Zobrazení uzlu obsahuje fyzické rozložení clusteru. Pro daný uzel můžete zjistit, které aplikace mají v uzlu nasazený kód. Přesněji řečeno, vidíte, na kterých replikách aktuálně běží.

## <a name="actions"></a>Akce
Service Fabric Explorer nabízí rychlý způsob, jak vyvolat akce na uzlech, aplikacích a službách v rámci vašeho clusteru.

Pokud chcete například odstranit instanci aplikace, zvolte aplikaci ze stromu na levé straně a pak zvolte **Akce**  >  **Odstranit aplikaci**.

![Odstranění aplikace v Service Fabric Explorer][sfx-delete-application]

> [!TIP]
> Stejné akce můžete provést kliknutím na tři tečky vedle každého prvku.
>
> Všechny akce, které lze provést prostřednictvím Service Fabric Explorer lze také provést prostřednictvím prostředí PowerShell nebo REST API, aby bylo možné povolit automatizaci.
>
>

Můžete také použít Service Fabric Explorer k vytvoření instancí aplikace pro daný typ a verzi aplikace. Ve stromovém zobrazení zvolte typ aplikace a pak klikněte na odkaz **vytvořit instanci aplikace** vedle požadované verze v pravém podokně.

![Vytvoření instance aplikace v Service Fabric Explorer][sfx-create-app-instance]

> [!NOTE]
> Service Fabric Explorer nepodporuje parametry při vytváření instancí aplikace. Instance aplikace používají výchozí hodnoty parametrů.
>
>

## <a name="event-store"></a>Úložiště událostí
Eventstoru je funkce nabízená platformou, která poskytuje Service Fabric události platformy dostupné v Service Fabric Explorer a REST API. Pro každou entitu, např. Node, službu, aplikace a dotaz na základě času události, se můžete podívat na to, co se ve vašem clusteru chystá. Další informace o Eventstoru najdete v tématu [Přehled eventstoru](service-fabric-diagnostics-eventstore.md).   

![Snímek obrazovky se zobrazí v podokně uzly s vybranými UDÁLOSTmi.][sfx-eventstore]

>[!NOTE]
>Od verze Service Fabric 6,4. Eventstoru není ve výchozím nastavení povolené a musí být povolená v šabloně Resource Manageru.

>[!NOTE]
>Od verze Service Fabric 6,4. Rozhraní API Eventstoru jsou dostupná jenom pro clustery s Windows, které běží jenom na Azure. Pracujeme na přenosu této funkce na Linux i na naší samostatné clustery.

## <a name="image-store-viewer"></a>Image Store Viewer
Prohlížeč úložiště imagí je funkce nabízená při použití nativního Image Store, která umožňuje zobrazit aktuální obsah úložiště imagí a získat informace o souborech a složkách spolu s odebráním souborů nebo složek.

![Service Fabric Explorer mapa clusteru][sfx-imagestore]

## <a name="backup-and-restore"></a>Zálohování a obnovení
Service Fabric Explorer nabízí možnost rozhraní se [zálohováním a obnovením](./service-fabric-reliable-services-backup-restore.md). Aby bylo možné zobrazit funkce zálohování a obnovení v SFX, musí být povolen rozšířený režim.

![Povolit rozšířený režim][0]
 
Je možné provést následující operace:

* Vytvořte, upravte a odstraňte zásady zálohování.
* Povolí nebo zakáže zálohování pro aplikaci, službu nebo oddíl.
* Pozastavení a obnovení zálohování aplikace, služby nebo oddílu.
* Aktivovat a sledovat zálohu oddílu
* Aktivace a sledování obnovení oddílu.

Další informace o službě Backup and Restore Service najdete v [referenčních informacích o REST API](/rest/api/servicefabric/sfclient-index-backuprestore).
## <a name="next-steps"></a>Další kroky
* [Správa aplikací Service Fabric v aplikaci Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Service Fabric nasazení aplikace pomocí PowerShellu](service-fabric-deploy-remove-applications.md)

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
