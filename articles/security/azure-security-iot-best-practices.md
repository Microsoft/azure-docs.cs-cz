---
title: Internet of Things osvědčené postupy zabezpečení | Dokumentace Microsoftu
description: Tento článek poskytuje uspořádaný seznam Microsoft Internet of Things Security Best Practices and obecná doporučení.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 2d5598c5-4c30-481d-b8f4-51ee024ea9a7
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 9413c0503c1b78550776d1c2f6ab8239205a788b
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56117863"
---
# <a name="internet-of-things-security-best-practices"></a>Internet of Things osvědčené postupy zabezpečení

Zabezpečení infrastruktury Internetu věcí (IoT) je kritický podniku pro každého, kdo spojené s řešení IoT. Dopad události zabezpečení související s ohrozit z milionů zařízení IoT z důvodu počet zařízení zahrnutých a distribuovaná povaha těchto zařízení je netriviální a může mít širší dopad.

Zabezpečení IoT z tohoto důvodu musí přístup založený na zabezpečení do hloubky. Data musí být zabezpečené v cloudu a při jejich přesunu přes privátní a veřejné sítě. Metody musí být splněné bezpečně zřízení zařízení IoT, sami. Každá vrstva ze zařízení, sítě, a back-end cloudu vyžaduje záruku silné zabezpečení.

Osvědčené postupy IoT lze označit následujícím způsobem:

* Výrobce hardwaru IoT nebo integrátor
* Řešení IoT pro vývojáře
* Nástroje pro nasazení řešení IoT
* Operátor řešení IoT

Tento článek shrnuje [Internetu z věci osvědčené postupy zabezpečení](../iot-suite/iot-security-best-practices.md). K tomuto článku podrobnější informace najdete.

## <a name="iot-hardware-manufacturer-or-integrator"></a>Výrobce hardwaru IoT nebo integrátor

Pokud jste výrobě hardwaru IoT nebo integrátor hardwaru, postupujte podle níže uvedených osvědčených postupů:

* **Určení oboru, minimální požadavky na hardware**: návrh hardwaru by měl obsahovat minimální požadované pro fungování hardwaru a žádné další funkce. 
* **Ujistěte se, hardware manipulovat testování**: sestavení mechanismy umožňuje zjistit případnou manipulaci fyzického hardwaru, jako je například otevírání titulní zařízení, odebrání součástí zařízení atd. 
* **Sestavení kolem zabezpečený hardware**: Pokud [COGS](https://en.wikipedia.org/wiki/Cost_of_goods_sold) povolit, sestavovat funkce zabezpečení, jako je zabezpečené a šifrované úložiště a funkce spouštěcí Trusted Platform Module TPM.
* **Zabezpečit inovace**: upgrade firmwaru během životního cyklu zařízení je nevyhnutelné.

## <a name="iot-solution-developer"></a>Řešení IoT pro vývojáře

Pokud jste vývojář řešení IoT, postupujte podle níže uvedených osvědčených postupů:

* **Postupujte podle metodologie vývoje zabezpečeného softwaru**: vývoj zabezpečeného softwaru vyžaduje základů přemýšlení o zabezpečení od zahájení projektu až po jeho implementaci, testování a nasazení.
* **Zvolte možnost open source softwaru pečlivě**: open source softwaru poskytuje možnost rychle vyvíjet řešení.
* **Integrace s opatrností**: řadu nedostatků zabezpečení softwaru existovat na hranici knihovny a rozhraní API. 

## <a name="iot-solution-deployer"></a>Nástroje pro nasazení řešení IoT

Pokud jste deployer řešení IoT, postupujte podle níže uvedených osvědčených postupů:

* **Bezpečné nasazení hardwaru**: Nasazení IoT může vyžadovat hardware pro nasazení v nezabezpečených umístění, jako je například veřejný mezery nebo bez dohledu národní prostředí.
* **Bezpečnost ověřovací klíče**: během nasazování, každé zařízení vyžaduje ID zařízení a související ověřovací klíče generované cloudovou službu. Bezpečnost tyto klíče fyzicky po nasazení. Žádné ohrožený klíč umožňuje škodlivý zařízení maskovat jako ze stávajících zařízení.

## <a name="iot-solution-operator"></a>Operátor řešení IoT

Pokud se operátor řešení IoT, postupujte podle níže uvedených osvědčených postupů:

* **Zajištění aktuálnosti systémy**: Zkontrolujte operační systémy zařízení a všechny ovladače zařízení se aktualizují na nejnovější verze. 
* **Ochrana před škodlivými aktivitami**: Pokud operačního systému povolí, umístěte nejnovější funkce dané antivirových a antimalwarových programů na každý operační systém zařízení. 
* **Auditovat často**: auditování infrastruktury IoT na zabezpečení související s problémy je klíč při reakci na incidenty zabezpečení.
* **Fyzicky ochrana infrastruktury IoT**: nejhorší zabezpečení útoky na infrastrukturu IoT spustily pomocí fyzický přístup k zařízením.
* **Ochrana přihlašovacích údajů cloudu**: cloudové ověřování pověření používaná pro konfiguraci a provozu při nasazení IoT se pravděpodobně nejjednodušší způsob, jak získat přístup a ohrozit systém IoT. 

