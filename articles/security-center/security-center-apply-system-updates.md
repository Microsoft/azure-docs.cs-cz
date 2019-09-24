---
title: Použít aktualizace systému v Azure Security Center | Microsoft Docs
description: V tomto dokumentu se dozvíte, jak implementovat Azure Security Center doporučení **použít aktualizace systému** a **restartovat po aktualizacích systému**.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 1688e85c6e6ed57892ccdffdf0813c8628127cc5
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202501"
---
# <a name="apply-system-updates-in-azure-security-center"></a>Použít aktualizace systému v Azure Security Center
Azure Security Center monitoruje každodenní virtuální počítače s Windows a Linuxem pro chybějící aktualizace operačního systému. Security Center načte seznam dostupných aktualizací zabezpečení a kritické aktualizace od web Windows Update nebo Windows Server Update Services (WSUS) podle toho, která služba je nakonfigurovaná na počítači s Windows. Security Center také kontroluje nejnovější aktualizace v systémech Linux. Pokud na vašem VIRTUÁLNÍm počítači nebo v počítači chybí aktualizace systému, Security Center bude doporučit, abyste použili aktualizace systému.

## <a name="implement-the-recommendation"></a>Implementace doporučení
Použití aktualizací systému se zobrazí jako doporučení v Security Center. Pokud na vašem VIRTUÁLNÍm počítači nebo v počítači chybí aktualizace systému, zobrazí se toto doporučení v části **doporučení** a v části **COMPUTE**.  Výběrem doporučení se otevře řídicí panel **použít aktualizace systému** .

V tomto příkladu použijeme **COMPUTE**.

1. V hlavní nabídce Security Center vyberte **COMPUTE** .

   ![Výběr COMPUTE][1]

2. V části **COMPUTE**vyberte **chybějící aktualizace systému**. Otevře se řídicí panel **použít aktualizace systému** .

   ![Použít řídicí panel aktualizací systému][2]

   Horní část řídicího panelu poskytuje:

    - Celkový počet virtuálních počítačů s Windows a Linux a počítačů chybějících aktualizací systému.
    - Celkový počet kritických aktualizací chybějících v rámci virtuálních počítačů a počítačů.
    - Celkový počet aktualizací zabezpečení chybějících v rámci virtuálních počítačů a počítačů.

   V dolní části řídicího panelu se zobrazují všechny chybějící aktualizace napříč virtuálními počítači a počítači a závažnost chybějící aktualizace.  Seznam obsahuje:

    - JMÉNO: Název chybějící aktualizace
    - NE. Virtuálních počítačů & počítačů: Celkový počet virtuálních počítačů a počítačů, ve kterých chybí Tato aktualizace.
    - STÁTECH Aktuální stav doporučení:

      - Otevírají Doporučení ještě není vyřešené.
      - Probíhá: Doporučení se v tuto chvíli aplikuje na tyto prostředky a nevyžaduje žádnou akci.
      - Přeložit Doporučení již bylo dokončeno. (Pokud byl problém vyřešen, položka je vyšedlá.)

    - ZÁVAŽNOST Popisuje závažnost tohoto konkrétního doporučení:

      - Maximální Existuje chyba s smysluplným prostředkem (aplikace, virtuální počítač nebo skupina zabezpečení sítě) a vyžaduje pozornost.
      - Úrovně K dokončení procesu nebo odstranění ohrožení zabezpečení se vyžadují nekritické nebo další kroky.
      - Slab Ohrožení zabezpečení by se mělo řešit, ale nevyžaduje okamžitou pozornost. (Ve výchozím nastavení nejsou doporučení s nízkou závažností uváděny, ale pokud je chcete zobrazit, je možné je vyfiltrovat.)

3. Pokud chcete zobrazit podrobnosti, vyberte v seznamu chybějící aktualizace.

   ![Chybějící aktualizace zabezpečení][3]

4. Vyberte ikonu **hledání** na horním pásu karet.  Dotaz pro vyhledávání protokolů Azure Monitor otevře filtrovaný na počítačích, ve kterých chybí aktualizace.

   ![Hledání protokolů Azure Monitor][4]

5. Pro další informace vyberte počítač ze seznamu. Otevře se další výsledek hledání s informacemi filtrovanými pouze pro tento počítač.

    ![Hledání protokolů Azure Monitor][5]

## <a name="next-steps"></a>Další kroky
Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – Přečtěte si, jak doporučení vám pomůžou chránit vaše prostředky Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – Přečtěte si blogové příspěvky o zabezpečení Azure a dodržování předpisů.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png
