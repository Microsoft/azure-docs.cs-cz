---
title: Schéma Azure Cloud Services NetworkConfiguration | Microsoft Docs
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 28
author: tgore03
ms.author: tagore
ms.openlocfilehash: cacc8b1f2909965594fdf0d841963e792acf648c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75385420"
---
# <a name="azure-cloud-services-config-networkconfiguration-schema"></a>Schéma NetworkConfiguration pro Azure Cloud Services config

`NetworkConfiguration` element konfiguračního souboru služby určuje Virtual Network a hodnoty DNS. Tato nastavení jsou pro Cloud Services volitelná.

Následující prostředek můžete použít k získání dalších informací o virtuálních sítích a přidružených schématech:

- [Schéma konfigurace cloudové služby (Classic)](schema-cscfg-file.md)
- [Schéma definice cloudové služby (Classic)](schema-csdef-file.md)
- [Vytvoření Virtual Network (Classic)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)

## <a name="networkconfiguration-element"></a>Element NetworkConfiguration
Následující příklad ukazuje prvek `NetworkConfiguration` a jeho podřízené prvky.

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

Následující tabulka popisuje podřízené prvky prvku `NetworkConfiguration`.

| Element       | Popis |
| ------------- | ----------- |
| AccessControl | Nepovinný parametr. Určuje pravidla pro přístup k koncovým bodům v cloudové službě. Název řízení přístupu je definován řetězcem pro `name` atributu. Element `AccessControl` obsahuje jeden nebo více `Rule` prvků. Je možné definovat více než jeden prvek `AccessControl`.|
| Pravidlo | Nepovinný parametr. Určuje akci, která má být provedena pro zadaný rozsah IP adres. Pořadí pravidla je definováno hodnotou řetězce pro atribut `order`. Čím nižší je číslo pravidla, tím vyšší prioritou. Můžete například zadat pravidla s čísly pořadí 100, 200 a 300. Pravidlo s pořadovým číslem 100 má přednost před pravidlem, které má pořadí 200.<br /><br /> Akce pro pravidlo je definována řetězcem pro atribut `action`. Možné hodnoty:<br /><br /> -   `permit` – určuje, že s koncovým bodem můžou komunikovat jenom pakety ze zadaného rozsahu podsítě.<br />-   `deny` – určuje, že přístup k koncovým bodům v zadaném rozsahu podsítě byl odepřen.<br /><br /> Rozsah podsítě IP adres, na které se pravidlo vztahuje, je definován řetězcem pro atribut `remoteSubnet`. Popis pravidla je definován pomocí řetězce pro atribut `description`.|
| EndpointAcl | Nepovinný parametr. Určuje přiřazení pravidel řízení přístupu ke koncovému bodu. Název role, která obsahuje koncový bod, je definován řetězcem pro atribut `role`. Název koncového bodu je definován řetězcem pro atribut `endpoint`. Název sady pravidel `AccessControl`, které by měly být aplikovány na koncový bod, je definován v řetězci pro atribut `accessControl`. Lze definovat více než jeden prvek `EndpointAcl`.|
| DnsServer | Nepovinný parametr. Určuje nastavení serveru DNS. Můžete zadat nastavení pro servery DNS bez Virtual Network. Název serveru DNS je definovaný řetězcem pro atribut `name`. IP adresa serveru DNS je definována řetězcem pro atribut `IPAddress`. IP adresa musí být platná IPv4 adresa.|
| VirtualNetworkSite | Nepovinný parametr. Určuje název lokality Virtual Network, do které chcete nasadit cloudovou službu. Toto nastavení nevytvoří web Virtual Network. Odkazuje na lokalitu, která byla dříve definována v síťovém souboru pro váš Virtual Network. Cloudová služba může být jenom členem jednoho Virtual Network. Pokud toto nastavení nezadáte, cloudová služba nebude nasazená do Virtual Network. Název Virtual Network lokality je definován řetězcem pro atribut `name`.|
| InstanceAddress | Nepovinný parametr. Určuje přidružení role k podsíti nebo sadě podsítí v Virtual Network. Pokud přidružíte název role k adrese instance, můžete určit podsítě, ke kterým chcete přiřadit tuto roli. `InstanceAddress` obsahuje prvek podsítě. Název role, která je přidružena k podsíti nebo podsítím, je definován řetězcem pro atribut `roleName`.|
| Podsíť | Nepovinný parametr. Určuje podsíť, která odpovídá názvu podsítě v souboru konfigurace sítě. Název podsítě je definován řetězcem pro atribut `name`.|
| Adresu | Nepovinný parametr. Určuje rezervovanou IP adresu, která má být přidružena k nasazení. K vytvoření rezervované IP adresy je nutné použít Vyhrazená IP adresa vytvořit adresu. Každé nasazení v cloudové službě může být přidruženo k jedné rezervované IP adrese. Název rezervované IP adresy je definován řetězcem pro atribut `name`.|

## <a name="see-also"></a>Viz také
[Schéma konfigurace cloudové služby (Classic)](schema-cscfg-file.md)
