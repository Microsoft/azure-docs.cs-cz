---
title: Azure Cloud Services Def. Schéma NetworkTrafficRules | Dokumentace Microsoftu
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
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: 8925943b0a5d151d55adedcfe3f01b5a14c63c1b
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55821676"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Azure Cloud Services Definition NetworkTrafficRules Schema
`NetworkTrafficRules` Uzlu je volitelný prvek v souboru definice služby, která určuje způsob komunikace rolí mezi sebou. Omezuje rolí můžete přístup ke koncovým bodům s interním konkrétní role. `NetworkTrafficRules` Není samostatný prvek; se zkombinuje s dvěma nebo více rolí v definičním souboru služby.

Výchozí přípona pro definiční soubor služby je .csdef.

> [!NOTE]
>  `NetworkTrafficRules` Uzlu je pouze k dispozici prostřednictvím Azure SDK verze 1.3 nebo vyšší.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Služby na úrovni Basic definice schématu pro pravidla pro provoz sítě
Základní formát souboru definice služby obsahující definice provozu sítě je následujícím způsobem.

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
`NetworkTrafficRules` Uzel definiční soubor služby zahrnuje tyto prvky, které jsou podrobně popsány v následujících oddílech v tomto tématu:

[NetworkTrafficRules Element](#NetworkTrafficRules)

[OnlyAllowTrafficTo Element](#OnlyAllowTrafficTo)

[Element cíle](#Destinations)

[RoleEndpoint – Element](#RoleEndpoint)

AllowAllTraffic – Element

[WhenSource – Element](#WhenSource)

[FromRole – Element](#FromRole)

##  <a name="NetworkTrafficRules"></a> NetworkTrafficRules Element
`NetworkTrafficRules` Element určuje, jaké role může komunikovat s který koncový bod na jiné role. Služba může obsahovat jednu `NetworkTrafficRules` definice.

##  <a name="OnlyAllowTrafficTo"></a> OnlyAllowTrafficTo – Element
`OnlyAllowTrafficTo` Element popisuje kolekci cílové koncové body a rolí, které mohou komunikovat s nimi. Lze zadat více `OnlyAllowTrafficTo` uzly.

##  <a name="Destinations"></a> Element cíle
`Destinations` Element popisuje kolekci RoleEndpoints, než lze komunikovat s.

##  <a name="RoleEndpoint"></a> RoleEndpoint – Element
`RoleEndpoint` Element popisuje role a povolit komunikaci s koncový bod. Lze zadat více `RoleEndpoint` elementů, pokud existuje více než jeden koncový bod v roli.

| Atribut      | Type     | Popis |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Povinná hodnota. Název koncového bodu, která umožňují přenosy na.|
| `roleName`     | `string` | Povinná hodnota. Název webové role pro byla povolena komunikace s.|

## <a name="allowalltraffic-element"></a>AllowAllTraffic – Element
`AllowAllTraffic` Je pravidlo, které umožňuje všechny role, aby komunikovat s koncovými body, definované v elementu `Destinations` uzlu.

##  <a name="WhenSource"></a> WhenSource – Element
`WhenSource` Element popisuje kolekci rolí, než může komunikovat s koncovými body, definované v `Destinations` uzlu.

| Atribut | Type     | Popis |
| --------- | -------- | ----------- |
| `matches` | `string` | Povinná hodnota. Určuje pravidlo platit při povolení komunikace. Jediná platná hodnota je aktuálně `AnyRule`.|
  
##  <a name="FromRole"></a> FromRole – Element
`FromRole` Prvek určuje role, které mohou komunikovat s koncovými body, definované v `Destinations` uzlu. Lze zadat více `FromRole` elementů, pokud existuje více než jednu roli, který může komunikovat s koncovými body.

| Atribut  | Type     | Popis |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Povinná hodnota. Název role, ze kterého chcete povolit komunikaci.|

## <a name="see-also"></a>Viz také
[Cloudové služby (klasické) schématu definice](schema-csdef-file.md)
