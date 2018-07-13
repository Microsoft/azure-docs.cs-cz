---
title: Definice schématu (soubor .cscfg) služby Azure Cloud Services | Dokumentace Microsoftu
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
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: 96df87a0d49296280140e392509c0d735f904957
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39007018"
---
# <a name="azure-cloud-services-config-schema-cscfg-file"></a>Konfigurační schéma (soubor .cscfg) služby Azure Cloud Services
Konfigurační soubor služby definuje počet instancí role pro nasazení pro každou roli ve službě, hodnoty nastavení konfigurace a kryptografický otisk pro jakékoli certifikáty přidružené k roli. Pokud služba není součástí virtuální sítě, musí být uvedeny informace o konfiguraci sítě v konfiguračním souboru služby, stejně jako v konfiguračním souboru virtuální sítě. Výchozí přípona pro konfigurační soubor služby je .cscfg.

Model služby je popsán [Cloudová služba (klasická) schématu definice](schema-csdef-file.md).

Ve výchozím nastavení, je nainstalován soubor schématu konfigurace diagnostiky Azure do `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` adresáře. Nahraďte `<version>` s nainstalovanou verzí [sady Azure SDK](https://azure.microsoft.com/downloads/).

Další informace o konfiguraci role ve službě naleznete v tématu [co je Cloudová služba model](cloud-services-model-and-package.md).

## <a name="basic-service-configuration-schema"></a>Schéma konfigurace služby na úrovni Basic
Základní formát konfiguračního souboru služby je následujícím způsobem.

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
Následující témata popisují schéma `ServiceConfiguration` element:

- [Schéma rolí](schema-cscfg-role.md)
- [Schéma NetworkConfiguration](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Služba konfigurace Namespace
Obor názvů XML pro konfigurační soubor služby je: `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`.

##  <a name="ServiceConfiguration"></a> ServiceConfiguration Element
`ServiceConfiguration` Prvek je prvek nejvyšší úrovně v konfiguračním souboru služby.

Následující tabulka popisuje atributy `ServiceConfiguration` elementu. Všechny hodnoty atributů jsou typy řetězců.

| Atribut | Popis |
| --------- | ----------- |
|serviceName|Povinná hodnota. Název cloudové služby. Název zde musí odpovídat názvu určenému v definičním souboru služby.|
|atribut osFamily|Volitelné. Určuje hostovaný operační systém, který poběží v instancích role v cloudové službě. Informace o podporovaných verzích hostovaného operačního systému najdete v tématu [verze operačního systému hosta Azure a sady SDK matice kompatibility](cloud-services-guestos-update-matrix.md).<br /><br /> Pokud není zadána `osFamily` hodnoty a jste nenastavili `osVersion` se používá atribut pro konkrétní verze operačního systému hosta, výchozí hodnotu 1.|
|osVersion|Volitelné. Určuje verzi hostovaného operačního systému, který poběží v instancích role v cloudové službě. Další informace o verzích hostovaného operačního systému najdete v tématu [verzí hostovaného operačního systému Azure a sady SDK matice kompatibility](cloud-services-guestos-update-matrix.md).<br /><br /> Můžete určit, že hostovaný operační systém by měl automaticky upgradovat na nejnovější verzi. Chcete-li to provést, nastavte hodnotu `osVersion` atribut `*`. Pokud je nastavena na `*`, instance rolí jsou nasazeny pomocí nejnovější verze hostovaného operačního systému pro zadaná řada operačního systému a budou automaticky upgradovány při vydání nových verzí hostovaného operačního systému.<br /><br /> Chcete-li ručně zadat konkrétní verzi, použijte `Configuration String` z tabulky v **budoucnost, aktuální a přechodné verze operačního systému hosta** část [verzí hostovaného operačního systému Azure a sady SDK matice kompatibility](cloud-services-guestos-update-matrix.md) .<br /><br /> Výchozí hodnota `osVersion` atribut je `*`.|
|schemaVersion|Volitelné. Určuje verzi schématu konfigurace služby. Verze schématu umožňuje vybrat správné nástroje sady SDK a použít pro ověřování schématu v případě více než jedna verze sady SDK je nainstalovaná sada Visual Studio vedle sebe. Další informace o kompatibilitě schématu a verze, najdete v části [verzí hostovaného operačního systému Azure a matice kompatibility sady SDK](cloud-services-guestos-update-matrix.md)|

Konfigurační soubor služby musí obsahovat jeden `ServiceConfiguration` elementu. `ServiceConfiguration` Element může obsahovat libovolný počet `Role` elementy a nula nebo 1 `NetworkConfiguration` elementy.