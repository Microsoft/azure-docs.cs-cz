---
title: Správa přístupu k hostované službě Azure AD přístup recenze | Microsoft Docs
description: Spravovat uživatele typu Host jako členové skupiny nebo přiřazené k aplikaci s Azure Active Directory přístup recenze
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: compliance-reports
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: d593a59c9e190eda73497aef09d4259d0947eb24
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37085102"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Správa přístupu k hostované službě Azure AD přístup recenze


S Azure Active Directory (Azure AD), můžete snadno povolit spolupráce mezi hranice organizace pomocí [funkce Azure AD s B2B](active-directory-b2b-what-is-azure-ad-b2b.md). Uživatele typu Host od ostatních klientů může být [pozvat správci](active-directory-b2b-admin-add-users.md) nebo [jiných uživatelů](active-directory-b2b-how-it-works.md). Tato možnost platí i pro sociální identity, jako jsou účty Microsoft.

Také můžete snadno zajistíte, že hosta uživatelé mají odpovídající přístup. Požádejte hosty sami nebo pracovník s rozhodovací pravomocí k účasti v kontrola přístupu a znovu potvrzení (nebo dokládají odbornou) pro přístup k u hostů. Revidující se mohou vyjádřit k nutnosti dalšího přístupu pro jednotlivé uživatele, a to na základě návrhů z Azure AD. Po dokončení kontrola přístupu můžete provést změny a odebrat přístup pro hosty, kteří již nepotřebujete.

