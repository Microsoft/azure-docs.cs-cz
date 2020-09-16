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
ms.openlocfilehash: 838d2a1dd1c7b89c1f5c9fec6578312107264958
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2020
ms.locfileid: "90602060"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>Podmíněný přístup: cloudové aplikace nebo akce

Cloudové aplikace nebo akce jsou klíčovým signálem v zásadách podmíněného přístupu. Zásady podmíněného přístupu umožňují správcům přiřazovat ovládací prvky konkrétním aplikacím nebo akcím.

- Správci si můžou vybrat ze seznamu aplikací, které obsahují integrované aplikace Microsoftu a všechny [integrované aplikace Azure AD](../manage-apps/what-is-application-management.md) , včetně galerie, mimo galerie a aplikace publikované prostřednictvím [proxy aplikací](../manage-apps/what-is-application-proxy.md).
- Správci se můžou rozhodnout definovat zásady, které nejsou založené na cloudové aplikaci, ale na akci uživatele. Jediná podporovaná akce je registrace informací o zabezpečení (Preview), která umožňuje podmíněný přístup vymáhat ovládací prvky v rámci [kombinovaného prostředí pro registraci informací o zabezpečení](../authentication/howto-registration-mfa-sspr-combined.md).

![Definování zásad podmíněného přístupu a určení cloudových aplikací](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Cloudové aplikace Microsoftu

Mnohé z existujících cloudových aplikací Microsoftu jsou uvedené v seznamu aplikací, ze kterých můžete vybírat. 

Správci můžou k těmto cloudovým aplikacím od Microsoftu přiřazovat zásady podmíněného přístupu. Některé aplikace, jako je například sada Office 365 (Preview) a Správa Microsoft Azure, zahrnují několik souvisejících podřízených aplikací nebo služeb. Následující seznam není vyčerpávající a může se změnit.

- [Office 365 (Preview)](#office-365-preview)
- Azure Analysis Services
- Azure DevOps
- [Azure SQL Database a Data Warehouse](../../azure-sql/database/conditional-access-configure.md)
- Dynamics CRM Online
- Analýzy Microsoft Application Insights
- [Microsoft Azure Information Protection](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Správa Microsoft Azure](#microsoft-azure-management)
- Správa předplatného Microsoft Azure
- Microsoft Cloud App Security
- Portál Microsoft Commerce Tools Access Control Portal
- Služba Microsoft Commerce Tools Authentication Service
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Registrace Microsoft Intune](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Hledání ve službě Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Exchange Online
- SharePoint
- Yammer
- Office Delve
- Sway pro Office
- Outlook Groups
- Služba Power BI
- Project Online
- Online Skype pro firmy
- Virtuální privátní síť (VPN)
- Ochrana ATP v programu Windows Defender

### <a name="office-365-preview"></a>Office 365 (Preview)

Microsoft 365 poskytuje cloudové služby pro produktivitu a spolupráci, jako je Exchange, SharePoint a Microsoft Teams. Cloudové služby Microsoft 365 jsou hluboko integrované, aby bylo zajištěno hladké a spolupracující prostředí. Tato integrace může při vytváření zásad způsobit nejasnost, protože některé aplikace, jako je například Microsoft teams, mají závislosti na jiných, jako je SharePoint nebo Exchange.

Aplikace Office 365 (Preview) umožňuje zaměřit se na tyto služby najednou. Doporučujeme používat novou aplikaci Office 365 (Preview) místo cílení na jednotlivé cloudové aplikace, abyste se vyhnuli problémům se [závislostmi služby](service-dependencies.md). Cílení na tuto skupinu aplikací pomáhá zabránit problémům, ke kterým může dojít kvůli nekonzistentním zásadám a závislostem.

Správci se můžou rozhodnout vyloučit konkrétní aplikace ze zásad, pokud si chtějí zahrnout aplikaci Office 365 (Preview) a vyloučit konkrétní aplikace podle jejich výběru v zásadách.

Klíčové aplikace, které jsou zahrnuté v klientské aplikaci Office 365 (Preview):

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Exchange Online
   - SharePoint Online
   - Microsoft 365 Search Service
   - Yammer
   - Office Delve
   - Online Office
   - Office.com
   - OneDrive
   - PowerApps
   - Online Skype pro firmy
   - Sway

### <a name="microsoft-azure-management"></a>Správa Microsoft Azure

Aplikace pro správu Microsoft Azure zahrnuje několik základních služeb. 

   - portál Azure
   - Poskytovatel Azure Resource Manager
   - Rozhraní API modelu nasazení Classic
   - Azure PowerShell
   - Portál pro správu předplatných sady Visual Studio
   - Azure DevOps
   - Portál Azure Data Factory

> [!NOTE]
> Aplikace Microsoft Azure Management se vztahuje na Azure PowerShell, která volá rozhraní API Azure Resource Manager. Nevztahuje se na Azure AD PowerShell, který volá Microsoft Graph.

## <a name="other-applications"></a>Další aplikace

Kromě aplikací Microsoftu můžou správci přidat do zásad podmíněného přístupu libovolnou aplikaci registrovanou v Azure AD. Tyto aplikace můžou zahrnovat: 

- Aplikace publikované prostřednictvím [Azure proxy aplikací služby AD](../manage-apps/what-is-application-proxy.md)
- [Aplikace přidané z Galerie](../manage-apps/add-application-portal.md)
- [Vlastní aplikace, které nejsou v galerii](../manage-apps/view-applications-portal.md)
- [Starší verze aplikací publikované prostřednictvím řadičů pro doručování aplikací a sítí](../manage-apps/secure-hybrid-access.md)
- Aplikace, které používají [jednotné přihlašování založené na heslech](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)

> [!NOTE]
> Vzhledem k tomu, že zásada podmíněného přístupu nastavuje požadavky pro přístup ke službě, nemůžete ji použít pro klientskou (veřejnou/nativní) aplikaci. Jiná slova zásada není nastavena přímo v klientské (veřejné nebo nativní) aplikaci, ale je použita, když klient zavolá službu. Například zásada nastavená ve službě SharePoint se vztahuje na klienty, kteří volají službu SharePoint. Pro přístup k e-mailu pomocí klienta aplikace Outlook se použije zásada nastavená na Exchangi. Proto nejsou aplikace klienta (veřejné/nativní) k dispozici pro výběr v nastaveních pro výběr cloudových aplikací a možnost podmíněný přístup není k dispozici v nastavení aplikace pro klientskou (veřejnou/nativní) aplikaci registrovanou ve vašem tenantovi. 


## <a name="user-actions"></a>Akce uživatele

Akce uživatele jsou úkoly, které může provést uživatel. Jediná aktuálně podporovaná akce je **Registrace informací o zabezpečení**, která umožňuje vyhovět zásadám podmíněného přístupu, když se uživatelům, kteří mají povolený pokus o registraci v rámci kombinované registrace, pokusí zaregistrovat své bezpečnostní údaje. Další informace najdete v článku [o registraci kombinovaných bezpečnostních údajů](../authentication/concept-registration-mfa-sspr-combined.md).

## <a name="next-steps"></a>Další kroky

- [Podmíněný přístup: podmínky](concept-conditional-access-conditions.md)

- [Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)
- [Závislosti klientských aplikací](service-dependencies.md)