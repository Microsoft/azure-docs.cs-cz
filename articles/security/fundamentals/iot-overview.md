---
title: Zabezpečení Internet věcí (IoT) v Azure | Microsoft Docs
description: " Služby Azure Internet věcí (IoT) nabízejí širokou škálu funkcí. Tento článek vám pomůže pochopit, jak zabezpečit vaše řešení IoT v Azure. "
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: 1473c8dd-8669-48fb-86db-b3c50e2eaf59
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 670fcc5e92cdb9937026d91ee85b7e8e856a3025
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727038"
---
# <a name="internet-of-things-security-overview"></a>Přehled zabezpečení Internet věcí
Služby Azure Internet věcí (IoT) nabízejí širokou škálu funkcí. Tyto služby na úrovni řešení pro velké firmy umožňují:

* sběr dat ze zařízení
* analýzu datových proudů za provozu
* ukládání objemných datových sad a dotazy na ně
* vizualizaci historických dat i dat v reálném čase
* integraci se systémy administrativní podpory (back-office)

Pro zajištění těchto schopností balíček akcelerátorů řešení Azure IoT společně s vlastními rozšířeními v rámci předkonfigurovaných řešení nabízí několik služeb Azure. Tato předkonfigurovaná řešení jsou základní implementací běžných vzorů řešení v oblasti IoT, které by vám měly pomoci snížit časovou náročnost implementace vašich vlastních IoT řešení. Pomocí sad pro vývoj softwaru IoT můžete tato řešení přizpůsobit a rozšíříte tak, aby splňovala vaše požadavky. Tato řešení můžete využít i jako příklady či šablony při vývoji nových řešení IoT.

Akcelerátory řešení Azure IoT jsou výkonné řešení pro potřeby vašich IoT. Je ale velmi důležité, že vaše řešení IoT jsou navržená s ohledem na zabezpečení od začátku. Z důvodu sheerho počtu zařízení IoT se může každý incident zabezpečení rychle stát rozšířenou událostí s významnými důsledky.

Abychom vám pomohli pochopit, jak zabezpečit vaše řešení IoT, máme tyto informace.

## <a name="security-architecture"></a>Architektura zabezpečení
Při navrhování systému je důležité pochopit potenciální hrozby pro daný systém a odpovídajícím způsobem přidat patřičnou ochranu, protože systém je navržený a navržený. Je důležité navrhnout produkt od začátku s ohledem na zabezpečení, protože porozumět tomu, jak útočník může ohrozit zabezpečení systému, pomáhá zajistit, aby byla na začátku vhodná omezení.

O architektuře zabezpečení IoT si můžete přečíst v tématu [Internet věcí architektury zabezpečení](/azure/iot-fundamentals/iot-security-architecture).

Tento článek popisuje v následujících tématech:

* [Zabezpečení začíná modelem hrozeb](/azure/iot-fundamentals/iot-security-architecture#security-starts-with-a-threat-model)
* [Zabezpečení v IoT](/azure/iot-fundamentals/iot-security-architecture#security-in-iot)
* [Modelování hrozeb referenční architektury Azure IoT](/azure/iot-fundamentals/iot-security-architecture)

## <a name="security-from-the-ground-up"></a>Zabezpečení od počátku
IoT přináší jedinečné výzvy k zabezpečení, ochraně osobních údajů a dodržování předpisů pro firmy po celém světě. Na rozdíl od tradičních počítačové technologie, kde se tyto problémy dotýkají softwaru a způsobu jeho implementace, se v IoT projeví, co se stane, když se světů na internetu a fyzickou akci sblížení. Ochrana řešení IoT vyžaduje zajištění zabezpečeného zřizování zařízení, zabezpečení připojení mezi těmito zařízeními a cloudem a zabezpečení ochrany dat v cloudu během zpracování a ukládání. Práce s těmito funkcemi je však omezená na prostředky, geografickou distribuci nasazení a mnoho zařízení v rámci řešení.

Informace o tom, jak řešit zabezpečení v těchto oblastech, najdete v [části Internet věcí zabezpečení od základu](/azure/iot-fundamentals/iot-security-ground-up).

Článek popisuje následující témata:

* [Zabezpečená infrastruktura od základu](/azure/iot-fundamentals/iot-security-ground-up#secure-infrastructure-from-the-ground-up)
* [Microsoft Azure – Zabezpečte infrastrukturu IoT pro vaši firmu.](/azure/iot-fundamentals/iot-security-ground-up#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>Osvědčené postupy
Zabezpečení infrastruktury IoT vyžaduje přísnou strategii zabezpečení. V rámci zabezpečení dat v cloudu chrání integritu dat při přenosu přes veřejný Internet, aby bylo zajištěno bezpečné zřízení zařízení, každá vrstva sestavuje větší bezpečnostní ujištění v celkové infrastruktuře.

Informace o osvědčených postupech zabezpečení Internet věcí najdete v [Internet věcí osvědčených postupech zabezpečení](/azure/iot-fundamentals/iot-security-best-practices).

Článek popisuje následující témata:

* [Výrobce nebo integrátor hardwaru IoT](/azure/iot-fundamentals/iot-security-best-practices#iot-hardware-manufacturerintegrator)
* [Vývojář řešení IoT](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-developer)
* [Nástroj pro nasazení řešení IoT](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-deployer)
* [Operátor řešení IoT](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-operator)