> [!NOTE]
> Tento dokument se zaměřuje na kontroly přístupu uživatelů typu Host. Pokud chcete zkontrolovat přístup všech uživatelů, ne jenom hostů, přečtěte si téma [spravovat přístup uživatelů s přístup recenze](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md). Pokud chcete zkontrolovat uživatelů členství v rolích pro správu, jako je například globální správce, přečtěte si téma [spuštění kontrola přístupu v Azure AD Privileged Identity Management](active-directory-privileged-identity-management-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Požadavky 


Kontroly přístupu jsou k dispozici v edici Premium P2 služby Azure AD, která je součástí řešení Microsoft Enterprise Mobility + Security, E5. Další informace najdete v článku [Edice služby Azure Active Directory](active-directory-editions.md). Každý uživatel, který komunikuje s touto funkcí, včetně akcí jako vytváření kontroly, vyplnění kontroly nebo potvrzení přístupu, musí mít licenci. 

Pokud budete chtít požádat uživatele typu Host zkontrolovat svůj vlastní přístup, přečtěte si informace o licencování uživatele guest. Další informace najdete v tématu [licencování spolupráce Azure AD B2B](active-directory-b2b-licensing.md).

## <a name="create-and-perform-an-access-review-for-guests"></a>Vytvoření a provedení kontrola přístupu pro hosty

Nejprve povolte přístup recenze se objevily na panelů přístup kontrolora. Jako globální správce nebo správce účtu uživatele, přejděte na [přístupu zkontroluje stránky](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/). 

Azure AD umožňuje několika scénářích kontroly uživatele typu Host.

Vyberte jednu z následujících akcí:

 - Skupiny ve službě Azure AD, který má jeden nebo více hosté jako členy.
 - Aplikace připojené ke službě Azure AD, který má jeden nebo více uživatelů typu Host přiřazen. 

Potom se můžete rozhodnout, zda požádat každý hostovaný můžete zhodnotit jejich vlastní přístup nebo požádejte jeden nebo více uživatelům zkontrolovat přístup každý hosta.

 Tyto scénáře jsou popsané v následujících částech.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Požádejte hosté ke kontrole vlastní členství ve skupině

Recenze přístup vám pomůže zajistit, že uživatelé, kteří byly pozvali a přidat do skupiny dál potřebovat přístup. Snadno můžete pokládat hosté ke kontrole vlastní členství v této skupině.

1. Spuštění kontrola přístupu pro skupinu, vyberte kontrolní zahrnout pouze členy uživatele guest a členy zkontrolovat sami. Další informace najdete v popisu [vytvoření kontroly přístupu](active-directory-azure-ad-controls-create-access-review.md).

2. Požádejte každý hostovi Zkontrolujte své vlastní členství. Ve výchozím nastavení každý hosta, který přijmout pozvánku obdrží e-mailu z Azure AD s odkazem ke kontrole přístupu. Azure AD má pokyny pro hosty o tom, jak [kontrolovat jejich přístup](active-directory-azure-ad-controls-perform-access-review.md).

3. Jakmile revidující poskytnou potřebné informace, zastavte kontrolu přístupu a proveďte příslušné změny. Další informace najdete v popisu [dokončení kontroly přístupu](active-directory-azure-ad-controls-complete-access-review.md).

4. Kromě uživatelů, kteří vlastní potřebu nepřetržitý přístup odepřen rovněž můžete odebrat uživatele, kteří neodpověděla. Uživatelé bez neodpovídá potenciálně už obdrží e-mailu.

5. Pokud skupinu pro správu přístupu nepoužívá, můžete také odebrat uživatele, kteří nebyla vybrána k účasti v recenzi, protože nebyla přijmou své pozvánky. Nepřijímají může znamenat, že pozvané uživatele e-mailovou adresu měl překlepem. Pokud skupina slouží jako distribuční seznam, případně některé uživatele typu Host nebyly zvoleny se zúčastnit, protože jsou kontaktní objekty.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Požádejte sponzor ke kontrole hosta členství ve skupině

Požádejte řad, jako je vlastník skupiny, chcete-li zkontrolovat hosta potřebu trvalá členství ve skupině.

1. Pokud chcete spustit kontrola přístupu pro skupinu, vyberte kontrolní zahrnout pouze členy uživatelské hosta. Zadejte jeden nebo více kontrolorů. Další informace najdete v popisu [vytvoření kontroly přístupu](active-directory-azure-ad-controls-create-access-review.md).

2. Požádejte revidující o informace. Ve výchozím nastavení každý z nich obdrží e-mailu od Azure AD s odkazem na panel přístupu, kde bude [provádět kontrolu přístupu](active-directory-azure-ad-controls-perform-access-review.md).

3. Jakmile revidující poskytnou potřebné informace, zastavte kontrolu přístupu a proveďte příslušné změny. Další informace najdete v popisu [dokončení kontroly přístupu](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>ASK hostech ke kontrole vlastní přístup k aplikaci

Recenze přístup vám pomůže zajistit, že uživatelé, kteří byly pozvat u konkrétní aplikace dál potřebovat přístup. Snadno můžete požádat, hosté, sami ke kontrole vlastní potřebují pro přístup.

1. Pokud chcete spustit kontrola přístupu pro aplikace, vyberte kontrolní zahrnout pouze hosty a, uživatelé si svůj vlastní přístup. Další informace najdete v popisu [vytvoření kontroly přístupu](active-directory-azure-ad-controls-create-access-review.md).

2. Požádejte každý hostovi Zkontrolujte své vlastní přístup k aplikaci. Ve výchozím nastavení každý hosta, který přijmout pozvánku obdrží e-mailu z Azure AD s odkazem ke kontrole přístupu ve vaší organizaci přístupového panelu. Azure AD má pokyny pro hosty o tom, jak [kontrolovat jejich přístup](active-directory-azure-ad-controls-perform-access-review.md).

3. Jakmile revidující poskytnou potřebné informace, zastavte kontrolu přístupu a proveďte příslušné změny. Další informace najdete v popisu [dokončení kontroly přístupu](active-directory-azure-ad-controls-complete-access-review.md).

4. Kromě uživatelů, kteří odepřen vlastní nutnost nepřetržitý přístup, můžete také odebrat uživatele typu Host kteří neodpověděla. Uživatelé bez neodpovídá potenciálně už obdrží e-mailu. Můžete také odebrat uživatele typu Host kteří nebyly zvoleny se zúčastnit, zejména v případě, že nedostali jste pozvánku, nedávno. Tyto uživatele nebylo přijmout že pozvánku k jejich a tak neměly přístup k aplikaci. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Požádejte sponzor ke kontrole přístupu k aplikaci hosta

Požádejte řad, jako je vlastník aplikace, chcete-li zkontrolovat hosta potřebu nepřetržitý přístup k aplikaci.

1. Pokud chcete spustit kontrola přístupu pro aplikace, vyberte kontrolní zahrnout pouze hosté. Jako kontroloři zadejte jeden nebo více uživatelů. Další informace najdete v popisu [vytvoření kontroly přístupu](active-directory-azure-ad-controls-create-access-review.md).

2. Požádejte revidující o informace. Ve výchozím nastavení každý z nich obdrží e-mailu od Azure AD s odkazem na panel přístupu, kde bude [provádět kontrolu přístupu](active-directory-azure-ad-controls-perform-access-review.md).

3. Jakmile revidující poskytnou potřebné informace, zastavte kontrolu přístupu a proveďte příslušné změny. Další informace najdete v popisu [dokončení kontroly přístupu](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Požádejte hosté zkontrolovat jejich potřebují pro přístup, obecné

V některých organizacích hosté nemusíte být vědomi jejich členství ve skupinách.

> [!NOTE]
> Starší verze na portálu Azure nebyla povolit přístup pro správu uživatelů s UserType hosta. V některých případech správcem ve vašem adresáři mohlo změnit hodnotu vlastnosti UserType hostovaný na člen pomocí prostředí PowerShell. Pokud už k této změně došlo v adresáři, předchozí dotaz nemusí zahrnovat všechny hosta uživatelů, kteří v minulosti vytvořili přístupová práva pro správu. V takovém případě musíte změnit UserType hosta nebo ručně zahrnout Host členství ve skupině.

1. Vytvořte skupinu zabezpečení ve službě Azure AD s hosté jako členové, pokud ještě neexistuje vhodné skupiny. Můžete například vytvořit skupinu s ručně zachována členství hostů. Nebo můžete vytvořit dynamická skupina s názvem, například "Hosté Contoso" pro uživatele v klientovi Contoso, kteří mají hodnotu atribut UserType hosta.  Pro efektivitu zajistěte skupiny guests převážně – nemáte vyberte skupinu, která obsahuje uživatele, kteří mají být zkontrolovány nepotřebujete.

2. Pokud chcete spustit kontrola přístupu pro tuto skupinu, vyberte kontroloři mají být členy sami. Další informace najdete v popisu [vytvoření kontroly přístupu](active-directory-azure-ad-controls-create-access-review.md).

3. Požádejte každý hostovi Zkontrolujte své vlastní členství. Ve výchozím nastavení každý hosta, který přijmout pozvánku obdrží e-mailu z Azure AD s odkazem ke kontrole přístupu ve vaší organizaci přístupového panelu. Azure AD má pokyny pro hosty o tom, jak [kontrolovat jejich přístup](active-directory-azure-ad-controls-perform-access-review.md).  Tyto hosté, kteří nepřijal jejich pozvání se zobrazí ve výsledcích zkontrolujte jako "Není oznámení".

4. Po kontroloři poskytnout vstup, zastavte kontrola přístupu. Další informace najdete v popisu [dokončení kontroly přístupu](active-directory-azure-ad-controls-complete-access-review.md).

5. Pro hosty, kteří byly byl odepřen, nebyla dokončena kontrola nebo nebyla dříve přijmout pozvánku k jejich odebrání přístupu hosta. Pokud některé z hostů jsou kontakty, které byly vybrány k účasti v recenzi nebo Pozvánka nebyla dříve přijetí, můžete zakázat své účty pomocí portálu Azure nebo prostředí PowerShell. Pokud Host už potřebuje přístup a není kontaktu, můžete odebrat jejich objekt uživatele z adresáře pomocí portálu Azure nebo prostředí PowerShell k odstranění tohoto objektu uživatele guest.

## <a name="next-steps"></a>Další postup

[Vytváření kontroly přístupu pro členy skupiny nebo přístupu k aplikaci](active-directory-azure-ad-controls-create-access-review.md)







