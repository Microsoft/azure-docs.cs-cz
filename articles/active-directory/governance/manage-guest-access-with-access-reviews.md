---
title: Správa přístupu hostů pomocí kontrol přístupu – Azure AD
description: Správa uživatelů typu Host jako členů skupiny nebo přiřazených k aplikaci pomocí kontrol přístupu ke službě Azure Active Directory
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/13/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bf2f9503ae704110786a1e73aec3da18c17e4ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932427"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Správa přístupu uživatelů typu host pomocí kontrol přístupu Azure AD


S Azure Active Directory (Azure AD) můžete snadno povolit spolupráci napříč hranicemi organizace pomocí [funkce Azure AD B2B](../b2b/what-is-b2b.md). Uživatelé typu Host z jiných klientů mohou být [pozváni správci](../b2b/add-users-administrator.md) nebo [jinými uživateli](../b2b/what-is-b2b.md). Tato funkce platí také pro sociální identity, jako jsou účty Microsoft.

Můžete také snadno zajistit, aby uživatelé typu Host měli odpovídající přístup. Můžete požádat samotné hosty nebo osoby s rozhodovací pravomocí, aby se zúčastnili kontroly přístupu a znovu potvrdili (nebo potvrdili) přístup hostů. Revidující se mohou vyjádřit k nutnosti dalšího přístupu pro jednotlivé uživatele, a to na základě návrhů z Azure AD. Po dokončení kontroly přístupu můžete provést změny a odebrat přístup hostům, kteří ji již nepotřebují.

> [!NOTE]
> Tento dokument se zaměřuje na kontrolu přístupu uživatelů typu Host. Pokud chcete zkontrolovat přístup všech uživatelů, nejen hostů, přečtěte si informace [o kontrole přístupu uživatelů](manage-user-access-with-access-reviews.md). Pokud chcete zkontrolovat členství uživatelů v rolích pro správu, jako je globální správce, přečtěte si informace [o spuštění kontroly přístupu v části Správa privilegovaných identit azure ad](../privileged-identity-management/pim-how-to-start-security-review.md).

## <a name="prerequisites"></a>Požadavky

- Azure AD Premium P2

