---
title: 'Zabezpečení clusteru Service Fabric: role klienta'
description: Tento článek popisuje dvě role klienta a oprávnění k rolím.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: abca19e686d39338fcaa2e0b0c8126913135170b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451899"
---
# <a name="role-based-access-control-for-service-fabric-clients"></a>Řízení přístupu na základě rolí pro klienty Service Fabric
Azure Service Fabric podporuje dva různé typy řízení přístupu pro klienty, kteří jsou připojeni k clusteru Service Fabric: správce a uživatel. Řízení přístupu umožňuje správci clusteru omezit přístup k určitým operacím clusteru pro různé skupiny uživatelů, čímž se cluster více zabezpečí.  

**Správci** mají plný přístup k možnostem správy (včetně možností čtení a zápisu). Ve výchozím nastavení mají **uživatelé** pouze přístup pro čtení k možnostem správy (například možnosti dotazů) a možnost řešení aplikací a služeb.

Dvě role klienta (správce a klient) při vytváření clusteru zadáte poskytnutím samostatných certifikátů pro každou z nich. Podrobnosti o nastavení zabezpečeného clusteru Service Fabric naleznete v [tématu Zabezpečení clusteru Service Fabric.](service-fabric-cluster-security.md)

## <a name="default-access-control-settings"></a>Výchozí nastavení řízení přístupu
Typ řízení přístupu správce má úplný přístup ke všem rozhraním API infrastruktury klienta. Může provádět všechna čtení a zápisy v clusteru Service Fabric, včetně následujících operací:

### <a name="application-and-service-operations"></a>Provoz aplikací a služeb
* **CreateService**: vytvoření služby                             
* **CreateServiceFromTemplate**: vytvoření služby ze šablony                             
* **UpdateService**: aktualizace služby                             
* **DeleteService**: odstranění služby                             
* **ProvisionApplicationType**: zřizování typu aplikace                             
* **CreateApplication**: vytvoření aplikace                               
* **Odstranit aplikaci**: odstranění aplikace                             
* **UpgradeAplikace**: spuštění nebo přerušení upgradů aplikací                             
* **UnprovisionApplicationType**: typ aplikace unprovisioning                             
* **MoveNextUpgradeDomain**: obnovení upgradů aplikací s explicitní aktualizační doménou                             
* **ReportUpgradeHealth**: obnovení upgradů aplikací s aktuálním průběhem upgradu                             
* **ReportHealth**: hlášení stavu                             
* **PredeployPackageToNode**: rozhraní API před nasazením                            
* **CodePackageControl**: restartování balíčků kódu                             
* **RecoverPartition**: obnovení oddílu                             
* **RecoverPartitions**: obnovení oddílů                             
* **RecoverServicePartitions**: obnovení oddílů služby                             
* **RecoverSystemPartitions**: obnovení oddílů systémových služeb                             

### <a name="cluster-operations"></a>Operace clusteru
* **ProvisionFabric**: MSI nebo manifest clusteru zřizování                             
* **Upgrade Fabric:** spuštění upgradů clusteru                             
* **UnprovisionFabric:** MSI nebo manifest clusteru unprovisioning                         
* **MoveNextFabricUpgradeDomain**: obnovení upgradů clusteru pomocí explicitní aktualizační domény                             
* **ReportFabricUpgradeHealth**: obnovení upgradů clusteru s aktuálním průběhem upgradu                             
* **StartInfrastructureTask**: spuštění úloh infrastruktury                             
* **FinishInfrastructureTask**: dokončení úkolů infrastruktury                             
* **InvokeInfrastructureCommand**: příkazy pro správu úloh infrastruktury                              
* **ActivateNode**: aktivace uzlu                             
* **Deaktivovatuzel**: deaktivace uzlu                             
* **DeactivateNodesBatch**: deaktivace více uzlů                             
* **RemoveNodeDeActivations**: návrat deaktivace na více uzlech                             
* **GetNodeDeactivationStatus**: kontrola stavu deaktivace                             
* **NodeStateRemoved**: odebrání stavu uzlu hlášení                             
* **ReportFault**: hlášení poruchy                             
* **FileContent**: přenos souborů klienta úložiště obrázků (externí do clusteru)                             
* **FileDownload**: zahájení stahování souboru klienta úložiště obrázků (externí pro cluster)                             
* **InternalList**: operace seznamu souborů klienta úložiště obrázků (interní)                             
* **Odstranit**: operace odstranění klienta úložiště bitových obrázků                              
* **Upload**: image store klienta upload operace                             
* **NodeControl**: spuštění, zastavení a restartování uzlů                             
* **MoveReplicaControl:** přesunutí replik z jednoho uzlu do druhého                             

### <a name="miscellaneous-operations"></a>Různé operace
* **Ping**: příkaz ping klienta                             
* **Dotaz**: všechny povolené dotazy
* **NameExists**: pojmenování kontroly existence identifikátoru URI                             

Typ řízení přístupu uživatelů je ve výchozím nastavení omezen na následující operace: 

* **EnumerateSubnames**: pojmenování výčtu identifikátoru URI                             
* **EnumerateProperties**: pojmenování výčtu vlastností                             
* **PropertyReadBatch**: pojmenování operací čtení vlastností                             
* **GetServiceDescription**: oznámení služby s dlouhým dotazem a popisy služby čtení                             
* **ResolveService**: řešení služeb založených na stížnostech                             
* **ResolveNameOwner**: překlad pojmenování vlastníka identifikátoru URI                             
* **ResolvePartition**: řešení systémových služeb                             
* **ServiceNotifications**: oznámení služby založené na událostech                             
* **GetUpgradeStatus**: stav upgradu aplikace dotazování                             
* **GetFabricUpgradeStatus**: stav upgradu clusteru dotazování                             
* **InvokeInfrastructureQuery**: dotazování úloh infrastruktury                             
* **Seznam**: operace seznamu souborů klienta úložiště obrázků                             
* **ResetPartitionLoad:** resetování zatížení jednotky s podporou převzetí služeb při selhání                             
* **ToggleVerboseServicePlacementHealthReporting**: přepínání podrobných zpráv o stavu umístění služby                             

Řízení přístupu správce má také přístup k předchozím operacím.

## <a name="changing-default-settings-for-client-roles"></a>Změna výchozího nastavení rolí klientů
V souboru manifestu clusteru můžete v případě potřeby klientovi poskytnout možnosti správce. Výchozí hodnoty můžete změnit tak, že během [vytváření clusteru](service-fabric-cluster-creation-via-portal.md)přejdete na možnost Nastavení prostředků **infrastruktury** a udáte předchozí nastavení v polích **název**, **správce**, **uživatel**a **hodnota.**

## <a name="next-steps"></a>Další kroky
[Zabezpečení clusteru Service Fabric](service-fabric-cluster-security.md)

[Vytvoření clusteru Service Fabric](service-fabric-cluster-creation-via-portal.md)

