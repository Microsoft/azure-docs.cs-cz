---
title: 'Zabezpečení clusteru Service Fabric: klientské role | Dokumentace Microsoftu'
description: Tento článek popisuje dva klientské role a oprávnění k dispozici k rolím.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: coreysa
editor: ''
ms.assetid: 7bc808d9-3609-46a1-ac12-b4f53bff98dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: ed000dc4be1ae45382d688d4a596ec745c69d0bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60711149"
---
# <a name="role-based-access-control-for-service-fabric-clients"></a>Řízení přístupu na základě rolí pro klienty Service Fabric
Azure Service Fabric podporuje dva typy ovládacích prvků jiný přístup pro klienty, kteří jsou připojené ke clusteru Service Fabric: správce a uživatele. Řízení přístupu umožňuje omezit přístup k určité operace clusteru pro různé skupiny uživatelů, lepší zabezpečení clusteru pomocí Správce clusteru.  

**Správci** úplný přístup k funkcím správy (včetně možností pro čtení a zápis). Ve výchozím nastavení **uživatelé** pouze mají přístup pro čtení k funkce pro správu (například schopnosti příkazů jazyka) a schopnost řešení aplikací a služeb.

Dva klientské role (správce a klient) je zadat v době vytváření clusteru poskytnutím samostatné certifikáty pro každý. Zobrazit [zabezpečení clusteru Service Fabric](service-fabric-cluster-security.md) podrobné informace o vytvoření zabezpečeného clusteru Service Fabric.

## <a name="default-access-control-settings"></a>Výchozí nastavení řízení přístupu
Typ řízení přístupu správce má úplný přístup ke všem rozhraním API FabricClient. Můžete provádět všechny operace čtení a zápisů v clusteru Service Fabric, včetně následujících operací:

### <a name="application-and-service-operations"></a>Operace služby a aplikace
* **CreateService**: vytvořit službu                             
* **CreateServiceFromTemplate**: vytvoření ze šablony služby                             
* **UpdateService**: aktualizace služeb                             
* **DeleteService**: odstranění služby                             
* **ProvisionApplicationType**: zřízení typu aplikace                             
* **CreateApplication**: vytvoření aplikace                               
* **DeleteApplication**: odstranění aplikace                             
* **UpgradeApplication**: spuštění nebo by bylo třeba přerušit upgrady aplikací                             
* **UnprovisionApplicationType**: zrušení zřízení typu aplikace                             
* **MoveNextUpgradeDomain**: obnovení upgrady aplikací s explicitní aktualizační doména                             
* **ReportUpgradeHealth**: obnovení upgrady aplikací s aktuální průběh upgradu                             
* **ReportHealth**: generování sestav stavu                             
* **PredeployPackageToNode**: před nasazením rozhraní API                            
* **CodePackageControl**: restartování balíčky kódu                             
* **RecoverPartition**: obnovení oddílu                             
* **RecoverPartitions**: obnovení oddílů                             
* **RecoverServicePartitions**: obnovení oddílů služby                             
* **RecoverSystemPartitions**: obnovení oddílů systémové služby                             

### <a name="cluster-operations"></a>Operace clusteru
* **ProvisionFabric**: MSI a/nebo clusteru manifest zřizování                             
* **UpgradeFabric**: spouští se upgrade clusteru                             
* **UnprovisionFabric**: MSI a/nebo clusteru manifest zrušení zřízení                         
* **MoveNextFabricUpgradeDomain**: obnovení inovace clusteru se explicitní aktualizační doména                             
* **ReportFabricUpgradeHealth**: obnovení inovace clusteru se aktuální průběh upgradu                             
* **StartInfrastructureTask**: spuštění úlohy infrastruktury                             
* **FinishInfrastructureTask**: dokončení úlohy infrastruktury                             
* **InvokeInfrastructureCommand**: příkazy pro správu úloh infrastruktury                              
* **ActivateNode**: Aktivace uzlu                             
* **DeactivateNode**: deaktivace uzlu                             
* **DeactivateNodesBatch**: deaktivace více uzlů                             
* **RemoveNodeDeactivations**: vrací deaktivaci na několika uzlech                             
* **GetNodeDeactivationStatus**: Kontrola stavu deaktivace                             
* **NodeStateRemoved**: generování sestav stavu uzel odebrán                             
* **ReportFault**: hlášení chyb                             
* **Vlastností FileContent**: image store přenos souborů klienta (externí do clusteru)                             
* **FileDownload**: image store klienta soubor ke stažení zahájení (mimo cluster)                             
* **InternalList**: image store klienta soubor seznamu operace (interní)                             
* **Odstranit**: operace odstranění klienta úložiště obrázků                              
* **Nahrát**: operace nahrávání klienta úložiště obrázků                             
* **NodeControl**: spuštění, zastavení a restartování uzlů                             
* **MoveReplicaControl**: přesunutí repliky z jednoho uzlu do druhého                             

### <a name="miscellaneous-operations"></a>Různé operace
* **Příkaz ping**: příkazy ping pro zjištění klienta                             
* **Dotaz**: všechny dotazy, které jsou povoleny
* **NameExists**: pojmenování kontroly existence identifikátoru URI                             

Typ ovládacího prvku přístupu uživatele je ve výchozím nastavení omezen na tyto operace: 

* **EnumerateSubnames**: pojmenování výčtu identifikátoru URI                             
* **EnumerateProperties**: pojmenování výčet vlastností                             
* **PropertyReadBatch**: názvy vlastností operace čtení                             
* **GetServiceDescription**: oznámení služby dlouho dotazování a čtení služby popisy                             
* **ResolveService**: služba založená na stížnosti řešení                             
* **ResolveNameOwner**: řešení názvů identifikátoru URI vlastníka                             
* **ResolvePartition**: řešení systémových služeb                             
* **ServiceNotifications**: oznámení služby založené na událostech                             
* **GetUpgradeStatus**: dotazování stav upgradu aplikace                             
* **GetFabricUpgradeStatus**: dotazování stav upgradu clusteru                             
* **InvokeInfrastructureQuery**: dotazování infrastrukturních úloh                             
* **Seznam**: operace seznam souborů klienta úložiště obrázků                             
* **ResetPartitionLoad**: resetování zatížení jednotky převzetí služeb při selhání                             
* **ToggleVerboseServicePlacementHealthReporting**: Přepnutí stavu umístění podrobné služby generování sestav                             

Správce řízení přístupu má také přístup k předchozí operace.

## <a name="changing-default-settings-for-client-roles"></a>Změna výchozího nastavení pro klientské role
V souboru manifestu clusteru můžete zadat možnosti Správce klienta v případě potřeby. Výchozí nastavení můžete změnit tak, že přejdete **nastavení prostředků infrastruktury** možnost během [vytvoření clusteru](service-fabric-cluster-creation-via-portal.md)a zadání předchozích nastavení v **název**,  **správce**, **uživatele**, a **hodnotu** pole.

## <a name="next-steps"></a>Další postup
[Zabezpečení clusteru Service Fabric](service-fabric-cluster-security.md)

[Vytvoření clusteru Service Fabric](service-fabric-cluster-creation-via-portal.md)

