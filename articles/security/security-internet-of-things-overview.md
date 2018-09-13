---
title: Zabezpečte svůj Internet věcí (IoT) v Azure | Dokumentace Microsoftu
description: " Azure pro internet věcí (IoT) služby nabízejí řadu možností. Tento článek vám pomůže pochopit postup zabezpečení řešení IoT v Azure. "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TomSh
ms.assetid: 1473c8dd-8669-48fb-86db-b3c50e2eaf59
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 41e422d0808cafb45b182c5f0a6bb7176a35516f
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35758616"
---
# <a name="internet-of-things-security-overview"></a>Přehled zabezpečení IOT
Azure pro internet věcí (IoT) služby nabízejí řadu možností. Tyto služby na úrovni řešení pro velké firmy umožňují:

* sběr dat ze zařízení
* analýzu datových proudů za provozu
* ukládání objemných datových sad a dotazy na ně
* vizualizaci historických dat i dat v reálném čase
* integraci se systémy administrativní podpory (back-office)

K poskytování těchto možností, balíček akcelerátory řešení Azure IoT společně několik služeb Azure s vlastními rozšířeními jako předkonfigurovaná řešení. Tato předkonfigurovaná řešení jsou základní implementací běžných vzorů řešení v oblasti IoT, které by vám měly pomoci snížit časovou náročnost implementace vašich vlastních IoT řešení. Použití sady IoT software development Kit, můžete přizpůsobit a rozšířit těchto řešení, která splní vaše požadavky. Tato řešení můžete využít i jako příklady či šablony při vývoji nových řešení IoT.

Akcelerátory řešení Azure IoT jsou výkonná řešení pro potřeby IoT. Je však upmost význam, jsou zabezpečení od samého začátku navržené řešení IoT. Kvůli velkému počtu zařízení IoT všech incidentů zabezpečení rychle stal rozšířených událostí s významné důsledky.

Abychom vás seznámí s postupy pro zabezpečení řešení IoT, máme následující informace.

## <a name="security-architecture"></a>Architektura zabezpečení
Při návrhu systému, je důležité porozumět potenciálních hrozeb na daném systému a přidejte odpovídající ochrany podle toho, jak je systém navržený a navržen. Je důležité při návrhu produktů od začátku s ohledem na bezpečnost, protože princip, jak útočník může mohl ohrozit systém pomáhá, ujistěte se, že odpovídající způsoby zmírnění rizik jsou na místě od začátku.

Informace o architektuře IoT zabezpečení načtením [Internet věcí zabezpečení architektura](/azure/iot-fundamentals/iot-security-architecture).

Tento článek popisuje v následujících tématech:

* [Zabezpečení se spustí Model hrozeb](/azure/iot-fundamentals/iot-security-architecture#security-starts-with-a-threat-model)
* [Zabezpečení ve službě IoT](/azure/iot-fundamentals/iot-security-architecture#security-in-iot)
* [Před internetovými útoky Modeling the Architecture referenční informace k Azure IoT](/azure/iot-fundamentals/iot-security-architecture#threat-modeling-the-azure-iot-reference-architecture)

## <a name="security-from-the-ground-up"></a>Zabezpečení od počátku
IoT představuje jedinečné výzvy zabezpečení, ochrany osobních údajů a dodržování předpisů pro firmy po celém světě. Na rozdíl od tradiční kybernetických technologie, kde tyto problémy točí kolem software a jak je implementován IoT se týká, co se stane, když kybernetických a fyzických světů sloučit. Ochrana řešení IoT vyžaduje zajištění zabezpečeného zřizování zařízení, zabezpečené připojení mezi tato zařízení a cloud a ochranu zabezpečení dat v cloudu při zpracování a ukládání. Práce proti takové funkce, ale zařízení s omezenými zdroji, geografické rozptýlení nasazení a velký počet zařízení v rámci řešení.

Zjistěte, jak zpracovat zabezpečení v těchto oblastech najdete [zabezpečení Internetu věcí od počátku](/azure/iot-fundamentals/iot-security-ground-up).

Tento článek obsahuje následující témata:

* [Zabezpečené infrastruktury od počátku](/azure/iot-fundamentals/iot-security-ground-up#secure-infrastructure-from-the-ground-up)
* [Microsoft Azure – zabezpečené infrastruktury IoT pro vaši firmu](/azure/iot-fundamentals/iot-security-ground-up#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>Osvědčené postupy
Zabezpečení infrastruktury IoT vyžaduje přísné strategie zabezpečení do hloubky. Každá vrstva ze zabezpečení dat v cloudu a chrání integritu dat při přenosu přes veřejný internet, bezpečně zřízení zařízení, vytvoří větší zajištění zabezpečení v celé infrastruktury.

Informace o zabezpečení Internetu věcí osvědčené postupy načtením [osvědčené postupy zabezpečení IOT](/azure/iot-fundamentals/iot-security-best-practices).

Tento článek obsahuje následující témata:

* [Výrobce hardwaru IoT/integrátor](/azure/iot-fundamentals/iot-security-best-practices#iot-hardware-manufacturerintegrator)
* [Řešení IoT pro vývojáře](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-developer)
* [Nástroje pro nasazení řešení IoT](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-deployer)
* [Operátor řešení IoT](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-operator)
