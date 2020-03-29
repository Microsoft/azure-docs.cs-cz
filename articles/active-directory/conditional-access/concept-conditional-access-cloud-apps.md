---
title: Cloudové aplikace nebo akce v zásadách podmíněného přístupu – Azure Active Directory
description: Co jsou cloudové aplikace nebo akce v zásadách podmíněného přístupu Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69bdd2d6825427597e9030a03aae7d219361ba25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671952"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>Podmíněný přístup: Cloudové aplikace nebo akce

Cloudové aplikace nebo akce jsou klíčovým signálem v zásadách podmíněného přístupu. Zásady podmíněného přístupu umožňují správcům přiřadit ovládací prvky určitým aplikacím nebo akcím.

- Správci si mohou vybrat ze seznamu aplikací, které zahrnují předdefinované aplikace Microsoftu a všechny [integrované aplikace Azure AD,](../manage-apps/what-is-application-management.md) včetně galerie, jiných galerií a aplikací publikovaných prostřednictvím [proxy aplikace](../manage-apps/what-is-application-proxy.md).
- Správci se mohou rozhodnout definovat zásady, které nejsou založeny na cloudové aplikaci, ale na akci uživatele. Jedinou podporovanou akcí je Registrace informací o zabezpečení (preview), která umožňuje podmíněnému přístupu vynucovat ovládací prvky kolem [kombinovaného prostředí registrace informací o zabezpečení](../authentication/howto-registration-mfa-sspr-combined.md).

![Definování zásad podmíněného přístupu a určení cloudových aplikací](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Cloudové aplikace Microsoftu

Mnoho existujících cloudových aplikací Microsoftu je zahrnuto do seznamu aplikací, ze kterých si můžete vybrat. 

Správci můžou přiřadit zásady podmíněného přístupu k následujícím cloudovým aplikacím od Microsoftu. Některé aplikace, jako je Office 365 (preview) a Microsoft Azure Management, zahrnují několik souvisejících podřízených aplikací nebo služeb. Následující seznam není vyčerpávající a může se změnit.

- [Office 365 (náhled)](#office-365-preview)
- Azure Analysis Services
- Azure DevOps
- [Azure SQL Database a Data Warehouse](../../sql-database/sql-database-conditional-access.md)
- Dynamics CRM Online
- Microsoft Application Insights Analytics
- [Ochrana informací Microsoft Azure](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Správa Microsoft Azure](#microsoft-azure-management)
- Správa předplatného Microsoft Azure
- Microsoft Cloud App Security
- Portál řízení přístupu k nástrojům microsoftu
- Služba ověřování nástrojů služby Microsoft Commerce Tools
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Registrace Microsoft Intune](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Hledání microsoftu ve službě Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Office Sway
- Outlook Groups
- Služba Power BI
- Project Online
- Online Skype pro firmy
- Virtuální privátní síť (VPN)
- Ochrana ATP v programu Windows Defender

### <a name="office-365-preview"></a>Office 365 (náhled)

Office 365 poskytuje cloudové služby pro zvýšení produktivity a spolupráce, jako jsou Exchange, SharePoint a Microsoft Teams. Cloudové služby Office 365 jsou hluboce integrované, aby zajistily plynulé a kolaborativní prostředí. Tato integrace může způsobit nejasnosti při vytváření zásad, protože některé aplikace, jako je Microsoft Teams, mají závislosti na jiných, jako je SharePoint nebo Exchange.

Aplikace Office 365 (preview) umožňuje cílit na tyto služby najednou. Doporučujeme používat novou aplikaci Office 365 (preview) místo cílení na jednotlivé cloudové aplikace. Cílení na tuto skupinu aplikací pomáhá vyhnout se problémům, které mohou vzniknout v důsledku nekonzistentních zásad a závislostí.

Správci se můžou rozhodnout, že vyloučí konkrétní aplikace ze zásad, pokud si to přejí, a to tak, že zadají aplikaci Office 365 (preview) a vyloučí konkrétní aplikace podle vlastního výběru v zásadách.

Klíčové aplikace, které jsou součástí klientské aplikace Office 365 (preview):

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Office 365 Exchange Online
   - Office 365 SharePoint Online
   - Vyhledávací služba Office 365
   - Office 365 Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - Online Skype pro firmy
   - Sway

### <a name="microsoft-azure-management"></a>Správa Microsoft Azure

Aplikace Microsoft Azure Management zahrnuje několik základních služeb. 

   - portál Azure
   - Zprostředkovatel Azure Resource Manageru
   - Klasická prostředí API modelu nasazení
   - Azure PowerShell
   - Portál pro správce předplatných Visual Studia
   - Azure DevOps
   - Portál Azure Data Factory

> [!NOTE]
> Aplikace Microsoft Azure Management se vztahuje na Azure PowerShell, který volá rozhraní API Azure Resource Manager. Nevztahuje se na Azure AD PowerShell, který volá Microsoft Graph.

## <a name="other-applications"></a>Ostatní aplikace

Kromě aplikací Microsoftu můžou správci přidat do zásad podmíněného přístupu libovolnou registrovanou aplikaci Azure AD. Tyto žádosti mohou zahrnovat: 

- Aplikace publikované prostřednictvím [proxy aplikace Azure AD](../manage-apps/what-is-application-proxy.md)
- [Aplikace přidané z galerie](../manage-apps/add-application-portal.md)
- [Vlastní aplikace, které nejsou v galerii](../manage-apps/add-non-gallery-app.md)
- [Starší aplikace publikované prostřednictvím řadičů pro doručování aplikací a sítí](../manage-apps/secure-hybrid-access.md)

## <a name="user-actions"></a>Akce uživatele

Akce uživatele jsou úkoly, které může provádět uživatel. Jedinou aktuálně podporovanou akcí je **Registrace informací o zabezpečení (preview),** která umožňuje zásadám podmíněného přístupu vynutit, když se uživatelé, kteří mají povolenou kombinovanou registraci, pokusí zaregistrovat své informace o zabezpečení. Více informací naleznete v článku [Kombinovaná registrace bezpečnostních informací (preview).](../authentication/concept-registration-mfa-sspr-combined.md)

## <a name="next-steps"></a>Další kroky

- [Podmíněný přístup: Podmínky](concept-conditional-access-conditions.md)

- [Podmíněné přístupové běžné zásady](concept-conditional-access-policy-common.md)
- [Závislosti klientských aplikací](service-dependencies.md)
