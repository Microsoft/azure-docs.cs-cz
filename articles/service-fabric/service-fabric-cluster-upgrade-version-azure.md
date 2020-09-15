---
title: Upgrade verze Azure Service Fabric v clusteru
description: Upgradujte Service Fabric kód nebo konfiguraci, která spouští Cluster Service Fabric, včetně nastavení režimu aktualizace clusteru, upgradu certifikátů, přidávání portů aplikací, provádění oprav operačního systému a tak dále. Co můžete očekávat při provádění upgradů?
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 01fe916f0ee78c8481ac6b17b8f7409b47c852ee
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90564283"
---
# <a name="upgrade-the-service-fabric-version-of-a-cluster"></a>Upgrade verze Service Fabric v clusteru

Pro jakýkoliv moderní systém je pro zajištění dlouhodobé úspěšnosti produktu navržený návrh pro zajištění náročnosti. Cluster Azure Service Fabric je prostředek, který vlastníte, ale částečně ho spravuje Microsoft. Tento článek popisuje, jak upgradovat verzi Service Fabric spuštěného v clusteru Azure.

Cluster můžete nastavit tak, aby přijímal automatické upgrady prostředků infrastruktury, když jsou vydané společností Microsoft, nebo můžete vybrat podporovanou verzi prostředků infrastruktury, na které má být cluster zapnutý.

Provedete to tak, že nastavíte konfiguraci clusteru "upgradeMode" na portálu nebo pomocí Správce prostředků v době vytváření nebo později v živém clusteru. 

> [!NOTE]
> Zajistěte, aby cluster používal podporovanou verzi prostředků infrastruktury vždycky. Jak a když oznamujeme vydání nové verze Service fabricu, bude předchozí verze označená pro konec podpory po dobu minimálně 60 dní od tohoto data. Nové verze jsou oznámeny [na blogu týmu Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric). K dispozici je nová verze, kterou si můžete vybrat. 
> 
> 

14 dnů před vypršením platnosti vydaných verzí clusteru se vygeneruje událost stavu, která převede váš cluster do stavu s varováním. Cluster zůstane ve stavu varování, dokud neprovedete upgrade na podporovanou verzi prostředků infrastruktury.

## <a name="set-the-upgrade-mode-in-the-azure-portal"></a>Nastavte režim upgradu v Azure Portal
Při vytváření clusteru můžete cluster nastavit na automatické nebo ruční.

![Snímek obrazovky se zobrazí v podokně vytvořit Service Fabric cluster s vybranou možností 2 konfigurace clusteru a otevře se podokno konfigurace clusteru.][Create_Manualmode]

Cluster můžete nastavit na automatické nebo ruční, pokud používáte živý cluster, a to pomocí prostředí pro správu. 

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Upgrade na novou verzi v clusteru, který je nastaven na ruční režim prostřednictvím portálu.
Pokud chcete upgradovat na novou verzi, stačí, když vyberete dostupnou verzi z rozevíracího seznamu a uložíte ji. Upgrade prostředků infrastruktury se spustí automaticky. Zásady stavu clusteru (kombinace stavu uzlu a stavu všech aplikací spuštěných v clusteru) jsou v průběhu upgradu dodrženy.

Pokud nejsou splněny zásady stavu clusteru, upgrade se vrátí zpět. Posuňte se dolů na tento dokument a přečtěte si další informace o tom, jak tyto vlastní zásady stavu nastavit. 

Po odstranění problémů, které vedly k vrácení zpět, je nutné znovu zahájit upgrade, a to pomocí stejných kroků jako v tomto postupu.

![Snímek obrazovky se zobrazí okno Service Fabric clustery s otevřeným podoknem upgradu prostředků infrastruktury a zvýrazněnými možnostmi upgradu, včetně automatických a ručních.][Manage_Automaticmode]

## <a name="set-the-upgrade-mode-using-a-resource-manager-template"></a>Nastavení režimu upgradu pomocí šablony Správce prostředků
Přidejte konfiguraci "upgradeMode" do definice prostředku Microsoft. ServiceFabric/Clusters a nastavte "clusterCodeVersion" na jednu z podporovaných verzí prostředků infrastruktury, jak je znázorněno níže, a pak šablonu nasaďte. Platné hodnoty "upgradeMode" jsou "ruční" nebo "automatické".

![Snímek obrazovky zobrazuje šablonu, která je v nešifrovaném textu odsazená tak, aby odrážela strukturu a clusterCodeVersion a upgradeMode jsou zvýrazněné.][ARMUpgradeMode]

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Upgrade na novou verzi v clusteru, který je nastaven na ruční režim pomocí šablony Správce prostředků.
Pokud je cluster v ručním režimu, pokud chcete upgradovat na novou verzi, změňte "clusterCodeVersion" na podporovanou verzi a nasaďte ji. Nasazení šablony se aktivuje při automatickém vypnutí upgradu prostředků infrastruktury. Zásady stavu clusteru (kombinace stavu uzlu a stavu všech aplikací spuštěných v clusteru) jsou v průběhu upgradu dodrženy.

Pokud nejsou splněny zásady stavu clusteru, upgrade se vrátí zpět.  

Po odstranění problémů, které vedly k vrácení zpět, je nutné znovu zahájit upgrade, a to pomocí stejných kroků jako v tomto postupu.

## <a name="set-custom-health-polices-for-upgrades"></a>Nastavení vlastních zásad stavu pro upgrady
Pro upgrade prostředků infrastruktury můžete zadat vlastní zásady stavu. Pokud jste cluster nastavili na automatické upgrady prostředků infrastruktury, pak se tyto zásady uplatní na [fázi 1 pro automatické upgrady prostředků infrastruktury](service-fabric-cluster-upgrade.md#fabric-upgrade-behavior-during-automatic-upgrades).
Pokud jste cluster nastavili pro ruční upgrady prostředků infrastruktury, pak se tyto zásady uplatní pokaždé, když vyberete novou verzi, která aktivuje upgrade prostředků infrastruktury ve vašem clusteru. Pokud zásady nepřepisujete, použijí se výchozí hodnoty.

Můžete zadat vlastní zásady stavu nebo zkontrolovat aktuální nastavení v okně "upgrade prostředků infrastruktury" výběrem možnosti Upřesnit nastavení upgradu. Přečtěte si následující obrázek o postupu. 

![Správa vlastních zásad stavu][HealthPolices]

## <a name="list-all-available-versions-for-all-environments-for-a-given-subscription"></a>Vypíše všechny dostupné verze pro všechna prostředí pro dané předplatné.
Spusťte následující příkaz a měli byste získat výstup podobný tomuto.

"supportExpiryUtc" oznamuje, kdy platnost dané vydané verze vypršela nebo vypršela její platnost. Nejnovější vydaná verze nemá platné datum – má hodnotu "9999-12-31T23:59:59.9999999", která pouze znamená, že datum vypršení platnosti ještě není nastavené.

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
* Informace o tom, jak přizpůsobit některá [nastavení prostředků infrastruktury Service Fabric pro clustery](service-fabric-cluster-fabric-settings.md)
* Přečtěte si, jak [škálovat cluster na úrovni a ven](service-fabric-cluster-scale-in-out.md) .
* Další informace o [upgradech aplikací](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
