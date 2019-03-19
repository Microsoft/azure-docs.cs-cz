---
title: Nastavení samoobslužné správy skupin – Azure Active Directory | Dokumentace Microsoftu
description: Vytváření a správa skupin zabezpečení nebo skupin Office 365 ve službě Azure Active Directory a žádosti o členství ve skupině zabezpečení nebo skupině Office 365
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5122c9142eccf12193e7e429a3af5ac44fbb8fd6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58111323"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Nastavení samoobslužné správy skupin v Azure Active Directory 

Můžete povolit uživatelům vytvářet a spravovat své vlastní skupiny zabezpečení nebo skupiny Office 365 ve službě Azure Active Directory (Azure AD). Vlastník skupiny můžete schválit nebo odmítnout žádosti o členství a můžete delegovat řízení členství ve skupině. Funkce Samoobslužné skupiny správy nejsou dostupné pro skupiny zabezpečení s povoleným e-mailem a distribuční seznamy. 

## <a name="self-service-group-membership-defaults"></a>Výchozí nastavení samoobslužné skupiny členství

Když se vytvoří skupiny zabezpečení na webu Azure Portal nebo pomocí prostředí PowerShell Azure AD, můžete pouze vlastníci skupiny aktualizovat členství. Skupiny zabezpečení vytvořené v [přístupového panelu](https://account.activedirectory.windowsazure.com/r#/joinGroups) a všechny skupiny Office 365 jsou k dispozici pro připojení pro všechny uživatele, ať už schválené vlastníkem nebo schvalovány automaticky. Na přístupovém panelu můžete změnit členství možnosti při vytváření skupiny.

Skupiny vytvořené v | Chování výchozí skupiny zabezpečení | Výchozí chování skupiny Office 365
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](groups-settings-cmdlets.md) | Pouze vlastníci mohou přidávat členy<br>Viditelný, ale není k dispozici pro připojení v přístupovém panelu | Otevřít do ní přidat všichni uživatelé
[Azure Portal](https://portal.azure.com) | Pouze vlastníci mohou přidávat členy<br>Viditelný, ale není k dispozici pro připojení v přístupovém panelu<br>Vlastník není přiřazen automaticky při vytvoření skupiny | Otevřít do ní přidat všichni uživatelé
[Přístupový panel](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Otevřít do ní přidat všichni uživatelé<br>Možnosti členství můžete změnit po vytvoření skupiny | Otevřít do ní přidat všichni uživatelé<br>Možnosti členství můžete změnit po vytvoření skupiny

## <a name="self-service-group-management-scenarios"></a>Scénáře správy samoobslužné skupiny

* **Delegovaná správa skupin** – příkladem je správce, který spravuje přístup k aplikaci SaaS, kterou používá jeho společnost. Správa těchto přístupových práv je čím dál náročnější, takže správce požádá majitele firmy, aby vytvořil novou skupinu. Správce přiřadí nové skupině přístup k aplikaci a do této skupiny přidá všechny uživatele, kteří už přístup k aplikaci mají. Majitel firmy potom může přidat další uživatele a tito uživatelé budou automaticky přiřazeni k aplikaci. Kvůli správě přístupu pro uživatele tak majitel firmy nemusí čekat na správce. Pokud správce udělí stejné oprávnění správci v jiné firemní skupině, potom tato osoba může spravovat také přístup pro členy skupiny. Majitel firmy ani správce můžete zobrazit nebo spravovat uživatele toho druhého členství ve skupinách. Správce může stále vidět všechny uživatele, kteří mají přístup k aplikaci, a v případě potřeby je může zablokovat.
* **Samoobslužná správa skupin** – příkladem tohoto scénáře jsou dva uživatelé, kteří mají nezávisle nastavené weby SharePoint Online. Oba chtějí týmu toho druhého poskytnout přístup na svůj web. Aby toho dosáhli, můžou vytvořit jednu skupinu v Azure AD a potom v SharePointu Online každý z nich vybere tu skupinu, které chce poskytnout přístup na svůj web. Pokud chce uživatel získat přístup, požádá o něj na přístupovém panelu a po schválení získá přístup k oběma webům SharePoint Online automaticky. Později se jeden z nich rozhodne, že všichni uživatelé s přístupem k webu by měli získat přístup i k určité aplikaci SaaS. Správce aplikace SaaS může přidat přístupová práva k aplikacím na web SharePoint Online. Od toho okamžiku získají veškeré jím schválené žádosti přístup na oba weby SharePoint Online a také k této aplikaci SaaS.

## <a name="make-a-group-available-for-user-self-service"></a>Zpřístupnění skupiny pro uživatelskou samoobsluhu
1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který má k adresáři oprávnění globálního správce.
2. Vyberte **Uživatelé a skupiny** a pak vyberte **Nastavení skupin**.
3. Nastavte možnost **Samoobslužná správa skupin povolena** na **Ano**.
4. Nastavte možnost **Uživatelé můžou vytvářet skupiny zabezpečení** nebo **Uživatelé můžou vytvářet skupiny Office 365** na **Ano**.
   * Pokud jsou tato nastavení povolena, všichni uživatelé ve vašem adresáři mají povoleno vytvářet nové skupiny zabezpečení a přidávat do těchto skupin členy. Tyto nové skupiny se zobrazí také všem ostatním uživatelům na přístupovém panelu. Pokud to nastavení zásad skupiny umožňuje, můžou ostatní uživatelé vytvářet žádosti o připojení k těmto skupinám. 
   * Pokud jsou tato nastavení zakázána, uživatelé nemůžou vytvářet skupiny a nemůžou měnit existující skupiny, ve kterých figurují jako vlastníci. Můžou však stále spravovat členství v těchto skupinách a schvalovat žádosti o členství od jiných uživatelů.

Pomocí možností **Uživatelé, kteří můžou spravovat skupiny zabezpečení** a **Uživatelé, kteří můžou spravovat skupiny Office 365** můžete také zajistit podrobnější řízení přístupu, který vám samoobslužná správa skupin nenabídne. Pokud je povolená možnost **Uživatelé můžou vytvářet skupiny**, mají všichni uživatelé ve vašem tenantovi povoleno vytvářet nové skupiny a přidávat do těchto skupin členy. Nastavením těchto možností na **Někteří** omezíte správu skupin pouze na omezenou skupinu uživatelů. Pokud je tento přepínač nastavený na možnost **Někteří**, musíte uživatele nejprve přidat do skupiny SSGMSecurityGroupsUsers a teprve potom budou tito uživatelé moct vytvářet nové skupiny a přidávat do nich členy. Nastavením možností **Uživatelé, kteří můžou využívat samoobslužné funkce pro skupiny zabezpečení** a **Uživatelé, kteří můžou spravovat skupiny Office 365** na **Všichni** povolíte všem uživatelům ve vašem tenantovi vytvářet nové skupiny.

Pomocí možnosti **Skupina, která může spravovat skupiny zabezpečení** nebo **Skupina, která může spravovat skupiny Office 365** můžete také určit jednu skupinu, jejíž členové můžou používat samoobslužné funkce.

## <a name="next-steps"></a>Další postup
Následující články poskytují další informace o službě Azure Active Directory.

* [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Rutiny služby Azure Active Directory pro konfiguraci nastavení skupiny](groups-settings-cmdlets.md)
* [Správa aplikací ve službě Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Představení služby Azure Active Directory](../fundamentals/active-directory-whatis.md)
* [Integrace místních identit do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
