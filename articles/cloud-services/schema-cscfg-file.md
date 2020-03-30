---
title: Schéma definice cloudových služeb Azure (soubor cscfg) | Dokumenty společnosti Microsoft
description: Soubor konfigurace služby (.cscfg) určuje, kolik instancí rolí se má nasadit pro každou roli, hodnoty konfigurace a kryptografické otisky certifikátu pro roli.
services: cloud-services
ms.custom: ''
ms.date: 12/07/2016
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 35
author: tgore03
ms.author: tagore
ms.openlocfilehash: cb77181e00c97b7f426429793f17af3cb5e84ebe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534741"
---
# <a name="azure-cloud-services-config-schema-cscfg-file"></a>Schéma konfigurace cloudových služeb Azure (soubor cscfg)
Konfigurační soubor služby určuje počet instancí rolí, které mají být nasazeny pro každou roli ve službě, hodnoty všech nastavení konfigurace a kryptografické otisky všech certifikátů přidružených k roli. Pokud je služba součástí virtuální sítě, musí být informace o konfiguraci sítě poskytnuty v konfiguračním souboru služby i v konfiguračním souboru virtuální sítě. Výchozí přípona konfiguračního souboru služby je .cscfg.

Model služby je popsán schéma [definice cloudové služby (klasické).](schema-csdef-file.md)

Ve výchozím nastavení je do `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` adresáře nainstalován soubor schématu konfigurace diagnostiky Azure. Nahraďte `<version>` nainstalovanou verzí [sady Azure SDK](https://azure.microsoft.com/downloads/).

Další informace o konfiguraci rolí ve službě naleznete v [tématu Co je model cloudové služby](cloud-services-model-and-package.md).

## <a name="basic-service-configuration-schema"></a>Schéma konfigurace základní služby
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

## <a name="schema-definitions"></a>Definice schématu
Následující témata popisují schéma `ServiceConfiguration` prvku:

- [Schéma rolí](schema-cscfg-role.md)
- [Schéma NetworkConfiguration](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Obor názvů konfigurace služby
Obor názvů XML pro konfigurační soubor služby je: `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`.

##  <a name="serviceconfiguration-element"></a><a name="ServiceConfiguration"></a>Prvek konfigurace služby
Prvek `ServiceConfiguration` je prvek nejvyšší úrovně konfiguračního souboru služby.

Následující tabulka popisuje atributy `ServiceConfiguration` prvku. Všechny hodnoty atributů jsou typy řetězců.

| Atribut | Popis |
| --------- | ----------- |
|Název_služby|Povinná hodnota. Název cloudové služby. Zde uvedený název se musí shodovat s názvem zadaným v souboru definice služby.|
|osRodina|Nepovinný parametr. Určuje hostovaný operační systém, který bude spuštěn na instancích rolí v cloudové službě. Informace o podporovaných verzích hostovaného operačního systému naleznete v [tématu Azure Guest OS Releases a SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md).<br /><br /> Pokud nezahrnete hodnotu `osFamily` a `osVersion` nenastavili jste atribut na konkrétní verzi hostovaného operačního systému, použije se výchozí hodnota 1.|
|osVersion|Nepovinný parametr. Určuje verzi hostovaného operačního systému, který bude spuštěn na instancích rolí v cloudové službě. Další informace o verzích hostovaného operačního systému najdete [v tématu Verze hostovaného operačního systému Azure a Matice kompatibility sad SDK](cloud-services-guestos-update-matrix.md).<br /><br /> Můžete určit, že hostovaný operační systém by měl být automaticky upgradován na nejnovější verzi. Chcete-li to provést, `osVersion` nastavte `*`hodnotu atributu na . Pokud je `*`nastavena na , instance rolí jsou nasazeny pomocí nejnovější verze hostovaného operačního systému pro zadanou rodinu operačního systému a budou automaticky upgradovány při vydání nových verzí hostovaného operačního systému.<br /><br /> Chcete-li určit konkrétní verzi `Configuration String` ručně, použijte z tabulky v **části Budoucí, Aktuální a Přechodná verze hostovaného operačního systému** [Azure verze hostovaného operačního systému Azure a SDK Matice kompatibility](cloud-services-guestos-update-matrix.md).<br /><br /> Výchozí hodnota atributu `osVersion` `*`je .|
|verze schématu|Nepovinný parametr. Určuje verzi schématu Konfigurace služby. Verze schématu umožňuje sadě Visual Studio vybrat správné nástroje sady SDK, které se mají použít pro ověření schématu, pokud je nainstalována více než jedna verze sady SDK vedle sebe. Další informace o kompatibilitě schémat u verze najdete v [tématu Verze hostovaného operačního systému Azure a Matice kompatibility sad SDK](cloud-services-guestos-update-matrix.md)|

Konfigurační soubor `ServiceConfiguration` služby musí obsahovat jeden prvek. Prvek `ServiceConfiguration` může obsahovat `Role` libovolný počet prvků `NetworkConfiguration` a nula nebo 1 prvky.