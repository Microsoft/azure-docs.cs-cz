---
title: Poskytnutí podrobností o bezpečnostních kontaktech v Centru zabezpečení Azure | Dokumenty společnosti Microsoft
description: Tento dokument ukazuje, jak poskytnout podrobnosti o kontaktu zabezpečení v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2019
ms.author: memildin
ms.openlocfilehash: 64a9600a3014f7e85a7f924d38882bfadaf631db
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387814"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Poskytnutí kontaktních údajů zabezpečení v Azure Security Center
Azure Security Center vám doporučí, abyste ve svém předplatném Azure uvedli podrobnosti o kontaktu zabezpečení, pokud jste to zatím neudělali. Prostřednictvím tohoto kontaktu se na vás společnost Microsoft obrátí, pokud středisko Microsoft Security Response Center (MSRC) zjistí, že k datům zákazníka nezákonně nebo neoprávněně přistupovala třetí strana. Středisko MSRC provádí vybraný monitoring zabezpečení sítě a infrastruktury Azure a přijímá analytické informace o hrozbách a stížnosti na zneužití od třetích stran.

E-mailové oznámení se odešle při prvním výskytu výstrahy během dne a pouze u výstrah s vysokou závažností. Předvolby e-mailu lze konfigurovat pouze pro zásady předplatného. Skupiny prostředků v rámci předplatného zdědí tato nastavení. Výstrahy jsou dostupné jenom na úrovni Standard centra zabezpečení Azure.

E-mailová oznámení o upozornění se posílají:
- Jednomu příjemci e-mailu na typ výstrahy a den.  
- Ne více než 3 e-mailové zprávy jsou odesílány na jednoho příjemce v jednom dni
- Každá e-mailová zpráva obsahuje upozornění, nikoli souhrn upozornění.
- Pouze pro výstrahy s vysokou závažností.

> [!TIP]
> Pro výstrahy s jinými úrovněmi závažnosti [vytvořte automatizaci pracovního postupu](workflow-automation.md) pro použití aplikace logiky, která bude odesílat e-maily příslušným pracovníkům.
 
Pokud vám například byla odeslána e-mailová zpráva s upozorněním na útok RDP, nedostanete další e-mailovou zprávu o útoku RDP ve stejný den, i když se aktivuje další upozornění. 

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení.  Není to podrobný průvodce.

## <a name="set-up-email-notifications-for-alerts"></a>Nastavení e-mailových oznámení pro výstrahy<a name="email"></a>

1. Otevřete stránku **E-mailová oznámení:**

    - U upozornění otevřete **nastavení cenové&**, vyberte příslušné předplatné a vyberte **E-mailová oznámení**.

    - Pokud implementujete doporučení, pak v části **Doporučení**vyberte **Zadat kontaktní údaje zabezpečení**, vyberte předplatné Azure, na které chcete poskytnout kontaktní informace. Otevře **se e-mailová oznámení**.

   ![Poskytnutí podrobností kontaktů zabezpečení][2]

1. Zadejte e-mailovou adresu bezpečnostního kontaktu nebo adresy oddělené čárkami. Počet e-mailových adres, které můžete zadat, není nijak omezen.

1. Chcete-li dostávat e-maily o výstrahách vysoké závažnosti, zapněte možnost **Poslat e-maily o výstrahách**. Pro jiné úrovně závažnosti použijte aplikaci logiky, jak je vysvětleno v [automatizaci pracovního postupu](workflow-automation.md).

1. Můžete odesílat e-mailová oznámení vlastníkům předplatného (klasický správce služeb a spolusprávci a roli vlastníka RBAC v oboru předplatného).

1. Chcete-li použít kontaktní informace zabezpečení pro předplatné, vyberte **uložit**.

## <a name="see-also"></a>Viz také
Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky Azure.
* [Monitorování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – zjistěte, jak sledovat stav prostředků Azure.
* [Správa a reakce na výstrahy zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Monitorování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – zjistěte, jak sledovat stav partnerských řešení.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
