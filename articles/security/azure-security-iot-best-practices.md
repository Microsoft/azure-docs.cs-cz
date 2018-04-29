---
title: Internet věcí osvědčené postupy zabezpečení | Microsoft Docs
description: Tento článek poskytuje kurátorované seznam Microsoft Internet věcí osvědčené postupy zabezpečení a obecná doporučení.
services: security
documentationcenter: na
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: 2d5598c5-4c30-481d-b8f4-51ee024ea9a7
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 804dd6828931ca2801360ab18be938c8bacf2bfa
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="internet-of-things-security-best-practices"></a>Internet věcí osvědčené postupy zabezpečení

Zabezpečení infrastruktury Internetu věcí (IoT) je důležité podnikem pro každý, kdo spojené s řešení IoT. Kvůli distribuovaná povaha těchto zařízení a počet zařízení související se situací dopad událostí zabezpečení související s ohrožení miliony zařízení IoT je netriviální a může mít rozšířeným dopad.

Z tohoto důvodu musí IoT zabezpečení zabezpečení do hloubky přístup. Data musí být zabezpečené v cloudu a při jejich přesunu přes privátní a veřejné sítě. Metody musí být na místě bezpečně zřídit zařízeními IoT. Každou vrstvu ze zařízení k síti, do cloudu back-end vyžaduje silné zabezpečení záruky.

Osvědčené postupy IoT může být rozdělena následujícím způsobem:

* Výrobce hardwaru IoT nebo integrátor
* Vývojář řešení IoT
* Nástroje pro nasazení řešení IoT
* Operátor řešení IoT

Tento článek shrnuje [Internetu z věcí osvědčené postupy zabezpečení](../iot-suite/iot-security-best-practices.md). Naleznete v tomto článku najdete podrobnější informace.

## <a name="iot-hardware-manufacturer-or-integrator"></a>Výrobce hardwaru IoT nebo integrátor

Použijte následující osvědčené postupy, pokud jste IoT výrobce hardwaru nebo integrátor hardwaru:

* **Obor hardware a požadavky na minimální**: návrh hardwaru by měla zahrnovat nutných pro operaci hardwaru a nic další minimální funkce. 
* **Ujistěte se, hardwaru manipulovat ověření**: sestavení v mechanismy pro zjistit případnou manipulaci fyzického hardwaru, jako je například otevírání krytu zařízení, odebrání součástí zařízení atd. 
* **Sestavení kolem zabezpečený hardware**: Pokud [spotřebu](https://en.wikipedia.org/wiki/Cost_of_goods_sold) povolení, funkce zabezpečení, jako je zabezpečená a šifrovaná úložiště a na základě Trusted Platform Module TPM spouštěcí funkce sestavení.
* **Zabezpečit upgrady**: upgrade firmwaru během životního cyklu zařízení je nevyhnutelné.

## <a name="iot-solution-developer"></a>Vývojář řešení IoT

Použijte následující osvědčené postupy, pokud jste vývojář řešení IoT:

* **Postupujte podle zabezpečené softwaru vývoj metodika**: vývoj zabezpečené softwaru vyžaduje základů přemýšlení o zabezpečení od zahájení projektu zcela k jeho implementaci, testování a nasazení.
* **Vyberte software s otevřeným zdrojem dát pozor**: software s otevřeným zdrojem vám dává příležitost k rychlému vývoji řešení.
* **Integrovat dát pozor**: řadu nedostatků zabezpečení softwaru existovat hranice knihovny a rozhraní API. 

## <a name="iot-solution-deployer"></a>Nástroje pro nasazení řešení IoT

Použijte následující osvědčené postupy, pokud jste deployer řešení IoT:

* **Nasazení hardwaru bezpečně**: IoT nasazení může vyžadovat hardwaru, který má být nasazený v nezabezpečená umístění, například veřejné mezery nebo bez dohledu národní prostředí.
* **Chránit ověřovací klíče**: během nasazování každé zařízení vyžaduje ID zařízení a související ověřovací klíče generované cloudové služby. Chránit tyto klíče fyzicky i po jeho nasazení. Všechny ohrožené klíč lze škodlivý zařízení jako ze stávajících zařízení.

## <a name="iot-solution-operator"></a>Operátor řešení IoT

Použijte následující osvědčené postupy, pokud jste operátor řešení IoT:

* **Aktuálnost systémy**: Ujistěte se, operační systémy zařízení a všechny ovladače zařízení jsou aktualizovány na nejnovější verze. 
* **Ochrana proti škodlivé aktivity**: Pokud operačního systému povolí, umístěte nejnovějších funkcích antivirový a proti malwaru na každý operační systém zařízení. 
* **Audit často**: auditování IoT infrastruktury pro týkající se problémů je klíč při odpovědi na bezpečnostní incidenty v oblasti zabezpečení.
* **Fyzicky ochrana infrastruktury IoT**: nejhorší zabezpečení útoky na infrastrukturu IoT spustily pomocí fyzický přístup k zařízením.
* **Ochranu přihlašovacích údajů cloudu**: cloudové ověřování pověření použitá pro konfiguraci a provozní nasazení služby IoT jsou pravděpodobně nejjednodušší způsob, jak získat přístup a ohrozit systém IoT. 

