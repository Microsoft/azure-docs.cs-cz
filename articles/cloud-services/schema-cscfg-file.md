---
title: Služby Azure Cloud Services definici schématu (soubor .cscfg) | Microsoft Docs
services: cloud-services
ms.custom: ''
ms.date: 12/07/2016
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 3ddc7fea-3339-4fc0-bdf9-853c32b25f69
caps.latest.revision: 35
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: 6347314e7f279356f4f3944f3238deda84f10fc0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358213"
---
# <a name="azure-cloud-services-config-schema-cscfg-file"></a>Konfigurační schéma (soubor .cscfg) služby Azure Cloud Services
Konfigurační soubor služby definuje počet instancí role pro nasazení pro každou roli v rámci služby, hodnoty nastavení konfigurace a kryptografické otisky pro všechny certifikáty přidružené k roli. Pokud služba je součástí virtuální sítě, musí být uvedeny informace o konfiguraci sítě v konfiguračním souboru služby, a také v konfiguračním souboru virtuální sítě. Výchozí rozšíření pro konfigurační soubor služby je .cscfg.

Model služby je popsán [cloudové služby (klasické) definice schématu](schema-csdef-file.md).

Ve výchozím nastavení je konfiguračního souboru schématu Azure Diagnostics nainstaluje pro `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` adresáře. Nahraďte `<version>` s nainstalovanou verzí [Azure SDK](https://azure.microsoft.com/downloads/).

Další informace o konfiguraci role ve službě najdete v tématu [co je Cloudová služba model](cloud-services-model-and-package.md).

## <a name="basic-service-configuration-schema"></a>Schéma konfigurace služby na úrovni Basic
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
Následující témata popisují schéma pro `ServiceConfiguration` element:

- [Schéma rolí](schema-cscfg-role.md)
- [Schéma NetworkConfiguration](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Namespace konfigurace služby
Obor názvů XML pro konfigurační soubor služby je: `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`.

##  <a name="ServiceConfiguration"></a> Objekt ServiceConfiguration – Element
`ServiceConfiguration` Element je element nejvyšší úrovně v konfiguračním souboru služby.

Následující tabulka popisuje atributy `ServiceConfiguration` elementu. Všechny hodnoty atributů jsou typy řetězec.

| Atribut | Popis |
| --------- | ----------- |
|serviceName|Povinná hodnota. Název cloudové služby. Daný název tady musí odpovídat názvu zadaná v souboru definice služby.|
|atribut osFamily|Volitelné. Určuje hostovaného operačního systému, který se spustí u instancí role v rámci cloudové služby. Informace o podporovaných verzích hostovaného operačního systému najdete v tématu [verze operačního systému hosta Azure a kompatibilních sad SDK](cloud-services-guestos-update-matrix.md).<br /><br /> Pokud nepoužijete `osFamily` hodnota a jste nenastavili `osVersion` je použit atribut na konkrétní verzi hostovaného operačního systému, výchozí hodnotu 1.|
|OsVersion|Volitelné. Určuje verzi operačního systému hosta, který se spustí u instancí role v rámci cloudové služby. Další informace o verzích hostovaného operačního systému najdete v tématu [verzí hostovaného operačního systému Azure a kompatibilních sad SDK](cloud-services-guestos-update-matrix.md).<br /><br /> Můžete určit, že hostovaného operačního systému by měl automaticky upgradován na nejnovější verzi. Chcete-li to provést, nastavte hodnotu `osVersion` atribut `*`. Pokud nastavíte hodnotu `*`, instancí role jsou nasazeny pomocí nejnovější verze hostovaného operačního systému pro zadaná řada operačního systému a budou automaticky upgradovány jsou vydávány nové verze hostovaného operačního systému.<br /><br /> Chcete-li ručně zadat konkrétní verzi, použijte `Configuration String` z tabulky v **budoucí, aktuální a přechodném verze operačního systému hosta** části [verzí hostovaného operačního systému Azure a kompatibilních sad SDK](cloud-services-guestos-update-matrix.md) .<br /><br /> Výchozí hodnota `osVersion` atribut je `*`.|
|schemaVersion|Volitelné. Určuje verzi schématu služby konfigurace. Verze schématu umožňuje sadě Visual Studio k výběru správné sady SDK nástroje má použít pro ověření schématu, pokud je nainstalovaná více než jedna verze sady SDK vedle sebe. Další informace o kompatibilitě schéma a verze najdete v tématu [verzí hostovaného operačního systému Azure a kompatibilních sad SDK](cloud-services-guestos-update-matrix.md)|

Konfigurační soubor služby musí obsahovat jeden `ServiceConfiguration` elementu. `ServiceConfiguration` Element může obsahovat libovolný počet `Role` elementy a nula nebo 1 `NetworkConfiguration` elementy.