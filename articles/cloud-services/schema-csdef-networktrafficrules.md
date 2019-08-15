---
title: Azure Cloud Services def. NetworkTrafficRules schéma | Microsoft Docs
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
author: georgewallace
ms.author: gwallace
manager: gwallace
ms.openlocfilehash: e99b9f0f601841fe6ff32eba0a43bfafd652e941
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945934"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>NetworkTrafficRules schéma definice Azure Cloud Services
`NetworkTrafficRules` Uzel je volitelný prvek v definičním souboru služby, který určuje, jak vzájemně komunikují mezi rolemi. Omezuje role, které mají přístup k interním koncovým bodům konkrétní role. `NetworkTrafficRules` Není samostatný element; v souboru definice služby je v kombinaci se dvěma nebo více rolemi.

Výchozí přípona souboru definice služby je. csdef.

> [!NOTE]
>  `NetworkTrafficRules` Uzel je dostupný jenom pomocí sady Azure SDK verze 1,3 nebo vyšší.

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
Uzel `NetworkTrafficRules` definičního souboru služby obsahuje tyto prvky, které jsou podrobně popsané v následujících částech tohoto tématu:

[Element NetworkTrafficRules](#NetworkTrafficRules)

[Element OnlyAllowTrafficTo](#OnlyAllowTrafficTo)

[Cílová element](#Destinations)

[Element RoleEndpoint](#RoleEndpoint)

Element AllowAllTraffic

[Element WhenSource](#WhenSource)

[Element FromRole](#FromRole)

##  <a name="NetworkTrafficRules"></a>Element NetworkTrafficRules
`NetworkTrafficRules` Element určuje, které role mohou komunikovat s koncovým bodem v jiné roli. Služba může obsahovat jednu `NetworkTrafficRules` definici.

##  <a name="OnlyAllowTrafficTo"></a>Element OnlyAllowTrafficTo
`OnlyAllowTrafficTo` Element popisuje kolekci cílových koncových bodů a rolí, které s nimi můžou komunikovat. Můžete zadat více `OnlyAllowTrafficTo` uzlů.

##  <a name="Destinations"></a>Cílová element
`Destinations` Prvek popisuje kolekci RoleEndpoints, než může být oznámeno.

##  <a name="RoleEndpoint"></a>Element RoleEndpoint
`RoleEndpoint` Element popisuje koncový bod role, který umožňuje komunikaci s. Pokud je v roli `RoleEndpoint` více než jeden koncový bod, můžete zadat více prvků.

| Atribut      | type     | Popis |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Povinný parametr. Název koncového bodu, na který má být povolen provoz.|
| `roleName`     | `string` | Povinný parametr. Název webové role, na kterou má být povolena komunikace.|

## <a name="allowalltraffic-element"></a>Element AllowAllTraffic
Element je pravidlo, které umožňuje všem rolím komunikovat s koncovými body definovanými `Destinations` v uzlu. `AllowAllTraffic`

##  <a name="WhenSource"></a>Element WhenSource
Prvek popisuje kolekci rolí, než může komunikovat s koncovými body definovanými `Destinations` v uzlu. `WhenSource`

| Atribut | type     | Popis |
| --------- | -------- | ----------- |
| `matches` | `string` | Povinný parametr. Určuje pravidlo, které se má použít při povolování komunikace. Jediná platná hodnota je aktuálně `AnyRule`.|
  
##  <a name="FromRole"></a>Element FromRole
Prvek určuje role, které mohou komunikovat s koncovými body definovanými `Destinations` v uzlu. `FromRole` Pokud existuje více než `FromRole` jedna role, která může komunikovat s koncovými body, můžete zadat více prvků.

| Atribut  | type     | Popis |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Povinný parametr. Název role, ze které má být komunikace povolena.|

## <a name="see-also"></a>Viz také
[Schéma definice cloudové služby (Classic)](schema-csdef-file.md)
