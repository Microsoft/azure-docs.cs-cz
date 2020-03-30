---
title: Dokončení kontroly přístupu skupin & aplikací – Azure AD
description: Přečtěte si, jak dokončit kontrolu přístupu členů skupiny nebo přístupu k aplikacím v recenzích přístupu ke službě Azure Active Directory.
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
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 266234f2872cfe99509d564c9460bfba4a0e2bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932551"
---
# <a name="complete-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Dokončení kontroly přístupu skupin a aplikací v recenzích přístupu azure ad

Jako správce [vytvoříte kontrolu přístupu skupin nebo aplikací](create-access-review.md) a recenzenti [provádějí kontrolu přístupu](perform-access-review.md). Tento článek popisuje, jak zobrazit výsledky kontroly přístupu a použít výsledky.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Požadavky

- Azure AD Premium P2
- Globální správce, správce uživatele, správce zabezpečení nebo čtečka zabezpečení

Další informace naleznete v tématu [Licenční požadavky](access-reviews-overview.md#license-requirements).

## <a name="view-an-access-review"></a>Zobrazení kontroly přístupu

Průběh můžete sledovat v průběhu dokončení recenzí recenzentů.

1. Přihlaste se k portálu Azure a otevřete [stránku Zásad správného řízení identit](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. V levé nabídce klikněte na **Access reviews**.

1. V seznamu klikněte na kontrolu přístupu.

    Chcete-li zobrazit řadu kontrol přístupu, přejděte k recenzi přístupu a nadcházející výskyty najdete v naplánovaných recenzích.

    Na stránce **Přehled** uvidíte průběh. Dokud nebude kontrola dokončena, nebudou v adresáři změněna žádná přístupová práva.

    ![Průběh kontrol přístupu](./media/complete-access-review/overview-progress.png)

1. Pokud chcete zastavit kontrolu přístupu dříve, než dosáhne plánovaného koncového data, klikněte na tlačítko **Zastavit.**

    Po zastavení recenze již recenzenti nebudou moci přiznat odpovědi. Po zastavení recenze nelze recenzi restartovat.

1. Pokud už nemáte zájem o kontrolu přístupu, můžete ji odstranit kliknutím na tlačítko **Odstranit.**

## <a name="apply-the-changes"></a>Použití změn

Pokud bylo povoleno **automatické použití výsledků na prostředek** a na základě vašich výběrů v části Po dokončení **nastavení**, bude automatické použití provedeno po datu ukončení recenze nebo při ručním zastavení kontroly.

Pokud **automatické použití výsledků na prostředek** nebylo pro kontrolu povoleno, klikněte na **Použít** a ručně aplikujte změny. Pokud byl přístup uživatele odepřen v recenzi, po klepnutí na tlačítko **Použít**, Azure AD odebere jejich členství nebo přiřazení aplikace.

![Použití změn kontroly přístupu](./media/complete-access-review/apply-changes.png)

Stav revize se změní z **Dokončeno** do zprostředkujících stavů, jako je **použití** a nakonec do stavu **Výsledek použitý**. Měli byste očekávat, že odepření uživatelé, pokud existuje, jsou odebrány z členství ve skupině nebo přiřazení aplikace během několika minut.

Nakonfigurovaná automatická revize nebo **výběr** použít nemá vliv na skupinu, která pochází z místního adresáře nebo dynamické skupiny. Pokud chcete změnit skupinu, která pochází z místního prostředí, stáhněte výsledky a aplikujte tyto změny na reprezentaci skupiny v tomto adresáři.

## <a name="retrieve-the-results"></a>Načtení výsledků

Chcete-li zobrazit výsledky jednorázové kontroly přístupu, klikněte na stránku **Výsledky.** Chcete-li zobrazit pouze přístup uživatele, zadejte do pole Hledat zobrazované jméno nebo hlavní jméno uživatele, jehož přístup byl zkontrolován.

![Načtení výsledků pro kontrolu přístupu](./media/complete-access-review/retrieve-results.png)

Chcete-li zobrazit průběh opakované kontroly aktivního přístupu, klikněte na stránku **Výsledky.**

Chcete-li zobrazit výsledky dokončené instance opakované kontroly přístupu, klepněte na **položku Historie kontroly**a vyberte konkrétní instanci ze seznamu dokončených instancí kontroly přístupu na základě počátečního a koncového data instance. Výsledky této instance lze získat ze stránky **Výsledky.**

Chcete-li načíst všechny výsledky kontroly přístupu, klepněte na tlačítko **Stáhnout.** Výsledný soubor CSV lze zobrazit v aplikaci Excel nebo v jiných programech, které otevírají soubory CSV kódované u TF-8.

## <a name="remove-users-from-an-access-review"></a>Odebrání uživatelů z kontroly přístupu

 Ve výchozím nastavení zůstane odstraněný uživatel ve službě Azure Active Directory odstraněný na 30 dní, během kterých ho v případě potřeby může správce obnovit.  Po 30 dnech je tento uživatel odstraněn trvale.  Kromě toho může globální správce pomocí portálu Azure Active Directory explicitně [trvale odstranit nedávno odstraněného uživatele](../fundamentals/active-directory-users-restore.md) ještě před dosažením tohoto časového období.  Jakmile je uživatel trvale odstraněný, odeberou se následně data o tomto uživateli z aktivních kontrol přístupu.  Informace o auditování o odstraněných uživatelích zůstávají v protokolu auditu.

## <a name="next-steps"></a>Další kroky

- [Správa přístupu uživatelů pomocí kontrol přístupu Azure AD](manage-user-access-with-access-reviews.md)
- [Správa přístupu uživatelů typu host pomocí kontrol přístupu Azure AD](manage-guest-access-with-access-reviews.md)
- [Vytvoření kontroly přístupu skupin nebo aplikací](create-access-review.md)
- [Vytváření kontroly přístupu uživatelů v roli správce Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md)
