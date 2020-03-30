---
title: Azure Cloud Services Def. NetworkTrafficRules schéma | Dokumenty společnosti Microsoft
description: Další informace o NetworkTrafficRules, který omezuje role, které mají přístup k vnitřní koncové body role. Kombinuje s rolemi v souboru definice služby.
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
ms.openlocfilehash: e53c10395ec3168e656633cc43fb2d01902209fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534724"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Azure Cloud Services Definition NetworkTrafficRules Schéma
Uzel `NetworkTrafficRules` je volitelný prvek v souboru definice služby, který určuje, jak role vzájemně komunikují. Omezuje, které role mají přístup k interním koncovým bodům konkrétní role. Není `NetworkTrafficRules` samostatný prvek; je kombinován se dvěma nebo více rolemi v souboru definice služby.

Výchozí přípona pro soubor definice služby je .csdef.

> [!NOTE]
>  Uzel `NetworkTrafficRules` je k dispozici pouze pomocí sady Azure SDK verze 1.3 nebo vyšší.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Schéma definice základní služby pro pravidla síťového provozu
Základní formát souboru definice služby obsahující definice síťového provozu je následující.

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
Uzel `NetworkTrafficRules` souboru definice služby obsahuje tyto prvky, popsané podrobně v následujících částech v tomto tématu:

[NetworkTrafficRules Element](#NetworkTrafficRules)

[Pouze AllowTrafficTo Element](#OnlyAllowTrafficTo)

[Cílový prvek](#Destinations)

[RoleEndpoint Element](#RoleEndpoint)

Prvek AllowAllTraffic

[Prvek WhenSource](#WhenSource)

[FromRole Element](#FromRole)

##  <a name="networktrafficrules-element"></a><a name="NetworkTrafficRules"></a>NetworkTrafficRules Element
Prvek `NetworkTrafficRules` určuje, které role mohou komunikovat s koncovým bodem v jiné roli. Služba může obsahovat jednu `NetworkTrafficRules` definici.

##  <a name="onlyallowtrafficto-element"></a><a name="OnlyAllowTrafficTo"></a>Pouze AllowTrafficTo Element
Prvek `OnlyAllowTrafficTo` popisuje kolekci cílových koncových bodů a rolí, které s nimi mohou komunikovat. Můžete zadat `OnlyAllowTrafficTo` více uzlů.

##  <a name="destinations-element"></a><a name="Destinations"></a>Cílový prvek
Prvek `Destinations` popisuje kolekci RoleEndpoints, než může být komunikována s.

##  <a name="roleendpoint-element"></a><a name="RoleEndpoint"></a>RoleEndpoint Element
Prvek `RoleEndpoint` popisuje koncový bod na roli povolit komunikaci s. Můžete zadat `RoleEndpoint` více prvků, pokud existuje více než jeden koncový bod na roli.

| Atribut      | Typ     | Popis |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Povinná hodnota. Název koncového bodu povolit přenos.|
| `roleName`     | `string` | Povinná hodnota. Název webové role, ke které je třeba povolit komunikaci.|

## <a name="allowalltraffic-element"></a>Prvek AllowAllTraffic
Prvek `AllowAllTraffic` je pravidlo, které umožňuje všem rolím komunikovat `Destinations` s koncovými body definovanými v uzlu.

##  <a name="whensource-element"></a><a name="WhenSource"></a>Prvek WhenSource
Prvek `WhenSource` popisuje kolekci rolí, než může komunikovat s `Destinations` koncovými body definovanými v uzlu.

| Atribut | Typ     | Popis |
| --------- | -------- | ----------- |
| `matches` | `string` | Povinná hodnota. Určuje pravidlo, které má být aplikováno při povolování komunikace. Jediná platná hodnota je `AnyRule`aktuálně .|
  
##  <a name="fromrole-element"></a><a name="FromRole"></a>FromRole Element
Prvek `FromRole` určuje role, které mohou komunikovat s koncovými body definovanými `Destinations` v uzlu. Můžete zadat `FromRole` více prvků, pokud existuje více než jedna role, která může komunikovat s koncovými body.

| Atribut  | Typ     | Popis |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Povinná hodnota. Název role, ze které chcete povolit komunikaci.|

## <a name="see-also"></a>Viz také
[Schéma definice cloudové služby (klasické)](schema-csdef-file.md)




