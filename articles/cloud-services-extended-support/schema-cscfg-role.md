---
title: Schéma role Azure Cloud Services (Rozšířená podpora) | Microsoft Docs
description: Informace týkající se schématu rolí pro Cloud Services (Rozšířená podpora)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2567f5bb817a34f6274d5e265a266d67a9c81413
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744300"
---
# <a name="azure-cloud-services-extended-support-config-role-schema"></a>Schéma konfigurační role Azure Cloud Services (Rozšířená podpora)

`Role`Element konfiguračního souboru určuje počet instancí rolí, které mají být nasazeny pro každou roli ve službě, hodnoty nastavení konfigurace a kryptografické otisky pro všechny certifikáty přidružené k roli.

Další informace o schématu konfigurace služby Azure najdete v tématu [schéma konfigurace cloudové služby (Rozšířená podpora)](schema-cscfg-file.md). Další informace o schématu definice služby Azure najdete v tématu [schéma definice cloudové služby (Rozšířená podpora)](schema-csdef-file.md).

##  <a name="role-element"></a><a name="Role"></a> element role
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
[Schéma konfigurace cloudové služby (Rozšířená podpora)](schema-cscfg-file.md).