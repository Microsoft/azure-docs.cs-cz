---
title: Upgrade verze azure service fabric clusteru
description: Inovujte kód service fabric nebo konfiguraci, která spouští cluster Service Fabric, včetně nastavení režimu aktualizace clusteru, upgradu certifikátů, přidání portů aplikace, oprav operačního systému a tak dále. Co můžete očekávat při provádění upgradů?
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: c3ffcbd4296385623ff5e2c1ee001c27598ff3fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451805"
---
# <a name="upgrade-the-service-fabric-version-of-a-cluster"></a>Upgrade verze Service Fabric v clusteru

Pro každý moderní systém je návrh upgradability klíčem k dosažení dlouhodobého úspěchu vašeho produktu. Cluster Azure Service Fabric je prostředek, který vlastníte, ale částečně spravuje Microsoft. Tento článek popisuje, jak upgradovat verzi Service Fabric spuštěné v clusteru Azure.

Můžete nastavit cluster přijímat automatické upgrady prostředků infrastruktury, jak jsou vydány společností Microsoft nebo můžete vybrat podporovanou verzi prostředků infrastruktury, kterou chcete, aby byl cluster zapnutý.

To provést nastavením konfigurace clusteru "upgradeMode" na portálu nebo pomocí Správce prostředků v době vytvoření nebo později v živém clusteru 

> [!NOTE]
> Ujistěte se, že váš cluster běží podporovaná verze infrastruktury vždy. Jak a když oznamujeme vydání nové verze service fabric, předchozí verze je označena pro ukončení podpory po minimálně 60 dnů od tohoto data. Nové verze jsou oznámeny [na blogu týmu service fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). Nová verze je k dispozici na výběr pak. 
> 
> 

14 dní před vypršením platnosti verze clusteru je spuštěna událost stavu, která umístí cluster do stavu upozornění. Cluster zůstane ve stavu upozornění, dokud neupgradujete na podporovanou verzi infrastruktury.

## <a name="set-the-upgrade-mode-in-the-azure-portal"></a>Nastavení režimu upgradu na webu Azure Portal
Při vytváření clusteru můžete nastavit cluster na automatický nebo ruční.

![Create_Manualmode][Create_Manualmode]

Cluster můžete nastavit na automatické nebo ruční, když na živém clusteru, pomocí prostředí pro správu. 

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Upgrade na novou verzi v clusteru, který je nastaven na ruční režim prostřednictvím portálu.
Chcete-li upgradovat na novou verzi, stačí vybrat dostupnou verzi z rozbalovacího souboru a uložit. Fabric upgrade se spustí automaticky. Zásady stavu clusteru (kombinace stavu uzlu a stavu všechny aplikace spuštěné v clusteru) jsou dodržovány během upgradu.

Pokud nejsou splněny zásady stavu clusteru, upgrade je vrácena zpět. Posuňte se dolů v tomto dokumentu a přečtěte si další informace o tom, jak nastavit tyto vlastní zásady stavu. 

Jakmile jste opravili problémy, které vedly k vrácení zpět, je třeba zahájit upgrade znovu, podle stejných kroků jako dříve.

![Manage_Automaticmode][Manage_Automaticmode]

## <a name="set-the-upgrade-mode-using-a-resource-manager-template"></a>Nastavení režimu upgradu pomocí šablony Správce prostředků
Přidejte konfiguraci "upgradeMode" do definice prostředku Microsoft.ServiceFabric/clusters a nastavte "clusterCodeVersion" na jednu z podporovaných verzí prostředků infrastruktury, jak je znázorněno níže, a potom nasadit šablonu. Platné hodnoty pro "upgradeMode" jsou "Ruční" nebo "Automatické"

![REŽIM ARMUpgrade][ARMUpgradeMode]

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Upgrade na novou verzi v clusteru, který je nastaven na ruční režim prostřednictvím šablony Správce prostředků.
Pokud je cluster v ručním režimu, chcete-li upgradovat na novou verzi, změňte "clusterCodeVersion" na podporovanou verzi a nasaďte ji. Nasazení šablony, kopy fabric upgrade se spustí automaticky. Zásady stavu clusteru (kombinace stavu uzlu a stavu všechny aplikace spuštěné v clusteru) jsou dodržovány během upgradu.

Pokud nejsou splněny zásady stavu clusteru, upgrade je vrácena zpět.  

Jakmile jste opravili problémy, které vedly k vrácení zpět, je třeba zahájit upgrade znovu, podle stejných kroků jako dříve.

## <a name="set-custom-health-polices-for-upgrades"></a>Nastavení vlastních zdravotních hlídek pro upgrady
Můžete zadat vlastní zdravotní police pro upgrade tkaniny. Pokud jste nastavili cluster na automatické upgrady prostředků infrastruktury, pak tyto zásady získat použít [pro fáze 1 automatické upgrady prostředků infrastruktury](service-fabric-cluster-upgrade.md#fabric-upgrade-behavior-during-automatic-upgrades).
Pokud jste nastavili cluster pro ruční upgrady prostředků infrastruktury, pak tyto zásady získat použít pokaždé, když vyberete novou verzi spouštění systému k zahájení upgradu prostředků infrastruktury v clusteru. Pokud nepřepíšete zásady, budou použity výchozí hodnoty.

Můžete určit vlastní zásady stavu nebo zkontrolovat aktuální nastavení v okně "upgrade prostředků infrastruktury" výběrem rozšířeného nastavení upgradu. Přečtěte si následující obrázek o tom, jak. 

![Správa vlastních zásad stavu][HealthPolices]

## <a name="list-all-available-versions-for-all-environments-for-a-given-subscription"></a>Seznam všech dostupných verzí pro všechna prostředí pro dané předplatné
Spusťte následující příkaz a měli byste získat výstup podobný tomuto.

"supportExpiryUtc" vám řekne, kdy platnost dané verze vyprší nebo vypršela jeho platnost. Nejnovější verze nemá platné datum - má hodnotu "9999-12-31T23:59:59.9999999", což jen znamená, že datum vypršení platnosti ještě není nastaveno.

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

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak přizpůsobit některá [nastavení clusteru prostředků infrastruktury prostředků služby](service-fabric-cluster-fabric-settings.md)
* Přečtěte si, jak [škálovat cluster y a zmenšování](service-fabric-cluster-scale-up-down.md)
* Informace o [upgradech aplikací](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
