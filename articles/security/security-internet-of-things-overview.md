---
title: Zabezpečit vaše Internet věcí (IoT) v Azure | Microsoft Docs
description: " Azure pro internet věcí (IoT) services nabízí celou řadu funkcí. Tento článek vám pomůže pochopit postup zabezpečení vašich vlastních IoT řešení v Azure. "
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
ms.openlocfilehash: d5c1cb22fdfe59bd8409f9595b2fa4c3a0df771e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641233"
---
# <a name="internet-of-things-security-overview"></a>Přehled zabezpečení Internetu věcí
Azure pro internet věcí (IoT) services nabízí celou řadu funkcí. Tyto služby na úrovni řešení pro velké firmy umožňují:

* sběr dat ze zařízení
* analýzu datových proudů za provozu
* ukládání objemných datových sad a dotazy na ně
* vizualizaci historických dat i dat v reálném čase
* integraci se systémy administrativní podpory (back-office)

K poskytování těchto možnosti, balíčku akcelerátorů řešení Azure IoT společně více služeb Azure s vlastními rozšířeními jako předkonfigurovaná řešení. Tato předkonfigurovaná řešení jsou základní implementací běžných vzorů řešení v oblasti IoT, které by vám měly pomoci snížit časovou náročnost implementace vašich vlastních IoT řešení. Pomocí software development Kit IoT, můžete přizpůsobit a rozšířit těmito řešeními, aby vyhovovala vaším požadavkům. Tato řešení můžete využít i jako příklady či šablony při vývoji nových řešení IoT.

Akcelerátorů řešení Azure IoT jsou výkonné řešení pro vaše potřeby IoT. Je však upmost význam, vašich vlastních IoT řešení jsou navržená tak, s důrazem na bezpečnost od začátku. Kvůli velkému počtu zařízení IoT incidentů, zabezpečení rychle stát událost související s rozšířeným se významné důsledky.

Chcete-li vám pomohou pochopit, jak zabezpečit řešení IoT, máme následující informace.

## <a name="security-architecture"></a>Architektura zabezpečení
Při návrhu systému, je důležité pochopit potenciální hrozby na daný systém a přidejte příslušné obrany podle toho, jak systém je navržená tak a navržen. Je důležité při návrhu produktu od začátku s důrazem na bezpečnost, protože pochopení, jak může být útočník mohl ohrozit systém pomáhá, ujistěte se, zda odpovídající jejich zmírnění jsou zavedené od začátku.

Můžete další informace o architektuře IoT zabezpečení načtením [Internet věcí zabezpečení architektura](../iot-accelerators/iot-security-architecture.md).

Tento článek popisuje v následujících tématech:

* [Zabezpečení začíná Model hrozeb](../iot-accelerators/iot-security-architecture.md#security-starts-with-a-threat-model)
* [Zabezpečení v IoT](../iot-accelerators/iot-security-architecture.md#security-in-iot)
* [Azure IoT referenční architektura modelování hrozeb](../iot-accelerators/iot-security-architecture.md#threat-modeling-the-azure-iot-reference-architecture)

## <a name="security-from-the-ground-up"></a>Zabezpečení od počátku
IoT představuje jedinečné výzvy zabezpečení, ochrany osobních údajů a dodržování předpisů pro firmy po celém světě. Na rozdíl od tradičních internetový technologie, kde tyto problémy základem softwaru a o tom, jak je implementována IoT se vztahuje na co se stane, když internetový a fyzické světů sloučit. Ochrana řešení IoT vyžaduje zajištění zabezpečené zřizování zařízení, zabezpečené připojení mezi tato zařízení a cloudu a ochranu zabezpečení dat v cloudu při zpracování a úložiště. Pracovat se tyto funkce, ale jsou zařízení s omezenými zdroji, geografické rozptýlení nasazení a mnoho zařízení v rámci řešení.

Můžete naučit pro zpracování zabezpečení v těchto oblastech čtení [zabezpečení Internetu věcí od základů](../iot-accelerators/securing-iot-ground-up.md).

Článek obsahuje následující témata:

* [Zabezpečení infrastruktury od základů](../iot-accelerators/securing-iot-ground-up.md#secure-infrastructure-from-the-ground-up)
* [Microsoft Azure – zabezpečené IoT infrastrukturu pro vaši organizaci](../iot-accelerators/securing-iot-ground-up.md#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>Osvědčené postupy
Zabezpečení infrastruktury IoT vyžaduje přísných strategii zabezpečení do hloubky. Z zabezpečení dat v cloudu, chrání integritu dat v průběhu přenosu prostřednictvím veřejného Internetu bezpečně zřízení zařízení, vytvoří každé vrstvě větší zajištění zabezpečení v celé infrastruktury.

Dozvíte se o zabezpečení Internetu věcí osvědčené postupy načtením [osvědčené postupy pro zabezpečení Internetu věcí](../iot-accelerators/iot-security-best-practices.md).

Článek obsahuje následující témata:

* [Výrobce hardwaru IoT/integrátor](../iot-accelerators/iot-security-best-practices.md#iot-hardware-manufacturerintegrator)
* [Vývojář řešení IoT](../iot-accelerators/iot-security-best-practices.md#iot-solution-developer)
* [Nástroje pro nasazení řešení IoT](../iot-accelerators/iot-security-best-practices.md#iot-solution-deployer)
* [Operátor řešení IoT](../iot-accelerators/iot-security-best-practices.md#iot-solution-operator)
