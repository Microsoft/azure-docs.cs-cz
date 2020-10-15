---
title: Schéma Azure Cloud Services NetworkConfiguration | Microsoft Docs
description: Přečtěte si o podřízených prvcích elementu NetworkConfiguration konfiguračního souboru služby, který určuje hodnoty Virtual Network a DNS.
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 28
author: tgore03
ms.author: tagore
ms.openlocfilehash: a3f62bb33332e818cc40c5195a1cc2f667f819b4
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92072573"
---
# <a name="azure-cloud-services-config-networkconfiguration-schema"></a>Schéma NetworkConfiguration pro Azure Cloud Services config

`NetworkConfiguration`Element konfiguračního souboru služby určuje Virtual Network a hodnoty DNS. Tato nastavení jsou pro Cloud Services volitelná.

Následující prostředek můžete použít k získání dalších informací o virtuálních sítích a přidružených schématech:

- [Schéma konfigurace cloudové služby (Classic)](schema-cscfg-file.md)
- [Schéma definice cloudové služby (Classic)](schema-csdef-file.md)
- [Vytvoření Virtual Network (Classic)](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal)

## <a name="networkconfiguration-element"></a>Element NetworkConfiguration
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

Následující tabulka popisuje podřízené prvky `NetworkConfiguration` elementu.

| Element       | Popis |
| ------------- | ----------- |
| AccessControl | Nepovinný parametr. Určuje pravidla pro přístup k koncovým bodům v cloudové službě. Název řízení přístupu je definován řetězcem pro `name` atribut. `AccessControl`Element obsahuje jeden nebo více `Rule` prvků. `AccessControl`Je možné definovat více než jeden prvek.|
| Pravidlo | Nepovinný parametr. Určuje akci, která má být provedena pro zadaný rozsah IP adres. Pořadí pravidla je definováno hodnotou řetězce pro `order` atribut. Čím nižší je číslo pravidla, tím vyšší prioritou. Můžete například zadat pravidla s čísly pořadí 100, 200 a 300. Pravidlo s pořadovým číslem 100 má přednost před pravidlem, které má pořadí 200.<br /><br /> Akce pro pravidlo je definována řetězcem pro `action` atribut. Možné hodnoty:<br /><br /> -   `permit` – Určuje, že s koncovým bodem můžou komunikovat jenom pakety ze zadaného rozsahu podsítě.<br />-   `deny` – Určuje, že přístup je odepřen do koncových bodů v zadaném rozsahu podsítě.<br /><br /> Rozsah podsítě IP adres, na které se pravidlo vztahuje, je definován řetězcem pro `remoteSubnet` atribut. Popis pravidla je definován řetězcem pro `description` atribut.|
| EndpointAcl | Nepovinný parametr. Určuje přiřazení pravidel řízení přístupu ke koncovému bodu. Název role, která obsahuje koncový bod, je definován řetězcem pro `role` atribut. Název koncového bodu je definován řetězcem pro `endpoint` atribut. Název sady `AccessControl` pravidel, která by měla být použita na koncový bod, je definována v řetězci pro `accessControl` atribut. Lze definovat více než jeden `EndpointAcl` prvek.|
| Serveru DNS | Nepovinný parametr. Určuje nastavení serveru DNS. Můžete zadat nastavení pro servery DNS bez Virtual Network. Název serveru DNS je definovaný řetězcem pro `name` atribut. IP adresa serveru DNS je definována řetězcem pro `IPAddress` atribut. IP adresa musí být platná IPv4 adresa.|
| VirtualNetworkSite | Nepovinný parametr. Určuje název lokality Virtual Network, do které chcete nasadit cloudovou službu. Toto nastavení nevytvoří web Virtual Network. Odkazuje na lokalitu, která byla dříve definována v síťovém souboru pro váš Virtual Network. Cloudová služba může být jenom členem jednoho Virtual Network. Pokud toto nastavení nezadáte, cloudová služba nebude nasazená do Virtual Network. Název Virtual Network lokality je definován řetězcem pro `name` atribut.|
| InstanceAddress | Nepovinný parametr. Určuje přidružení role k podsíti nebo sadě podsítí v Virtual Network. Pokud přidružíte název role k adrese instance, můžete určit podsítě, ke kterým chcete přiřadit tuto roli. `InstanceAddress`Obsahuje element podsítě. Název role, která je přidružená k podsíti nebo podsítím, je definován řetězcem pro `roleName` atribut.|
| Podsíť | Nepovinný parametr. Určuje podsíť, která odpovídá názvu podsítě v souboru konfigurace sítě. Název podsítě je definován řetězcem pro `name` atribut.|
| Adresu | Nepovinný parametr. Určuje rezervovanou IP adresu, která má být přidružena k nasazení. K vytvoření rezervované IP adresy je nutné použít Vyhrazená IP adresa vytvořit adresu. Každé nasazení v cloudové službě může být přidruženo k jedné rezervované IP adrese. Název rezervované IP adresy je definován řetězcem pro `name` atribut.|

## <a name="see-also"></a>Viz také
[Schéma konfigurace cloudové služby (Classic)](schema-cscfg-file.md)