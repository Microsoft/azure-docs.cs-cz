---
title: Zadání podrobností o kontaktu zabezpečení v Azure Security Center | Microsoft Docs
description: V tomto dokumentu se dozvíte, jak zadat podrobnosti kontaktu zabezpečení v Azure Security Center.
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
ms.openlocfilehash: 08ad761e81909e6ab23c7c07f5ce05865136bc47
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204096"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Zadání podrobností o kontaktu zabezpečení v Azure Security Center
Azure Security Center vám doporučí, abyste ve svém předplatném Azure uvedli podrobnosti o kontaktu zabezpečení, pokud jste to zatím neudělali. Prostřednictvím tohoto kontaktu se na vás společnost Microsoft obrátí, pokud středisko Microsoft Security Response Center (MSRC) zjistí, že k datům zákazníka nezákonně nebo neoprávněně přistupovala třetí strana. Středisko MSRC provádí vybraný monitoring zabezpečení sítě a infrastruktury Azure a přijímá analytické informace o hrozbách a stížnosti na zneužití od třetích stran.

E-mailové oznámení se odešle při prvním výskytu výstrahy během dne a pouze u výstrah s vysokou závažností. Předvolby e-mailu lze konfigurovat pouze pro zásady předplatného. Skupiny prostředků v rámci předplatného zdědí tato nastavení. Výstrahy jsou k dispozici pouze na úrovni Standard Azure Security Center.

E-mailová oznámení o upozornění se posílají:
- Jednomu příjemci e-mailu na typ výstrahy a den.  
- Jednomu příjemci v jednom dni se neposílá více než 3 e-mailové zprávy.
- Každá e-mailová zpráva obsahuje upozornění, nikoli souhrn upozornění.
- Pouze pro výstrahy s vysokou závažností.

> [!TIP]
> Pro výstrahy s dalšími úrovněmi závažnosti vytvořte [automatizaci pracovního postupu](workflow-automation.md) , která bude používat aplikaci logiky, která odešle e-mail příslušným pracovníkům.
 
Pokud vám například byla odeslána e-mailová zpráva s upozorněním na útok RDP, nedostanete další e-mailovou zprávu o útoku RDP ve stejný den, i když se aktivuje další upozornění. 

> [!IMPORTANT]
> Tento dokument vám tuto službu představí formou ukázkového nasazení.  Není to podrobný průvodce.

## <a name="set-up-email-notifications-for-alerts"></a>Nastavení e-mailových oznámení pro výstrahy<a name="email"></a>

1. Jako uživatel s rolí správce zabezpečení nebo vlastníka předplatného otevřete stránku **e-mailová oznámení** :

    - V okně výstrahy otevřete **Nastavení cenové &**, vyberte příslušné předplatné a zvolte **e-mailová oznámení**.

    - Pokud implementujete doporučení, vyberte v části **doporučení**možnost **zadat podrobnosti kontaktu zabezpečení**, vyberte předplatné Azure, na které chcete zadat kontaktní údaje. Tím se otevřou **e-mailová oznámení**.

   ![Poskytnutí podrobností kontaktů zabezpečení][2]

1. Zadejte kontaktní e-mailovou adresu nebo adresy zabezpečení oddělené čárkami. Počet e-mailových adres, které můžete zadat, není nijak omezený.

1. Pokud chcete dostávat e-maily s výstrahami s vysokou závažností, zapněte možnost **Odeslat e-maily o výstrahách**. Pro jiné úrovně závažnosti použijte aplikaci logiky, jak je vysvětleno v tématu [automatizace pracovních postupů](workflow-automation.md).

1. Můžete posílat e-mailová oznámení vlastníkům předplatného (klasický správce služeb a spolusprávci a role vlastníka RBAC v oboru předplatného).

1. Pokud chcete u svého předplatného použít kontaktní informace zabezpečení, vyberte **Uložit**.

## <a name="see-also"></a>Viz také
Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – Přečtěte si, jak doporučení vám pomůžou chránit vaše prostředky Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
* [Správa a reakce na výstrahy zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Přečtěte si, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Monitorování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak monitorovat stav vašich partnerských řešení.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
