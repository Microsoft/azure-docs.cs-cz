---
title: Kontroly přístupu pomocí služby Azure AD ke správě uživatelů, které jsou vyloučené ze zásad podmíněného přístupu | Dokumentace Microsoftu
description: Použití Azure Active Directory ke správě uživatelů, kteří jsou ze zásad podmíněného přístupu vyloučené kontroly přístupu (Azure AD)
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
ms.subservice: compliance
ms.date: 09/25/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: a82fdfe598e11415130950443fb0d3e27f5d9d01
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56168536"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-that-have-been-excluded-from-conditional-access-policies"></a>Kontroly přístupu pomocí služby Azure AD ke správě uživatelů, které jsou vyloučené ze zásad podmíněného přístupu

Ideální světě, všemi uživateli proveďte následujícím způsobem přístup zásady zabezpečení přístupu k prostředkům vaší organizace. Někdy existují však obchodních případů, které vyžadují, abyste provedli výjimky. Tento článek popisuje některé příklady, kde může být vyžadováno vyloučení a jak můžete jako správce IT může spravovat tuto úlohu, vyhnout dohledu výjimky zásad a auditoři poskytnout důkaz, že tyto výjimky jsou kontrolovány pravidelně používáte Azure Kontroly přístupu Active Directory (Azure AD).

