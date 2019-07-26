---
title: Pomocí kontrol přístupu můžete spravovat uživatele vyloučené ze zásad podmíněného přístupu – Azure Active Directory | Microsoft Docs
description: Naučte se používat kontroly přístupu Azure Active Directory (Azure AD) ke správě uživatelů, kteří byli vyloučení ze zásad podmíněného přístupu.
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
ms.date: 09/25/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 246503fec6436bf49dcd5fb89c2dc0ed345a43ca
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499920"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Pomocí kontrol přístupu Azure AD Spravujte uživatele vyloučené ze zásad podmíněného přístupu.

V ideálním světě by všichni uživatelé měli postupovat podle zásad přístupu pro zabezpečení přístupu k prostředkům vaší organizace. Někdy ale existují obchodní případy, které vyžadují, abyste měli výjimky. Tento článek popisuje některé příklady, které mohou být vyžadovány pro vyloučení, a způsob, jakým správce IT může spravovat tuto úlohu, vyhněte se dohledům zásad a poskytuje auditorům kontrolu nad tím, že tyto výjimky jsou pravidelně přezkoumány pomocí Azure. Kontroly přístupu ke službě Active Directory (Azure AD).

> [!NOTE]
> K používání kontrol přístupu Azure AD se vyžaduje platná Azure AD Premium P2, Enterprise Mobility + Security E5 nebo zkušební licence. Další informace najdete v článku [Edice služby Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Proč byste měli vyloučit uživatele ze zásad?

Jako správce IT můžete použít [podmíněný přístup Azure AD](../conditional-access/overview.md) , který vyžaduje, aby uživatelé ověřili pomocí vícefaktorového ověřování (MFA) nebo se přihlásili z důvěryhodné sítě nebo zařízení. Během plánování nasazení zjistíte, že některé z těchto požadavků nemohou splnit všichni uživatelé. Existují například uživatelé, kteří pracují se vzdálenými pobočkami, které nejsou součástí vaší interní sítě nebo mají výkonného uživatele, který používá starý telefon, který není podporován. Podnik vyžaduje, aby se tito uživatelé mohli přihlásit a dělat jejich úlohy, proto jsou vyloučené ze zásad podmíněného přístupu.

V jiném příkladu můžete použít pojmenovaná [umístění](../conditional-access/location-condition.md) v podmíněném přístupu a nakonfigurovat tak sadu okresů a oblastí, ze kterých nechcete uživatelům povolit přístup ke svému tenantovi.

![Pojmenovaná umístění v podmíněném přístupu](./media/conditional-access-exclusion/named-locations.png)

V některých případech ale uživatelé můžou mít oprávněný důvod přihlásit se z těchto blokovaných zemí nebo oblastí. Například uživatelé můžou být cestování z pracovního nebo osobního důvodu. V tomto příkladu zásady podmíněného přístupu, které blokují tyto země nebo oblasti, můžou mít vyhrazenou skupinu zabezpečení cloudu pro uživatele, kteří jsou z těchto zásad vyloučení. Uživatelé, kteří potřebují přístup při cestování, se můžou do skupiny přidat pomocí [samoobslužné správy skupin Azure AD](../users-groups-roles/groups-self-service-management.md).

Dalším příkladem může být zásada podmíněného přístupu, která [blokuje starší verze ověřování pro převážnou většinu vašich uživatelů](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/). Microsoft důrazně doporučuje, abyste v tenantovi zablokovali použití starších protokolů pro zlepšení stav zabezpečení. Pokud ale máte někteří uživatelé, kteří naprosto potřebují používat starší metody ověřování pro přístup k prostředkům prostřednictvím klientů Office 2010 nebo IMAP/SMTP/POP, můžete tyto uživatele vyloučit ze zásad, které blokují starší metody ověřování.

## <a name="why-are-exclusions-challenging"></a>Proč jsou vyloučení náročné?

Ve službě Azure AD můžete nastavit obor zásad podmíněného přístupu na skupinu uživatelů. Některé z těchto uživatelů můžete také vyloučit tak, že vyberete role Azure AD, jednotlivé uživatele nebo hosty uživatelů. Je důležité si uvědomit, že když jsou tato vyloučení nakonfigurovaná, záměr zásad pro tyto uživatele nejde vynutit. Pokud byla tato vyloučení nakonfigurovaná jako seznam jednotlivých uživatelů nebo prostřednictvím starší verze místní skupiny zabezpečení, pak omezení viditelnosti tohoto seznamu vyloučení (uživatelé nemusí znát jeho existenci) a jeho kontrolu nad ním (uživatelé se můžou připojit k zásada skupiny zabezpečení pro předání zásady). Uživatelé, kteří jsou kvalifikováni pro vyloučení v jednom okamžiku, je už nepotřebují nebo by k nim měli nárok.

Na začátku vyloučení je k dispozici krátký seznam uživatelů, kteří tyto zásady obcházejí. V průběhu času se vyloučí více a více uživatelů a seznam se rozrůstá. V určitém okamžiku je potřeba zkontrolovat seznam a ověřit, že by se měli všichni tito uživatelé dál vyloučit. Správa seznamu z technického hlediska může být poměrně jednoduchá, ale v případě, že se jedná o obchodní rozhodnutí a jak se ujistit, že je vše auditováno?

Pokud ale konfigurujete vyloučení pro zásady podmíněného přístupu pomocí skupiny služby Azure AD, můžete použít kontroly přístupu jako kompenzační ovládací prvek, zvýšit viditelnost a snížit počet uživatelů, kteří mají výjimku.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Postup vytvoření skupiny vyloučení v zásadách podmíněného přístupu

Pomocí těchto kroků můžete vytvořit novou skupinu Azure AD a zásadu podmíněného přístupu, která se na tuto skupinu nevztahuje.

### <a name="create-an-exclusion-group"></a>Vytvoření skupiny vyloučení

1. Přihlaste se k portálu Azure.

1. V levém navigačním panelu klikněte na **Azure Active Directory** a pak klikněte na **skupiny**.

1. V horní nabídce klikněte na **Nová skupina** a otevřete podokno skupiny.

1. V seznamu **typ skupiny** vyberte **zabezpečení**. Zadejte název a popis.

1. Nezapomeňte nastavit typ **členství** na přiřazeno.

1. Vyberte uživatele, kteří by měli být součástí této skupiny vyloučení, a pak klikněte na **vytvořit**.

    ![Nové podokno skupiny v Azure Active Directory](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Vytvoření zásady podmíněného přístupu, která vylučuje skupinu

Nyní můžete vytvořit zásadu podmíněného přístupu, která používá tuto skupinu vyloučení.

1. V levém navigačním panelu klikněte na **Azure Active Directory** a potom kliknutím na **podmíněný přístup** otevřete okno **zásady** .

1. Kliknutím na **Nová zásada** otevřete **nové** podokno.

1. Zadejte název.

1. V části přiřazení klikněte na **Uživatelé a skupiny**.

1. Na kartě **Zahrnout** vyberte **Všichni uživatelé**.

1. Na kartě **vyloučit** přidejte značku zaškrtnutí pro **uživatele a skupiny** a potom klikněte na **Vybrat vyloučené uživatele**.

1. Vyberte skupinu vyloučení, kterou jste vytvořili.

    > [!NOTE]
    > V rámci osvědčeného postupu se doporučuje při testování vyloučit aspoň jeden účet správce ze zásady, abyste se ujistili, že se nezamknete ze svého tenanta.

1. Pokračujte v nastavování zásad podmíněného přístupu na základě požadavků vaší organizace.

    ![Vybrat podokno vyloučené uživatele v podmíněném přístupu](./media/conditional-access-exclusion/select-excluded-users.png)

Podíváme se na dva příklady, kde můžete pomocí kontrol přístupu spravovat vyloučení v zásadách podmíněného přístupu.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Příklad 1: Kontrola přístupu pro uživatele, kteří přistupují z blokovaných zemí nebo oblastí

Řekněme, že máte zásady podmíněného přístupu, které blokují přístup z určitých zemí nebo oblastí. Zahrnuje skupinu, která je z těchto zásad vyloučená. Tady je doporučený kontroler přístupu, kde jsou členové skupiny zkontrolováni.

> [!NOTE]
> K vytvoření kontrol přístupu je potřeba role globálního správce nebo Správce uživatelů.

1. Kontrola se bude opakovat každý týden.

2. Nikdy se neukončí, abyste se ujistili, že udržujete tuto skupinu vyloučení v aktuálním stavu.

3. Všichni členové této skupiny budou v oboru pro kontrolu.

4. Každý uživatel bude muset sami ověřit, že stále potřebují mít přístup z těchto blokovaných zemí nebo oblastí, takže stále musí být členem skupiny.

5. Pokud uživatel neodpoví na žádost o revizi, automaticky se odebere ze skupiny, a proto nebude moci získat přístup k tenantovi při cestování do těchto zemí nebo oblastí.

6. Povolit e-mailová oznámení, aby se uživatelům zobrazilo oznámení o zahájení a dokončení kontroly přístupu.

    ![Vytvoří podokno kontroly přístupu, například 1.](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Příklad 2: Kontrola přístupu pro uživatele, kteří přistupují pomocí starší verze ověřování

Řekněme, že máte zásady podmíněného přístupu, které blokují přístup pro uživatele pomocí starší verze ověřování a starších verzí klientů. Zahrnuje skupinu, která je z těchto zásad vyloučená. Tady je doporučený kontroler přístupu, kde jsou členové skupiny zkontrolováni.

1. Tato recenze by musela být opakovaná kontrola.

2. Všichni členové skupiny by museli zkontrolovat.

3. Dá se nakonfigurovat tak, aby vybral vlastníky obchodních jednotek jako vybrané kontrolory.

4. Automaticky použít výsledky a odebrat uživatele, kteří nebyli schválením, aby mohli dál používat starší metody ověřování.

5. Může být výhodné povolit doporučení, aby kontroloři velkých skupin mohli snadno učinit svá rozhodnutí.

6. Povolit e-mailová oznámení, aby se uživatelům zobrazilo oznámení o zahájení a dokončení kontroly přístupu.

    ![Vytvoření podokna kontroly přístupu například 2](./media/conditional-access-exclusion/create-access-review-2.png)

**Tip pro**: Pokud máte mnoho skupin vyloučení a proto potřebujete vytvořit více kontrol přístupu, teď máme v koncovém bodu Microsoft Graph beta rozhraní API, které umožňuje jejich vytváření a správu prostřednictvím kódu programu. Informace o tom, jak začít, najdete v referenčních informacích k [rozhraní API kontroly přístupu Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root) a [příklad načítání kontrol přístupu služby azure AD prostřednictvím Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096).

## <a name="access-review-results-and-audit-logs"></a>Výsledky kontroly přístupu a protokoly auditu

Teď, když máte všechno na místě, skupinu, zásady podmíněného přístupu a kontroly přístupu, je čas sledovat a sledovat výsledky těchto revizí.

1. V Azure Portal otevřete okno kontroly **přístupu** .

1. Otevřete ovládací prvek a program, který jste vytvořili pro správu skupiny vyloučení.

1. Kliknutím na **výsledky** zjistíte, kdo byl schválen, aby zůstal v seznamu a kdo byl odebrán.

    ![Výsledky kontrol přístupu ukazují, kdo byl schválen](./media/conditional-access-exclusion/access-reviews-results.png)

1. Pak klikněte na **protokoly auditu** a podívejte se na akce, které byly během této revize podniknuty.

    ![Kontroly přístupu – protokoly auditu v seznamu akcí](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Jako správce IT víte, že správa skupin vyloučení do vašich zásad je někdy nevyhnutelná. Tyto skupiny se ale udržují pravidelně a vlastními obchodníky nebo uživateli sami a auditování těchto změn se může zjednodušit pomocí kontrol přístupu Azure AD.

## <a name="next-steps"></a>Další kroky

- [Vytvoření kontroly přístupu skupin nebo aplikací](create-access-review.md)
- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)
