---
title: Nastavení samoobslužné správy skupin – Azure Active Directory | Microsoft Docs
description: Vytváření a Správa skupin zabezpečení nebo skupin Microsoft 365 v Azure Active Directory a vyžádání skupiny zabezpečení nebo Microsoft 365 členství ve skupinách
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 08/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6425809cfa6dde20b8ef2b1f6d4cfaf04cbef6e6
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647082"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Nastavení samoobslužné správy skupin v Azure Active Directory 

Uživatelům můžete povolit vytváření a správu vlastních skupin zabezpečení nebo skupin Microsoft 365 v Azure Active Directory (Azure AD). Vlastník skupiny může schvalovat nebo zamítnout žádosti o členství a může delegovat řízení členství ve skupině. Funkce Samoobslužné správy skupin nejsou k dispozici pro skupiny zabezpečení s povoleným e-mailem ani pro distribuční seznamy.

## <a name="self-service-group-membership-defaults"></a>Výchozí nastavení členství ve skupině samoobslužných služeb

Když se v Azure Portal nebo pomocí Azure AD PowerShellu vytvoří skupiny zabezpečení, můžou členství aktualizovat jenom vlastníci skupiny. Skupiny zabezpečení vytvořené samoobslužnými službami na [přístupovém panelu](https://account.activedirectory.windowsazure.com/r#/joinGroups) a všechny Microsoft 365 skupiny jsou k dispozici pro připojení pro všechny uživatele, ať už bylo schváleno nebo automaticky schváleno. Na přístupovém panelu můžete změnit možnosti členství při vytváření skupiny.

Skupiny vytvořené v | Výchozí chování skupiny zabezpečení | Výchozí chování skupiny Microsoft 365
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](../enterprise-users/groups-settings-cmdlets.md) | Členy můžou přidávat jenom vlastníci.<br>Viditelná, ale není dostupná pro připojení na přístupovém panelu | Otevřít pro připojení pro všechny uživatele
[Azure Portal](https://portal.azure.com) | Členy můžou přidávat jenom vlastníci.<br>Viditelná, ale není dostupná pro připojení na přístupovém panelu<br>Vlastník není automaticky přiřazen při vytváření skupiny. | Otevřít pro připojení pro všechny uživatele
[Přístupový panel](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Otevřít pro připojení pro všechny uživatele<br>Možnosti členství lze změnit při vytvoření skupiny. | Otevřít pro připojení pro všechny uživatele<br>Možnosti členství lze změnit při vytvoření skupiny.

## <a name="self-service-group-management-scenarios"></a>Scénáře správy skupin samoobslužných služeb

* **Delegovaná správa skupin** – příkladem je správce, který spravuje přístup k aplikaci SaaS, kterou používá jeho společnost. Správa těchto přístupových práv je čím dál náročnější, takže správce požádá majitele firmy, aby vytvořil novou skupinu. Správce přiřadí aplikaci k nové skupině a přidá je do skupiny všichni lidé, kteří už přistupují k aplikaci. Majitel firmy potom může přidat další uživatele a tito uživatelé budou automaticky přiřazeni k aplikaci. Kvůli správě přístupu pro uživatele tak majitel firmy nemusí čekat na správce. Pokud správce udělí stejné oprávnění správci v jiné obchodní skupině, pak může tato osoba také spravovat přístup vlastních členů skupiny. Obchodní vlastník ani správce nemůže zobrazit ani spravovat členství ve skupině ostatních. Správce může stále vidět všechny uživatele, kteří mají přístup k aplikaci, a v případě potřeby je může zablokovat.
* **Samoobslužná správa skupin** – příkladem tohoto scénáře jsou dva uživatelé, kteří mají nezávisle nastavené weby SharePoint Online. Chtějí svým týmům udělit přístup ke svým webům. Aby toho dosáhli, můžou vytvořit jednu skupinu v Azure AD a potom v SharePointu Online každý z nich vybere tu skupinu, které chce poskytnout přístup na svůj web. Pokud chce uživatel získat přístup, požádá o něj na přístupovém panelu a po schválení získá přístup k oběma webům SharePoint Online automaticky. Později se jeden z nich rozhodne, že všichni uživatelé s přístupem k webu by měli získat přístup i k určité aplikaci SaaS. Správce aplikace SaaS může přidat přístupová práva k aplikacím na web SharePoint Online. Od toho okamžiku získají veškeré jím schválené žádosti přístup na oba weby SharePoint Online a také k této aplikaci SaaS.

## <a name="make-a-group-available-for-user-self-service"></a>Zpřístupnění skupiny pro uživatelskou samoobsluhu

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který má k adresáři oprávnění globálního správce.
1. Vyberte **skupiny** a pak vyberte **Obecné** nastavení.
1. Možnost nastavit **vlastníky mohou spravovat požadavky na členství ve skupinách na přístupovém panelu** na **Ano**.
1. Nastavte **omezení přístupu ke skupinám na přístupovém panelu** na **ne**.
1. Pokud nastavíte **možnost Uživatelé můžou vytvářet skupiny zabezpečení na portálech Azure** nebo **Uživatelé můžou na portálech azure vytvářet skupiny Microsoft 365** na

    - **Ano**: všichni uživatelé v organizaci Azure AD můžou vytvářet nové skupiny zabezpečení a přidávat do těchto skupin členy. Tyto nové skupiny se zobrazí také všem ostatním uživatelům na přístupovém panelu. Pokud to nastavení zásad skupiny umožňuje, můžou jiní uživatelé vytvářet žádosti o připojení k těmto skupinám.
    - **Ne**: uživatelé nemůžou vytvářet skupiny a nemůžou měnit existující skupiny, pro které jsou vlastníci. Můžou však stále spravovat členství v těchto skupinách a schvalovat žádosti o členství od jiných uživatelů.

Můžete také použít **vlastníky, kteří můžou členům přiřadit členy jako vlastníci skupiny v Azure Portal** a dosáhnout tak přesnější řízení přístupu pro uživatele samoobslužné správy skupin.

Když uživatelé můžou vytvářet skupiny, všichni uživatelé ve vaší organizaci můžou vytvářet nové skupiny a potom můžou jako výchozí vlastník přidávat členy do těchto skupin. Nemůžete určit jednotlivce, kteří můžou vytvářet své vlastní skupiny. Můžete určit jednotlivce jenom pro případ, že bude mít jiný člen skupiny jako vlastník skupiny.

> [!NOTE]
> Pro uživatele, aby požádala o připojení ke skupině zabezpečení nebo Microsoft 365 skupině a aby vlastníci mohli schvalovat nebo odmítat žádosti o členství, se vyžaduje licence Azure Active Directory Premium (P1 nebo P2). Bez licence Azure Active Directory Premium můžou uživatelé dál spravovat své skupiny na přístupovém panelu, ale nemůžou vytvořit skupinu, která vyžaduje schválení vlastníka na přístupovém panelu, a nemůže požádat o připojení ke skupině.

## <a name="next-steps"></a>Další kroky

Následující články poskytují další informace o službě Azure Active Directory.

* [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Rutiny Azure Active Directory pro konfiguraci nastavení skupiny](../enterprise-users/groups-settings-cmdlets.md)
* [Správa aplikací ve službě Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Co je Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrace místních identit do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
