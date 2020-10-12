---
title: Schéma role Azure Cloud Services | Microsoft Docs
description: Element role konfiguračního souboru služby určuje, kolik instancí rolí se má nasadit pro každou roli, konfigurační hodnoty a kryptografické otisky certifikátů.
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 12
author: tgore03
ms.author: tagore
ms.openlocfilehash: b64f9d27e382a39b132593502fed32c565af473a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "79528417"
---
# <a name="azure-cloud-services-config-role-schema"></a>Schéma role konfigurace Azure Cloud Services

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