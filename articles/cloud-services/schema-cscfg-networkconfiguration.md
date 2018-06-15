---
title: Schéma NetworkConfiguration služby Azure Cloud Services | Microsoft Docs
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: c1b94a9e-46e8-4a18-ac99-343c94b1d4bd
caps.latest.revision: 28
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: ebe81b2e4dea347eb22b173ff1e9baf1ee6bb75d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359641"
---
# <a name="azure-cloud-services-config-networkconfiguration-schema"></a>Schéma NetworkConfiguration konfigurace služby Azure Cloud Services

`NetworkConfiguration` Element konfiguračního souboru služby určuje hodnoty virtuální sítě a DNS. Tato nastavení jsou volitelné pro cloudové služby.

Další informace o virtuálních sítí a přidružených schémat. můžete použít následující zdroje:

- [Cloudové služby (klasické) schéma konfigurace](schema-cscfg-file.md)
- [Cloudové služby (klasické) definice schématu](schema-csdef-file.md)
- [Vytvoření virtuální sítě (klasické)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)

## <a name="networkconfiguration-element"></a>NetworkConfiguration Element
Následující příklad ukazuje `NetworkConfiguration` elementu a jeho podřízené elementy.

```xml
<ServiceConfiguration>
  <NetworkConfiguration>
    <AccessControls>
      <AccessControl name="aclName1">
        <Rule order="<rule-order>" action="<rule-action>" remoteSubnet="<subnet-address>" description="rule-description"/>
      </AccessControl>
    </AccessControls>
    <EndpointAcls>
      <EndpointAcl role="<role-name>" endpoint="<endpoint-name>" accessControl="<acl-name>"/>
    </EndpointAcls>
    <Dns>
      <DnsServers>
        <DnsServer name="<server-name>" IPAddress="<server-address>" />
      </DnsServers>
    </Dns>
    <VirtualNetworkSite name="<site-name>"/>
    <AddressAssignments>
      <InstanceAddress roleName="<role-name>">
        <Subnets>
          <Subnet name="<subnet-name>"/>
        </Subnets>
      </InstanceAddress>
      <ReservedIPs>
        <ReservedIP name="<reserved-ip-name>"/>
      </ReservedIPs>
    </AddressAssignments>
  </NetworkConfiguration>
</ServiceConfiguration>
```

Následující tabulka popisuje podřízených elementů `NetworkConfiguration` elementu.

| Element       | Popis |
| ------------- | ----------- |
| AccessControl | Volitelné. Určuje pravidla pro přístup ke koncovým bodům v cloudové službě. Název ovládacího prvku přístup je definována řetězce pro `name` atribut. `AccessControl` Element obsahuje jeden nebo více `Rule` elementy. Více než jeden `AccessControl` element může být definováno.|
| Pravidlo | Volitelné. Určuje akci, která má být použito pro rozsah IP adres zadané podsítě. Pořadí pravidla je definováno hodnotu řetězce pro `order` atribut. Pravidlo Čím nižší číslo tím vyšší je priorita. Například pravidla může být zadaným pořadová čísla 100, 200 a 300. Pravidlo s pořadovým číslem 100 má přednost před pravidlo, které má pořadí 200.<br /><br /> Akce pravidla je definováno řetězec pro `action` atribut. Možné hodnoty:<br /><br /> -   `permit` – Určuje, že pouze pakety z rozsahu zadanou podsíť může komunikovat s koncový bod.<br />-   `deny` – Určuje, že přístup je odepřen ke koncovým bodům v rozsahu zadané podsítě.<br /><br /> Rozsah IP adres podsítě, které jsou ovlivněny pravidlem jsou definovány pro řetězec `remoteSubnet` atribut. Popis pravidla je definováno řetězec pro `description` atribut.|
| EndpointAcl | Volitelné. Určuje přiřazení pravidly řízení přístupu na koncový bod. Název role, která obsahuje koncový bod je definována řetězce pro `role` atribut. Název koncového bodu je definován řetězce pro `endpoint` atribut. Název sadu `AccessControl` v řetězci pro jsou definovaná pravidla, které bude použito ke koncovému bodu `accessControl` atribut. Více než jeden `EndpointAcl` elementy lze definovat.|
| Server DNS | Volitelné. Určuje nastavení pro DNS server. Můžete zadat nastavení pro servery DNS bez připojení k virtuální síti. Název serveru DNS je definován řetězce pro `name` atribut. IP adresa serveru DNS je definována řetězce pro `IPAddress` atribut. IP adresa musí být platná IPv4 adresa.|
| VirtualNetworkSite | Volitelné. Určuje název virtuálního síťového webu, který chcete nasadit cloudovou službu. Toto nastavení nevytvoří virtuální síť. Odkazuje na lokalitu, který byl dříve definován v souboru sítě pro virtuální síť. Cloudové služby může být pouze členem jednu virtuální síť. Pokud nezadáte toto nastavení, nebude nasazen cloudové službě k virtuální síti. Název virtuálního síťového webu je definován řetězce pro `name` atribut.|
| InstanceAddress | Volitelné. Určuje přidružení role k podsíti nebo sadu podsítí ve virtuální síti. Když přiřadíte roli název na adresu instance, můžete zadat pro podsítě, u kterých chcete této role, které chcete přidružit. `InstanceAddress` Obsahuje element podsítě. Název role, který je přidružen k podsíti nebo podsítě je definován řetězce pro `roleName` atribut.|
| Podsíť | Volitelné. Určuje podsítě, která odpovídá názvu podsítě v konfiguračním souboru na síti. Název podsítě je definován řetězce pro `name` atribut.|
| Vyhrazená IP adresa | Volitelné. Určuje vyhrazenou IP adresu, která by měly být přidružené nasazení. Chcete-li vytvořit vyhrazenou IP adresu, musíte použít vytvořit vyhrazenou IP adresu. Každé nasazení v cloudové službě lze přidružit jednu vyhrazenou IP adresu. Název vyhrazené IP adresy je definován řetězce pro `name` atribut.|

## <a name="see-also"></a>Viz také
[Cloudové služby (klasické) schéma konfigurace](schema-cscfg-file.md)