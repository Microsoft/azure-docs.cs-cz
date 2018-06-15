---
title: 'Zabezpečení clusteru Service Fabric: rolí klienta | Microsoft Docs'
description: Tento článek popisuje dva klientské role a oprávnění poskytované role.
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
ms.openlocfilehash: 39b77813e3494e809b92738924544e196d89a166
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204647"
---
# <a name="role-based-access-control-for-service-fabric-clients"></a>Řízení přístupu na základě rolí pro klienty Service Fabric
Azure Service Fabric podporuje dva typy ovládacích prvků různý přístup pro klienty, kteří jsou připojené ke clusteru Service Fabric: správce a uživatele. Řízení přístupu umožňuje omezit přístup k určité operace clusteru pro různé skupiny uživatelů, lepší zabezpečení clusteru pomocí Správce clusteru.  

**Správci** mají úplný přístup k funkcím správy (včetně možnosti pro čtení i zápis). Ve výchozím nastavení **uživatelé** mít pouze pro čtení přístup k možnosti správy (například možnosti dotazu) a možnost řešení aplikace a služby.

Tím, že poskytuje samostatné certifikáty pro každou zadáte dva klientské role (správce a klient) v době vytváření clusteru. V tématu [zabezpečení clusteru Service Fabric](service-fabric-cluster-security.md) podrobnosti o nastavení zabezpečení clusteru Service Fabric.

## <a name="default-access-control-settings"></a>Výchozí nastavení řízení přístupu
Typ ovládacího prvku správce přístupu má úplný přístup k rozhraní API FabricClient. Můžete provádět žádné čtení a zápisy na cluster Service Fabric, včetně následujících operací:

### <a name="application-and-service-operations"></a>Operace služby a aplikace
* **CreateService**: vytváření služby                             
* **CreateServiceFromTemplate**: vytváření ze šablony služby                             
* **UpdateService**: servis aktualizací                             
* **DeleteService**: Služba odstranění                             
* **ProvisionApplicationType**: zřizování typ aplikace                             
* **CreateApplication**: vytvoření aplikace                               
* **DeleteApplication**: odstranění aplikace                             
* **UpgradeApplication**: spuštění nebo přerušení upgradů aplikací                             
* **UnprovisionApplicationType**: rušení zajišťování typ aplikace                             
* **MoveNextUpgradeDomain**: obnovení upgradů aplikací s doménou explicitní aktualizace                             
* **ReportUpgradeHealth**: obnovení upgradů aplikací s aktuální průběh upgradu                             
* **ReportHealth**: generování sestav stavu                             
* **PredeployPackageToNode**: před nasazením rozhraní API                            
* **CodePackageControl**: restartování balíčky kódu                             
* **RecoverPartition**: obnovení oddíl                             
* **RecoverPartitions**: obnovení oddíly                             
* **RecoverServicePartitions**: obnovení oddílů služby                             
* **RecoverSystemPartitions**: obnovení systému oddílů služby                             

### <a name="cluster-operations"></a>Operace clusteru
* **ProvisionFabric**: MSI nebo clusteru manifest zřizování                             
* **UpgradeFabric**: spouštění upgrade clusteru                             
* **UnprovisionFabric**: MSI nebo clusteru manifest rušení zajišťování                         
* **MoveNextFabricUpgradeDomain**: obnovení upgradu clusteru s doménou explicitní aktualizace                             
* **ReportFabricUpgradeHealth**: obnovení upgradu clusteru s aktuální průběh upgradu                             
* **StartInfrastructureTask**: spouštění úlohy infrastruktury                             
* **FinishInfrastructureTask**: dokončení úlohy infrastruktury                             
* **InvokeInfrastructureCommand**: příkazy pro správu infrastruktury úloh                              
* **ActivateNode**: Aktivace uzlu                             
* **DeactivateNode**: deaktivace uzlu                             
* **DeactivateNodesBatch**: deaktivace několika uzly                             
* **RemoveNodeDeactivations**: navrácení deaktivace ve více uzlech                             
* **GetNodeDeactivationStatus**: Kontrola stavu deaktivace                             
* **NodeStateRemoved**: generování sestav stav uzlu odebrat                             
* **ReportFault**: generování sestav chyb                             
* **FileContent**: obrázek přenos souborů klienta úložiště (externí do clusteru)                             
* **FileDownload**: image store klienta soubor stažení spuštění (externí do clusteru)                             
* **InternalList**: image store klienta soubor seznamu operaci (interní)                             
* **Odstranit**: operace odstranění klienta úložiště image                              
* **Nahrát**: operace nahrávání klienta úložiště image                             
* **NodeControl**: spuštění, zastavení a restartování uzlů                             
* **MoveReplicaControl**: přesunutí repliky z jednoho uzlu do jiného                             

### <a name="miscellaneous-operations"></a>Různým operacím
* **Příkaz ping**: příkazy ping klienta                             
* **Dotaz**: všechny dotazy na povoleno
* **NameExists**: pojmenování URI existence kontroly                             

Typ řízení přístupu uživatele je ve výchozím nastavení omezen na následující operace: 

* **EnumerateSubnames**: pojmenování URI – výčet                             
* **EnumerateProperties**: názvy vlastností – výčet                             
* **PropertyReadBatch**: názvy vlastností operace čtení                             
* **GetServiceDescription**: oznámení služby dlouho dotazování a čtení služby popisy                             
* **ResolveService**: řešení služeb na základě předpisy                             
* **ResolveNameOwner**: řešení pojmenování URI vlastníka                             
* **ResolvePartition**: řešení systémových služeb                             
* **ServiceNotifications**: oznámení služeb na základě událostí                             
* **GetUpgradeStatus**: dotazování na stav upgradu aplikace                             
* **GetFabricUpgradeStatus**: dotazování na stav upgradu clusteru                             
* **InvokeInfrastructureQuery**: dotazování úlohy infrastruktury                             
* **Seznam**: operace seznamu soubor klienta úložiště image                             
* **ResetPartitionLoad**: resetování zatížení jednotky převzetí služeb při selhání                             
* **ToggleVerboseServicePlacementHealthReporting**: přepnutím stavu umístění podrobné služby generování sestav                             

Řízení přístupu správce má také přístup k předchozí operace.

## <a name="changing-default-settings-for-client-roles"></a>Změna výchozí nastavení klienta rolí
V souboru manifestu clusteru můžete zadat možnosti Správce klienta v případě potřeby. Výchozí nastavení můžete změnit tak, že přejdete do **nastavení prostředků infrastruktury** možnost během [vytvoření clusteru](service-fabric-cluster-creation-via-portal.md)a poskytuje předchozí nastavení v **název**,  **správce**, **uživatele**, a **hodnotu** pole.

## <a name="next-steps"></a>Další postup
[Zabezpečení clusteru Service Fabric](service-fabric-cluster-security.md)

[Vytvoření clusteru Service Fabric](service-fabric-cluster-creation-via-portal.md)

