---
title: Osvědčené postupy zabezpečení Internet věcí | Microsoft Docs
description: Tento článek poskytuje seznam osvědčených postupů zabezpečení Microsoft Internet věcí a obecná doporučení.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 2d5598c5-4c30-481d-b8f4-51ee024ea9a7
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 14f1905b1acd60acdf7b3dad0686e3fa34629953
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727061"
---
# <a name="internet-of-things-security-best-practices"></a>Osvědčené postupy zabezpečení Internet věcí

Zabezpečení infrastruktury Internet věcí (IoT) je důležitým podnikem pro kohokoli zapojeným do řešení IoT. Vzhledem k počtu zapojených zařízení a distribuované povaze těchto zařízení není dopad bezpečnostní události související s ohrožením milionů zařízení IoT netriviální a může mít širší dopad.

Z tohoto důvodu zabezpečení IoT potřebuje přístup v podrobném zabezpečení. Data musí být zabezpečená v cloudu a přesouvají se přes soukromé a veřejné sítě. Aby bylo možné bezpečně zřídit zařízení IoT, je nutné, aby byly k dismístě metody. Každá vrstva od zařízení až po síť až po cloudový back-end potřebuje silné záruky zabezpečení.

Osvědčené postupy IoT je možné kategorizovat následujícím způsobem:

* Výrobce nebo integrátor hardwaru IoT
* Vývojář řešení IoT
* Nástroj pro nasazení řešení IoT
* Operátor řešení IoT

Tento článek shrnuje [Internet věcí osvědčené postupy zabezpečení](/azure/iot-fundamentals/iot-security-best-practices). Podrobnější informace najdete v tomto článku.

## <a name="iot-hardware-manufacturer-or-integrator"></a>Výrobce nebo integrátor hardwaru IoT

Pokud jste výrobcem hardwaru IoT nebo hardwarového integrátoru, postupujte podle níže uvedených osvědčených postupů:

* **Rozsah hardwaru na minimální požadavky**: návrh hardwaru by měl zahrnovat minimální funkce vyžadované pro provoz hardwaru a nic dalšího. 
* **Kontrola manipulace**s hardwarem: sestavení v mechanismech pro detekci fyzické manipulace s hardwarem, jako je například otevření krytého zařízení, odebrání části zařízení atd. 
* Sestavování s zabezpečeným [](https://en.wikipedia.org/wiki/Cost_of_goods_sold) **hardwarem**: Pokud je to povoleno, Sestavujte bezpečnostní funkce, jako je zabezpečené a šifrované úložiště a spouštěcí funkce založené na čipu TPM (Trusted Platform Module).
* **Zabezpečený upgrade**: upgrade firmwaru během životnosti zařízení je nevyhnutelný.

## <a name="iot-solution-developer"></a>Vývojář řešení IoT

Pokud jste vývojář řešení IoT, postupujte podle níže uvedených osvědčených postupů:

* **Dodržujte zabezpečení metodologie vývoje softwaru**: vývoj zabezpečeného softwaru vyžaduje základní úvahu o zabezpečení od vzniku projektu po celý způsob implementace, testování a nasazení.
* **Volba open source softwaru s péčí**: Open source software nabízí příležitost k rychlému vývoji řešení.
* **Integrace s péčí**: mnohé z vad zabezpečení softwaru existují na hranici knihoven a rozhraní API. 

## <a name="iot-solution-deployer"></a>Nástroj pro nasazení řešení IoT

Pokud jste nástrojem pro nasazení řešení IoT, postupujte podle níže uvedených osvědčených postupů:

* **Bezpečně nasaďte hardware**: Nasazení IoT můžou vyžadovat nasazení hardwaru v nezabezpečených umístěních, jako jsou třeba veřejné prostory nebo nepod dohledem národních prostředí.
* **Udržujte ověřovací klíče v bezpečí**: během nasazování vyžaduje každé zařízení ID zařízení a přidružené ověřovací klíče vygenerované cloudovou službou. Tyto klíče si nechte fyzicky bezpečně i po nasazení. Jakékoli napadené klíče může použít škodlivé zařízení k maskování jako stávající zařízení.

## <a name="iot-solution-operator"></a>Operátor řešení IoT

Pokud jste operátorem řešení IoT, postupujte podle níže uvedených osvědčených postupů:

* **Udržujte systémy v aktuálním stavu**: Ujistěte se, že operační systémy zařízení a všechny ovladače zařízení jsou aktualizované na nejnovější verze. 
* **Ochrana před škodlivou aktivitou**: Pokud operační systém umožňuje, umístěte nejnovější možnosti antivirové ochrany a ochrany proti malwaru na každý operační systém zařízení. 
* **Nejčastější audit**: auditování infrastruktury IoT pro problémy související se zabezpečením je klíč při reakci na incidenty zabezpečení.
* **Fyzicky chránit infrastrukturu IoT**: nejhorší bezpečnostní útoky proti infrastruktuře IoT se spouští pomocí fyzického přístupu k zařízením.
* **Chránit cloudová pověření**: přihlašovací údaje cloudového ověřování používané ke konfiguraci a provozování nasazení IoT jsou pravděpodobně nejjednodušší způsob, jak získat přístup a ohrozit systém IoT. 

