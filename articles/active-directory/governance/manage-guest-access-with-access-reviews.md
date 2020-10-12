---
title: Správa přístupu hosta pomocí kontrol přístupu – Azure AD
description: Správa uživatelů typu Host jako členů skupiny nebo přiřazení k aplikaci s využitím kontrol přístupu Azure Active Directory
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77c560ffd01fe6d0b93e18b963574136c086fdc4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88783191"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Správa přístupu uživatelů typu host pomocí kontrol přístupu Azure AD


Pomocí služby Azure Active Directory (Azure AD) můžete snadno povolit spolupráci napříč hranicemi organizace pomocí [funkce Azure AD B2B](../external-identities/what-is-b2b.md). Správce nebo [jiní uživatelé](../external-identities/what-is-b2b.md)můžou [pozvat](../external-identities/add-users-administrator.md) uživatele typu host z jiných tenantů. Tato možnost platí i pro sociální identity, jako jsou účty Microsoft.

Můžete taky snadno zajistit, aby měli uživatelé typu Host odpovídající přístup. Můžete požádat hosty nebo rozhodovacího tvůrce, aby se účastnili kontroly přístupu a recertifikace (nebo potvrzení) pro přístup hostů. Revidující se mohou vyjádřit k nutnosti dalšího přístupu pro jednotlivé uživatele, a to na základě návrhů z Azure AD. Po dokončení kontroly přístupu pak můžete provést změny a odebrat přístup hostům, kteří ho už nepotřebují.

> [!NOTE]
> Tento dokument se zaměřuje na kontrolu přístupu uživatelů typu Host. Pokud chcete zkontrolovat přístup všech uživatelů, nikoli jenom hosté, přečtěte si téma [Správa přístupu uživatelů pomocí kontrol přístupu](manage-user-access-with-access-reviews.md). Pokud chcete zkontrolovat členství uživatelů v rolích pro správu, jako je například globální správce, přečtěte si téma [spuštění kontroly přístupu v Azure AD Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md).

## <a name="prerequisites"></a>Požadavky

- Azure AD Premium P2

