---
title: Upgrade clusteru Azure Service Fabric | Dokumentace Microsoftu
description: Upgrade kódu Service Fabric a/nebo konfigurace, která se spouští cluster Service Fabric, včetně nastavení režimu aktualizace clusteru upgrade certifikáty, přidání aplikace portů, provádění oprav operačního systému, a tak dále. Co můžete očekávat, když probíhají upgrady?
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: aljo
ms.openlocfilehash: 3e71199c19fffae0bb7dfa6a59245f1d43cd5065
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2018
ms.locfileid: "51855123"
---
# <a name="upgrade-the-service-fabric-version-of-a-cluster"></a>Upgrade clusteru Service Fabric verze

Návrh pro zdokonalovány moderního systému, je klíčem k dosažení dlouhodobý úspěch vašeho produktu. Cluster Azure Service Fabric je prostředek, který vlastníte, ale částečně spravovaného společností Microsoft. Tento článek popisuje, jak upgradovat na verzi spuštěné v clusteru Azure Service Fabric.

Můžete nastavit pro příjem upgradů automatické prostředků infrastruktury, jako jsou vydané společností Microsoft nebo můžete vybrat verzi podporovaných prostředků infrastruktury, že chcete, aby váš cluster na clusteru.

To provedete nastavením konfigurace clusteru "upgradeMode" na portálu nebo pomocí Resource Manageru v době vytváření nebo později na aktivní cluster 

> [!NOTE]
> Ujistěte se, že zachovat cluster vždy používá verzi podporovanou prostředků infrastruktury. Jak a kdy jsme oznamujeme vydání nové verze service Fabric, předchozí verze budou označena k ukončení podpory po minimálně za 60 dní od data. Oznámení nových vydaných verzích [na blog týmu service fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). Vyberete, pak k dispozici je nová verze. 
> 
> 

14 dní před vypršení platnosti vydané verze, po který váš cluster běží, vygeneruje událost stavu, který váš cluster přepne do stavu upozornění. Cluster zůstane ve varovném stavu, dokud neprovedete upgrade na verzi podporovaných prostředků infrastruktury.

## <a name="set-the-upgrade-mode-in-the-azure-portal"></a>Nastavit režim upgradu na portálu Azure portal
Cluster můžete nastavit na automatické nebo ruční při vytváření clusteru.

![Create_Manualmode][Create_Manualmode]

Cluster můžete nastavit na automatické nebo ruční na aktivní cluster pomocí spravovat prostředí. 

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Upgrade na novou verzi na cluster, který je nastavený na ruční režim prostřednictvím portálu.
Upgrade na novou verzi, vše, co je třeba provést je z rozevíracího seznamu vyberte verzi k dispozici a uložte. Upgrade pro Fabric získá spustila automaticky. Zásady stavu clusteru (kombinace uzlu stav a stav všechny aplikace spuštěné v clusteru) jsou zachovány během upgradu.

Pokud zásady stavu clusteru nejsou splněny, upgrade se vrátí zpět. Přejděte na tento dokument Další informace o tom, jak nastavit tyto zásady vlastní stavu. 

Po opravě problémů, z kterých vzniklo vrácení změn potřebujete k zahájení upgradu znovu podle stejných kroků jako před.

![Manage_Automaticmode][Manage_Automaticmode]

## <a name="set-the-upgrade-mode-using-a-resource-manager-template"></a>Nastavit režim upgradu pomocí šablony Resource Manageru
Přidat konfiguraci "upgradeMode" v definici zdrojů Microsoft.ServiceFabric/clusters a nastavit "clusterCodeVersion" na jednu z podporovaných fabric verzí, jak je znázorněno níže a potom šablonu nasadit. Platné hodnoty pro "upgradeMode" jsou "Ruční" nebo "Automatické"

![ARMUpgradeMode][ARMUpgradeMode]

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Upgrade na novou verzi na cluster, který je nastavený na ruční režim pomocí šablony Resource Manageru.
Pokud je cluster v režimu ručního, upgrade na novou verzi, změňte "clusterCodeVersion" na podporovanou verzi a nasaďte ji. Nasazení šablony, zejména kopance upgradu prostředků infrastruktury získá spustila automaticky. Zásady stavu clusteru (kombinace uzlu stav a stav všechny aplikace spuštěné v clusteru) jsou zachovány během upgradu.

Pokud zásady stavu clusteru nejsou splněny, upgrade se vrátí zpět.  

Po opravě problémů, z kterých vzniklo vrácení změn potřebujete k zahájení upgradu znovu podle stejných kroků jako před.

## <a name="set-custom-health-polices-for-upgrades"></a>Sada vlastních stavových zásady pro upgrady
Můžete určit, že stav vlastní zásady pro upgrade pro fabric. Pokud nastavíte váš cluster na upgrady prostředků infrastruktury automatické, pak použije tyto zásady na [fáze 1 fabric automatické upgrady](service-fabric-cluster-upgrade.md#fabric-upgrade-behavior-during-automatic-upgrades).
Pokud nastavíte cluster pro ruční fabric upgrady, získat tyto zásady použijí pokaždé, když vyberete možnost nové verze aktivuje systém pro pusťte se do upgradu prostředků infrastruktury ve vašem clusteru. Pokud není přepsat zásady, použijí se výchozí hodnoty.

Můžete zadat vlastní bezpečnostní zásady, případně zkontrolujte aktuální nastavení pod oknem "upgrade pro fabric" tak, že vyberete Upřesnit nastavení upgradu. Přečtěte si o tom, jak na následujícím obrázku. 

![Správa vlastní stav zásad][HealthPolices]

## <a name="list-all-available-versions-for-all-environments-for-a-given-subscription"></a>Zobrazí seznam všech verzí dostupných pro všechna prostředí pro daný odběr
Spusťte následující příkaz a měli byste obdržet výstup podobný tomuto.

říká "supportExpiryUtc" Pokud dané vydání vyprší platnost nebo vypršela platnost. Nejnovější verze neobsahuje platné datum – má hodnotu "9999-12-31T23:59:59.9999999", což znamená jen, že datum vypršení platnosti zatím není nastavená.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2016-09-01

Example: https://management.azure.com/subscriptions/1857f442-3bce-4b96-ad95-627f76437a67/providers/Microsoft.ServiceFabric/locations/eastus/clusterVersions?api-version=2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }
```

## <a name="next-steps"></a>Další postup
* Zjistěte, jak přizpůsobit některé z [service fabric nastavení prostředků infrastruktury clusteru](service-fabric-cluster-fabric-settings.md)
* Zjistěte, jak [dovnitř a ven škálování clusteru](service-fabric-cluster-scale-up-down.md)
* Další informace o [upgrady aplikací](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
