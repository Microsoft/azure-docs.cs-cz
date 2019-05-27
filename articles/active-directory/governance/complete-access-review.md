---
title: Dokončení kontroly přístupu skupiny nebo aplikace – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak dokončení kontroly přístupu členů skupiny nebo přístupu k aplikacím v kontrol přístupu Azure Active Directory.
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
ms.date: 05/22/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: bae204ec1789f227150adc560d4a292404d23b7e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66113310"
---
# <a name="complete-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>Dokončení kontroly přístupu skupin nebo kontrol přístupu aplikací ve službě Azure AD

Jako správce můžete [vytvoření kontroly přístupu skupinám nebo aplikacím](create-access-review.md) a revidující [provádění kontroly přístupu](perform-access-review.md). Tento článek popisuje postup zobrazení výsledků kontroly přístupu a použití výsledky.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Požadavky

- Azure AD Premium P2
- Globální správce, Správce uživatelů, správce zabezpečení nebo Čtenář zabezpečení

Další informace najdete v tématu [kteří uživatelé musí mít licence?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="view-an-access-review"></a>Zobrazit kontroly přístupu

Průběh můžete sledovat, jak se revidující dokončení jejich revize.

1. Přihlaste se k Azure portal a otevřete [stránku kontrol přístupu](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. V nabídce vlevo klikněte na tlačítko **kontrol přístupu**.

1. V seznamu klikněte na možnost kontroly přístupu.

    Chcete-li zobrazit řadu přístup kontroly, přejděte na kontrolu přístupu a nadcházející události zjistíte v naplánované kontroly.

    Na **přehled** stránky, zobrazí se průběh. Žádné přístupová práva jsou změnit v adresáři, dokud se kontrola dokončí.

    ![Průběh kontroly přístupu](./media/complete-access-review/overview-progress.png)

1. Pokud chcete zastavit kontrolu přístupu před dosažením naplánované koncové datum, klikněte na tlačítko **Zastavit** tlačítko.

    Při zastavení kontrolu, revidující nebude moct poskytovat odpovědi. Kontrola nelze restartovat po je zastaven.

1. Pokud už máte zájem kontroly přístupu, můžete ji odstranit kliknutím **odstranit** tlačítko.

## <a name="apply-the-changes"></a>Použít změny

Pokud **automaticky zavést výsledky do prostředku** byla povolena a na základě vašeho výběru v **nastavení činností po dokončení**, automaticky – použít se bude provedena po koncové datum kontrola nebo když ručně zastavte kontrolu.

Pokud **automaticky zavést výsledky do prostředku** nebyla povolena pro revizi, klepněte na tlačítko **použít** ručně změny se projeví. Pokud byl odepřen přístup uživatele při kontrole, po kliknutí na **použít**, odebere jejich přiřazení členství nebo aplikace Azure AD.

![Použít změny vyplývající z revize přístupu](./media/complete-access-review/apply-changes.png)

Stav kontroly se změní z **dokončeno** prostřednictvím průběžných stavů například **použití** a nakonec do stavu **použitý výsledek**. Měli byste očekávat zobrazíte zamítnutým uživatelům, pokud existuje, odebírán z přiřazení skupiny členství nebo aplikaci za několik minut.

Nakonfigurované automatické použití kontroly, nebo jeho výběru **použít** nemá vliv na skupinu, která pochází z místního adresáře nebo dynamické skupiny. Pokud chcete změnit skupinu, která mohou být místní, stáhněte si výsledky a tyto změny použít k reprezentaci dané skupiny v tomto adresáři.

## <a name="retrieve-the-results"></a>Načtení výsledků

Chcete-li zobrazit výsledky kontroly jednorázové přístupu, klikněte na tlačítko **výsledky** stránky. Chcete-li zobrazit pouze přístup uživatelů, do vyhledávacího pole, zadejte zobrazované jméno nebo hlavní název uživatele pro uživatele byl recenzován má přístup.

![Načíst výsledky kontroly přístupu](./media/complete-access-review/retrieve-results.png)

Chcete-li zobrazit průběh kontroly aktivní přístupu, který se opakuje, klikněte na **výsledky** stránky.

Chcete-li zobrazit výsledky dokončenou instanci sady kontroly přístupu, který se opakuje, klikněte na tlačítko **kontrola historie**a pak vyberte konkrétní instanci ze seznamu instance kontrol přístupu dokončené, podle instance počáteční a koncové datum. Můžete získat výsledky této instance **výsledky** stránky.

Pokud chcete načíst všechny výsledky kontroly přístupu, klikněte na tlačítko **Stáhnout** tlačítko. Výsledný soubor CSV lze je zobrazit v Excelu nebo v jiných aplikacích, které se otevřou UTF-8 kódování souborů CSV.

## <a name="remove-users-from-an-access-review"></a>Odebrat uživatele z kontroly přístupu

 Ve výchozím nastavení zůstane odstraněný uživatel ve službě Azure Active Directory odstraněný na 30 dní, během kterých ho v případě potřeby může správce obnovit.  Po 30 dnech je tento uživatel odstraněn trvale.  Kromě toho může globální správce pomocí portálu Azure Active Directory explicitně [trvale odstranit nedávno odstraněného uživatele](../fundamentals/active-directory-users-restore.md) ještě před dosažením tohoto časového období.  Jakmile je uživatel trvale odstraněný, odeberou se následně data o tomto uživateli z aktivních kontrol přístupu.  Informace o auditování o odstraněných uživatelích zůstávají v protokolu auditu.

## <a name="next-steps"></a>Další postup

- [Správa přístupu uživatelů pomocí kontrol přístupu Azure AD](manage-user-access-with-access-reviews.md)
- [Správa přístupu uživatelů typu host pomocí kontrol přístupu Azure AD](manage-guest-access-with-access-reviews.md)
- [Vytvoření kontroly přístupu skupiny nebo aplikace](create-access-review.md)
- [Vytváření kontroly přístupu uživatelů v roli správce Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md)
