---
title: Schéma NetworkConfiguration služby Azure Cloud Services | Dokumentace Microsoftu
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
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: ed071d1da30a598eef830b4485c246ffae09c950
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39004110"
---
# <a name="azure-cloud-services-config-networkconfiguration-schema"></a>Schéma NetworkConfiguration konfigurace služby Azure Cloud Services

`NetworkConfiguration` Element konfigurační soubor služby určuje hodnoty virtuální sítě a DNS. Tato nastavení jsou volitelné pro cloudové služby.

Další informace o virtuálních sítích a přidružených schémat. můžete použít následující zdroje:

- [Schéma konfigurace cloudové služby (klasické)](schema-cscfg-file.md)
- [Cloudové služby (klasické) schématu definice](schema-csdef-file.md)
- [Vytvoření virtuální sítě (klasické)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)

## <a name="networkconfiguration-element"></a>NetworkConfiguration Element
Následující příklad ukazuje `NetworkConfiguration` elementu a jeho podřízené prvky.

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
| AccessControl | Volitelné. Určuje pravidla pro přístup ke koncovým bodům v cloudové službě. Název řízení přístupu je určené pro řetězec `name` atribut. `AccessControl` Element obsahuje jeden nebo více `Rule` elementy. Více než jeden `AccessControl` element může být definován.|
| Pravidlo | Volitelné. Určuje akci, která se má vzít pro rozsah IP adres zadané podsíti. Pořadí tohoto pravidla je definováno řetězcovou hodnotu pro `order` atribut. Čím nižší číslo pravidla vyšší je priorita. Například pravidla, které by mohl být specifikován pomocí pořadová čísla 100, 200 a 300. Pravidlo s pořadovým číslem 100 má přednost pravidlo, které má pořadí 200.<br /><br /> Řetězec pro je definována akce pro pravidlo `action` atribut. Možné hodnoty:<br /><br /> -   `permit` – Určuje, že pouze pakety z rozsahu zadanou podsíť může komunikovat s koncovým bodem.<br />-   `deny` – Určuje, že přístup byl odepřen ke koncovým bodům v rozsahu zadanou podsíť.<br /><br /> Rozsah IP adres podsítě, které jsou ovlivněny pravidlo, které jsou definovány pomocí řetězce pro `remoteSubnet` atribut. Popis pro pravidlo je definován na řetězec `description` atribut.|
| EndpointAcl | Volitelné. Určuje přiřazení pravidla pro řízení přístupu na koncový bod. Název role, která obsahuje koncový bod je určené pro řetězec `role` atribut. Název koncového bodu je určené pro řetězec `endpoint` atribut. Název sady `AccessControl` pravidla, která má být použit pro koncový bod jsou definovány v řetězci pro `accessControl` atribut. Více než jeden `EndpointAcl` může být definované elementy.|
| Serveru DNS | Volitelné. Určuje nastavení pro DNS server. Můžete zadat nastavení pro servery DNS bez virtuální sítě. Název DNS serveru je určené pro řetězec `name` atribut. IP adresa serveru DNS je definován na řetězec `IPAddress` atribut. IP adresa musí být platná IPv4 adresa.|
| VirtualNetworkSite | Volitelné. Určuje název virtuální síťové lokality, ve kterém chcete nasadit cloudovou službu. Toto nastavení nevytvoří virtuální síťovou lokalitu. Odkazuje na lokalitu, která je dříve definována v souboru sítě pro vaši virtuální síť. Cloudové služby může být jenom členem jedné virtuální sítě. Pokud toto nastavení není zadán, cloudovou službu se nenasadí do virtuální sítě. Název lokality virtuální sítě definoval řetězec pro `name` atribut.|
| InstanceAddress | Volitelné. Určuje přiřazení rolí k podsíti nebo sadu podsítí ve virtuální síti. Když přiřadíte roli název na adresu instance, můžete určit podsítě, ke kterým má role souviset. `InstanceAddress` Obsahuje prvku podsítím. Název role, který je přidružený k podsíti nebo podsítě je určené pro řetězec `roleName` atribut.|
| Podsíť | Volitelné. Určuje podsíť, která odpovídá názvu podsítě v konfiguračním souboru sítě. Název podsítě je určené pro řetězec `name` atribut.|
| Vyhrazená IP adresa | Volitelné. Určuje vyhrazenou IP adresu, která by měly být přidružené nasazení. Chcete-li vytvořit vyhrazenou IP adresu, musíte použít vytvořit vyhrazenou IP adresu. Každé nasazení v cloudové službě můžou být spojené s vyhrazenou IP adresu. Vyhrazená IP adresa je definována v řetězci pro `name` atribut.|

## <a name="see-also"></a>Viz také
[Schéma konfigurace cloudové služby (klasické)](schema-cscfg-file.md)