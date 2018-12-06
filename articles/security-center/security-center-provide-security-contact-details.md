---
title: Poskytnutí podrobností kontaktů zabezpečení ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento dokument ukazuje, jak poskytnutí podrobností kontaktů zabezpečení ve službě Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/20/2018
ms.author: rkarlin
ms.openlocfilehash: 5ed7550b3582fe90bd581dff068e2a6e2c8b72d8
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52967431"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Poskytnutí podrobností kontaktů zabezpečení ve službě Azure Security Center
Azure Security Center vám doporučí poskytnutí podrobností kontaktů zabezpečení pro vaše předplatné Azure, pokud jste tak již neučinili. Prostřednictvím tohoto kontaktu se na vás společnost Microsoft obrátí, pokud středisko Microsoft Security Response Center (MSRC) zjistí, že k datům zákazníka nezákonně nebo neoprávněně přistupovala třetí strana. Střediska MSRC provádí monitorování zabezpečení vyberte síť Azure a infrastrukturou a přijímá threat intelligence a zneužití stížností od třetích stran.

E-mailové oznámení se odešle při prvním výskytu výstrahy a pouze u výstrah s vysokou závažností. Předvolby e-mailu lze konfigurovat pouze pro zásady předplatného. Skupiny prostředků v rámci předplatného zdědí tato nastavení.

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení.  Není to podrobný průvodce.
>
>

## <a name="implement-the-recommendation"></a>Implementace doporučení
1. V části **doporučení**vyberte **poskytnutí podrobností kontaktů zabezpečení**.
   ![Zadejte kontaktu zabezpečení][1]
2. Vyberte předplatné Azure, které poskytují kontaktní informace o.
3. Tím se otevře **e-mailová oznámení**.

   ![Poskytnutí podrobností kontaktů zabezpečení][2]

   * Zadejte zabezpečení kontaktní e- mailových adres oddělených čárkami. Není k dispozici omezený počet e-mailové adresy, které můžete zadat.
   * Zadejte jeden zabezpečení kontaktní telefonní číslo v mezinárodním.
   * Pokud chcete dostávat e-maily o výstrahách s vysokou závažností, zapněte možnost **zaslat e-mailem o výstrahách**.
   * V budoucnu budete mít možnost odeslat e-mailová oznámení vlastníkům předplatného. Tato možnost je nyní zobrazena šedě.
   * Vyberte **Uložit** použít informace o kontaktu zabezpečení do vašeho předplatného.

## <a name="see-also"></a>Další informace najdete v tématech
Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-azure-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – zjistěte, jak můžete monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – získejte nejnovější zprávy zabezpečení Azure a informace.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
