---
title: Dvojité šifrování v Microsoft Azure
description: Tento článek popisuje, jak Microsoft Azure poskytuje dvojité šifrování pro data v klidovém režimu a data při přenosu.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: terrylan
ms.openlocfilehash: 020e8249f57ccb1a14da798a717a00dcc3962389
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88227154"
---
# <a name="double-encryption"></a>Dvojité šifrování
Dvojité šifrování je v případě, že je povolená dvě nebo víc nezávislých vrstev šifrování proti ohrožení bezpečnosti jakékoli jedné vrstvy šifrování. Použití dvou vrstev šifrování zmírnit hrozby, které jsou součástí šifrování dat. Například:

- Chyby konfigurace v šifrování dat
- Chyby implementace v šifrovacím algoritmu
- Ohrožení zabezpečení jednoho šifrovacího klíče

Azure poskytuje dvojité šifrování pro data v klidovém režimu a data při přenosu.

## <a name="data-at-rest"></a>Neaktivní uložená data
Přístup Microsoftu k povolení dvou vrstev šifrování pro neaktivní data:

- **Šifrování disku pomocí klíčů spravovaných zákazníkem**. Máte k dispozici vlastní klíč pro šifrování disku. Můžete přenést vlastní klíče do Key Vault (BYOK – Bring Your Own Key) nebo vygenerovat nové klíče v Azure Key Vault k zašifrování požadovaných prostředků.
- **Šifrování infrastruktury pomocí klíčů spravovaných platformou**  Ve výchozím nastavení jsou disky automaticky šifrovány v klidovém stavu pomocí šifrovacích klíčů spravovaných platformou.

## <a name="data-in-transit"></a>Přenášená data
Přístup Microsoftu k povolení dvou vrstev šifrování pro přenos dat je následující:

- Přenosové **šifrování pomocí protokolu TLS (Transport Layer Security) 1,2 umožňuje chránit data při cestování mezi cloudovou službou a vámi**. Veškerý provoz opouštějící datacentrum se zašifruje při přenosu, i když je cíl přenosu jiný řadič domény ve stejné oblasti. Protokol TLS 1,2 je použit jako výchozí protokol zabezpečení. Protokol TLS zajišťuje silné ověřování, soukromí zpráv a integritu (povolení detekce manipulace, zachycení a padělání zpráv), interoperability, flexibility algoritmů a snadné nasazení a použití.
- **Další úroveň šifrování poskytovaná v infrastruktuře infrastruktury**. Metoda šifrování vrstvy propojení dat pomocí standardů zabezpečení IEEE 802.1 AE MAC (označované také jako MACsec) se aplikuje z Point-to-Point v podkladovém síťovém hardwaru. Pokaždé, když se pohyb zákazníků Azure pohybuje mezi datovými centry – mimo fyzické hranice, která není řízená Microsoftem (nebo jménem Microsoftu) – pakety se před odesláním šifrují a dešifrují a zabraňují tak fyzickým útokům typu "man-in-the-middle" nebo "Snoop/wiretapping". Vzhledem k tomu, že je tato technologie integrovaná na samotném síťovém hardwaru, zajišťuje na síťovém hardwaru přenosovou rychlost při nezvyšování latence spojení. Toto šifrování MACsec je ve výchozím nastavení zapnuté pro všechny přenosy dat Azure na cestách v rámci oblasti nebo mezi oblastmi a na straně zákazníků není potřeba žádná akce, kterou by bylo možné povolit.

## <a name="next-steps"></a>Další kroky
Přečtěte si, jak [se používá šifrování v Azure](encryption-overview.md).
