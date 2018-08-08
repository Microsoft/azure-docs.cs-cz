---
title: Správa přístupu hostů pomocí Azure AD kontrol přístupu | Dokumentace Microsoftu
description: Spravovat uživatele typu Host jako členové skupiny nebo přidružených k aplikaci pomocí kontrol přístupu Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 3f7580b39e0ce9b1b83b5bc8ee0ed897b77e030a
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39620003"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Správa přístupu hostů pomocí Azure AD kontroly přístupu


S Azure Active Directory (Azure AD), můžete snadno povolit spolupráce napříč hranicemi organizace pomocí [funkce Azure AD B2B](active-directory-b2b-what-is-azure-ad-b2b.md). Může být uživatelé typu Host z jiných tenantů [pozvat správci](active-directory-b2b-admin-add-users.md) nebo [ostatním uživatelům](active-directory-b2b-how-it-works.md). Tato funkce platí také pro sociálních identit, jako jsou účty Microsoft.

Můžete také snadno zajistit, že uživatelé typu Host mají odpovídající přístup. Můžete požádat o hostům sami s rozhodovací pravomocí o účast v kontrolu přístupu a znovu Certifikujte (nebo potvrďte) přístup u hostů. Revidující se mohou vyjádřit k nutnosti dalšího přístupu pro jednotlivé uživatele, a to na základě návrhů z Azure AD. Po dokončení kontroly přístupu můžete provést změny a odebrat přístup pro hosty, kteří ho už nepotřebují.

