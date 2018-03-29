---
title: Služby Azure Cloud Services definici schématu (soubor .csdef) | Microsoft Docs
ms.custom: ''
ms.date: 04/14/2015
ms.prod: azure
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: b7735dbf-8e91-4d1b-89f7-2f17e9302469
caps.latest.revision: ''
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: d49112df207e3b8e781480e25855ebb259bbab8e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cloud-services-definition-schema-csdef-file"></a>Azure Cloud Services definici schématu (soubor .csdef)
Soubor definice služby definuje model služby pro aplikaci. Soubor obsahuje definice pro role, které jsou k dispozici v cloudové službě, určuje koncové body služby a vytváří nastavení konfigurace pro službu. Konfigurace nastavení hodnoty se nastavují v konfiguračním souboru služby podle [schéma konfigurace cloudové služby (klasické)](http://msdn.microsoft.com/library/b1ae68cd-cc95-48cb-a4a4-da91dc708a35).

Ve výchozím nastavení je konfiguračního souboru schématu Azure Diagnostics nainstaluje pro `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` adresáře. Nahraďte `<version>` s nainstalovanou verzí [Azure SDK](http://www.windowsazure.com/develop/downloads/).

Výchozí přípona souboru definice služby je .csdef.

## <a name="basic-service-definition-schema"></a>Definice schématu služby na úrovni Basic
Soubor definice služby musí obsahovat jeden `ServiceDefinition` elementu. Definice služby musí obsahovat alespoň jednu roli (`WebRole` nebo `WorkerRole`) elementu. Může obsahovat až pro 25 role v jednom definici a je možné kombinovat role typy. Definice služby obsahuje taky volitelné `NetworkTrafficRules` element, který omezuje jednotlivé role může komunikovat se zadanou vnitřních koncových bodů. Definice služby obsahuje taky volitelné `LoadBalancerProbes` element, který obsahuje zákazník definovaný sondy stavu koncových bodů.

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
Následující témata popisují schématu:

- [Schéma LoadBalancerProbe](schema-csdef-loadbalancerprobe.md)
- [Schéma WebRole](schema-csdef-webrole.md)
- [Schéma WorkerRole](schema-csdef-workerrole.md)
- [Schéma NetworkTrafficRules](schema-csdef-networktrafficrules.md)

##  <a name="ServiceDefinition"></a> ServiceDefinition Element
`ServiceDefinition` Element je element nejvyšší úrovně v definičním souboru služby.

Následující tabulka popisuje atributy `ServiceDefinition` elementu.

| Atribut               | Popis |
| ----------------------- | ----------- |
| jméno                    |Povinná hodnota. Název služby. Název musí být jedinečný v rámci účtu služby.|
| topologyChangeDiscovery | Volitelné. Určuje typ oznámení o změně topologie. Možné hodnoty:<br /><br /> -   `Blast` -Odešle aktualizace co nejdříve do všech instancí rolí. Pokud zvolíte možnost, roli byste měli mít pro zpracování aktualizace topologie bez restartování.<br />-   `UpgradeDomainWalk` – Odešle aktualizace každá instance role sekvenční způsobem po předchozí instanci úspěšně přijal aktualizace.|
| schemaVersion           | Volitelné. Určuje verzi schématu definice služby. Verze schématu umožňuje sadě Visual Studio k výběru správné sady SDK nástroje má použít pro ověření schématu, pokud je nainstalovaná více než jedna verze sady SDK vedle sebe.|
| upgradeDomainCount      | Volitelné. Určuje počet upgradovacích domén, přes které jsou přiděleny rolí v rámci této služby. Instance role jsou přiděleny do upgradu domény při nasazení služby. Další informace najdete v tématu [aktualizovat roli služby cloudu nebo nasazení](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment).<br /><br /> Můžete zadat až 20 upgradovacích domén. Pokud není zadáno, výchozí počet domén upgradu je 5.|