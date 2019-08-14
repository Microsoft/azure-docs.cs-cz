---
title: Zadání podrobností o kontaktu zabezpečení v Azure Security Center | Microsoft Docs
description: V tomto dokumentu se dozvíte, jak zadat podrobnosti kontaktu zabezpečení v Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2019
ms.author: rkarlin
ms.openlocfilehash: ae7c130df40ea0553bd9e3ae117534fe98466f26
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "68934895"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Zadání podrobností o kontaktu zabezpečení v Azure Security Center
Pokud jste to ještě neudělali, Azure Security Center vám doporučujeme zadat podrobnosti o kontaktu zabezpečení pro vaše předplatné Azure. Prostřednictvím tohoto kontaktu se na vás společnost Microsoft obrátí, pokud středisko Microsoft Security Response Center (MSRC) zjistí, že k datům zákazníka nezákonně nebo neoprávněně přistupovala třetí strana. MSRC provádí výběr sledování zabezpečení sítě a infrastruktury Azure a přijímá analýzy hrozeb a stížnosti na zneužití od třetích stran.

E-mailové oznámení se odešle při prvním výskytu výstrahy během dne a pouze u výstrah s vysokou závažností. Předvolby e-mailu lze konfigurovat pouze pro zásady předplatného. Skupiny prostředků v rámci předplatného zdědí tato nastavení. 

E-mailová oznámení o upozornění se posílají:
- Pouze pro výstrahy s vysokou závažností.
- Jednomu příjemci e-mailu na typ výstrahy a den.  
- Jednomu příjemci v jednom dni se neposílá více než 3 e-mailové zprávy.
- Každá e-mailová zpráva obsahuje upozornění, nikoli souhrn upozornění.
 
Pokud vám například byla odeslána e-mailová zpráva s upozorněním na útok RDP, nedostanete další e-mailovou zprávu o útoku RDP ve stejný den, i když se aktivuje další upozornění. 

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení.  Není to podrobný průvodce.

## Nastavení e-mailových oznámení pro výstrahy<a name="email"></a>

1. Na portálu vyberte **cenové & nastavení**.
1. Klikněte na předplatné.
1. Klikněte na **e-mailová oznámení**.

> [!NOTE]
> Pokud implementujete doporučení, vyberte v části **doporučení**možnost **zadat podrobnosti kontaktu zabezpečení**, vyberte předplatné Azure, na které chcete zadat kontaktní údaje. Tím se otevřou **e-mailová oznámení**.

   ![Zadat podrobnosti kontaktů zabezpečení][2]

   * Zadejte kontaktní e-mailovou adresu nebo adresy zabezpečení oddělené čárkami. Počet e-mailových adres, které můžete zadat, není omezený.
   * Zadejte jeden kontakt zabezpečení mezinárodní telefonní číslo.
   * Pokud chcete dostávat e-maily s výstrahami s vysokou závažností, zapněte možnost **Odeslat e-maily o výstrahách**.
   * V budoucnu budete mít možnost posílat e-mailová oznámení vlastníkům předplatného. Tato možnost je aktuálně šedá.
   * Vyberte **Uložit** a použijte tak kontaktní informace zabezpečení pro vaše předplatné.

## <a name="see-also"></a>Viz také:
Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – Přečtěte si, jak doporučení vám pomůžou chránit vaše prostředky Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – Získejte nejnovější novinky a informace o zabezpečení Azure.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