Další informace najdete v [licenčních požadavcích](access-reviews-overview.md#license-requirements).

## <a name="create-and-perform-an-access-review-for-guests"></a>Vytvoření a provedení kontroly přístupu pro hosty

Nejdřív jako globální správce nebo Správce uživatelů přejděte na [stránku zásad správného řízení identity](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) , abyste měli jistotu, že jsou recenze pro přístup připravené pro vaši organizaci.

Azure AD umožňuje několik scénářů pro kontrolu uživatelů typu Host.

Můžete si projít:

 - Skupina ve službě Azure AD, která má jednoho nebo více hostů jako členy.
 - Aplikace připojená ke službě Azure AD, která má přiřazeného jednoho nebo více uživatelů typu Host. 

Pak se můžete rozhodnout, jestli má každý host pověřit svůj vlastní přístup, nebo požádat jednoho nebo více uživatelů, aby zkontrolovali přístup všech hostů.

 Tyto scénáře jsou pokryté v následujících oddílech.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Požádat hosty o kontrolu vlastního členství ve skupině

Pomocí kontrol přístupu můžete zajistit, že uživatelé, kteří byli pozváni a přidáni do skupiny, budou dál potřebovat přístup. Můžete snadno požádat hosty, aby si zkontrolovali vlastní členství v této skupině.

1. Chcete-li vytvořit kontrolu přístupu pro skupinu, vyberte tuto kontrolu, aby zahrnovala pouze uživatelské členy typu Host a aby se členové zkontrolovali sami. Další informace najdete v tématu [Vytvoření kontroly přístupu skupin nebo aplikací](create-access-review.md).

2. Požádejte každého hosta, aby zkontroloval vlastní členství. Ve výchozím nastavení každý host, který přijal pozvánku, obdrží e-mail od Azure AD s odkazem na kontrolu přístupu. Azure AD obsahuje pokyny pro hosty o tom, jak [kontrolovat přístup ke skupinám nebo aplikacím](perform-access-review.md).

3. Jakmile revidující poskytnou potřebné informace, zastavte kontrolu přístupu a proveďte příslušné změny. Další informace najdete v tématu [dokončení kontroly přístupu skupin nebo aplikací](complete-access-review.md).

4. Kromě uživatelů, kteří odmítli vlastní potřebu přístupu, můžete také odebrat uživatele, kteří neodpověděli. Nereagující uživatelé již nemohou přijímat e-maily.

5. Pokud se skupina nepoužívá pro správu přístupu, můžete také odebrat uživatele, kteří nebyli vybráni k účasti na revizi, protože nepřijali jejich pozvánku. Nepřijetí může značit, že e-mailová adresa pozvaného uživatele měla překlep. Pokud se skupina používá jako distribuční seznam, možná někteří uživatelé typu Host nebyli vybráni, aby se účastnili, protože se jedná o objekty kontaktů.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Požádat investora, aby zkontroloval členství hosta ve skupině

Můžete požádat o sponzora, například vlastníka skupiny, a zkontrolovat potřebu hosta ve skupině.

1. Chcete-li vytvořit kontrolu přístupu pro skupinu, vyberte revizi, která bude obsahovat pouze uživatelské členy typu Host. Pak zadejte jednoho nebo více revidujících. Další informace najdete v tématu [Vytvoření kontroly přístupu skupin nebo aplikací](create-access-review.md).

2. Požádejte revidující o informace. Ve výchozím nastavení každý z nich obdrží e-mail od Azure AD s odkazem na přístupový panel, kde se [posoudí o přístupu ke skupinám nebo aplikacím](perform-access-review.md).

3. Jakmile revidující poskytnou potřebné informace, zastavte kontrolu přístupu a proveďte příslušné změny. Další informace najdete v tématu [dokončení kontroly přístupu skupin nebo aplikací](complete-access-review.md).

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Požádat hosty, aby zkontrolovali vlastní přístup k aplikaci

Pomocí kontrol přístupu můžete zajistit, že uživatelé, kteří byli pozváni pro konkrétní aplikaci, budou dál potřebovat přístup. Můžete snadno požádat o samotné hosty, aby zkontrolovali vlastní potřebu přístupu.

1. Chcete-li vytvořit kontrolu přístupu pro aplikaci, vyberte tuto kontrolu, aby zahrnovala pouze hosty a aby uživatelé zkontrolovali vlastní přístup. Další informace najdete v tématu [Vytvoření kontroly přístupu skupin nebo aplikací](create-access-review.md).

2. Požádejte každého hosta, aby zkontroloval svůj vlastní přístup k aplikaci. Ve výchozím nastavení každý host, který přijal pozvánku, obdrží e-mail od Azure AD. Tento e-mail obsahuje odkaz na kontrolu přístupu na přístupovém panelu vaší organizace. Azure AD obsahuje pokyny pro hosty o tom, jak [kontrolovat přístup ke skupinám nebo aplikacím](perform-access-review.md).

3. Jakmile revidující poskytnou potřebné informace, zastavte kontrolu přístupu a proveďte příslušné změny. Další informace najdete v tématu [dokončení kontroly přístupu skupin nebo aplikací](complete-access-review.md).

4. Kromě uživatelů, kteří odepřeli vlastní potřebu přístupu, můžete také odebrat uživatele typu Host, kteří neodpověděli. Nereagující uživatelé již nemohou přijímat e-maily. Můžete také odebrat uživatele typu Host, kteří nebyli vybráni k účasti, zejména v případě, že nebyli nedávno pozváni. Uživatelé nepřijali svou pozvánku, takže nemají přístup k aplikaci. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Zeptejte se sponzora, aby zkontroloval přístup hosta k aplikaci.

Můžete požádat o sponzora, jako je třeba vlastník aplikace, a zkontrolovat potřebu hosta při pokračování přístupu k aplikaci.

1. Chcete-li vytvořit kontrolu přístupu pro aplikaci, vyberte tuto kontrolu, aby zahrnovala pouze hosty. Pak zadejte jednoho nebo více uživatelů jako kontrolory. Další informace najdete v tématu [Vytvoření kontroly přístupu skupin nebo aplikací](create-access-review.md).

2. Požádejte revidující o informace. Ve výchozím nastavení každý z nich obdrží e-mail od Azure AD s odkazem na přístupový panel, kde se [posoudí o přístupu ke skupinám nebo aplikacím](perform-access-review.md).

3. Jakmile revidující poskytnou potřebné informace, zastavte kontrolu přístupu a proveďte příslušné změny. Další informace najdete v tématu [dokončení kontroly přístupu skupin nebo aplikací](complete-access-review.md).

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Požádejte hosty, aby zkontrolovali potřebu přístupu, obecně

V některých organizacích nemusí hosté vědět o jejich členství ve skupinách.

> [!NOTE]
> Starší verze Azure Portal nepovolovaly přístup správců pro uživatele s použitím UserType hosta. V některých případech se může stát, že správce v adresáři změnil hodnotu hodnoty UserType hosta na člen pomocí prostředí PowerShell. Pokud tato změna proběhla dříve v adresáři, nemusí předchozí dotaz zahrnovat všechny uživatele typu Host, kteří mají historická práva pro správu. V takovém případě je nutné buď změnit uživatelskou roli hosta, nebo ručně zahrnout hosta do členství ve skupině.

1. Pokud příslušná skupina ještě neexistuje, vytvořte v Azure AD skupinu zabezpečení s hosty jako členy. Můžete například vytvořit skupinu s ručně udržovaným členstvím hostů. Nebo můžete vytvořit dynamickou skupinu s názvem, například "hosté společnosti Contoso" pro uživatele v tenantovi společnosti Contoso, kteří mají hodnotu atributu UserType Host.  Pro efektivitu zajistěte, aby byla skupina předem hosty – nevybírejte skupinu, která má členské uživatele, protože členské uživatele není nutné kontrolovat.  Pamatujte také na to, že uživatel typu Host, který je členem skupiny, uvidí ostatní členy skupiny.

2. Pokud chcete pro tuto skupinu vytvořit kontrolu přístupu, vyberte kontrolory, které mají být členy samotného. Další informace najdete v tématu [Vytvoření kontroly přístupu skupin nebo aplikací](create-access-review.md).

3. Požádejte každého hosta, aby zkontroloval vlastní členství. Ve výchozím nastavení každý host, který přijal pozvánku, obdrží e-mail od Azure AD s odkazem na kontrolu přístupu v přístupovém panelu vaší organizace. Azure AD obsahuje pokyny pro hosty o tom, jak [kontrolovat přístup ke skupinám nebo aplikacím](perform-access-review.md).  Účastníci, kteří nepřijali své pozvánky, se zobrazí ve výsledcích kontroly jako "neoznámeno".

4. Po zadání revidujících zastavte kontrolu přístupu. Další informace najdete v tématu [dokončení kontroly přístupu skupin nebo aplikací](complete-access-review.md).

5. Odeberte přístup hosta pro hosty, kterým byla zamítnutá, nedokončili jste kontrolu nebo jste předtím nepřijali svoji pozvánku. Pokud se některým z hostů nachází kontakty, které byly vybrány k účasti na revizi nebo dříve nepřijali pozvánku, můžete účty zakázat pomocí Azure Portal nebo PowerShellu. Pokud již uživatel nemá přístup, a není to kontakt, můžete z adresáře odebrat jeho objekt uživatele pomocí Azure Portal nebo PowerShellu k odstranění objektu uživatele typu Host.

## <a name="next-steps"></a>Další kroky

[Vytvoření kontroly přístupu skupin nebo aplikací](create-access-review.md)