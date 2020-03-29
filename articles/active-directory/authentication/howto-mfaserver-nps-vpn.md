---
title: Azure MFA Server a virtuální názvy virtuálních médií jiných výrobců – Azure Active Directory
description: Podrobné konfigurační příručky pro Azure MFA Server pro integraci se společnostmi Cisco, Citrix a Juniper.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: fdb24bcd79f1766a52f290fd6fe0e6e5bf17e7c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74847945"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>Pokročilé scénáře s Azure MFA Server a řešení VPN třetích stran

Server Azure Multi-Factor Authentication Server (Azure MFA Server) lze použít k bezproblémovému propojení s různými řešeními VPN jiných výrobců. Tento článek se zaměřuje na zařízení Cisco® ASA VPN, zařízení Citrix NetScaler SSL VPN a zařízení Juniper Networks Secure Access/Pulse Secure Connect Secure SSL VPN. Vytvořili jsme konfigurační příručky pro řešení těchto tří běžných zařízení. Azure MFA Server můžete také integrovat s většinou ostatních systémů, které používají RADIUS, LDAP, IIS nebo ověřování na základě deklarací identity služby AD FS. Další podrobnosti najdete v [konfiguracích serveru Azure MFA](howto-mfaserver-deploy.md#next-steps)Server .

> [!IMPORTANT]
> července 2019 již společnost Microsoft nebude nabízet server MFA pro nová nasazení. Noví zákazníci, kteří by chtěli od svých uživatelů vyžadovat vícefaktorové ověřování, by měli používat vícefaktorové ověřování Azure na základě cloudu. Stávající zákazníci, kteří aktivovali server MFA před 1.

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>Zařízení CISCO ASA VPN a server Azure MFA
Azure MFA Server se integruje s vaším zařízením Cisco® ASA VPN, aby poskytoval další zabezpečení pro přihlášení vpn Cisco AnyConnect® vpn a přístup k portálu.  Můžete použít protokol LDAP nebo RADIUS.  Vyberte jednu z následujících možností a stáhněte si podrobné pokyny pro konfiguraci krok za krokem.

| Průvodce konfigurací | Popis |
| --- | --- |
| [Cisco ASA s libovolnou VPN a azure mfa konfigurace pro LDAP](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Integrace zařízení Cisco ASA VPN s Azure MFA pomocí protokolu LDAP |
| [Cisco ASA s libovolnou VPN a konfigurací Azure MFA pro RADIUS](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Integrace zařízení CISCO ASA VPN s Azure MFA pomocí RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>Citrix NetScaler SSL VPN a Server Azure MFA
Azure MFA Server se integruje s vaším zařízením Citrix NetScaler SSL VPN a poskytuje další zabezpečení pro přihlášení k síti VPN Citrix NetScaler SSL a přístup k portálu.  Můžete použít protokol LDAP nebo RADIUS.  Vyberte jednu z následujících možností a stáhněte si podrobné pokyny pro konfiguraci krok za krokem.

| Průvodce konfigurací | Popis |
| --- | --- |
| [Konfigurace Citrix NetScaler SSL VPN a Azure MFA pro LDAP](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integrace sítě Citrix NetScaler SSL VPN s azure mfa zařízení pomocí LDAP |
| [Konfigurace Citrix NetScaler SSL VPN a Azure MFA pro RADIUS](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Integrace zařízení Citrix NetScaler SSL VPN s Azure MFA pomocí RADIUS |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>Zařízení VPN Juniper/Pulse Secure SSL a server Azure MFA
Azure MFA Server se integruje s vaším zařízením Juniper/Pulse Secure SSL VPN, aby zajistil další zabezpečení pro přihlášení k vpn Juniper/Pulse Secure SSL a přístup k portálu.  Můžete použít protokol LDAP nebo RADIUS.  Vyberte jednu z následujících možností a stáhněte si podrobné pokyny pro konfiguraci krok za krokem.

| Průvodce konfigurací | Popis |
| --- | --- |
| [Konfigurace Juniper/Pulse Secure SSL VPN a Azure MFA pro LDAP](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Integrace juniper/pulse secure ssl VPN s azure mfa zařízení pomocí LDAP |
| [Konfigurace Juniper/Pulse Secure SSL VPN a Azure MFA pro RADIUS](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Integrace zařízení Juniper/Pulse Secure SSL VPN s Azure MFA pomocí RADIUS |

## <a name="next-steps"></a>Další kroky

- [Rozšiřte svou stávající infrastrukturu ověřování o rozšíření NPS pro vícefaktorové ověřování Azure](howto-mfa-nps-extension.md)

- [Konfigurace nastavení služby Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)
