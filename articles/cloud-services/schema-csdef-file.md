---
title: Schéma definice Azure Cloud Services (klasické) (soubor. csdef) | Microsoft Docs
description: Soubor definice služby (. csdef) definuje model služby pro aplikaci, který obsahuje dostupné role, koncové body a konfigurační hodnoty pro službu.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: b98534b049698ea95c6738ce3404dd5ef8ff7a28
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102502260"
---
# <a name="azure-cloud-services-classic-definition-schema-csdef-file"></a>Schéma definice Azure Cloud Services (Classic) (soubor. csdef)

> [!IMPORTANT]
> [Azure Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md) je nový model nasazení založený na Azure Resource Manager pro produkt Azure Cloud Services.V důsledku této změny se Azure Cloud Services běžící na modelu nasazení založeném na Azure Service Manager přejmenovala jako Cloud Services (Classic) a všechna nová nasazení by měla používat [Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md).

Definiční soubor služby definuje model služby pro aplikaci. Soubor obsahuje definice rolí, které jsou k dispozici pro cloudovou službu, určuje koncové body služby a vytváří nastavení konfigurace pro službu. Hodnoty nastavení konfigurace se nastavují v konfiguračním souboru služby, jak je popsáno v tématu [schéma konfigurace cloudové služby (Classic)](/previous-versions/azure/reference/ee758710(v=azure.100)).

Ve výchozím nastavení se soubor konfiguračního schématu Azure Diagnostics nainstaluje do `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` adresáře. Nahraďte `<version>` nainstalovanou verzí sady [Azure SDK](https://www.windowsazure.com/develop/downloads/).

Výchozí přípona souboru definice služby je. csdef.

## <a name="basic-service-definition-schema"></a>Základní schéma definice služby
Definiční soubor služby musí obsahovat jeden `ServiceDefinition` prvek. Definice služby musí obsahovat alespoň jeden element role ( `WebRole` nebo `WorkerRole` ). Může obsahovat až 25 rolí definovaných v rámci jedné definice a můžete kombinovat typy rolí. Definice služby obsahuje také volitelný prvek, `NetworkTrafficRules` který omezuje, které role mohou komunikovat se zadanými interními koncovými body. Definice služby obsahuje také volitelný prvek, `LoadBalancerProbes` který obsahuje sondy stavu definované zákazníkem koncových bodů.

Základní formát definičního souboru služby je následující.

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

## <a name="schema-definitions"></a>Definice schémat
Schéma popisuje následující témata:

- [Schéma LoadBalancerProbe](schema-csdef-loadbalancerprobe.md)
- [Schéma WebRole](schema-csdef-webrole.md)
- [Schéma WorkerRole](schema-csdef-workerrole.md)
- [Schéma NetworkTrafficRules](schema-csdef-networktrafficrules.md)

##  <a name="servicedefinition-element"></a><a name="ServiceDefinition"></a> Element ServiceDefinition
`ServiceDefinition`Element je element nejvyšší úrovně v definičním souboru služby.

Následující tabulka popisuje atributy `ServiceDefinition` prvku.

| Atribut               | Popis |
| ----------------------- | ----------- |
| name                    |Povinná hodnota. Název služby Název musí být v rámci účtu služby jedinečný.|
| topologyChangeDiscovery | Nepovinný parametr. Určuje typ oznámení o změně topologie. Možné hodnoty:<br /><br /> -   `Blast` – Pošle aktualizaci co nejdřív pro všechny instance rolí. Zvolíte-li možnost, role by měla být schopna zpracovat aktualizaci topologie bez restartování.<br />-   `UpgradeDomainWalk` – Pošle aktualizaci do každé instance role sekvenčním způsobem po úspěšném přijetí aktualizace předchozí instance.|
| schemaVersion           | Nepovinný parametr. Určuje verzi schématu definice služby. Verze schématu umožňuje sadě Visual Studio vybrat správné nástroje sady SDK, které se mají použít při ověřování schématu, pokud je nainstalovaná více než jedna verze sady SDK vedle sebe.|
| upgradeDomainCount      | Nepovinný parametr. Určuje počet domén upgradu, ve kterých se přidělují role v této službě. Instance rolí jsou přiděleny k upgradovací doméně při nasazení služby. Další informace najdete v tématu [aktualizace role nebo nasazení cloudové služby](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment), [Správa dostupnosti virtuálních počítačů](../virtual-machines/availability.md) a [co je model cloudové služby](./cloud-services-model-and-package.md).<br /><br /> Můžete zadat až 20 upgradovacích domén. Pokud tento parametr nezadáte, výchozí počet domén upgradu je 5.|