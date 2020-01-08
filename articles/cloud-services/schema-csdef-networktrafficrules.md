---
title: Schéma Azure Cloud Services def. NetworkTrafficRules | Microsoft Docs
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 351b369f-365e-46c1-82ce-03fc3655cc88
caps.latest.revision: 17
author: tgore03
ms.author: tagore
ms.openlocfilehash: e6d156810b9fdee69ddac122eec06db7267ddf36
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449041"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>NetworkTrafficRules schéma definice Azure Cloud Services
Uzel `NetworkTrafficRules` je volitelný prvek v definičním souboru služby, který určuje, jak vzájemně komunikují mezi rolemi. Omezuje role, které mají přístup k interním koncovým bodům konkrétní role. `NetworkTrafficRules` není samostatný element; v souboru definice služby je v kombinaci se dvěma nebo více rolemi.

Výchozí přípona souboru definice služby je. csdef.

> [!NOTE]
>  Uzel `NetworkTrafficRules` je k dispozici pouze pomocí sady Azure SDK verze 1,3 nebo vyšší.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Základní schéma definice služby pro pravidla síťového provozu
Základní formát definičního souboru služby obsahující definice síťového provozu je následující.

```xml
<ServiceDefinition …>
   <NetworkTrafficRules>
      <OnlyAllowTrafficTo>
         <Destinations>
            <RoleEndpoint endpointName="<name-of-the-endpoint>" roleName="<name-of-the-role-containing-the-endpoint>"/>
         </Destinations>
         <AllowAllTraffic/>
         <WhenSource matches="[AnyRule]">
            <FromRole roleName="<name-of-the-role-to-allow-traffic-from>"/>
         </WhenSource>
      </OnlyAllowTrafficTo>
   </NetworkTrafficRules>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Prvky schématu
`NetworkTrafficRules` uzel definičního souboru služby obsahuje tyto prvky, které jsou podrobně popsané v dalších částech tohoto tématu:

[Element NetworkTrafficRules](#NetworkTrafficRules)

[Element OnlyAllowTrafficTo](#OnlyAllowTrafficTo)

[Cílová element](#Destinations)

[Element RoleEndpoint](#RoleEndpoint)

Element AllowAllTraffic

[Element WhenSource](#WhenSource)

[Element FromRole](#FromRole)

##  <a name="NetworkTrafficRules"></a>Element NetworkTrafficRules
Element `NetworkTrafficRules` určuje, které role mohou komunikovat se koncovým bodem v jiné roli. Služba může obsahovat jednu definici `NetworkTrafficRules`.

##  <a name="OnlyAllowTrafficTo"></a>Element OnlyAllowTrafficTo
Element `OnlyAllowTrafficTo` popisuje kolekci cílových koncových bodů a rolí, které s nimi můžou komunikovat. Můžete zadat více `OnlyAllowTrafficTo` uzlů.

##  <a name="Destinations"></a>Cílová element
Element `Destinations` popisuje kolekci RoleEndpoints, než lze komunikovat s.

##  <a name="RoleEndpoint"></a>Element RoleEndpoint
Element `RoleEndpoint` popisuje koncový bod role, který umožňuje komunikaci s. Pokud je v roli více než jeden koncový bod, můžete zadat více prvků `RoleEndpoint`.

| Atribut      | Typ     | Popis |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Povinná hodnota. Název koncového bodu, na který má být povolen provoz.|
| `roleName`     | `string` | Povinná hodnota. Název webové role, na kterou má být povolena komunikace.|

## <a name="allowalltraffic-element"></a>Element AllowAllTraffic
Element `AllowAllTraffic` je pravidlo, které umožňuje všem rolím komunikovat s koncovými body definovanými v uzlu `Destinations`.

##  <a name="WhenSource"></a>Element WhenSource
Element `WhenSource` popisuje kolekci rolí, než může komunikovat s koncovými body definovanými v uzlu `Destinations`.

| Atribut | Typ     | Popis |
| --------- | -------- | ----------- |
| `matches` | `string` | Povinná hodnota. Určuje pravidlo, které se má použít při povolování komunikace. Jediná platná hodnota je aktuálně `AnyRule`.|
  
##  <a name="FromRole"></a>Element FromRole
Element `FromRole` určuje role, které mohou komunikovat s koncovými body definovanými v uzlu `Destinations`. Pokud existuje více než jedna role, která může komunikovat s koncovými body, můžete zadat více `FromRole` prvků.

| Atribut  | Typ     | Popis |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Povinná hodnota. Název role, ze které má být komunikace povolena.|

## <a name="see-also"></a>Viz také
[Schéma definice cloudové služby (Classic)](schema-csdef-file.md)




