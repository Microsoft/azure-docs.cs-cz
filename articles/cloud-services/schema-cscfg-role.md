---
title: Schéma role pro Azure Cloud Services (Classic) | Microsoft Docs
description: Element role konfiguračního souboru služby určuje, kolik instancí rolí se má nasadit pro každou roli, konfigurační hodnoty a kryptografické otisky certifikátů.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2dc8e14a4e4d8855abb615632bb7d43b9034d360
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743436"
---
# <a name="azure-cloud-services-classic-config-role-schema"></a>Schéma konfigurační role pro Azure Cloud Services (Classic)

> [!IMPORTANT]
> [Azure Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md) je nový model nasazení založený na Azure Resource Manager pro produkt Azure Cloud Services.V důsledku této změny se Azure Cloud Services běžící na modelu nasazení založeném na Azure Service Manager přejmenovala jako Cloud Services (Classic) a všechna nová nasazení by měla používat [Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md).

`Role`Element konfiguračního souboru určuje počet instancí rolí, které mají být nasazeny pro každou roli ve službě, hodnoty nastavení konfigurace a kryptografické otisky pro všechny certifikáty přidružené k roli.

Další informace o schématu konfigurace služby Azure najdete v tématu [schéma konfigurace cloudové služby (Classic)](schema-cscfg-file.md). Další informace o schématu definice služby Azure najdete v tématu [schéma definice cloudové služby (Classic)](schema-csdef-file.md).

##  <a name="role-element"></a><a name="Role"></a> Element role
Následující příklad ukazuje `Role` prvek a jeho podřízené prvky.

```xml 
<ServiceConfiguration>
  <Role name="<role-name>" vmName="<vm-name>">
    <Instances count="<number-of-instances>"/>
    <ConfigurationSettings>
      <Setting name="<setting-name>" value="<setting-value>" />
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="<certificate-name>" thumbprint="<certificate-thumbprint>" thumbprintAlgorithm="<algorithm>"/>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

Následující tabulka popisuje atributy `Role` prvku.

| Atribut | Popis |
| --------- | ----------- |
| name   | Povinná hodnota. Určuje název role. Název se musí shodovat s názvem zadaným pro roli v definičním souboru služby.|
| vmName | Nepovinný parametr. Určuje název DNS pro virtuální počítač. Název nesmí být delší než 10 znaků.|

Následující tabulka popisuje podřízené prvky `Role` elementu.

| Element | Popis |
| ------- | ----------- |
| Instance | Povinná hodnota. Určuje počet instancí, které mají být pro roli nasazeny. Počet instancí je definován celým číslem pro `count` atribut.|
| Nastavení   | Nepovinný parametr. Určuje název nastavení a hodnotu v kolekci nastavení pro roli. Název nastavení je definován řetězcem pro `name` atribut a hodnota nastavení je definována řetězcem pro `value` atribut.|
| Certifikát | Nepovinný parametr. Určuje název, kryptografický otisk a algoritmus certifikátu služby, který má být přidružen k roli. Název certifikátu je definován řetězcem pro `name` atribut. Kryptografický otisk certifikátu je definován pomocí řetězce hexadecimálních čísel, který neobsahuje žádné mezery pro `thumbprint` atribut. Šestnáctková čísla musí být reprezentovaná pomocí číslic a znaků abecedy Alpha. Algoritmus certifikátu je definován řetězcem pro `thumbprintAlgorithm` atribut.|

## <a name="see-also"></a>Viz také
[Schéma konfigurace cloudové služby (Classic)](schema-cscfg-file.md)