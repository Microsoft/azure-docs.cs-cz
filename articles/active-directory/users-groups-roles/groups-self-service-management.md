---
title: Nastavení samoobslužné správy skupin – Azure Active Directory | Microsoft Docs
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
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b52604d4ad20fed83c4649f046722ed45e766c4
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097700"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Nastavení samoobslužné správy skupin v Azure Active Directory 

Uživatelům můžete povolit vytváření a správu vlastních skupin zabezpečení nebo skupin Office 365 ve službě Azure Active Directory (Azure AD). Vlastník skupiny může schvalovat nebo zamítnout žádosti o členství a může delegovat řízení členství ve skupině. Funkce Samoobslužné správy skupin nejsou k dispozici pro skupiny zabezpečení s povoleným e-mailem ani pro distribuční seznamy.

## <a name="self-service-group-membership-defaults"></a>Výchozí nastavení členství ve skupině samoobslužných služeb

Když se v Azure Portal nebo pomocí Azure AD PowerShellu vytvoří skupiny zabezpečení, můžou členství aktualizovat jenom vlastníci skupiny. Skupiny zabezpečení vytvořené na [přístupovém panelu](https://account.activedirectory.windowsazure.com/r#/joinGroups) a všech skupinách sady Office 365 jsou k dispozici pro připojení všem uživatelům, ať už se jedná o vlastníka schválené nebo automaticky schválené. Na přístupovém panelu můžete změnit možnosti členství při vytváření skupiny.

Skupiny vytvořené v | Výchozí chování skupiny zabezpečení | Výchozí chování skupiny Office 365
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](groups-settings-cmdlets.md) | Členy můžou přidávat jenom vlastníci.<br>Viditelná, ale není dostupná pro připojení na přístupovém panelu | Otevřít pro připojení pro všechny uživatele
[Azure Portal](https://portal.azure.com) | Členy můžou přidávat jenom vlastníci.<br>Viditelná, ale není dostupná pro připojení na přístupovém panelu<br>Vlastník není automaticky přiřazen při vytváření skupiny. | Otevřít pro připojení pro všechny uživatele
[Přístupový panel](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Otevřít pro připojení pro všechny uživatele<br>Možnosti členství lze změnit při vytvoření skupiny. | Otevřít pro připojení pro všechny uživatele<br>Možnosti členství lze změnit při vytvoření skupiny.

## <a name="self-service-group-management-scenarios"></a>Scénáře správy skupin samoobslužných služeb

* **Delegovaná správa skupin** – příkladem je správce, který spravuje přístup k aplikaci SaaS, kterou používá jeho společnost. Správa těchto přístupových práv je čím dál náročnější, takže správce požádá majitele firmy, aby vytvořil novou skupinu. Správce přiřadí aplikaci k nové skupině a přidá je do skupiny všichni lidé, kteří už přistupují k aplikaci. Majitel firmy potom může přidat další uživatele a tito uživatelé budou automaticky přiřazeni k aplikaci. Kvůli správě přístupu pro uživatele tak majitel firmy nemusí čekat na správce. Pokud správce udělí stejné oprávnění správci v jiné obchodní skupině, pak může tato osoba také spravovat přístup vlastních členů skupiny. Obchodní vlastník ani správce nemůže zobrazit ani spravovat členství ve skupině ostatních. Správce může stále vidět všechny uživatele, kteří mají přístup k aplikaci, a v případě potřeby je může zablokovat.
* **Samoobslužná správa skupin** – příkladem tohoto scénáře jsou dva uživatelé, kteří mají nezávisle nastavené weby SharePoint Online. Oba chtějí týmu toho druhého poskytnout přístup na svůj web. Aby toho dosáhli, můžou vytvořit jednu skupinu v Azure AD a potom v SharePointu Online každý z nich vybere tu skupinu, které chce poskytnout přístup na svůj web. Pokud chce uživatel získat přístup, požádá o něj na přístupovém panelu a po schválení získá přístup k oběma webům SharePoint Online automaticky. Později se jeden z nich rozhodne, že všichni uživatelé s přístupem k webu by měli získat přístup i k určité aplikaci SaaS. Správce aplikace SaaS může přidat přístupová práva k aplikacím na web SharePoint Online. Od toho okamžiku získají veškeré jím schválené žádosti přístup na oba weby SharePoint Online a také k této aplikaci SaaS.

## <a name="make-a-group-available-for-user-self-service"></a>Zpřístupnění skupiny pro uživatelskou samoobsluhu

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který má k adresáři oprávnění globálního správce.
2. Vyberte **Uživatelé a skupiny** a pak vyberte **Nastavení skupin**.
3. Nastavte možnost **Samoobslužná správa skupin povolena** na **Ano**.
4. Nastavte možnost **Uživatelé můžou vytvářet skupiny zabezpečení** nebo **Uživatelé můžou vytvářet skupiny Office 365** na **Ano**.
   * Pokud jsou tato nastavení povolena, všichni uživatelé ve vašem adresáři mají povoleno vytvářet nové skupiny zabezpečení a přidávat do těchto skupin členy. Tyto nové skupiny se zobrazí také všem ostatním uživatelům na přístupovém panelu. Pokud to nastavení zásad skupiny umožňuje, můžou ostatní uživatelé vytvářet žádosti o připojení k těmto skupinám. 
   * Pokud jsou tato nastavení zakázána, uživatelé nemůžou vytvářet skupiny a nemůžou měnit existující skupiny, ve kterých figurují jako vlastníci. Můžou však stále spravovat členství v těchto skupinách a schvalovat žádosti o členství od jiných uživatelů.

Pomocí možností **Uživatelé, kteří můžou spravovat skupiny zabezpečení** a **Uživatelé, kteří můžou spravovat skupiny Office 365** můžete také zajistit podrobnější řízení přístupu, který vám samoobslužná správa skupin nenabídne. Pokud je povolená možnost **Uživatelé můžou vytvářet skupiny**, mají všichni uživatelé ve vašem tenantovi povoleno vytvářet nové skupiny a přidávat do těchto skupin členy. Nemůžete určit jednotlivce, kteří můžou vytvářet své vlastní skupiny. Můžete určit jednotlivce jenom pro případ, že bude mít jiný člen skupiny jako vlastník skupiny.

Když nastavíte **uživatele, kteří můžou používat samoobslužné funkce pro skupiny zabezpečení** a **uživatele, kteří můžou spravovat skupiny Office 365** na **Ano**, povolíte všem uživatelům ve vašem tenantovi vytvářet nové skupiny.

Pomocí možnosti **Skupina, která může spravovat skupiny zabezpečení** nebo **Skupina, která může spravovat skupiny Office 365** můžete také určit jednu skupinu, jejíž členové můžou používat samoobslužné funkce.

## <a name="next-steps"></a>Další kroky

Následující články poskytují další informace o službě Azure Active Directory.

* [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Rutiny služby Azure Active Directory pro konfiguraci nastavení skupiny](groups-settings-cmdlets.md)
* [Správa aplikací ve službě Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Představení služby Azure Active Directory](../fundamentals/active-directory-whatis.md)
* [Integrace místních identit do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
