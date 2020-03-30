---
title: Správa uživatelů vyloučených ze zásad podmíněného přístupu – Azure AD
description: Zjistěte, jak používat kontroly přístupu k Přístupu služby Azure Active Directory (Azure AD) ke správě uživatelů, kteří byli vyloučeni ze zásad podmíněného přístupu.
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
ms.openlocfilehash: d1d7bce940f2b614c239e8b5e5719d96da10a6c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422713"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Použití kontrol přístupu azure ad ke správě uživatelů vyloučených ze zásad podmíněného přístupu

V ideálním světě by všichni uživatelé sledovali přístupovou policii, aby zabezpečili přístup k prostředkům vaší organizace. Někdy však existují obchodní případy, které vyžadují, abyste udělali výjimky. Tento článek popisuje některé příklady, kde může být vyžadováno vyloučení a jak můžete jako správce IT spravovat tento úkol, vyhnout se dohledu nad výjimkami zásad a poskytnout auditorům důkaz, že tyto výjimky jsou pravidelně kontrolovány pomocí Azure Nástroje active Directory (Azure AD) zkontrolovaly přístup.

> [!NOTE]
> K použití kontrol přístupu k Azure AD je vyžadována platná licence Azure AD Premium P2, Enterprise Mobility + Security E5 nebo zkušební licence. Další informace naleznete v [tématu Edice Služby Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Proč byste vyloučili uživatele ze zásad?

Jako správce IT můžete použít [podmíněný přístup Azure AD](../conditional-access/overview.md) k vyžadování, aby se uživatelé ověřovali pomocí vícefaktorového ověřování (MFA) nebo se přihlašovali z důvěryhodné sítě nebo zařízení. Během plánování nasazení zjistíte, že některé z těchto požadavků nemohou splnit všichni uživatelé. Existují například uživatelé, kteří pracují ze vzdálené kanceláře, která není součástí vaší interní sítě, nebo existuje vedoucí pracovník, který používá starý telefon, který není podporován. Firma vyžaduje, aby se tito uživatelé mohli přihlásit a dělat svou práci, proto jsou vyloučeni ze zásad podmíněného přístupu.

Jako další příklad můžete použít [pojmenované umístění](../conditional-access/location-condition.md) v podmíněném přístupu ke konfiguraci sady okresů a oblastí, ze kterých nechcete povolit uživatelům přístup k jejich tenantovi.

![Pojmenovaná umístění v podmíněném přístupu](./media/conditional-access-exclusion/named-locations.png)

V některých případech však mohou mít uživatelé legitimní důvod k přihlášení z těchto blokovaných zemí nebo oblastí. Uživatelé mohou například cestovat z pracovních nebo osobních důvodů. V tomto příkladu zásady podmíněného přístupu blokovat tyto země nebo oblasti může mít vyhrazené skupiny zabezpečení cloudu pro uživatele, kteří jsou vyloučeni ze zásad. Uživatelé, kteří potřebují přístup na cestách, se můžou přidat do skupiny pomocí [samoobslužné správy skupin azure ad](../users-groups-roles/groups-self-service-management.md).

Dalším příkladem může být, že máte zásadu podmíněného přístupu, která [blokuje starší verze ověřování pro drtivou většinu uživatelů](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/). Společnost Microsoft důrazně doporučuje blokovat používání starších protokolů ve vašem tenantovi ke zlepšení stavu zabezpečení. Pokud však máte některé uživatele, kteří absolutně potřebují používat starší metody ověřování pro přístup k prostředkům prostřednictvím klientů založených na Office 2010 nebo IMAP/SMTP/POP, můžete tyto uživatele vyloučit ze zásad, které blokují starší metody ověřování.

## <a name="why-are-exclusions-challenging"></a>Proč jsou vyloučení náročná?

Ve službě Azure AD můžete obor zásady podmíněného přístupu pro sadu uživatelů. Některé z těchto uživatelů můžete také vyloučit výběrem rolí Azure AD, jednotlivých uživatelů nebo hostů uživatelů. Je důležité si uvědomit, že při konfiguraci těchto vyloučení, záměr zásad nelze vynutit pro tyto uživatele. Pokud byla tato vyloučení nakonfigurována jako seznam jednotlivých uživatelů nebo prostřednictvím starší místní skupiny zabezpečení, omezuje viditelnost tohoto seznamu vyloučení (uživatelé nemusí vědět o jeho existenci) a kontrolu správce IT (uživatelé se mohou připojit k bezpečnostní skupiny, aby se tato zásada neobeštla). Uživatelé, kteří se kvalifikovali pro vyloučení najednou, ji navíc již nemusí potřebovat nebo na něj mohou mít nárok.

Na začátku vyloučení je krátký seznam uživatelů, kteří obcházejí zásady. V průběhu času je stále více uživatelů vyloučeno a seznam roste. V určitém okamžiku je třeba seznam zkontrolovat a potvrdit, že každý z těchto uživatelů by měl být stále vyloučen. Správa seznamu z technického hlediska může být poměrně snadná, ale kdo dělá obchodní rozhodnutí a jak se ujistíte, že je vše auditovatelné?

Pokud však nakonfigurujete vyloučení zásad y podmíněného přístupu pomocí skupiny Azure AD, můžete použít kontroly přístupu jako kompenzační řízení, zvýšit viditelnost a snížit počet uživatelů, kteří mají výjimku.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Jak vytvořit skupinu vyloučení v zásadách podmíněného přístupu

Podle těchto kroků vytvořte novou skupinu Azure AD a zásady podmíněného přístupu, které se na tuto skupinu nevztahují.

### <a name="create-an-exclusion-group"></a>Vytvoření skupiny vyloučení

1. Přihlaste se k portálu Azure.

1. V levém navigačním panelu klikněte na **Azure Active Directory** a potom klikněte na **Skupiny**.

1. V horní nabídce klikněte na **Nová skupina** a otevřete podokno skupiny.

1. V seznamu **Typ skupiny** vyberte **Možnost Zabezpečení**. Zadejte název a popis.

1. Nezapomeňte nastavit typ **členství** na **Přiřazeno**.

1. Vyberte uživatele, kteří by měli být součástí této skupiny vyloučení, a klepněte na tlačítko **Vytvořit**.

    ![Podokno Nové skupiny ve službě Azure Active Directory](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Vytvoření zásady podmíněného přístupu, která vylučuje skupinu

Nyní můžete vytvořit zásadu podmíněného přístupu, která používá tuto skupinu vyloučení.

1. V levém navigačním panelu klikněte na **Azure Active Directory** a potom kliknutím na Podmíněný **přístup** otevřete okno **Zásady.**

1. Kliknutím na **Nová zásada** otevřete **podokno Nový.**

1. Zadejte název.

1. V části Přiřazení klikněte na **Uživatelé a skupiny**.

1. Na kartě **Zahrnout** vyberte **Všichni uživatelé**.

1. Na kartě **Vyloučit** přidejte zaškrtnutí **do skupin Uživatelů a skupin a** klikněte na Vybrat **vyloučené uživatele**.

1. Vyberte skupinu vyloučení, kterou jste vytvořili.

    > [!NOTE]
    > Jako osvědčený postup doporučujeme vyloučit alespoň jeden účet správce ze zásad při testování, abyste se ujistili, že nejste uzamčeni z vašeho tenanta.

1. Pokračujte v nastavování zásad podmíněného přístupu na základě vašich organizačních požadavků.

    ![Podokno Vybrat vyloučené uživatele v podmíněném přístupu](./media/conditional-access-exclusion/select-excluded-users.png)

Podívejme se na dva příklady, kde můžete pomocí kontrol přístupu spravovat vyloučení v zásadách podmíněného přístupu.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Příklad 1: Kontrola přístupu pro uživatele, kteří přistupují z blokovaných zemí nebo oblastí

Řekněme, že máte zásady podmíněného přístupu, které blokují přístup z určitých zemí nebo oblastí. Zahrnuje skupinu, která je vyloučena ze zásady. Zde je doporučená kontrola přístupu, kde jsou členové skupiny kontrolováni.

> [!NOTE]
> K vytvoření kontrol y přístupu je vyžadována role globálního správce nebo správce uživatele.

1. Přezkum se bude opakovat každý týden.

2. Nikdy neskončí, abyste se ujistili, že udržujete tuto skupinu vyloučení nejaktuálnější.

3. Všichni členové této skupiny budou mít prostor pro přezkum.

4. Každý uživatel bude muset sami dosvědčit, že stále potřebují přístup z těchto blokovaných zemí nebo oblastí, a proto musí být stále členem skupiny.

5. Pokud uživatel neodpoví na žádost o kontrolu, bude automaticky odebrán ze skupiny, a proto již nebude mít přístup k tenantovi při cestování do těchto zemí nebo oblastí.

6. Povolte e-mailová oznámení, aby byli uživatelé upozorněni na zahájení a dokončení kontroly přístupu.

    ![Vytvoření podokna kontroly přístupu například 1](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Příklad 2: Kontrola přístupu pro uživatele, kteří přistupují ke staršímu ověřování

Řekněme, že máte zásadu podmíněného přístupu, která blokuje přístup pro uživatele pomocí staršíverze ověřování a starší chod klienta. Zahrnuje skupinu, která je vyloučena ze zásady. Zde je doporučená kontrola přístupu, kde jsou členové skupiny kontrolováni.

1. Tato recenze by musela být opakovanou recenzí.

2. Všichni ve skupině by museli být přezkoumáni.

3. Může být nakonfigurován tak, aby seznam vlastníků organizační jednotky jako vybraných recenzentů.

4. Automaticky použijte výsledky a odeberte uživatele, kteří nebyli schváleni, aby mohli nadále používat starší metody ověřování.

5. Může být výhodné povolit doporučení, aby se recenzenti velkých skupin mohli snadno rozhodovat.

6. Povolte e-mailová oznámení, aby byli uživatelé upozorněni na zahájení a dokončení kontroly přístupu.

    ![Vytvoření podokna kontroly přístupu například 2](./media/conditional-access-exclusion/create-access-review-2.png)

**Pro Tip:** Pokud máte mnoho skupin vyloučení, a proto potřebujete vytvořit více kontrol přístupu, máme nyní rozhraní API v beta koncovém bodě Microsoft Graph, který vám umožní vytvářet a spravovat je programově. Pokud chcete začít, přečtěte si [odkaz na rozhraní API pro kontroly přístupu azure a](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root) [například načítání recenzí přístupu k Azure AD prostřednictvím Microsoft Graphu](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096).

## <a name="access-review-results-and-audit-logs"></a>Přístup k výsledkům kontroly a protokolům auditu

Teď, když máte vše na svém místě, seskupí, zásady podmíněného přístupu a kontroly přístupu, je čas sledovat a sledovat výsledky těchto recenzí.

1. Na webu Azure Portal otevřete okno **Access recenze.**

1. Otevřete ovládací prvek a program, který jste vytvořili pro správu skupiny vyloučení.

1. Kliknutím na **Výsledky** zobrazíte, kdo byl schválen k tomu, aby zůstal v seznamu a kdo byl odebrán.

    ![Výsledky kontrol přístupu ukazují, kdo byl schválen](./media/conditional-access-exclusion/access-reviews-results.png)

1. Potom klikněte na **protokoly auditu** a podívejte se na akce, které byly během této kontroly podniknuty.

    ![Aplikace Access kontroluje protokoly auditu se seznamem akcí](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Jako správce IT víte, že správa skupin vyloučení do zásad je někdy nevyhnutelná. Však udržování těchto skupin, jejich pravidelné kontroly vlastníkem firmy nebo samotnými uživateli a auditování těchto změn může usnadnit s azure ad kontroly přístupu.

## <a name="next-steps"></a>Další kroky

- [Vytvoření kontroly přístupu skupin nebo aplikací](create-access-review.md)
- [Co je podmíněný přístup ve službě Azure Active Directory?](../conditional-access/overview.md)
