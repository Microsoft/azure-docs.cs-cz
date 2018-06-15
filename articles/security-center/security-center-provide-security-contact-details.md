---
title: Zadejte kontaktní údaje zabezpečení v Azure Security Center | Microsoft Docs
description: Tento dokument ukazuje, jak zajistit zabezpečení kontaktní údaje v Azure Security Center.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: terrylan
ms.openlocfilehash: 726b59c45e2eb18eebe28a180db23336ae141408
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866524"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Zadejte kontaktní údaje zabezpečení v Azure Security Center
Azure Security Center doporučí, pokud jste to ještě neudělali zadejte kontaktní údaje zabezpečení pro vaše předplatné Azure. Prostřednictvím tohoto kontaktu se na vás společnost Microsoft obrátí, pokud středisko Microsoft Security Response Center (MSRC) zjistí, že k datům zákazníka nezákonně nebo neoprávněně přistupovala třetí strana. Střediska MSRC provádí sledování zabezpečení vyberte síť Azure a infrastruktury a přijímá stížností intelligence a zneužití ohrožení od jiných výrobců.

E-mailové oznámení se odesílají na denní první výskyt výstrahy a jenom pro výstrahy s vysokou závažností. Předvolby e-mailu lze konfigurovat pouze pro zásady předplatného. Skupiny prostředků v rámci předplatného zdědí tato nastavení.

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení.  Není to podrobný průvodce.
>
>

## <a name="implement-the-recommendation"></a>Implementace doporučení
1. V části **doporučení**, vyberte **zadejte kontaktní údaje zabezpečení**.
   ![Zadejte kontakt zabezpečení][1]
2. Vyberte předplatné Azure poskytuje kontaktní informace.
3. Tím se otevře **zásady zabezpečení – e-mailová oznámení**.

   ![Poskytnutí podrobností kontaktů zabezpečení][2]

   * Zadejte zabezpečení kontaktní e-mailovou adresu nebo adresy oddělené čárkami. Není k dispozici limit počtu e-mailové adresy, které můžete zadat.
   * Zadejte jeden zabezpečení kontaktní telefonní číslo v mezinárodním.
   * Pokud chcete dostávat e-maily o vysokou závažností výstrahy, zapněte možnost **zaslat mi e-maily o výstrahách**.
   * V budoucnu budete mít možnost odeslat e-mailová oznámení na vlastníky předplatného. Tato možnost není aktuálně k dispozici.
   * Vyberte **Uložit** použít kontaktní informace zabezpečení do vašeho předplatného.

## <a name="see-also"></a>Viz také
Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky v Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o bezpečnosti Azure](http://blogs.msdn.com/b/azuresecurity/) – získejte nejnovější informace zabezpečení Azure a informace.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
