---
title: Načtení výsledků kontroly přístupu k Azure AD | Microsoft Docs
description: Přečtěte si, jak načíst výsledky kontrol přístupu do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: markwahl-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.component: compliance-reports
ms.date: 06/21/2018
ms.author: rolyon
ms.openlocfilehash: a2d356afd33ddd2e641c6f9bc028f235623b448b
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37024685"
---
# <a name="retrieve-access-review-results"></a>Načtení výsledků kontroly přístupu

Správci můžou pomocí Azure Active Directory (Azure AD) [vytvořit kontrolu přístupu](active-directory-azure-ad-controls-create-access-review.md) pro členy skupiny nebo uživatele přiřazené k aplikaci.  Uživatel, který je v roli **Globální správce**, **Správce uživatelských účtů**, **Správce zabezpečení** nebo **Čtenář zabezpečení**, může také číst výsledky kontroly přístupu.  Pokud chcete uživatele přiřadit k jedné z těchto rolí, může Správce privilegovaných rolí pomocí nástroje Azure Active Directory Privileged Identity Management nastavit u uživatele nárok na aktivaci role, nebo Globální správce může trvale [přiřadit uživatele k roli](fundamentals/active-directory-users-assign-role-azure-portal.md).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="locating-an-access-review"></a>Vyhledání kontroly přístupu

Pokud víte, který program obsahuje kontrolu přístupu, přejděte na stránku kontroly přístupu, vyberte **Programy** a vyberte program, který obsahuje ovládací prvek pro kontrolu přístupu.  Potom vyberte **Ovládací prvky** a vyberte ovládací prvek kontroly přístupu. Pokud v programu existuje mnoho ovládacích prvků, můžete vyfiltrovat ovládací prvky konkrétního typu a seřadit je podle jejich stavu. Můžete také hledat podle názvu ovládacího prvku kontroly přístupu nebo zobrazovaného názvu vlastníka, který ho vytvořil. 

Pokud nevíte, který program obsahuje kontrolu přístupu, přejděte na stránku kontrol přístupu a vyberte **Ovládací prvky**.  Můžete vyfiltrovat ovládací prvky konkrétního typu a seřadit je podle jejich stavu a také můžete hledat podle názvu ovládacího prvku kontroly přístupu nebo zobrazovaného názvu vlastníka, který ho vytvořil. 

## <a name="retrieving-the-results-for-a-one-time-access-review"></a>Načítání výsledků pro jednorázovou kontrolu přístupu

Pokud typ opakování kontroly je jednou, potom se průběh aktivní kontroly přístupu a výsledky dokončené kontroly přístupu dají získat v oddíle **Výsledky**.  Můžete zadat zobrazovaný název uživatele nebo hlavní název uživatele (UPN), jehož přístup se kontroluje, a zobrazit tak jenom přístup tohoto uživatele.  Pokud chcete načíst všechny výsledky dokončené kontroly přístupu, klikněte na tlačítko **Stáhnout**.

## <a name="retrieving-the-results-for-multiple-instances-of-a-recurring-access-review"></a>Načítání výsledků pro více instancí opakované kontroly přístupu

Pokud chcete zobrazit průběh aktivní kontroly přístupu, která se opakuje, klikněte na oddíl **Výsledky**.  Můžete zadat zobrazovaný název uživatele nebo hlavní název uživatele (UPN), jehož přístup se kontroluje.

Pokud chcete zobrazit výsledky dokončené instance kontroly přístupu, která se opakuje, vyberte **Historie kontrol** a potom vyberte konkrétní instanci ze seznamu dokončených instancí kontrol přístupu podle počátečního a koncového data instance.   Výsledky této instance jsou dostupné v oddíle **Výsledky**.  Můžete zadat zobrazovaný název uživatele nebo hlavní název uživatele (UPN), jehož přístup se kontroluje, a zobrazit tak jenom přístup tohoto uživatele.  Pokud chcete načíst všechny výsledky dokončené instance opakující se kontroly přístupu, klikněte na tlačítko **Stáhnout**.


## <a name="removing-users-from-an-access-review"></a>Odebírání uživatelů z kontroly přístupu

Ve výchozím nastavení zůstane odstraněný uživatel ve službě Azure Active Directory odstraněný na 30 dní, během kterých ho v případě potřeby může správce obnovit.  Po 30 dnech je tento uživatel odstraněn trvale.  Kromě toho může globální správce pomocí portálu Azure Active Directory explicitně [trvale odstranit nedávno odstraněného uživatele](fundamentals/active-directory-users-restore.md) ještě před dosažením tohoto časového období.  Jakmile je uživatel trvale odstraněný, odeberou se následně data o tomto uživateli z aktivních kontrol přístupu.  Informace o auditování o odstraněných uživatelích zůstávají v protokolu auditu.

## <a name="next-steps"></a>Další kroky

- [Správa přístupu uživatelů pomocí kontrol přístupu Azure AD](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
- [Správa přístupu uživatelů typu host pomocí kontrol přístupu Azure AD](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
- [Správa programů a ovládacích prvků pro kontroly přístupu Azure AD](active-directory-azure-ad-controls-manage-programs-controls.md)
- [Vytváření kontroly přístupu pro členy skupiny nebo přístupu k aplikaci](active-directory-azure-ad-controls-create-access-review.md)
- [Vytváření kontroly přístupu uživatelů v roli správce Azure AD](active-directory-privileged-identity-management-how-to-start-security-review.md)


