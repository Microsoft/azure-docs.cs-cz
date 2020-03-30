---
title: Schéma síťové konfigurace cloudových služeb Azure | Dokumenty společnosti Microsoft
description: Informace o podřízených prvcích prvku NetworkConfiguration konfiguračního souboru služby, který určuje hodnoty virtuální sítě a DNS.
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 28
author: tgore03
ms.author: tagore
ms.openlocfilehash: 695ba3acfd5af8797de6e6f7454e493d7863627c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529284"
---
# <a name="azure-cloud-services-config-networkconfiguration-schema"></a>Schéma konfigurace konfigurace konfigurace azure cloudových služeb

Prvek `NetworkConfiguration` konfiguračního souboru služby určuje hodnoty virtuální sítě a DNS. Tato nastavení jsou pro cloudové služby volitelná.

Pomocí následujícího zdroje se dozvíte více o virtuálních sítích a přidružených schématech:

- [Schéma konfigurace cloudové služby (klasické)](schema-cscfg-file.md)
- [Schéma definice cloudové služby (klasické)](schema-csdef-file.md)
- [Vytvoření virtuální sítě (klasické)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)

## <a name="networkconfiguration-element"></a>Prvek síťové konfigurace
Následující příklad ukazuje `NetworkConfiguration` prvek a jeho podřízené prvky.

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
    <VirtualNetworkSite name="Group <RG-VNet> <VNet-name>"/>
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

Následující tabulka popisuje podřízené prvky `NetworkConfiguration` prvku.

| Element       | Popis |
| ------------- | ----------- |
| Ovládací prvek AccessControl | Nepovinný parametr. Určuje pravidla pro přístup ke koncovým bodům v cloudové službě. Název řízení přístupu je definován `name` řetězcem pro atribut. Prvek `AccessControl` obsahuje jeden `Rule` nebo více prvků. Lze definovat `AccessControl` více než jeden prvek.|
| Pravidlo | Nepovinný parametr. Určuje akci, která má být provedena pro zadaný rozsah adres IP podsítě. Pořadí pravidla je definováno řetězcovou hodnotou atributu. `order` Čím nižší je číslo pravidla, tím vyšší je priorita. Pravidla mohou být například zadána s čísly objednávek 100, 200 a 300. Pravidlo s číslem objednávky 100 má přednost před pravidlem, které má pořadí 200.<br /><br /> Akce pro pravidlo je definována řetězcem pro `action` atribut. Možné hodnoty:<br /><br /> -   `permit`– Určuje, že s koncovým bodem mohou komunikovat pouze pakety ze zadaného rozsahu podsítě.<br />-   `deny`– Určuje, že přístup bude odepřen koncovým bodům v zadaném rozsahu podsítí.<br /><br /> Rozsah podsítě adres IP, které jsou ovlivněny pravidlem, je `remoteSubnet` definován řetězcem pro atribut. Popis pravidla je definován řetězcem pro `description` atribut.|
| Koncový bodAcl | Nepovinný parametr. Určuje přiřazení pravidel řízení přístupu ke koncovému bodu. Název role, která obsahuje koncový bod, je definován `role` řetězcem pro atribut. Název koncového bodu je definován řetězcem `endpoint` pro atribut. Název sady `AccessControl` pravidel, která by měla být použita na koncový `accessControl` bod, je definován v řetězci pro atribut. Lze definovat `EndpointAcl` více prvků.|
| Server Dns | Nepovinný parametr. Určuje nastavení serveru DNS. Nastavení serverů DNS můžete zadat bez virtuální sítě. Název serveru DNS je definován řetězcem `name` atributu. Ip adresa serveru DNS je definována řetězcem atributu. `IPAddress` Adresa IP musí být platná adresa IPv4.|
| Web VirtualNetworkSite | Nepovinný parametr. Určuje název lokality virtuální sítě, ve které chcete nasadit cloudovou službu. Toto nastavení nevytvoří web virtuální sítě. Odkazuje na web, který byl dříve definován v síťovém souboru pro vaši virtuální síť. Cloudová služba může být pouze členem jedné virtuální sítě. Pokud toto nastavení nezadáte, cloudová služba nebude nasazena do virtuální sítě. Název lokality virtuální sítě je definován řetězcem atributu. `name`|
| Adresa instance | Nepovinný parametr. Určuje přidružení role k podsíti nebo sadě podsítí ve virtuální síti. Když přidružíte název role k adrese instance, můžete určit podsítě, ke kterým má být tato role přidružena. Obsahuje `InstanceAddress` prvek Podsítě. Název role, která je přidružena k podsíti nebo podsíti, `roleName` je definován řetězcem atributu.|
| Podsíť | Nepovinný parametr. Určuje podsíť, která odpovídá názvu podsítě v konfiguračním síťovém souboru. Název podsítě je definován řetězcem atributu. `name`|
| Reservedip | Nepovinný parametr. Určuje vyhrazenou adresu IP, která by měla být přidružena k nasazení. K vytvoření vyhrazené adresy IP je nutné použít použít použít vytvořit vyhrazenou adresu IP. Každé nasazení v cloudové službě může být přidruženo k jedné vyhrazené IP adrese. Název vyhrazené adresy IP je definován řetězcem `name` atributu.|

## <a name="see-also"></a>Viz také
[Schéma konfigurace cloudové služby (klasické)](schema-cscfg-file.md)
