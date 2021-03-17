---
title: Schéma definice Azure Cloud Services (Rozšířená podpora) (soubor csdef) | Microsoft Docs
description: Informace týkající se schématu definice (csdef) pro Cloud Services (Rozšířená podpora)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: d9bf1b54f1bfeebacbb406a50c8496817857204c
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102507564"
---
# <a name="azure-cloud-services-extended-support-definition-schema-csdef-file"></a>Schéma definice Azure Cloud Services (Rozšířená podpora) (soubor csdef)

Definiční soubor služby definuje model služby pro aplikaci. Soubor obsahuje definice rolí, které jsou k dispozici pro cloudovou službu, určuje koncové body služby a vytváří nastavení konfigurace pro službu. Hodnoty nastavení konfigurace se nastavují v konfiguračním souboru služby, jak je popsáno v tématu [schéma konfigurace cloudové služby (rozšířené podpory)](schema-cscfg-file.md).

Ve výchozím nastavení se soubor konfiguračního schématu Azure Diagnostics nainstaluje do `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` adresáře. Nahraďte `<version>` nainstalovanou verzí sady [Azure SDK](https://www.windowsazure.com/develop/downloads/).

Výchozí přípona souboru definice služby je csdef.

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
| upgradeDomainCount      | Nepovinný parametr. Určuje počet domén upgradu, ve kterých se přidělují role v této službě. Instance rolí jsou přiděleny k upgradovací doméně při nasazení služby. Další informace najdete v tématech [aktualizace role cloudové služby nebo nasazení](sample-update-cloud-service.md) a [Správa dostupnosti virtuálních počítačů](../virtual-machines/availability.md) . můžete zadat až 20 domén upgradu. Pokud tento parametr nezadáte, výchozí počet domén upgradu je 5.|

## <a name="see-also"></a>Viz také

[Schéma konfigurace Azure Cloud Services (Rozšířená podpora) (soubor. cscfg)](schema-cscfg-file.md)