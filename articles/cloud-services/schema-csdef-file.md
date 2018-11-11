---
title: Definice schématu (soubor .csdef) služby Azure Cloud Services | Dokumentace Microsoftu
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: b7735dbf-8e91-4d1b-89f7-2f17e9302469
caps.latest.revision: 42
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: 9cb78362b5c0613d6ed6820bbf8e6d3275ab4787
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51250658"
---
# <a name="azure-cloud-services-definition-schema-csdef-file"></a>Schéma definice (soubor .csdef) služby Azure Cloud Services
Definiční soubor služby definuje model služby pro aplikaci. Soubor obsahuje definice pro role, které jsou k dispozici v cloudové službě, určuje koncové body služby a vytváří nastavení konfigurace pro službu. Konfigurace nastavení hodnoty se nastavují v konfiguračním souboru služby, jak je popsáno [schéma konfigurace cloudové služby (klasické)](https://msdn.microsoft.com/library/b1ae68cd-cc95-48cb-a4a4-da91dc708a35).

Ve výchozím nastavení, je nainstalován soubor schématu konfigurace diagnostiky Azure do `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` adresáře. Nahraďte `<version>` s nainstalovanou verzí [sady Azure SDK](http://www.windowsazure.com/develop/downloads/).

Výchozí přípona pro definiční soubor služby je .csdef.

## <a name="basic-service-definition-schema"></a>Schématu definice služby na úrovni Basic
Definiční soubor služby musí obsahovat jeden `ServiceDefinition` elementu. Definice služby musí obsahovat alespoň jednu roli (`WebRole` nebo `WorkerRole`) element. Může obsahovat až 25 definované v definici jedné role a je možné kombinovat typy rolí. Definice služby obsahuje také volitelné `NetworkTrafficRules` element, který omezuje které role může komunikovat k zadaným interním koncovým bodům. Definice služby obsahuje také volitelné `LoadBalancerProbes` element, který obsahuje zákazníka definované sondy stavu koncových bodů.

Základní formát souboru definice služby je následujícím způsobem.

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
Následující témata popisují schéma:

- [Schéma LoadBalancerProbe](schema-csdef-loadbalancerprobe.md)
- [Schéma WebRole](schema-csdef-webrole.md)
- [Schéma WorkerRole](schema-csdef-workerrole.md)
- [Schéma NetworkTrafficRules](schema-csdef-networktrafficrules.md)

##  <a name="ServiceDefinition"></a> ServiceDefinition Element
`ServiceDefinition` Prvek je prvek nejvyšší úrovně v definičním souboru služby.

Následující tabulka popisuje atributy `ServiceDefinition` elementu.

| Atribut               | Popis |
| ----------------------- | ----------- |
| jméno                    |Povinná hodnota. Název služby. Název musí být jedinečný v rámci účtu služby.|
| topologyChangeDiscovery | Volitelné. Určuje typ oznámení o změně topologie. Možné hodnoty:<br /><br /> -   `Blast` -Odešle aktualizace co nejdřív na všech instancích rolí. Pokud zvolíte možnost, by měla být schopná zpracovat topologie aktualizace bez restartování role.<br />-   `UpgradeDomainWalk` – Odešle aktualizaci ke každé instanci role sekvenčním způsobem po předchozí instanci úspěšně přijal aktualizace.|
| schemaVersion           | Volitelné. Určuje verzi schématu definice služby. Verze schématu umožňuje vybrat správné nástroje sady SDK a použít pro ověřování schématu v případě více než jedna verze sady SDK je nainstalovaná sada Visual Studio vedle sebe.|
| upgradeDomainCount      | Volitelné. Určuje počet upgradovacích domén, přes které se přidělují role v této službě. Instance rolí jsou přiděleny k upgradovací doméně, při nasazení služby. Další informace najdete v tématu [aktualizace role cloudové služby nebo nasazení](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment).<br /><br /> Můžete zadat až 20 upgradovacích domén. Pokud není zadán, výchozí počet upgradovacích domén je 5.|