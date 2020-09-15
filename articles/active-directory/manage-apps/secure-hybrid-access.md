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
ms.date: 9/10/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d64f7423d537958b6d3c388cb12f23bd2e30e36
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90087072"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>Zabezpečený hybridní přístup: zabezpečení starších verzí aplikací pomocí Azure Active Directory

Nyní můžete chránit místní a cloudové aplikace ověřování pomocí připojení k Azure Active Directory (AD) s:

- [Proxy aplikací služby AD Azure](#secure-hybrid-access-sha-through-azure-ad-application-proxy)

- [Vaše existující řadiče pro doručování aplikací a sítě](#sha-through-networking-and-delivery-controllers)

- [Virtuální privátní sítě (VPN) a softwarově definované hraniční aplikace (SDP)](#sha-through-vpn-and-sdp-applications)

Pomocí možností Azure AD, jako je Azure AD [podmíněný přístup](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) a Azure AD [Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection), můžete tuto mezeru Přemostit a posílit stav zabezpečení napříč všemi aplikacemi.

## <a name="secure-hybrid-access-sha-through-azure-ad-application-proxy"></a>Zabezpečení hybridního přístupu (SHA) prostřednictvím Azure Proxy aplikací služby AD
  
Pomocí [proxy aplikací](https://aka.ms/whyappproxy) můžete poskytovat [zabezpečený vzdálený přístup](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) k místním webovým aplikacím. Uživatelé nemusejí používat síť VPN. Výhodou pro uživatele je snadné připojení k aplikacím z libovolného zařízení po [jednotném přihlašování](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-sso). Proxy aplikací poskytuje vzdálený přístup jako služba a umožňuje [snadno publikovat místní aplikace](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application) uživatelům mimo podnikovou síť. Pomáhá škálovat správu cloudového přístupu bez nutnosti upravovat vaše místní aplikace. [Naplánování nasazení Azure proxy aplikací služby AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-deployment-plan) v následujícím kroku.

## <a name="azure-ad-partner-integrations"></a>Integrace partnerů Azure AD

### <a name="sha-through-networking-and-delivery-controllers"></a>SHA prostřednictvím sítě a řadičů doručení

Kromě [Azure proxy aplikací služby AD](https://aka.ms/whyappproxy), abyste mohli používat [rozhraní s nulovým vztahem důvěryhodnosti](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/), partneři Microsoftu s poskytovateli třetích stran. Můžete použít stávající sítě a řadiče pro doručování a snadno chránit starší aplikace, které jsou pro vaše obchodní procesy důležité, ale nemůžete je chránit před službou Azure AD. Je možné, že už máte všechno, co potřebujete, abyste mohli začít chránit tyto aplikace.

![Obrázek zobrazuje zabezpečený hybridní přístup pomocí síťových partnerů a proxy aplikací](./media/secure-hybrid-access/secure-hybrid-access.png)

Následující dodavatelé sítě nabízejí předem vytvořená řešení a podrobné pokyny pro integraci se službou Azure AD.

- [Přístup k podnikovým aplikacím Akamai (EAA)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)

- [Řadič pro doručování aplikací Citrix (ADC)](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)

- [S funkcí F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial)

- [Kemp](https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial)

### <a name="sha-through-vpn-and-sdp-applications"></a>SHA prostřednictvím aplikací sítě VPN a SDP

Pomocí řešení VPN a SDP můžete zajistit zabezpečený přístup k podnikové síti z libovolného zařízení, kdykoli a v jakémkoli umístění a zároveň ochránit data vaší organizace. Díky tomu, že Azure AD jako zprostředkovatel identity (IDP), můžete použít moderní metody ověřování a autorizace, jako je [jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) Azure AD a služba [Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) , k zabezpečení místních starších aplikací.  

![Obrázek zobrazuje zabezpečený hybridní přístup k partnerům VPN a proxy aplikací ](./media/secure-hybrid-access/app-proxy-vpn.png)

Následující dodavatelé sítě VPN a SDP nabízejí předem vytvořená řešení a podrobné pokyny pro integraci se službou Azure AD.

• [Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)

• [Fortinet](https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial)

• [F5 s funkcí APM pro Big-IP](https://aka.ms/f5-hybridaccessguide)

• [Palo Alto globální Ochrana sítě](https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial)

• [Zscaler Private Access (ZPA)](https://aka.ms/zscaler-hybridaccessguide)
