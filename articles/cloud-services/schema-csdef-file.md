---
title: Schéma definice cloudových služeb Azure (soubor.csdef) | Dokumenty společnosti Microsoft
description: Soubor definice služby (.csdef) definuje model služby pro aplikaci, který obsahuje dostupné role, koncové body a hodnoty konfigurace pro službu.
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 42
author: tgore03
ms.author: tagore
ms.openlocfilehash: dadb50bd0663f47e6a1bf3d58b5187c8b466964d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528366"
---
# <a name="azure-cloud-services-definition-schema-csdef-file"></a>Schéma definice cloudových služeb Azure (soubor.csdef)
Soubor definice služby definuje model služby pro aplikaci. Soubor obsahuje definice pro role, které jsou k dispozici pro cloudovou službu, určuje koncové body služby a stanoví nastavení konfigurace pro službu. Hodnoty nastavení konfigurace jsou nastaveny v konfiguračním souboru služby, jak je popsáno [v konfiguračním schématu cloudové služby (klasické).](/previous-versions/azure/reference/ee758710(v=azure.100))

Ve výchozím nastavení je do `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` adresáře nainstalován soubor schématu konfigurace diagnostiky Azure. Nahraďte `<version>` nainstalovanou verzí [sady Azure SDK](https://www.windowsazure.com/develop/downloads/).

Výchozí přípona pro soubor definice služby je .csdef.

## <a name="basic-service-definition-schema"></a>Schéma definice základní služby
Soubor definice služby `ServiceDefinition` musí obsahovat jeden prvek. Definice služby musí obsahovat`WebRole` alespoň `WorkerRole`jeden prvek role ( nebo ). Může obsahovat až 25 rolí definovaných v jedné definici a můžete kombinovat typy rolí. Definice služby také `NetworkTrafficRules` obsahuje volitelný prvek, který omezuje, které role mohou komunikovat do určených interních koncových bodů. Definice služby také `LoadBalancerProbes` obsahuje volitelný prvek, který obsahuje zákazníkem definované sondy stavu koncových bodů.

Základní formát souboru definice služby je následující.

```xml
<ServiceDefinition name="<service-name>" topologyChangeDiscovery="<change-type>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" upgradeDomainCount="<number-of-upgrade-domains>" schemaVersion="<version>">
  
  <LoadBalancerProbes>
         …
  </LoadBalancerProbes>
  
  <WebRole …>
         …
  </WebRole>
  
  <WorkerRole …>
         …
  </WorkerRole>
  
  <NetworkTrafficRules>
         …
  </NetworkTrafficRules>

</ServiceDefinition>
```

## <a name="schema-definitions"></a>Definice schématu
Následující témata popisují schéma:

- [Schéma LoadBalancerProbe](schema-csdef-loadbalancerprobe.md)
- [Schéma WebRole](schema-csdef-webrole.md)
- [Schéma WorkerRole](schema-csdef-workerrole.md)
- [Schéma NetworkTrafficRules](schema-csdef-networktrafficrules.md)

##  <a name="servicedefinition-element"></a><a name="ServiceDefinition"></a>Prvek ServiceDefinition
Prvek `ServiceDefinition` je prvek nejvyšší úrovně souboru definice služby.

Následující tabulka popisuje atributy `ServiceDefinition` prvku.

| Atribut               | Popis |
| ----------------------- | ----------- |
| jméno                    |Povinná hodnota. Název služby Název musí být jedinečný v rámci účtu služby.|
| topologieChangeDiscovery | Nepovinný parametr. Určuje typ oznámení o změně topologie. Možné hodnoty:<br /><br /> -   `Blast`- Odešle aktualizaci co nejdříve do všech instancí role. Pokud zvolíte možnost, role by měla být schopna zpracovat aktualizaci topologie bez restartování.<br />-   `UpgradeDomainWalk`– Odešle aktualizaci každé instanci role postupně poté, co předchozí instance úspěšně přijala aktualizaci.|
| verze schématu           | Nepovinný parametr. Určuje verzi schématu definice služby. Verze schématu umožňuje sadě Visual Studio vybrat správné nástroje sady SDK, které se mají použít pro ověření schématu, pokud je nainstalována více než jedna verze sady SDK vedle sebe.|
| upgradePočet domén      | Nepovinný parametr. Určuje počet upgradovacích domén, ve kterých jsou přiděleny role v této službě. Instance rolí jsou při nasazení služby přiděleny do domény upgradu. Další informace najdete [v tématu Aktualizace role cloudové služby nebo nasazení](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment), Správa [dostupnosti virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) a Co je model [cloudové služby](https://docs.microsoft.com/azure/cloud-services/cloud-services-model-and-package).<br /><br /> Můžete zadat až 20 upgradovacích domén. Pokud není zadán, výchozí počet upgradovacích domén je 5.|
