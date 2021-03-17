---
title: Zabezpečený hybridní přístup k Azure AD | Microsoft Docs
description: Tento článek popisuje Partnerská řešení pro integraci vaší staré aplikace v místním, veřejném cloudu nebo privátního cloudu s Azure AD. Zabezpečte své starší aplikace připojením řadičů pro doručování aplikací nebo sítí k Azure AD.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 2/16/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06ed37b914c352d7162db7f8409ac09b7615da47
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174423"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>Zabezpečený hybridní přístup: zabezpečení starších verzí aplikací pomocí Azure Active Directory

Nyní můžete chránit místní a cloudové aplikace ověřování pomocí připojení k Azure Active Directory (AD) s:

- [Proxy aplikací služby AD Azure](#secure-hybrid-access-sha-through-azure-ad-application-proxy)

- [Vaše existující řadiče pro doručování aplikací a sítě](#sha-through-networking-and-delivery-controllers)

- [Virtuální privátní sítě (VPN) a aplikace hraničního Software-Defined (SDP)](#sha-through-vpn-and-sdp-applications)

Pomocí možností Azure AD, jako je Azure AD [podmíněný přístup](../conditional-access/overview.md) a Azure AD [Identity Protection](../identity-protection/overview-identity-protection.md), můžete tuto mezeru Přemostit a posílit stav zabezpečení napříč všemi aplikacemi.

## <a name="secure-hybrid-access-sha-through-azure-ad-application-proxy"></a>Zabezpečení hybridního přístupu (SHA) prostřednictvím Azure Proxy aplikací služby AD
  
Pomocí [proxy aplikací](./what-is-application-proxy.md) můžete poskytovat [zabezpečený vzdálený přístup](./application-proxy.md) k místním webovým aplikacím. Uživatelé nemusejí používat síť VPN. Výhodou pro uživatele je snadné připojení k aplikacím z libovolného zařízení po [jednotném přihlašování](./add-application-portal-setup-sso.md). Proxy aplikací poskytuje vzdálený přístup jako služba a umožňuje [snadno publikovat místní aplikace](./application-proxy-add-on-premises-application.md) uživatelům mimo podnikovou síť. Pomáhá škálovat správu cloudového přístupu bez nutnosti upravovat vaše místní aplikace. [Naplánování nasazení Azure proxy aplikací služby AD](./application-proxy-deployment-plan.md) v následujícím kroku.

## <a name="azure-ad-partner-integrations"></a>Integrace partnerů Azure AD

### <a name="sha-through-networking-and-delivery-controllers"></a>SHA prostřednictvím sítě a řadičů doručení

Kromě [Azure proxy aplikací služby AD](./what-is-application-proxy.md), abyste mohli používat [rozhraní s nulovým vztahem důvěryhodnosti](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/), partneři Microsoftu s poskytovateli třetích stran. Můžete použít stávající sítě a řadiče pro doručování a snadno chránit starší aplikace, které jsou pro vaše obchodní procesy důležité, ale nemůžete je chránit před službou Azure AD. Je možné, že už máte všechno, co potřebujete, abyste mohli začít chránit tyto aplikace.

![Obrázek zobrazuje zabezpečený hybridní přístup pomocí síťových partnerů a proxy aplikací](./media/secure-hybrid-access/secure-hybrid-access.png)

Následující dodavatelé sítě nabízejí předem vytvořená řešení a podrobné pokyny pro integraci se službou Azure AD.

- [Přístup k podnikovým aplikacím Akamai (EAA)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)

- [Řadič pro doručování aplikací Citrix (ADC)](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)

- [S funkcí F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-integration)

- [Kemp](https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial)

- [Pulse Secure Virtual Traffic Manager (VTM)](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-virtual-traffic-manager-tutorial)

### <a name="sha-through-vpn-and-sdp-applications"></a>SHA prostřednictvím aplikací sítě VPN a SDP

Pomocí řešení VPN a SDP můžete zajistit zabezpečený přístup k podnikové síti z libovolného zařízení, kdykoli a v jakémkoli umístění a zároveň ochránit data vaší organizace. Díky tomu, že Azure AD jako zprostředkovatel identity (IDP), můžete použít moderní metody ověřování a autorizace, jako je [jednotné přihlašování](./what-is-single-sign-on.md) Azure AD a služba [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) , k zabezpečení místních starších aplikací.  

![Obrázek zobrazuje zabezpečený hybridní přístup k partnerům VPN a proxy aplikací ](./media/secure-hybrid-access/app-proxy-vpn.png)

Následující dodavatelé sítě VPN nabízejí předem vytvořená řešení a podrobné pokyny pro integraci se službou Azure AD.

- [Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)

- [Fortinet](https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial)

- [S funkcí F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-password-less-vpn)

- [Palo Alto globální Ochrana sítě](https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial)

- [Pulse Secure Pulse Connect Secure (počítače)](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-pcs-tutorial)

Následující dodavatelé služby SDP nabízejí předem vytvořená řešení a podrobné pokyny pro integraci se službou Azure AD.

- [Zprostředkovatel přístupu Datawiza](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-oidc-sso)

- [Perimeter 81](https://docs.microsoft.com/azure/active-directory/saas-apps/perimeter-81-tutorial#:~:text=For%20SSO%20to%20work,%20you%20need%20to%20establish,to%20test%20Azure%20AD%20single%20sign-on%20with%20B.Simon.)

- [Platforma ověřování Silverfort](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-oidc-sso)

- [Strata](https://docs.microsoft.com/azure/active-directory/saas-apps/maverics-identity-orchestrator-saml-connector-tutorial)

- [Zscaler Private Access (ZPA)](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)
