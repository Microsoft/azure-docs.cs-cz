---
title: Schéma role cloudových služeb Azure | Dokumenty společnosti Microsoft
description: Prvek role konfiguračního souboru služby určuje, kolik instancí rolí se má nasadit pro každou roli, hodnoty konfigurace a kryptografické otisky certifikátu.
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 12
author: tgore03
ms.author: tagore
ms.openlocfilehash: b64f9d27e382a39b132593502fed32c565af473a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528417"
---
# <a name="azure-cloud-services-config-role-schema"></a>Schéma konfigurace konfigurace cloudových služeb Azure

Prvek `Role` konfiguračního souboru určuje počet instancí rolí, které mají být nasazeny pro každou roli ve službě, hodnoty všech nastavení konfigurace a kryptografické otisky pro všechny certifikáty přidružené k roli.

Další informace o schématu konfigurace služby Azure najdete v tématu [Schéma konfigurace cloudové služby (klasické).](schema-cscfg-file.md) Další informace o schématu definice služby Azure najdete v [tématu Schéma definice cloudové služby (klasické).](schema-csdef-file.md)

##  <a name="role-element"></a><a name="Role"></a>Prvek role
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
| jméno   | Povinná hodnota. Určuje název role. Název se musí shodovat s názvem, který je uveden pro roli v souboru definice služby.|
| vmName | Nepovinný parametr. Určuje název DNS virtuálního počítače. Název musí být 10 znaků nebo méně.|

Následující tabulka popisuje podřízené prvky `Role` prvku.

| Element | Popis |
| ------- | ----------- |
| Instance | Povinná hodnota. Určuje počet instancí, které mají být nasazeny pro roli. Počet instancí je definován celé číslo `count` pro atribut.|
| Nastavení   | Nepovinný parametr. Určuje název a hodnotu nastavení v kolekci nastavení role. Název nastavení je definován řetězcem `name` pro atribut a hodnota nastavení je `value` definována řetězcem pro atribut.|
| Certifikát | Nepovinný parametr. Určuje název, kryptografický otisk a algoritmus certifikátu služby, který má být přidružen k roli. Název certifikátu je definován řetězcem atributu. `name` Kryptografický otisk certifikátu je definován řetězcem šestnáctkových čísel, `thumbprint` která neobsahují žádné mezery pro atribut. Šestnáctková čísla musí být reprezentována číslicemi a velkými alfa znaky. Algoritmus certifikátu je definován `thumbprintAlgorithm` řetězcem pro atribut.|

## <a name="see-also"></a>Viz také
[Schéma konfigurace cloudové služby (klasické)](schema-cscfg-file.md)