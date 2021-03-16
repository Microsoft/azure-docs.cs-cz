---
title: Cloudové aplikace nebo akce v zásadách podmíněného přístupu – Azure Active Directory
description: Co jsou cloudové aplikace nebo akce v zásadách podmíněného přístupu Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2895588a5a82ec2b6c69d33ff6cea39bbe3a0372
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491992"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>Podmíněný přístup: cloudové aplikace nebo akce

Cloudové aplikace nebo akce jsou klíčovým signálem v zásadách podmíněného přístupu. Zásady podmíněného přístupu umožňují správcům přiřazovat ovládací prvky konkrétním aplikacím nebo akcím.

- Správci si můžou vybrat ze seznamu aplikací, které obsahují integrované aplikace Microsoftu a všechny [integrované aplikace Azure AD](../manage-apps/what-is-application-management.md) , včetně galerie, mimo galerie a aplikace publikované prostřednictvím [proxy aplikací](../manage-apps/what-is-application-proxy.md).
- Správci se můžou rozhodnout definovat zásady, které nejsou založené na cloudové aplikaci, ale na akci uživatele. Jediná podporovaná akce je registrace informací o zabezpečení (Preview), která umožňuje podmíněný přístup vymáhat ovládací prvky v rámci [kombinovaného prostředí pro registraci informací o zabezpečení](../authentication/howto-registration-mfa-sspr-combined.md).

![Definování zásad podmíněného přístupu a určení cloudových aplikací](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Cloudové aplikace Microsoftu

Mnohé z existujících cloudových aplikací Microsoftu jsou uvedené v seznamu aplikací, ze kterých můžete vybírat. 

Správci můžou k těmto cloudovým aplikacím od Microsoftu přiřazovat zásady podmíněného přístupu. Některé aplikace, jako je například sada Office 365 a Správa Microsoft Azure, zahrnují několik souvisejících podřízených aplikací nebo služeb. Následující seznam není vyčerpávající a může se změnit.

- [Office 365](#office-365)
- Azure Analysis Services
- Azure DevOps
- [Azure SQL Database a Azure synapse Analytics](../../azure-sql/database/conditional-access-configure.md)
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

### <a name="office-365"></a>Office 365

Microsoft 365 poskytuje cloudové služby pro produktivitu a spolupráci, jako je Exchange, SharePoint a Microsoft Teams. Cloudové služby Microsoft 365 jsou hluboko integrované, aby bylo zajištěno hladké a spolupracující prostředí. Tato integrace může při vytváření zásad způsobit nejasnost, protože některé aplikace, jako je například Microsoft teams, mají závislosti na jiných, jako je SharePoint nebo Exchange.

Aplikace Office 365 umožňuje zaměřit se na tyto služby najednou. Doporučujeme použít novou aplikaci Office 365 místo cílení na jednotlivé cloudové aplikace, abyste se vyhnuli problémům se [závislostmi služby](service-dependencies.md). Cílení na tuto skupinu aplikací pomáhá zabránit problémům, ke kterým může dojít kvůli nekonzistentním zásadám a závislostem.

Správci se můžou rozhodnout vyloučit konkrétní aplikace ze zásad, pokud si chtějí zahrnout aplikaci Office 365 a vyloučit konkrétní aplikace podle jejich výběru v zásadách.

Klíčové aplikace, které jsou součástí klientské aplikace Office 365:

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
   - Azure CLI
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

Akce uživatele jsou úkoly, které může provést uživatel. V současné době podmíněný přístup podporuje dvě akce uživatele: 

- **Registrovat informace o zabezpečení**: Tato akce uživatele umožňuje vyhovět zásadám podmíněného přístupu, pokud se uživatelům, kteří mají povolený pokus o registraci v kombinaci, pokusí zaregistrovat své bezpečnostní údaje. Další informace najdete v článku [o registraci kombinovaných bezpečnostních údajů](../authentication/concept-registration-mfa-sspr-combined.md).

- **Registrace nebo připojení zařízení (Preview)**: Tato akce uživatele umožňuje správcům vyhovět zásadám podmíněného přístupu, když uživatelé [registrují](../devices/concept-azure-ad-register.md) nebo [připojí](../devices/concept-azure-ad-join.md) zařízení k Azure AD. Existují dvě klíčová doporučení pro tuto akci uživatele: 
   - `Require multi-factor authentication` je jediným řízením přístupu dostupným pro tuto akci uživatele a všechny ostatní jsou zakázané. Toto omezení zabrání konfliktům s ovládacími prvky přístupu, které jsou buď závislé na registraci zařízení Azure AD, nebo neplatí pro registraci zařízení Azure AD. 
   - Pokud je u této akce uživatele povolená zásada podmíněného přístupu, musíte nastavit **Azure Active Directory**  >    >  **nastavení zařízení**  -  `Devices to be Azure AD joined or Azure AD registered require Multi-Factor Authentication` na **ne**. V opačném případě není zásada podmíněného přístupu s touto akcí uživatele správně vynutila. Další informace týkající se tohoto nastavení zařízení najdete v v [konfiguraci nastavení zařízení](../device-management-azure-portal.md##configure-device-settings). Tato akce uživatele poskytuje flexibilitu, která vyžaduje vícefaktorové ověřování pro registraci nebo připojení zařízení pro konkrétní uživatele a skupiny nebo podmínky, a ne zásady pro tenanta v nastavení zařízení. 
   
## <a name="next-steps"></a>Další kroky

- [Podmíněný přístup: podmínky](concept-conditional-access-conditions.md)

- [Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)
- [Závislosti klientských aplikací](service-dependencies.md)
