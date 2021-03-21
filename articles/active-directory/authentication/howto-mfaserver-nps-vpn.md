---
title: Azure MFA Server a sítě VPN třetích stran – Azure Active Directory
description: Podrobné pokyny pro konfiguraci Azure MFA serveru pro integraci s Cisco, Citrix a Juniper.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88fe09199cb50d2a3796c3b638dca1a723016dc4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96742013"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>Pokročilé scénáře s využitím Azure MFA serveru a řešení VPN jiných výrobců

Azure Multi-Factor Authentication Server (Azure MFA Server) je možné využít k bezproblémovému připojení k různým řešením sítě VPN třetích stran. Tento článek se zaměřuje na &reg; zařízení VPN Cisco ASA, zařízení VPN Citrix NETSCALER SSL a zařízení Juniper sítě Secure SSL zabezpečeného přístupu/Pulse Secure Connect. Vytvořili jsme konfigurační příručky pro řešení těchto tří běžných zařízení. Azure MFA Server je taky možné integrovat s dalšími systémy, které pro AD FS používají ověřování pomocí protokolu RADIUS, LDAP, IIS nebo založené na deklaracích identity. Další podrobnosti najdete v [konfiguracích serveru Azure MFA](howto-mfaserver-deploy.md#next-steps).

> [!IMPORTANT]
> Od 1. července 2019 už společnost Microsoft nenabízí MFA Server pro nová nasazení. Noví zákazníci, kteří chtějí vyžadovat vícefaktorové ověřování (MFA) během přihlašovacích událostí, by měli používat cloudovou Multi-Factor Authentication Azure AD.
>
> Pokud chcete začít s cloudovým ověřováním MFA, přečtěte si téma [kurz: zabezpečení událostí přihlašování uživatelů pomocí Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Pokud používáte cloudové vícefaktorové ověřování, přečtěte si téma [integrace vaší infrastruktury sítě VPN s využitím Azure MFA](howto-mfa-nps-extension-vpn.md).
>
> Stávající zákazníci, kteří si vyaktivovali MFA Server dřív než 1. července 2019, můžou stáhnout nejnovější verzi, budoucí aktualizace a vygenerovat přihlašovací údaje pro aktivaci obvyklým způsobem.

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>Zařízení VPN Cisco ASA a Azure MFA Server
Azure MFA Server se integruje se &reg; zařízením Cisco ASA VPN, které poskytuje dodatečné zabezpečení pro &reg; přihlášení k síti VPN Cisco AnyConnect a přístup k portálu.  Můžete použít buď protokol LDAP, nebo protokol RADIUS.  Vyberte jednu z následujících možností ke stažení podrobných průvodců konfigurací.

| Průvodce konfigurací | Description |
| --- | --- |
| [Cisco ASA s AnyConnect VPN a konfigurací Azure MFA pro LDAP](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Integrace zařízení VPN Cisco ASA s Azure MFA pomocí protokolu LDAP |
| [Cisco ASA s AnyConnect VPN a konfigurací Azure MFA pro RADIUS](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Integrace zařízení VPN Cisco ASA s Azure MFA pomocí protokolu RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>Citrix NetScaler SSL VPN a Azure MFA Server
Azure MFA Server se integruje se zařízením Citrix NetScaler SSL VPN a poskytuje dodatečné zabezpečení pro přihlášení k síti VPN Citrix NetScaler SSL a přístup k portálu.  Můžete použít buď protokol LDAP, nebo protokol RADIUS.  Vyberte jednu z následujících možností ke stažení podrobných průvodců konfigurací.

| Průvodce konfigurací | Description |
| --- | --- |
| [Citrix NetScaler SSL VPN a konfigurace Azure MFA pro LDAP](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integrace sítě VPN s protokolem Citrix NetScaler SSL se zařízením Azure MFA pomocí protokolu LDAP |
| [Citrix NetScaler SSL VPN a konfigurace Azure MFA pro RADIUS](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Integrace zařízení VPN s protokolem Citrix NetScaler SSL s Azure MFA pomocí protokolu RADIUS |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>Zařízení sítě VPN Juniper/Pulse Secure SSL a Azure MFA Server
Azure MFA Server se integruje se zařízením Juniper/Pulse Secure SSL VPN, které poskytuje dodatečné zabezpečení pro přihlašování a přístup k portálu pomocí zabezpečeného SSL pro Juniper/Pulse.  Můžete použít buď protokol LDAP, nebo protokol RADIUS.  Vyberte jednu z následujících možností ke stažení podrobných průvodců konfigurací.

| Průvodce konfigurací | Description |
| --- | --- |
| [Juniper/Pulse Secure SSL VPN a konfigurace Azure MFA pro LDAP](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Integrace zabezpečeného SSL protokolu Juniper/Pulse s využitím protokolu LDAP pomocí zařízení s Azure MFA |
| [Juniper/Pulse Secure SSL VPN a konfigurace Azure MFA pro RADIUS](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Integrujte vaše zařízení VPN s protokolem Juniper/Pulse Secure SSL s Azure MFA pomocí protokolu RADIUS. |

## <a name="next-steps"></a>Další kroky

- [Rozšíření stávající ověřovací infrastruktury s rozšířením NPS pro Azure Multi-Factor Authentication](howto-mfa-nps-extension.md)

- [Konfigurace nastavení služby Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)