> [!NOTE]
> Tento dokument se zaměřuje na kontrole přístupu uživatelů typu Host. Pokud chcete kontrolovat přístup všech uživatelů, není stejně hostů, naleznete v tématu [správě přístupu uživatelů pomocí kontrol přístupu](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md). Pokud chcete kontrolovat členství uživatelů v rolích pro správu, jako je například globální správce, přečtěte si téma [zahájení kontroly přístupu v Azure AD Privileged Identity Management](privileged-identity-management/pim-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Požadavky 


Kontroly přístupu jsou k dispozici v edici Premium P2 služby Azure AD, která je součástí řešení Microsoft Enterprise Mobility + Security, E5. Další informace najdete v článku [Edice služby Azure Active Directory](active-directory-editions.md). Každý uživatel, který komunikuje s touto funkcí, včetně akcí jako vytváření kontroly, vyplnění kontroly nebo potvrzení přístupu, musí mít licenci. 

Pokud budete chtít požádat uživatele typu Host ke kontrole svůj vlastní přístup, přečtěte si informace o licencování uživatelů typu Host. Další informace najdete v tématu [licencování spolupráce B2B ve službě Azure AD](active-directory-b2b-licensing.md).

## <a name="create-and-perform-an-access-review-for-guests"></a>Vytvoření a provedení kontroly přístupu hostů

Nejprve povolte na přístupových panelech kontrolora zobrazení kontrol přístupu. Jako globální správce nebo správce uživatelských účtů přejděte na [stránku kontrol přístupu](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/). 

Azure AD umožňuje několik scénářů pro zkontrolujete uživatelů typu Host.

Vyberte jednu z následujících akcí:

 - Skupiny ve službě Azure AD, který má nejméně hosty jako členy.
 - Aplikaci připojenou k Azure AD, která má přiřazenou jednu nebo více uživatele typu Host. 

Potom se můžete rozhodnout, zda požádat každý hosta Chcete-li kontrolovat svůj vlastní přístup nebo položit jeden nebo více uživatelům zkontrolovat přístup každý hostovaný.

 Tyto scénáře jsou popsané v následujících částech.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Požádejte hosty ke kontrole jejich členství ve skupině

Kontroly přístupu můžete použít k zajištění, že uživatelé, kteří se pozvat a přidat do skupiny i nadále potřebovat přístup. Snadno můžete požádat o hosty ke kontrole jejich členství v této skupině.

1. Zahájení kontroly přístupu pro skupinu, vyberte revizi obsahovat jenom členy uživatelů typu Host a, členové si sami. Další informace najdete v popisu [vytvoření kontroly přístupu](active-directory-azure-ad-controls-create-access-review.md).

2. Požádejte každého typu Host ke kontrole jejich členství. Ve výchozím nastavení každého typu Host, kteří přijmout pozvánku obdrží e-mailu ze služby Azure AD s odkazem na kontrolu přístupu. Azure AD obsahuje pokyny pro hosty do [zkontrolujte svůj přístup](active-directory-azure-ad-controls-perform-access-review.md).

3. Jakmile revidující poskytnou potřebné informace, zastavte kontrolu přístupu a proveďte příslušné změny. Další informace najdete v popisu [dokončení kontroly přístupu](active-directory-azure-ad-controls-complete-access-review.md).

4. Kromě uživatelé, kteří vlastní potřebu přístup byl odepřen můžete také odebrat uživatele, kteří neodpověděl. Uživatelé bez reagovat potenciálně už nebude dostávat e-mailu.

5. Pokud skupina se nepoužívá pro správu přístupu, můžete také odebrat uživatele, kteří nebyly zvoleny k účasti v revizi, protože se svou pozvánku nepřijal. Nepřijímá může znamenat, že e-mailová adresa pozvaného uživatele měli překlep. Pokud skupina slouží jako distribuční seznam, například některé uživatele typu Host nebyly zvoleny k účasti, protože jsou to objekty kontaktů.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Požádejte sponzor ke kontrole hosta členství ve skupině

Můžete požádat o sponzor, jako je vlastníkem skupiny, přečtěte si o hostovaný potřebu pokračování členství ve skupině.

1. Zahájení kontroly přístupu pro skupinu, vyberte kontrole a obsahovat jenom členy uživatelů typu Host. Zadejte nejméně jeden revidující. Další informace najdete v popisu [vytvoření kontroly přístupu](active-directory-azure-ad-controls-create-access-review.md).

2. Požádejte revidující o informace. Ve výchozím nastavení každý z nich obdrží e-mailu od Azure AD s odkazem na panel přístupu, kde bude [provádět kontrolu přístupu](active-directory-azure-ad-controls-perform-access-review.md).

3. Jakmile revidující poskytnou potřebné informace, zastavte kontrolu přístupu a proveďte příslušné změny. Další informace najdete v popisu [dokončení kontroly přístupu](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Požádejte hosté kontrolovat svůj vlastní přístup k aplikaci

Kontroly přístupu můžete použít k zajištění, že uživatelé, kteří byli pozváni pro konkrétní aplikaci i nadále potřebovat přístup. Můžete snadno požádat o samotné hosty ke kontrole jejich vlastní potřebovat pro přístup.

1. Zahájení kontroly přístupu pro aplikaci, vyberte revizi hosté pouze a, uživatelé si svůj vlastní přístup. Další informace najdete v popisu [vytvoření kontroly přístupu](active-directory-azure-ad-controls-create-access-review.md).

2. Požádejte každý hostem a kontrolovat svůj vlastní přístup k aplikaci. Ve výchozím nastavení každého typu Host, kteří přijmout pozvánku obdrží e-mailu ze služby Azure AD s odkazem na kontrolu přístupu ve vaší organizaci přístupového panelu. Azure AD obsahuje pokyny pro hosty do [zkontrolujte svůj přístup](active-directory-azure-ad-controls-perform-access-review.md).

3. Jakmile revidující poskytnou potřebné informace, zastavte kontrolu přístupu a proveďte příslušné změny. Další informace najdete v popisu [dokončení kontroly přístupu](active-directory-azure-ad-controls-complete-access-review.md).

4. Vedle uživatelů, kteří odepřen vlastní nutné pro přístup, můžete také odebrat uživatele typu Host, kteří neodpověděl. Uživatelé bez reagovat potenciálně už nebude dostávat e-mailu. Můžete také odebrat uživatele typu Host, kteří nebyly zvoleny k účasti, zejména v případě, že nebyly nedávno pozvaný. Tito uživatelé neměli přijmout že pozvánku nepřijal, proto neměli mít přístup k aplikaci. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Požádejte sponzor ke kontrole hostů přístup k aplikaci

Můžete požádat o sponzor, jako je například vlastníka aplikace, ke kontrole hosta nutnosti dalšího přístupu k aplikaci pro.

1. Zahájení kontroly přístupu pro aplikaci, vyberte revizi zahrnout pouze hosty. Zadejte jeden nebo více uživatelů jako revidující. Další informace najdete v popisu [vytvoření kontroly přístupu](active-directory-azure-ad-controls-create-access-review.md).

2. Požádejte revidující o informace. Ve výchozím nastavení každý z nich obdrží e-mailu od Azure AD s odkazem na panel přístupu, kde bude [provádět kontrolu přístupu](active-directory-azure-ad-controls-perform-access-review.md).

3. Jakmile revidující poskytnou potřebné informace, zastavte kontrolu přístupu a proveďte příslušné změny. Další informace najdete v popisu [dokončení kontroly přístupu](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Požádejte hosty ke kontrole jejich potřebě přístup, obecné

V některých organizacích hosté se nemusíte být vědomi jejich členství ve skupinách.

> [!NOTE]
> Starší verze na webu Azure portal neměli povolit přístup pro správu uživatelů s UserType hosta. V některých případech se správcem v adresáři se mohlo změnit hodnotu UserType o hostovaný na člen pomocí prostředí PowerShell. Pokud dříve k této změně došlo ve vašem adresáři, předchozí dotaz nemusí obsahovat všechny uživatele typu Host, kteří v minulosti bylo pro správu přístupových práv. V takovém případě potřebujete změnit UserType hosta nebo hosta ručně zahrnout do členství ve skupině.

1. Vytvořte skupinu zabezpečení ve službě Azure AD s hosté jako členové, pokud ještě neexistuje vhodný skupiny. Můžete například vytvořit skupinu s ručně udržována členství hostů. Nebo můžete vytvořit dynamické skupiny s názvem jako je například "Hosté Contoso" pro uživatele v tenantovi Contoso, kteří mají hodnotu atribut UserType hosta.  Účinnost ověřte, že je skupina převážně hosté – nevybírejte skupinu, která má uživatele, kteří se nemusíte být.

2. Zahájení kontroly přístupu pro tuto skupinu, vyberte revidující mají stát členy sami. Další informace najdete v popisu [vytvoření kontroly přístupu](active-directory-azure-ad-controls-create-access-review.md).

3. Požádejte každého typu Host ke kontrole jejich členství. Ve výchozím nastavení každého typu Host, kteří přijmout pozvánku obdrží e-mailu ze služby Azure AD s odkazem na kontrolu přístupu ve vaší organizaci přístupového panelu. Azure AD obsahuje pokyny pro hosty do [zkontrolujte svůj přístup](active-directory-azure-ad-controls-perform-access-review.md).  Tyto hosté, kteří jejich pozvánku nepřijal se zobrazí ve výsledcích kontroly jako "Není oznámeno".

4. Jakmile revidující poskytnou potřebné informace, zastavte kontrolu přístupu. Další informace najdete v popisu [dokončení kontroly přístupu](active-directory-azure-ad-controls-complete-access-review.md).

5. Odeberte přístup hosta pro hosty, kteří se zamítla, se nepodařilo dokončit revizi nebo dříve nepřijal svou pozvánku. Pokud některé z hostů jsou kontakty, které byly vybrány pro účast při kontrole nebo se dříve nepřijal pozvání, můžete zakázat svoje účty pomocí webu Azure portal nebo Powershellu. Pokud Host už potřebuje přístup a není kontakt, můžete odebrat jejich objekt uživatele z adresáře pomocí webu Azure portal nebo Powershellu k odstranění objektu uživatele typu Host.

## <a name="next-steps"></a>Další postup

[Vytváření kontroly přístupu pro členy skupiny nebo přístupu k aplikaci](active-directory-azure-ad-controls-create-access-review.md)