Další informace naleznete v [licenčních požadavcích](access-reviews-overview.md#license-requirements).

## <a name="create-and-perform-an-access-review-for-guests"></a>Vytvoření a provedení kontroly přístupu pro hosty

Nejprve jako globální správce nebo správce uživatelů přejděte na [stránku Zásad správného řízení identit, abyste zajistili,](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) že kontroly přístupu budou pro vaši organizaci připravené.

Azure AD umožňuje několik scénářů pro kontrolu uživatelů typu Host.

Můžete zkontrolovat buď:

 - Skupina ve službě Azure AD, která má jednoho nebo více hostů jako členy.
 - Aplikace připojená k Azure AD, ke které je přiřazen jeden nebo více uživatelů typu Host. 

Poté se můžete rozhodnout, zda požádáte každého hosta, aby zkontroloval svůj vlastní přístup, nebo požádáte jednoho nebo více uživatelů, aby zkontrolovalpřístup každého hosta.

 Tyto scénáře jsou popsány v následujících částech.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Požádejte hosty, aby si zkontrolovali své vlastní členství ve skupině.

Pomocí kontrol přístupu můžete zajistit, aby uživatelé, kteří byli pozváni a přidáni do skupiny, nadále potřebovali přístup. Můžete hosty snadno požádat, aby si zkontrolovali své vlastní členství v této skupině.

1. Chcete-li pro skupinu vytvořit kontrolu přístupu, vyberte recenzi, která bude zahrnovat pouze členy uživatele typu Host a kterou členové sami zkontrolují. Další informace naleznete [v tématu Vytvoření kontroly přístupu skupin nebo aplikací](create-access-review.md).

2. Požádejte každého hosta, aby si zkontroloval své vlastní členství. Ve výchozím nastavení každý host, který přijal pozvánku obdrží e-mail z Azure AD s odkazem na kontrolu přístupu. Azure AD obsahuje pokyny pro hosty, jak [zkontrolovat přístup ke skupinám nebo aplikacím](perform-access-review.md).

3. Jakmile revidující poskytnou potřebné informace, zastavte kontrolu přístupu a proveďte příslušné změny. Další informace naleznete [v tématu Complete a access review of groups or applications](complete-access-review.md).

4. Kromě uživatelů, kteří odmítli vlastní potřebu trvalého přístupu, můžete také odebrat uživatele, kteří neodpověděli. Nereagující uživatelé už možná nedostávají e-maily.

5. Pokud se skupina nepoužívá pro správu přístupu, můžete také odebrat uživatele, kteří nebyli vybráni k účasti v recenzi, protože pozvánku nepřijali. Nepřijetí může znamenat, že e-mailová adresa pozvaných uživatelů měla překlep. Pokud se skupina používá jako distribuční seznam, možná někteří uživatelé typu Host nebyli vybráni k účasti, protože jsou to objekty kontaktů.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Požádejte sponzora, aby zkontroloval členství hosta ve skupině.

Můžete požádat sponzora, například vlastníka skupiny, aby zkontroloval potřebu hosta pro další členství ve skupině.

1. Chcete-li pro skupinu vytvořit kontrolu přístupu, vyberte ji, aby zahrnovala pouze členy uživatele typu Host. Pak zadejte jednoho nebo více recenzentů. Další informace naleznete [v tématu Vytvoření kontroly přístupu skupin nebo aplikací](create-access-review.md).

2. Požádejte revidující o informace. Ve výchozím nastavení každý obdrží e-mail z Azure AD s odkazem na přístupový panel, kde [kontrolují přístup ke skupinám nebo aplikacím](perform-access-review.md).

3. Jakmile revidující poskytnou potřebné informace, zastavte kontrolu přístupu a proveďte příslušné změny. Další informace naleznete [v tématu Complete a access review of groups or applications](complete-access-review.md).

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Požádejte hosty, aby si zkontrolovali vlastní přístup k aplikaci.

Pomocí kontrol přístupu můžete zajistit, aby uživatelé, kteří byli pozváni pro určitou aplikaci, nadále potřebovali přístup. Můžete snadno požádat hosty, aby zkontrolovali svou vlastní potřebu přístupu.

1. Chcete-li vytvořit kontrolu přístupu pro aplikaci, vyberte recenzi, která bude zahrnovat pouze hosty a že uživatelé zkontrolují svůj vlastní přístup. Další informace naleznete [v tématu Vytvoření kontroly přístupu skupin nebo aplikací](create-access-review.md).

2. Požádejte každého hosta, aby si zkontroloval svůj vlastní přístup k aplikaci. Ve výchozím nastavení každý host, který přijal pozvánku obdrží e-mail z Azure AD. Tento e-mail má odkaz na kontrolu přístupu na přístupovém panelu vaší organizace. Azure AD obsahuje pokyny pro hosty, jak [zkontrolovat přístup ke skupinám nebo aplikacím](perform-access-review.md).

3. Jakmile revidující poskytnou potřebné informace, zastavte kontrolu přístupu a proveďte příslušné změny. Další informace naleznete [v tématu Complete a access review of groups or applications](complete-access-review.md).

4. Kromě uživatelů, kterým byl odepřen vlastní potřeba trvalého přístupu, můžete také odebrat uživatele typu Host, kteří neodpověděli. Nereagující uživatelé už možná nedostávají e-maily. Můžete také odebrat uživatele typu Host, kteří nebyli vybráni k účasti, zejména pokud nebyli nedávno pozváni. Tito uživatelé pozvánku nepřijali, a proto neměli k aplikaci přístup. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Požádejte sponzora, aby zkontroloval přístup hosta k aplikaci.

Můžete požádat sponzora, například vlastníka aplikace, aby zkontroloval potřebu hosta pro trvalý přístup k aplikaci.

1. Chcete-li vytvořit kontrolu přístupu pro aplikaci, vyberte recenzi, která bude zahrnovat pouze hosty. Potom zadejte jednoho nebo více uživatelů jako recenzentů. Další informace naleznete [v tématu Vytvoření kontroly přístupu skupin nebo aplikací](create-access-review.md).

2. Požádejte revidující o informace. Ve výchozím nastavení každý obdrží e-mail z Azure AD s odkazem na přístupový panel, kde [kontrolují přístup ke skupinám nebo aplikacím](perform-access-review.md).

3. Jakmile revidující poskytnou potřebné informace, zastavte kontrolu přístupu a proveďte příslušné změny. Další informace naleznete [v tématu Complete a access review of groups or applications](complete-access-review.md).

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Požádejte hosty, aby zkontrolovali jejich potřebu přístupu obecně

V některých organizacích nemusí být hosté informováni o členství ve skupinách.

> [!NOTE]
> Dřívější verze portálu Azure nepovolily přístup uživatelů pro správu pomocí uživatelského typu hosta. V některých případech může správce ve vašem adresáři změnit hodnotu UserType hosta na Člen pomocí prostředí PowerShell. Pokud k této změně dříve došlo ve vašem adresáři, předchozí dotaz nemusí zahrnovat všechny uživatele typu Host, kteří v minulosti měli přístupová práva správce. V takovém případě je třeba buď změnit usertype hosta nebo ručně zahrnout hosta do členství ve skupině.

1. Vytvořte skupinu zabezpečení ve službě Azure AD s hosty jako členy, pokud vhodná skupina ještě neexistuje. Můžete například vytvořit skupinu s ručně udržovaným členstvím hostů. Nebo můžete vytvořit dynamickou skupinu s názvem, jako je například "Hosté Contoso" pro uživatele v tenantovi Contoso, kteří mají hodnotu atributu UserType Guest.  Z důvodu efektivity zajistěte, aby skupina byla převážně hosty – nevybírejte skupinu, která má členské uživatele, protože uživatelé členů nemusí být kontrolováni.  Mějte také na paměti, že uživatel typu Host, který je členem skupiny, může zobrazit ostatní členy skupiny.

2. Chcete-li pro tuto skupinu vytvořit kontrolu přístupu, vyberte recenzenty, kteří mají být členy sami. Další informace naleznete [v tématu Vytvoření kontroly přístupu skupin nebo aplikací](create-access-review.md).

3. Požádejte každého hosta, aby si zkontroloval své vlastní členství. Ve výchozím nastavení každý host, který přijal pozvánku obdrží e-mail z Azure AD s odkazem na kontrolu přístupu v přístupovém panelu vaší organizace. Azure AD obsahuje pokyny pro hosty, jak [zkontrolovat přístup ke skupinám nebo aplikacím](perform-access-review.md).  Hosté, kteří pozvánku nepřijali, se ve výsledcích recenze zobrazí jako "Not Not ified".

4. Poté, co recenzenti zadat vstup, zastavit kontrolu přístupu. Další informace naleznete [v tématu Complete a access review of groups or applications](complete-access-review.md).

5. Odeberte přístup hostů, kterým byl odepřen, nedokončil recenzi nebo pozvánku dříve nepřijal. Pokud někteří hosté jsou kontakty, které byly vybrány k účasti v recenzi nebo dříve nepřijali pozvánku, můžete zakázat jejich účty pomocí portálu Azure nebo PowerShellu. Pokud host již nepotřebuje přístup a není kontakt, můžete odebrat jeho objekt uživatele z adresáře pomocí portálu Azure nebo PowerShell u odstranění objektu uživatele hosta.

## <a name="next-steps"></a>Další kroky

[Vytvoření kontroly přístupu skupin nebo aplikací](create-access-review.md)







