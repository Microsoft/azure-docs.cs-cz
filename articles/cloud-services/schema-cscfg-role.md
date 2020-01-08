---
title: Schéma role Azure Cloud Services | Microsoft Docs
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 12
author: tgore03
ms.author: tagore
ms.openlocfilehash: 0f0e79b462726b1aa6a953a4b8c92334d6b16492
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449089"
---
# <a name="azure-cloud-services-config-role-schema"></a>Schéma role konfigurace Azure Cloud Services

Element `Role` konfiguračního souboru určuje počet instancí rolí, které mají být nasazeny pro každou roli ve službě, hodnoty všech nastavení konfigurace a kryptografické otisky pro všechny certifikáty přidružené k roli.

Další informace o schématu konfigurace služby Azure najdete v tématu [schéma konfigurace cloudové služby (Classic)](schema-cscfg-file.md). Další informace o schématu definice služby Azure najdete v tématu [schéma definice cloudové služby (Classic)](schema-csdef-file.md).

##  <a name="Role"></a>Element role
Následující příklad ukazuje prvek `Role` a jeho podřízené prvky.

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

Následující tabulka popisuje atributy prvku `Role`.

| Atribut | Popis |
| --------- | ----------- |
| jméno   | Povinná hodnota. Určuje název role. Název se musí shodovat s názvem zadaným pro roli v definičním souboru služby.|
| vmName | Nepovinný parametr. Určuje název DNS pro virtuální počítač. Název nesmí být delší než 10 znaků.|

Následující tabulka popisuje podřízené prvky prvku `Role`.

| Element | Popis |
| ------- | ----------- |
| Instance | Povinná hodnota. Určuje počet instancí, které mají být pro roli nasazeny. Počet instancí je definován celým číslem pro atribut `count`.|
| Nastavení   | Nepovinný parametr. Určuje název nastavení a hodnotu v kolekci nastavení pro roli. Název nastavení je definován řetězcem pro atribut `name` a hodnota nastavení je definována řetězcem pro atribut `value`.|
| Certifikát | Nepovinný parametr. Určuje název, kryptografický otisk a algoritmus certifikátu služby, který má být přidružen k roli. Název certifikátu je definován řetězcem pro atribut `name`. Kryptografický otisk certifikátu je definován pomocí řetězce hexadecimálních čísel, který neobsahuje žádné mezery pro atribut `thumbprint`. Šestnáctková čísla musí být reprezentovaná pomocí číslic a znaků abecedy Alpha. Algoritmus certifikátu je definován řetězcem pro atribut `thumbprintAlgorithm`.|

## <a name="see-also"></a>Viz také
[Schéma konfigurace cloudové služby (Classic)](schema-cscfg-file.md)