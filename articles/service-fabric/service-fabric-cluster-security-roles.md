---
title: 'Service Fabric zabezpečení clusteru: role klientů'
description: Tento článek popisuje dvě role klienta a oprávnění poskytnutá rolím.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: abca19e686d39338fcaa2e0b0c8126913135170b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "75451899"
---
# <a name="role-based-access-control-for-service-fabric-clients"></a>Řízení přístupu na základě role pro klienty Service Fabric
Azure Service Fabric podporuje pro klienty, kteří jsou připojení ke clusteru Service Fabric, dva různé typy řízení přístupu: správce a uživatel. Řízení přístupu umožňuje správci clusteru omezit přístup k určitým clusterovým operacím pro různé skupiny uživatelů a tím zvýšit zabezpečení clusteru.  

**Správci** mají plný přístup k funkcím správy (včetně funkcí pro čtení a zápis). Ve výchozím nastavení mají **Uživatelé** oprávnění ke čtení jenom pro funkce správy (například možnosti dotazů) a schopnost vyřešit aplikace a služby.

V době vytváření clusteru určíte dvě role klienta (správce a klient), a to tak, že pro každý z nich poskytnete samostatné certifikáty. Podrobnosti o nastavení zabezpečeného clusteru Service Fabric najdete v tématu [Service Fabric zabezpečení clusteru](service-fabric-cluster-security.md) .

## <a name="default-access-control-settings"></a>Výchozí nastavení řízení přístupu
Typ řízení přístupu správce má úplný přístup ke všem rozhraním API FabricClient. Může provádět všechny operace čtení a zápisu v clusteru Service Fabric, včetně následujících operací:

### <a name="application-and-service-operations"></a>Operace s aplikacemi a službami
* **Operace CreateService**: vytvoření služby                             
* **CreateServiceFromTemplate**: vytvoření služby ze šablony                             
* **Operace updateservice**: aktualizace služby                             
* **DeleteService**: odstranění služby                             
* **ProvisionApplicationType**: zřizování typu aplikace                             
* **CreateApplication**: Vytvoření aplikace                               
* **DeleteApplication**: odstranění aplikace                             
* **UpgradeApplication**: spuštění nebo přerušení upgradu aplikace                             
* **UnprovisionApplicationType**: zrušení zřizování typu aplikace                             
* **MoveNextUpgradeDomain**: obnovování upgradů aplikace s explicitní aktualizační doménou                             
* **ReportUpgradeHealth**: obnovování upgradů aplikací s aktuálním průběhem upgradu                             
* **ReportHealth**: vytváření sestav stavu                             
* **PredeployPackageToNode**: rozhraní API pro předinstalaci                            
* **CodePackageControl**: restartování balíčků kódu                             
* **RecoverPartition**: obnovování oddílu                             
* **Operace recoverpartitions**: obnovování oddílů                             
* **RecoverServicePartitions**: obnovování oddílů služby                             
* **Operace recoversystempartitions**: obnovování oddílů systémových služeb                             

### <a name="cluster-operations"></a>Operace clusteru
* **ProvisionFabric**: zřizování MSI nebo manifestu clusteru                             
* **UpgradeFabric**: spouštění upgradů clusteru                             
* **UnprovisionFabric**: rušení zřizování MSI nebo manifestu clusteru                         
* **MoveNextFabricUpgradeDomain**: Probíhá obnovení upgradu clusteru s explicitní aktualizační doménou.                             
* **ReportFabricUpgradeHealth**: obnovování upgradů clusteru s aktuálním průběhem upgradu                             
* **StartInfrastructureTask**: spouštění úloh infrastruktury                             
* **FinishInfrastructureTask**: Probíhá dokončování úloh infrastruktury                             
* **InvokeInfrastructureCommand**: příkazy správy úloh infrastruktury                              
* **ActivateNode**: Aktivace uzlu                             
* **Operace deactivatenode**: deaktivace uzlu                             
* **DeactivateNodesBatch**: deaktivace více uzlů                             
* **RemoveNodeDeactivations**: vrácení deaktivace na více uzlech                             
* **GetNodeDeactivationStatus**: Kontrola stavu deaktivace                             
* **Operace nodestateremoved**: byl odebrán stav uzlu vytváření sestav.                             
* **ReportFault**: Chyba generování sestav                             
* **Obsah** souboru: přenos souborů klienta úložiště imagí (externí do clusteru)                             
* Soubor **ke stažení**: zahájení stahování souboru klienta úložiště imagí (externí do clusteru)                             
* **InternalList**: operace seznamu klientských souborů v úložišti imagí (interní)                             
* **Odstranit**: operace odstranění klienta úložiště imagí                              
* **Nahrání**: operace odeslání klienta úložiště imagí                             
* **NodeControl**: spouštění, zastavování a restartování uzlů                             
* **MoveReplicaControl**: přesun replik z jednoho uzlu do druhého                             

### <a name="miscellaneous-operations"></a>Různé operace
* **Test** klienta: příkazy pro odeslání klientů                             
* **Dotaz**: všechny dotazy povoleny
* **NameExists**: pojmenovávání kontrol existence identifikátoru URI                             

Typ řízení přístupu uživatele je ve výchozím nastavení omezen na následující operace: 

* **EnumerateSubnames**: pojmenování výčtu IDENTIFIKÁTORů URI                             
* **EnumerateProperties**: vytváření výčtu vlastností                             
* **PropertyReadBatch**: operace čtení vlastností pojmenování                             
* **Getservicedescription**: dlouhodobá oznámení o službě dotazování a čtení popisů služeb                             
* **ResolveService**: řešení služby založené na stížnostech                             
* **ResolveNameOwner**: překládání vlastníka identifikátoru URI názvu                             
* **ResolvePartition**: řešení systémových služeb                             
* **ServiceNotifications**: oznámení služby založené na událostech                             
* **GetUpgradeStatus**: stav upgradu aplikace s dotazem                             
* **GetFabricUpgradeStatus**: dotazování na stav upgradu clusteru                             
* **InvokeInfrastructureQuery**: dotazování na úlohy infrastruktury                             
* **Seznam**: operace se seznamem klientských souborů v úložišti imagí                             
* **ResetPartitionLoad**: obnovení zatížení jednotky převzetí služeb při selhání                             
* **ToggleVerboseServicePlacementHealthReporting**: přepínání vytváření sestav o stavu umístění služby verbose                             

Řízení přístupu správce má také přístup k předchozím operacím.

## <a name="changing-default-settings-for-client-roles"></a>Změna výchozího nastavení pro role klientů
V souboru manifestu clusteru můžete v případě potřeby poskytnout klientovi možnosti správy. Výchozí hodnoty můžete změnit tak, že v průběhu [vytváření clusteru](service-fabric-cluster-creation-via-portal.md)kliknete na možnost **nastavení prostředků infrastruktury** a v poli **název**, **správce**, **uživatel** a **hodnota** zadáte předchozí nastavení.

## <a name="next-steps"></a>Další kroky
[Service Fabric zabezpečení clusteru](service-fabric-cluster-security.md)

[Vytvoření clusteru Service Fabric](service-fabric-cluster-creation-via-portal.md)

