---
title: Schéma Role služby Azure Cloud Services | Microsoft Docs
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: e4fbffc1-98eb-449c-971c-de415e45ab34
caps.latest.revision: 12
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: 2f5c657bb80ad0788bcc3dd19d962b3f21afa4a8
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358995"
---
# <a name="azure-cloud-services-config-role-schema"></a>Schéma konfigurace Role služby Azure Cloud Services

`Role` Element konfiguračního souboru Určuje počet instancí role pro nasazení pro každou roli v rámci služby, hodnoty všech nastavení konfigurace a kryptografické otisky pro všechny certifikáty, které jsou přidružené k roli.

Další informace o schématu konfigurace služby Azure najdete v tématu [schéma konfigurace cloudové služby (klasické)](schema-cscfg-file.md). Další informace o schématu definice služby Azure najdete v tématu [cloudové služby (klasické) definice schématu](schema-csdef-file.md).

##  <a name="Role"></a> Role Element
Následující příklad ukazuje `Role` elementu a jeho podřízené elementy.

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

Následující tabulka popisuje atributy `Role` elementu.

| Atribut | Popis |
| --------- | ----------- |
| jméno   | Povinná hodnota. Určuje název role. Název musí odpovídat názvu zadaný pro roli v definičním souboru služby.|
| vmName | Volitelné. Určuje název DNS pro virtuální počítač. Název musí mít 10 znaků nebo méně.|

Následující tabulka popisuje podřízených elementů `Role` elementu.

| Element | Popis |
| ------- | ----------- |
| Instance | Povinná hodnota. Určuje počet instancí pro nasazení pro roli. Počet instancí je definována pro celé číslo `count` atribut.|
| Nastavení   | Volitelné. Určuje název nastavení a hodnota v kolekci nastavení pro roli. Název nastavení je definováno řetězec pro `name` atribut a hodnotu nastavení je definováno řetězec pro `value` atribut.|
| Certifikát | Volitelné. Určuje název, kryptografický otisk a algoritmus certifikát služby, které se má s touto rolí spojení. Název certifikátu je definována řetězce pro `name` atribut. Kryptografický otisk certifikátu je definována řetězec neobsahující žádné mezery pro šestnáctková číslice `thumbprint` atribut. Hexadecimální číslice musí být reprezentován pomocí číslic a velká písmena alfanumerické znaky. Algoritmus certifikátu je definována řetězce pro `thumbprintAlgorithm` atribut.|

## <a name="see-also"></a>Viz také
[Cloudové služby (klasické) schéma konfigurace](schema-cscfg-file.md)