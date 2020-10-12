---
title: Dokončení kontroly přístupu skupin & aplikací – Azure AD
description: Naučte se, jak dokončit kontrolu přístupu pro členy skupiny nebo přístup k aplikacím v Azure Active Directory kontroly přístupu.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23266043d5efd65a4e6e79dd515a54a2c321b077
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798830"
---
# <a name="complete-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Dokončení kontroly přístupu skupin a aplikací v prohlídekch Azure AD Access

Jako správce [vytvoříte kontrolu přístupu pro skupiny nebo aplikace](create-access-review.md) a kontrolory, kteří [provádějí kontrolu přístupu](perform-access-review.md). Tento článek popisuje, jak zobrazit výsledky kontroly přístupu a použít výsledky.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Požadavky

- Azure AD Premium P2
- Globální správce, Správce uživatelů, správce zabezpečení nebo čtecí modul zabezpečení

Další informace najdete v tématu [licenční požadavky](access-reviews-overview.md#license-requirements).

## <a name="view-an-access-review"></a>Zobrazit kontrolu přístupu

Můžete sledovat průběh, když kontroloři dokončí revize.

1. Přihlaste se k Azure Portal a otevřete [stránku zásad správného řízení identity](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. V nabídce vlevo klikněte na **recenze přístupů**.

1. V seznamu klikněte na kontrolu přístupu.

    Pokud chcete zobrazit řadu kontrol přístupu, přejděte na kontrolu přístupu a v části naplánované recenze najdete nadcházející výskyty.

    Na stránce **Přehled** můžete zobrazit průběh. V adresáři se nezměnila žádná přístupová práva, dokud se kontrola nedokončí.

    ![Průběh kontrol přístupu](./media/complete-access-review/overview-progress.png)

1. Chcete-li před dosažením naplánovaného koncového data zastavit kontrolu přístupu, klikněte na tlačítko **zastavit** .

    Po zastavení recenze již recenzenti nebude moci poskytnout odpovědi. Po zastavení nemůžete kontrolu znovu spustit.

1. Pokud již nejste zajímat kontrolu přístupu, můžete ji odstranit kliknutím na tlačítko **Odstranit** .

## <a name="apply-the-changes"></a>Použít změny

Pokud je možnost **automaticky použít výsledky do prostředku** zapnutá a na základě vašich výběrů v **nastavení po dokončení**, provede se automatické použití po datu ukončení revize nebo při ručním zastavení kontroly.

Pokud se u prostředku pro kontrolu nepovolilo **automatické použití výsledků** , klikněte na **použít** , aby se změny projevily ručně. Pokud se k přístupu uživatele v recenzi odepře přístup, po kliknutí na **použít**Azure AD odebere přiřazení členství nebo aplikací.

![Použití změn kontroly přístupu](./media/complete-access-review/apply-changes.png)

Stav revize se změní ze **dokončených** do mezistavů, jako je **použití** a nakonec na **výsledek použití**. Měli byste očekávat, že v několika minutách se v případě potřeby odeberou Zakázaní uživatelé z členství ve skupině nebo přiřazení aplikace.

Nakonfigurované automatické použití revize nebo volba **použít** nemá vliv na skupinu, která pochází z místního adresáře nebo dynamické skupiny. Pokud chcete změnit skupinu, která pochází z místního prostředí, Stáhněte výsledky a použijte tyto změny v zastoupení skupiny v tomto adresáři.

## <a name="retrieve-the-results"></a>Načtení výsledků

Chcete-li zobrazit výsledky pro jednorázovou kontrolu přístupu, klikněte na stránku **výsledky** . Chcete-li zobrazit přístup pouze uživatele, zadejte do vyhledávacího pole název zobrazení nebo hlavní název uživatele, jehož přístup byl zkontrolován.

![Načtení výsledků pro kontrolu přístupu](./media/complete-access-review/retrieve-results.png)

Pokud chcete zobrazit průběh aktivní kontroly přístupu, klikněte na stránku **výsledky** .

Chcete-li zobrazit výsledky dokončené instance kontroly přístupu, klikněte na tlačítko **zkontrolovat historii**a pak vyberte konkrétní instanci ze seznamu dokončených instancí kontroly přístupu na základě počátečního a koncového data instance. Výsledky této instance lze získat ze stránky **výsledků** .

Chcete-li načíst všechny výsledky kontroly přístupu, klikněte na tlačítko **Stáhnout** . Výsledný soubor CSV se dá zobrazit v Excelu nebo v jiných programech, které otevřou soubory CSV s kódováním UTF-8.

## <a name="remove-users-from-an-access-review"></a>Odebrání uživatelů z kontroly přístupu

 Ve výchozím nastavení zůstane odstraněný uživatel ve službě Azure Active Directory odstraněný na 30 dní, během kterých ho v případě potřeby může správce obnovit.  Po 30 dnech je tento uživatel odstraněn trvale.  Kromě toho může globální správce pomocí portálu Azure Active Directory explicitně [trvale odstranit nedávno odstraněného uživatele](../fundamentals/active-directory-users-restore.md) ještě před dosažením tohoto časového období.  Jakmile je uživatel trvale odstraněný, odeberou se následně data o tomto uživateli z aktivních kontrol přístupu.  Informace o auditování o odstraněných uživatelích zůstávají v protokolu auditu.

## <a name="next-steps"></a>Další kroky

- [Správa přístupu uživatelů pomocí kontrol přístupu Azure AD](manage-user-access-with-access-reviews.md)
- [Správa přístupu uživatelů typu host pomocí kontrol přístupu Azure AD](manage-guest-access-with-access-reviews.md)
- [Vytvoření kontroly přístupu skupin nebo aplikací](create-access-review.md)
- [Vytváření kontroly přístupu uživatelů v roli správce Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md)
