---
title: Schéma definice Azure Cloud Services (Rozšířená podpora) (soubor. cscfg) | Microsoft Docs
description: Informace týkající se schématu definice pro Cloud Services (Rozšířená podpora)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 3aac5ca8d146f0cd74f483846d22fdf0052f86f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98744294"
---
# <a name="azure-cloud-services-extended-support-config-schema-cscfg-file"></a>Schéma konfigurace Azure Cloud Services (Rozšířená podpora) (soubor. cscfg)

Konfigurační soubor služby určuje počet instancí rolí, které mají být nasazeny pro každou roli ve službě, hodnoty nastavení konfigurace a kryptografické otisky pro všechny certifikáty přidružené k roli. Je-li služba součástí Virtual Network, musí být v konfiguračním souboru služby k dispozici informace o konfiguraci sítě, a to i v konfiguračním souboru virtuální sítě. Výchozí přípona konfiguračního souboru služby je cscfg.

Model služby je popsaný v rámci [schématu definice cloudové služby (Rozšířená podpora)](schema-csdef-file.md).

Ve výchozím nastavení se soubor konfiguračního schématu Azure Diagnostics nainstaluje do `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` adresáře. Nahraďte `<version>` nainstalovanou verzí sady [Azure SDK](https://azure.microsoft.com/downloads/).

Další informace o konfiguraci rolí ve službě najdete v tématu [co je model cloudové služby](../cloud-services/cloud-services-model-and-package.md).

## <a name="basic-service-configuration-schema"></a>Základní schéma konfigurace služby
Základní formát konfiguračního souboru služby je následující.

```xml
<ServiceConfiguration serviceName="<service-name>" osFamily="<osfamily-number>" osVersion="<os-version>" schemaVersion="<schema-version>">

  <Role …>
    …
  </Role>

  <NetworkConfiguration>
    …
  </NetworkConfiguration>

</ServiceConfiguration>
```

## <a name="schema-definitions"></a>Definice schémat
Následující témata popisují schéma pro `ServiceConfiguration` element:

- [Schéma rolí](schema-cscfg-role.md)
- [Schéma NetworkConfiguration](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Obor názvů konfigurace služby
Obor názvů XML pro konfigurační soubor služby je: `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration` .

##  <a name="serviceconfiguration-element"></a><a name="ServiceConfiguration"></a> Element ServiceConfiguration
`ServiceConfiguration`Element je element nejvyšší úrovně konfiguračního souboru služby.

Následující tabulka popisuje atributy `ServiceConfiguration` prvku. Všechny hodnoty atributů jsou typy řetězců.

| Atribut | Popis |
| --------- | ----------- |
|serviceName|Povinná hodnota. Název cloudové služby. Zadaný název se musí shodovat s názvem zadaným v definičním souboru služby.|
|Atribut|Nepovinný parametr. Určuje hostovaný operační systém, který se spustí na instancích role v cloudové službě. Informace o podporovaných verzích hostovaných operačních systémů najdete v tématu [věnovaném vydání hostovaného operačního systému Azure a s maticí kompatibility SDK](../cloud-services/cloud-services-guestos-update-matrix.md).<br /><br /> Pokud nezahrnete `osFamily` hodnotu a nenastavíte `osVersion` atribut na konkrétní verzi HOSTOVANÉHO operačního systému, použije se výchozí hodnota 1.|
|osVersion|Nepovinný parametr. Určuje verzi hostovaného operačního systému, který se spustí na instancích role v cloudové službě. Další informace o verzích operačního systému hosta najdete v tématu [věnovaném vydání hostovaného operačního systému Azure a s maticí kompatibility SDK](../cloud-services/cloud-services-guestos-update-matrix.md).<br /><br /> Můžete určit, že se má hostovaný operační systém automaticky upgradovat na nejnovější verzi. To provedete tak, že nastavíte hodnotu `osVersion` atributu na `*` . Při nastavení na `*` se instance rolí nasazují pomocí nejnovější verze hostovaného operačního systému pro zadanou rodinu operačních systémů a při vydání nových verzí hostovaného operačního systému se automaticky upgradují.<br /><br /> Konkrétní verzi určíte ručně tak, že použijete v `Configuration String` tabulce v části **budoucí verze operačního systému hosta** v rámci [vydání Azure hostovaného operačního systému a v matici kompatibility SDK](../cloud-services/cloud-services-guestos-update-matrix.md).<br /><br /> Výchozí hodnota pro `osVersion` atribut je `*` .|
|schemaVersion|Nepovinný parametr. Určuje verzi schématu konfigurace služby. Verze schématu umožňuje sadě Visual Studio vybrat správné nástroje sady SDK, které se mají použít při ověřování schématu, pokud je nainstalovaná více než jedna verze sady SDK vedle sebe. Další informace o kompatibilitě schématu a verzí najdete v tématu věnovaném [vydáním hostovaného operačního systému Azure a s maticí kompatibility SDK](../cloud-services/cloud-services-guestos-update-matrix.md) .|

Konfigurační soubor služby musí obsahovat jeden `ServiceConfiguration` prvek. `ServiceConfiguration`Element může obsahovat libovolný počet `Role` prvků a 0 nebo 1 `NetworkConfiguration` prvky.

## <a name="see-also"></a>Viz také

[Schéma definice Azure Cloud Services (Rozšířená podpora) (soubor csdef)](schema-csdef-file.md)