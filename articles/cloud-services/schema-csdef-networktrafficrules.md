---
title: Schéma Azure Cloud Services def. NetworkTrafficRules | Microsoft Docs
description: Přečtěte si o NetworkTrafficRules, která omezuje role, které mají přístup k interním koncovým bodům role. Kombinuje s rolemi v definičním souboru služby.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "79534724"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>NetworkTrafficRules schéma definice Azure Cloud Services
`NetworkTrafficRules`Uzel je volitelný prvek v definičním souboru služby, který určuje, jak vzájemně komunikují mezi rolemi. Omezuje role, které mají přístup k interním koncovým bodům konkrétní role. Není `NetworkTrafficRules` samostatný element; v souboru definice služby je v kombinaci se dvěma nebo více rolemi.

Výchozí přípona souboru definice služby je. csdef.

> [!NOTE]
>  `NetworkTrafficRules`Uzel je dostupný jenom pomocí sady Azure SDK verze 1,3 nebo vyšší.

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
`NetworkTrafficRules`Uzel definičního souboru služby obsahuje tyto prvky, které jsou podrobně popsané v následujících částech tohoto tématu:

[Element NetworkTrafficRules](#NetworkTrafficRules)

[Element OnlyAllowTrafficTo](#OnlyAllowTrafficTo)

[Cílová element](#Destinations)

[Element RoleEndpoint](#RoleEndpoint)

Element AllowAllTraffic

[Element WhenSource](#WhenSource)

[Element FromRole](#FromRole)

##  <a name="networktrafficrules-element"></a><a name="NetworkTrafficRules"></a> Element NetworkTrafficRules
`NetworkTrafficRules`Element určuje, které role mohou komunikovat s koncovým bodem v jiné roli. Služba může obsahovat jednu `NetworkTrafficRules` definici.

##  <a name="onlyallowtrafficto-element"></a><a name="OnlyAllowTrafficTo"></a> Element OnlyAllowTrafficTo
`OnlyAllowTrafficTo`Element popisuje kolekci cílových koncových bodů a rolí, které s nimi můžou komunikovat. Můžete zadat více `OnlyAllowTrafficTo` uzlů.

##  <a name="destinations-element"></a><a name="Destinations"></a> Cílová element
`Destinations`Prvek popisuje kolekci RoleEndpoints, než může být oznámeno.

##  <a name="roleendpoint-element"></a><a name="RoleEndpoint"></a> Element RoleEndpoint
`RoleEndpoint`Element popisuje koncový bod role, který umožňuje komunikaci s. `RoleEndpoint`Pokud je v roli více než jeden koncový bod, můžete zadat více prvků.

| Atribut      | Typ     | Popis |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Povinná hodnota. Název koncového bodu, na který má být povolen provoz.|
| `roleName`     | `string` | Povinná hodnota. Název webové role, na kterou má být povolena komunikace.|

## <a name="allowalltraffic-element"></a>Element AllowAllTraffic
`AllowAllTraffic`Element je pravidlo, které umožňuje všem rolím komunikovat s koncovými body definovanými v `Destinations` uzlu.

##  <a name="whensource-element"></a><a name="WhenSource"></a> Element WhenSource
`WhenSource`Prvek popisuje kolekci rolí, než může komunikovat s koncovými body definovanými v `Destinations` uzlu.

| Atribut | Typ     | Popis |
| --------- | -------- | ----------- |
| `matches` | `string` | Povinná hodnota. Určuje pravidlo, které se má použít při povolování komunikace. Jediná platná hodnota je aktuálně `AnyRule` .|
  
##  <a name="fromrole-element"></a><a name="FromRole"></a> Element FromRole
`FromRole`Prvek určuje role, které mohou komunikovat s koncovými body definovanými v `Destinations` uzlu. `FromRole`Pokud existuje více než jedna role, která může komunikovat s koncovými body, můžete zadat více prvků.

| Atribut  | Typ     | Popis |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Povinná hodnota. Název role, ze které má být komunikace povolena.|

## <a name="see-also"></a>Viz také
[Schéma definice cloudové služby (Classic)](schema-csdef-file.md)




