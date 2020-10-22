---
title: Spravovat uživatele vyloučené ze zásad podmíněného přístupu – Azure AD
description: Naučte se používat kontroly přístupu Azure Active Directory (Azure AD) ke správě uživatelů, kteří byli vyloučení ze zásad podmíněného přístupu.
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
ms.date: 06/17/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4a18ad04064ef8356e21d106843d6c946f50741
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92362718"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Pomocí kontrol přístupu Azure AD Spravujte uživatele vyloučené ze zásad podmíněného přístupu.

V ideálním světě budou všichni uživatelé dodržovat zásady přístupu, aby zabezpečený přístup k prostředkům vaší organizace. Někdy ale existují obchodní případy, které vyžadují, abyste měli výjimky. Tento článek prochází několika příklady situací, kdy může být potřeba vyloučit. Vy, jako správce IT, může spravovat tuto úlohu, vyhýbat se nedohledům zásad a poskytovat auditorům kontrolu nad tím, že se tyto výjimky pravidelně přezkoumávají pomocí Azure Active Directory (Azure AD) kontroly přístupu.

>[!NOTE]
> K používání kontrol přístupu Azure AD se vyžaduje platná Azure AD Premium P2, Enterprise Mobility + Security E5 nebo zkušební licence. Další informace najdete v tématu [Azure Active Directory edice](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Proč byste měli vyloučit uživatele ze zásad?

Řekněme, že jako správce se rozhodnete použít [podmíněný přístup Azure AD](../conditional-access/concept-conditional-access-policy-common.md) k vyžádání vícefaktorového ověřování (MFA) a omezení žádostí o ověření na konkrétní sítě nebo zařízení. Během plánování nasazení si myslíte, že ne všichni uživatelé můžou tyto požadavky splnit. Můžete mít například uživatele, kteří pracují se vzdálenými pobočkami, nikoli součástí vaší interní sítě. Může být také nutné připojovat uživatele, kteří se připojují pomocí nepodporovaných zařízení při čekání na nahrazení těchto zařízení. V krátkém případě firmy potřebují tyto uživatele, aby se přihlásili a mohli svou úlohu vyřadit ze zásad podmíněného přístupu.

Dalším příkladem může být použití [pojmenovaných umístění](../conditional-access/location-condition.md) v podmíněném přístupu k určení sady zemí a oblastí, ze kterých nechcete uživatelům povolit přístup ke svému tenantovi.

![Pojmenovaná umístění v podmíněném přístupu](./media/conditional-access-exclusion/named-locations.png)

Někteří uživatelé bohužel můžou mít i nadále platný důvod pro přihlášení z těchto blokovaných zemí nebo oblastí. Uživatelé můžou být například na cestách pro práci a potřebují přístup k podnikovým prostředkům. V takovém případě zásada podmíněného přístupu zablokuje, že tyto země nebo oblasti můžou používat skupinu zabezpečení cloudu pro vyloučené uživatele ze zásad. Uživatelé, kteří potřebují přístup při cestování, se můžou do skupiny přidat pomocí [samoobslužné správy skupin Azure AD](../enterprise-users/groups-self-service-management.md).

Dalším příkladem může být, že máte zásady podmíněného přístupu [blokující starší verze ověřování pro většinu uživatelů](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/). Pokud ale máte nějaké uživatele, kteří potřebují používat starší metody ověřování pro přístup k prostředkům prostřednictvím klientů Office 2010 nebo IMAP/SMTP/na bázi POP, můžete tyto uživatele vyloučit ze zásad, které blokují starší metody ověřování.

>[!NOTE]
>Microsoft důrazně doporučuje, abyste v tenantovi zablokovali použití starších protokolů pro zlepšení stav zabezpečení.

## <a name="why-are-exclusions-challenging"></a>Proč jsou vyloučení náročné?

Ve službě Azure AD můžete nastavit obor zásad podmíněného přístupu na skupinu uživatelů. Vyloučení můžete taky nakonfigurovat tak, že vyberete role Azure AD, jednotlivé uživatele nebo hosty. Měli byste mít na paměti, že při konfiguraci vyloučení nelze záměr zásad vynutit u vyloučených uživatelů. Pokud jsou vyloučení nakonfigurovaná pomocí seznamu uživatelů nebo starší verze místních skupin zabezpečení, budete mít k vyloučení omezené přehledy. Výsledek:

- Uživatelé nemusí mít jistotu, že jsou vyloučeni.

- Uživatelé se mohou připojit ke skupině zabezpečení a obejít zásadu.

- Vyloučení uživatelé můžou být kvalifikováni pro vyloučení dřív, ale už se o ně nemůžou kvalifikovat.

Při první konfiguraci vyloučení se často shortlist uživatelům, kteří tyto zásady nepoužívají. V průběhu času se k vyloučení přidá více uživatelů a seznam se zvětší. V některých případech je třeba zkontrolovat seznam a ověřit, že každý z těchto uživatelů je stále způsobilý k vyloučení. Správa seznamu vyloučení z technického hlediska může být poměrně jednoduchá, ale v rozhodování o obchodních rozhodnutích a jak se ujistit, že je vše auditováno? Pokud ale konfigurujete vyloučení pomocí skupiny Azure AD, můžete použít kontroly přístupu jako kompenzační ovládací prvek, zvýšit viditelnost a snížit počet vyloučených uživatelů.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Postup vytvoření skupiny vyloučení v zásadách podmíněného přístupu

Pomocí těchto kroků můžete vytvořit novou skupinu Azure AD a zásadu podmíněného přístupu, která se na tuto skupinu nevztahuje.

### <a name="create-an-exclusion-group"></a>Vytvoření skupiny vyloučení

1. Přihlaste se k portálu Azure.

2. V levém navigačním panelu klikněte na **Azure Active Directory** a pak klikněte na **skupiny**.

3. V horní nabídce klikněte na **Nová skupina** a otevřete podokno skupiny.

4. V seznamu **typ skupiny** vyberte **zabezpečení**. Zadejte název a popis.

5. Nezapomeňte nastavit typ **členství** na **přiřazeno**.

6. Vyberte uživatele, kteří by měli být součástí této skupiny vyloučení, a pak klikněte na **vytvořit**.

![Nové podokno skupiny v Azure Active Directory](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Vytvoření zásady podmíněného přístupu, která vylučuje skupinu

Nyní můžete vytvořit zásadu podmíněného přístupu, která používá tuto skupinu vyloučení.

1. V levém navigačním panelu klikněte na **Azure Active Directory** a potom kliknutím na **podmíněný přístup** otevřete okno **zásady** .

2. Kliknutím na **Nová zásada** otevřete **nové** podokno.

3. Zadejte název.

4. V části přiřazení klikněte na **Uživatelé a skupiny**.

5. Na kartě **Zahrnout** vyberte **Všichni uživatelé**.

6. Na kartě **vyloučit** přidejte značku zaškrtnutí pro **uživatele a skupiny** a potom klikněte na **Vybrat vyloučené uživatele**.

7. Vyberte skupinu vyloučení, kterou jste vytvořili.

   > [!NOTE] 
   > V rámci osvědčeného postupu se doporučuje při testování vyloučit aspoň jeden účet správce ze zásady, abyste se ujistili, že se nezamknete ze svého tenanta.

8. Pokračujte v nastavování zásad podmíněného přístupu na základě požadavků vaší organizace.

![Vybrat podokno vyloučené uživatele v podmíněném přístupu](./media/conditional-access-exclusion/select-excluded-users.png)
  
Podíváme se na dva příklady, kde můžete pomocí kontrol přístupu spravovat vyloučení v zásadách podmíněného přístupu.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Příklad 1: kontrola přístupu pro uživatele, kteří přistupují z blokovaných zemí nebo oblastí

Řekněme, že máte zásady podmíněného přístupu, které blokují přístup z určitých zemí nebo oblastí. Zahrnuje skupinu, která je z těchto zásad vyloučená. Tady je doporučený kontroler přístupu, kde jsou členové skupiny zkontrolováni.

> [!NOTE] 
> K vytvoření kontrol přístupu je potřeba role globálního správce nebo Správce uživatelů.

1. K revizi dojde každý týden.

2. Nikdy se neukončí, abyste se ujistili, že udržujete tuto skupinu vyloučení nejaktuálnější.

3. Všichni členové této skupiny budou v oboru pro kontrolu.

4. Každý uživatel bude muset sami ověřit, že stále potřebují přístup z těchto blokovaných zemí nebo oblastí, takže stále musí být členem skupiny.

5. Pokud uživatel neodpoví na žádost o revizi, automaticky se odebere ze skupiny a nebude už mít přístup ke klientovi a přitom na cestách do těchto zemí nebo oblastí.

6. Povolte e-mailová oznámení, aby uživatelé věděli o zahájení a dokončení kontroly přístupu.

    ![Vytvoří podokno kontroly přístupu, například 1.](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Příklad 2: kontrola přístupu pro uživatele, kteří přistupují pomocí starší verze ověřování

Řekněme, že máte zásadu podmíněného přístupu, která uživatelům blokuje přístup pomocí starší verze ověřování a starších verzí klientů a zahrnuje skupinu, která je z těchto zásad vyloučená. Tady je doporučený kontroler přístupu, kde jsou členové skupiny zkontrolováni.

1. Tato recenze by musela být opakovaná kontrola.

2. Všichni členové skupiny by museli zkontrolovat.

3. Dá se nakonfigurovat tak, aby vybral vlastníky obchodních jednotek jako vybrané kontrolory.

4. Automaticky použít výsledky a odebrat uživatele, kteří nebyli schválením, aby mohli dál používat starší metody ověřování.

5. Může být výhodné povolit doporučení, aby kontroloři velkých skupin mohli snadno učinit svá rozhodnutí.

6. Povolit e-mailová oznámení, aby se uživatelům zobrazilo oznámení o zahájení a dokončení kontroly přístupu.

    ![Vytvoření podokna kontroly přístupu například 2](./media/conditional-access-exclusion/create-access-review-2.png)

>[!IMPORTANT] 
>Pokud máte mnoho skupin vyloučení a proto potřebujete vytvořit více kontrol přístupu, teď máme v koncovém bodu Microsoft Graph beta rozhraní API, které umožňuje jejich vytváření a správu prostřednictvím kódu programu. Informace o tom, jak začít, najdete v [referenčních informacích k rozhraní API kontroly přístupu Azure AD](/graph/api/resources/accessreviews-root?view=graph-rest-beta) a [příklad načítání kontrol přístupu služby azure AD prostřednictvím Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096).

## <a name="access-review-results-and-audit-logs"></a>Výsledky kontroly přístupu a protokoly auditu

Teď, když máte všechno na místě, skupinu, zásady podmíněného přístupu a kontroly přístupu, je čas sledovat a sledovat výsledky těchto revizí.

1. V Azure Portal otevřete okno kontroly **přístupu** .

2. Otevřete ovládací prvek a program, který jste vytvořili pro správu skupiny vyloučení.

3. Kliknutím na **výsledky** zjistíte, kdo byl schválen, aby zůstal v seznamu a kdo byl odebrán.

    ![Výsledky kontrol přístupu ukazují, kdo byl schválen](./media/conditional-access-exclusion/access-reviews-results.png)

4. Pak klikněte na **protokoly auditu** a podívejte se na akce, které byly během této revize podniknuty.

    ![Kontroly přístupu – protokoly auditu v seznamu akcí](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Jako správce IT víte, že správa skupin vyloučení do vašich zásad je někdy nevyhnutelná. Tyto skupiny si ale můžete pravidelně kontrolovat podle vlastního obchodního vlastníka nebo uživatelů sami a auditování těchto změn se dá zjednodušit pomocí kontrol přístupu Azure AD.

## <a name="next-steps"></a>Další kroky

- [Vytvoření kontroly přístupu skupin nebo aplikací](create-access-review.md)
- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)