> [!NOTE]
> Kontroly přístupu platný Azure AD Premium P2, Enterprise Mobility + Security E5, nepotřebujete k tomu použít Azure AD placené nebo zkušební licenci. Další informace najdete v článku [Edice služby Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Proč by vám vyloučení uživatelů ze zásad?

Jako IT správce, můžete použít [podmíněný přístup Azure AD](../conditional-access/overview.md) budou muset uživatelé ověřovat pomocí služby Multi-Factor authentication (MFA) nebo přihlášení z důvěryhodné sítě nebo zařízení. Při plánování nasazení, zjistíte, že některé z těchto požadavků nelze splnit třídou všichni uživatelé. Například jsou uživatelé, kteří pracují z vzdálené kanceláře, které nejsou součástí vaší interní sítě nebo je výkonný, který používá starý telefon, který není podporován. Podnik vyžaduje, že tito uživatelé moct přihlásit a výkon své práce, proto, že jsou vyloučené ze zásad podmíněného přístupu.

Další příklad, můžete použít [pojmenovaná umístění](../conditional-access/location-condition.md) podmíněného přístupu pro konfiguraci funkce set okresech a oblastí, ze kterých nechcete uživatelům povolit přístup k jejich tenanta.

![Pojmenovaná umístění](./media/conditional-access-exclusion/named-locations.png)

V některých případech ale uživatelé můžou mít rozumný důvod k přihlášení z těchto zemí blokované. Například uživatelé mohou být na služební cestě pro pracovní nebo osobní důvodů. V tomto příkladu může mít zásady podmíněného přístupu k blokování těchto zemí skupinu vyhrazené cloudové zabezpečení pro uživatele, kteří jsou ze zásad vyloučení. Uživatelé, kteří potřebují přístup při cestování, sami můžete přidat do skupiny pomocí [Azure AD Samoobslužná správa skupiny](../users-groups-roles/groups-self-service-management.md).

Dalším příkladem může být, že máte zásady podmíněného přístupu, který [bloky starší verze ověřování pro většinu uživatelů](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/). Společnost Microsoft důrazně doporučuje blokuje použití starších verzí protokolů ve vašem tenantovi a zlepšit tak stav zabezpečení. Ale pokud máte uživatele, kteří potřebují vůbec používat metody starší verze ověřování pro přístup k prostředkům pomocí Office 2010 nebo IMAP, SMTP/POP na základě klientů, pak můžete vyloučit tyto uživatele ze zásad, kterou blokovat ověřování starší verze metody.

## <a name="why-are-exclusions-challenging"></a>Vyloučení jsou náročné

Ve službě Azure AD můžete omezit rozsah zásady podmíněného přístupu pro skupinu uživatelů. Můžete také vyloučit některé z těchto uživatelů výběrem role adresáře, jednotlivé uživatele nebo hosté jednotlivých uživatelů. Je dobré si uvědomit, že když jsou nakonfigurované tyto výjimky, záměru zásad se nedá vynutit pro tyto uživatele. Pokud tyto výjimky byly nakonfigurovány jako seznam jednotlivých uživatelů nebo prostřednictvím starší místní skupiny zabezpečení, pak omezí viditelnost tohoto seznamu vyloučení (uživatelé nemusí vědět o jeho existenci) a správce IT kontrolu nad jeho (uživatelé mohou připojit Skupina zabezpečení obejít zásady). Kromě toho mohou uživatelé, kteří kvalifikovaný pro vyloučení v jednom okamžiku už ho potřebovat nebo způsobilé pro něj.

Na začátku vyloučení je krátký seznam uživatelů, kteří obejít zásady. V průběhu času jsou vyloučeny více uživatelů a roste seznamu. V určitém okamžiku je potřeba zkontrolovat seznam a potvrďte, že každý z těchto uživatelů stále vyloučeny. Správa seznamu z technického hlediska, může být poměrně snadné, ale který činím obchodní rozhodnutí a jak můžete se ujistit, že je všechno auditovatelných?

Ale pokud nakonfigurujete vyloučení do skupiny Azure AD pomocí zásad podmíněného přístupu, pak vám pomůže kontroly přístupu jako ovládací prvek kompenzační viditelnost pomoci zvýšit a snížit počet uživatelů, kteří mají k výjimce.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Jak vytvořit skupinu pro vyloučení v zásadách podmíněného přístupu

Postupujte podle těchto kroků a vytvořte novou skupinu Azure AD a zásady podmíněného přístupu, který se nedá použít u této skupiny.

### <a name="create-an-exclusion-group"></a>Vytvořit skupinu pro vyloučení

1. Přihlaste se k portálu Azure.

1. V levém navigačním panelu klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **skupiny**.

1. V horní nabídce klikněte na tlačítko **nová skupina** a otevřete tak podokno skupiny.

1. V **typ skupiny** seznamu vyberte **zabezpečení**. Zadejte název a popis.

1. Nezapomeňte nastavit **členství** typ, který **přiřazeno**.

1. Vyberte uživatele, kteří by měl být součástí této skupiny vyloučení a pak klikněte na tlačítko **vytvořit**.

    ![Podokno nové skupiny](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Vytvořit zásady podmíněného přístupu, které nezahrnuje skupiny

Nyní můžete vytvořit zásady podmíněného přístupu, který používá tuto skupinu pro vyloučení.

1. V levém navigačním panelu klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **podmíněného přístupu** otevřít **zásady** okno.

1. Klikněte na tlačítko **nové zásady** otevřít **nový** podokně.

1. Zadejte název.

1. V části úlohy klikněte na **uživatelů a skupin**.

1. Na **zahrnout** kartu, vyberte možnost **všichni uživatelé**.

1. Na **vyloučit** kartu, zaškrtněte políčko **uživatelů a skupin** a potom klikněte na tlačítko **vybrat vyloučené uživatele**.

1. Vyberte skupinu, kterou jste vytvořili pro vyloučení pak.

    > [!NOTE]
    > Jako osvědčený postup doporučujeme alespoň jeden účet správce vyloučit ze zásad, když se test a ujistěte se, že nejsou zamknutí mimo vašeho tenanta.

1. Pokračujte v nastavení zásad podmíněného přístupu na základě požadavků organizace.

    ![Vybrat vyloučené uživatele](./media/conditional-access-exclusion/select-excluded-users.png)

Pojďme zahrnují dva příklady, kde můžete použít kontroly přístupu ke správě vyloučení v rámci zásad podmíněného přístupu.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countries"></a>Příklad 1: Kontroly přístupu pro uživatele, kteří používají ze zemí blokované

Řekněme, že máte, která zablokuje přístup pro zásady podmíněného přístupu v určitých zemích. Obsahuje skupinu, která je vyloučena ze zásad. Tady je kontrola doporučené přístupu ve kterém jsou kontrolovány členy skupiny.

> [!NOTE]
> Vytvoření kontroly přístupu se vyžaduje roli Globální správce nebo správce uživatelských účtů.

1. Kontrola bude se každý týden.

2. Nikdy se ukončí pokud chcete mít jistotu, že je tato skupina vyloučení uchování nejnovější.

3. Všichni členové této skupiny budou v oboru pro revizi.

4. Každý uživatel bude mít vlastní ověřit, že stále potřebují mít přístup z těchto zemí blokované, proto ještě musí být členem skupiny.

5. Pokud uživatel nemá reagovat na žádosti o revizi, se automaticky odeberou ze skupiny a proto se mít nadále přístup k tenantovi při cestě do těchto zemí.

6. Povolte e-mailová oznámení, takže uživatelům se zobrazí oznámení o zahájení a dokončení kontroly přístupu.

    ![Vytvoření kontroly přístupu](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Příklad 2: Kontroly přístupu pro uživatele, kteří používají starší verze ověřování

Řekněme, že máte zásady podmíněného přístupu, která zablokuje přístup pro uživatele, kteří používají starší verze ověřování a starší verze klienta. Obsahuje skupinu, která je vyloučena ze zásad. Tady je kontrola doporučené přístupu ve kterém jsou kontrolovány členy skupiny.

1. Tato kontrola musel být opakované kontroly.

2. Všem uživatelům ve skupině by bylo potřeba měli zkontrolovat.

3. To je možné nakonfigurovat seznam vlastníkům obchodních jednotek jako Vybraní kontroloři.

4. Automaticky použít výsledky a odebírat uživatele, které nebyly schváleny nadále používat starší verze metod.

5. Může být užitečné povolte doporučení, aby kontroloři velkých skupin může snadno rozhodnutí jejich.

6. Povolte e-mailová oznámení, takže uživatelům se zobrazí oznámení o zahájení a dokončení kontroly přístupu.

    ![Vytvoření kontroly přístupu](./media/conditional-access-exclusion/create-access-review-2.png)

**Tipu pro**: Pokud máte mnoho vyloučení skupin a proto potřeba vytvořit několik kontrol přístupu, teď máme rozhraní API v beta verzi Microsoft Graphu koncového bodu, který vám umožní vytvořit a spravovat prostřednictvím kódu programu. Abyste mohli začít, najdete v článku [kontroly přístupu Azure AD, reference k rozhraní API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root) a [kontroly příklad načítání přístup služby Azure AD prostřednictvím Microsoft Graphu](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096).

## <a name="access-review-results-and-audit-logs"></a>Výsledky kontroly přístupu a protokolů auditu

Teď, když máte všechno, co v místě, skupiny, zásady podmíněného přístupu a kontrol přístupu, je čas pro monitorování a sledování důsledků tyto kontroly.

1. Na webu Azure Portal, otevřete **kontrol přístupu** okno.

1. Otevřete ovládací prvek a program, který jste vytvořili pro správu skupiny vyloučení.

1. Klikněte na tlačítko **výsledky** zobrazíte, který byl schválen zůstat na seznamu a který byl odebrán.

    ![Výsledky kontroly přístupu](./media/conditional-access-exclusion/access-reviews-results.png)

1. Pak klikněte na tlačítko **protokoly auditu** zobrazíte akce, které byly během této revize.

    ![Protokoly auditu kontroly přístupu](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Jako správce IT víte, že správa vyloučení skupin do zásad je někdy nevyhnutelné. Ale údržbu těchto skupin, je kontrola v pravidelných intervalech majitel firmy a uživatelé sami a auditování, které tyto změny můžete provádět snadněji díky přístupu Azure AD kontroly.

## <a name="next-steps"></a>Další postup

- [Vytvoření kontroly přístupu členů skupiny nebo přístupu k aplikacím v Azure AD](create-access-review.md)
- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)
