---
title: Nastavení samoobslužné správy skupin – Azure Active Directory | Dokumenty společnosti Microsoft
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
ms.date: 03/10/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 541deb5cf44ad5440e31641b673ed5da5b5d2b26
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768540"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Nastavení samoobslužné správy skupin ve službě Azure Active Directory 

Uživatelům můžete povolit vytváření a správu vlastních skupin zabezpečení nebo skupin Office 365 ve službě Azure Active Directory (Azure AD). Vlastník skupiny může schválit nebo odepřít žádosti o členství a může delegovat řízení členství ve skupině. Funkce samoobslužné správy skupin nejsou k dispozici pro skupiny zabezpečení povolené poštou nebo distribuční seznamy.

## <a name="self-service-group-membership-defaults"></a>Výchozí hodnoty členství ve samoobslužných skupinách

Když se skupiny zabezpečení vytvoří na webu Azure Portal nebo pomocí Azure AD PowerShell, můžou členství aktualizovat jenom vlastníci skupiny. Skupiny zabezpečení vytvořené samoobslužnou službou na [přístupovém panelu](https://account.activedirectory.windowsazure.com/r#/joinGroups) a ve všech skupinách Office 365 jsou k dispozici pro všechny uživatele, ať už jsou schváleny vlastníkem nebo automaticky schváleny. Na přístupovém panelu můžete při vytváření skupiny změnit možnosti členství.

Skupiny vytvořené v | Výchozí chování skupiny zabezpečení | Výchozí chování skupiny Office 365
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](groups-settings-cmdlets.md) | Členy mohou přidávat pouze vlastníci.<br>Viditelné, ale není k dispozici pro připojení na přístupovém panelu | Otevřít pro připojení pro všechny uživatele
[portál Azure](https://portal.azure.com) | Členy mohou přidávat pouze vlastníci.<br>Viditelné, ale není k dispozici pro připojení na přístupovém panelu<br>Vlastník není přiřazen automaticky při vytváření skupiny | Otevřít pro připojení pro všechny uživatele
[Přístupový panel](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Otevřít pro připojení pro všechny uživatele<br>Možnosti členství lze změnit při vytvoření skupiny | Otevřít pro připojení pro všechny uživatele<br>Možnosti členství lze změnit při vytvoření skupiny

## <a name="self-service-group-management-scenarios"></a>Scénáře správy samoobslužné skupiny

* **Delegovaná správa skupin** – příkladem je správce, který spravuje přístup k aplikaci SaaS, kterou používá jeho společnost. Správa těchto přístupových práv je čím dál náročnější, takže správce požádá majitele firmy, aby vytvořil novou skupinu. Správce přiřadí aplikaci přístup k nové skupině a přidá do skupiny všechny osoby, které již přistupují k aplikaci. Majitel firmy potom může přidat další uživatele a tito uživatelé budou automaticky přiřazeni k aplikaci. Kvůli správě přístupu pro uživatele tak majitel firmy nemusí čekat na správce. Pokud správce udělí stejné oprávnění manažerovi v jiné obchodní skupině, může tato osoba také spravovat přístup pro své vlastní členy skupiny. Vlastník firmy ani správce nemohou vzájemně zobrazit ani spravovat členství ve skupinách. Správce může stále vidět všechny uživatele, kteří mají přístup k aplikaci, a v případě potřeby je může zablokovat.
* **Samoobslužná správa skupin** – příkladem tohoto scénáře jsou dva uživatelé, kteří mají nezávisle nastavené weby SharePoint Online. Chtějí dát svým týmům přístup na jejich stránky. Aby toho dosáhli, můžou vytvořit jednu skupinu v Azure AD a potom v SharePointu Online každý z nich vybere tu skupinu, které chce poskytnout přístup na svůj web. Pokud chce uživatel získat přístup, požádá o něj na přístupovém panelu a po schválení získá přístup k oběma webům SharePoint Online automaticky. Později se jeden z nich rozhodne, že všichni uživatelé s přístupem k webu by měli získat přístup i k určité aplikaci SaaS. Správce aplikace SaaS může přidat přístupová práva k aplikacím na web SharePoint Online. Od toho okamžiku získají veškeré jím schválené žádosti přístup na oba weby SharePoint Online a také k této aplikaci SaaS.

## <a name="make-a-group-available-for-user-self-service"></a>Zpřístupnění skupiny pro uživatelskou samoobsluhu

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který má k adresáři oprávnění globálního správce.
1. Vyberte **Skupiny**a pak vyberte **Obecné** nastavení.
1. **Nastavit vlastníci mohou spravovat žádosti o členství ve skupinách na přístupovém panelu** **ano**.
1. Nastavte **možnost Omezit přístup ke skupinám na přístupovém panelu** na **ne.**
1. Pokud nastavíte **Uživatelé můžou vytvářet skupiny zabezpečení na portálech Azure** nebo **Uživatelé můžou na portálech Azure vytvářet skupiny Office 365,** aby

    - **Ano:** Všichni uživatelé ve vaší organizaci Azure AD mohou vytvářet nové skupiny zabezpečení a přidávat členy do těchto skupin. Tyto nové skupiny se zobrazí také všem ostatním uživatelům na přístupovém panelu. Pokud to nastavení zásad ve skupině umožňuje, mohou ostatní uživatelé vytvářet požadavky na připojení k těmto skupinám.
    - **Ne:** Uživatelé nemohou vytvářet skupiny a nemohou měnit existující skupiny, pro které jsou vlastníky. Můžou však stále spravovat členství v těchto skupinách a schvalovat žádosti o členství od jiných uživatelů.

Můžete také použít **vlastníky, kteří mohou přiřadit členy jako vlastníky skupin na portálech Azure** a **vlastníci, kteří mohou přiřadit členy jako vlastníky skupin na portálech Azure,** abyste dosáhli podrobnější kontroly přístupu nad samoobslužnou správou skupin pro vaše uživatele.

Když mohou uživatelé vytvářet skupiny, mohou všichni uživatelé ve vaší organizaci vytvářet nové skupiny a pak mohou jako výchozí vlastník přidávat členy do těchto skupin. Nelze určit jednotlivce, kteří mohou vytvářet vlastní skupiny. Můžete určit pouze jednotlivce pro vytvoření jiného člena skupiny jako vlastníka skupiny.

> [!NOTE]
> Licence Azure Active Directory Premium (P1 nebo P2) je vyžadována, aby uživatelé požádali o připojení ke skupině zabezpečení nebo skupině Office 365 a aby vlastníci schvalovali nebo odepírali žádosti o členství. Bez licence Služby Azure Active Directory Premium mohou uživatelé stále spravovat své skupiny na přístupovém panelu, ale nemohou vytvořit skupinu, která vyžaduje schválení vlastníka na přístupovém panelu, a nemohou požádat o připojení ke skupině. 

## <a name="next-steps"></a>Další kroky

Následující články poskytují další informace o službě Azure Active Directory.

* [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Rutiny Azure Active Directory pro konfiguraci nastavení skupiny](groups-settings-cmdlets.md)
* [Správa aplikací ve službě Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Představení služby Azure Active Directory](../fundamentals/active-directory-whatis.md)
* [Integrace místních identit do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
