---
title: Složitější scénáře s vícefaktorovým Ověřováním Azure a sítě VPN třetí strany
description: Krok za krokem konfigurace příručky pro Azure MFA integrovat s Cisco, Citrix a Juniper.
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.assetid: 1f94a214-d6f6-48a8-8a12-006b5896ae45
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: joflore
ms.openlocfilehash: 3d59be74ec73aa10bdfba5b58a1bff4630985259
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="advanced-scenarios-with-azure-multi-factor-authentication-and-third-party-vpn-solutions"></a>Složitější scénáře s Azure Multi-Factor Authentication a řešení VPN jiných výrobců
Azure Multi-Factor Authentication umožňuje bezproblémově připojit pomocí různých řešení VPN jiných výrobců. Tento článek se týká zařízení Cisco® ASA VPN, Citrix NetScaler SSL VPN zařízení a zařízení Juniper sítě zabezpečit přístup/Pulse Secure připojit zabezpečení SSL VPN. Jsme vytvořili konfigurace příručky k řešení těchto tří běžné zařízení. Aplikace Multi-Factor Authentication Server můžete také integrovat s nejvíce jiné systémy, které pomocí protokolu RADIUS, protokolu LDAP, IIS nebo ověřování založené na deklaracích identity ve službě AD FS. Můžete najít další podrobnosti v [konfigurací MFA Server](howto-mfaserver-deploy.md#next-steps).

## <a name="cisco-asa-vpn-appliance-and-azure-multi-factor-authentication"></a>Zařízení Cisco ASA VPN a ověřování Azure Multi-Factor Authentication
Vaše zařízení Cisco® ASA VPN zajistit další zabezpečení pro přihlášení Cisco AnyConnect® VPN a přístup k portálu se službou Azure Multi-Factor Authentication.  Pomocí protokolu RADIUS nebo LDAP.  Vyberte jednu z následujících způsobů stáhnout podrobnou konfiguraci podrobné příručky.

| Průvodce konfigurací | Popis |
| --- | --- |
| [Cisco ASA s Anyconnect VPN a Azure MFA konfigurací protokolu LDAP](http://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Vaše zařízení Cisco ASA VPN integrovat Azure MFA pomocí protokolu LDAP |
| [Cisco ASA s Anyconnect VPN a Azure MFA konfigurací protokolu RADIUS](http://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Vaše zařízení Cisco ASA VPN integrovat Azure MFA pomocí protokolu RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-multi-factor-authentication"></a>Citrix NetScaler SSL VPN a Azure Multi-Factor Authentication
Azure Multi-Factor Authentication se integruje s vaší Citrix NetScaler SSL VPN zařízení k zajištění další zabezpečení pro přihlášení Citrix NetScaler SSL VPN a přístup k portálu.  Pomocí protokolu RADIUS nebo LDAP.  Vyberte jednu z následujících způsobů stáhnout podrobnou konfiguraci podrobné příručky.

| Průvodce konfigurací | Popis |
| --- | --- |
| [Konfigurace SSL VPN nejen Citrix NetScaler a Azure MFA pro LDAP](http://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integrovat váš Citrix NetScaler SSL VPN Azure MFA zařízení pomocí protokolu LDAP |
| [Konfigurace Citrix NetScaler SSL VPN a Azure MFA pro server RADIUS](http://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Vaše zařízení Citrix NetScaler SSL VPN integrovat Azure MFA pomocí protokolu RADIUS |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-multi-factor-authentication"></a>Juniper/Pulse Secure SSL VPN zařízení a ověřování Azure Multi-Factor Authentication
Azure Multi-Factor Authentication se integruje s vaší Juniper/Pulse Secure SSL VPN zařízení k zajištění další zabezpečení pro přihlášení Juniper/Pulse Secure SSL VPN a přístup k portálu.  Pomocí protokolu RADIUS nebo LDAP.  Vyberte jednu z následujících způsobů stáhnout podrobnou konfiguraci podrobné příručky.

| Průvodce konfigurací | Popis |
| --- | --- |
| [Konfigurace Juniper/Pulse zabezpečení SSL VPN a Azure MFA pro LDAP](http://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Integrovat váš Juniper/Pulse Secure SSL VPN Azure MFA zařízení pomocí protokolu LDAP |
| [Konfigurace zabezpečení SSL VPN nejen Juniper/Pulse a Azure MFA protokolu RADIUS](http://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Vaše zařízení Juniper/Pulse Secure SSL VPN integrovat Azure MFA pomocí protokolu RADIUS |

## <a name="next-steps"></a>Další postup

- [Posílení vaší stávající infrastruktury pro ověřování s příponou NPS pro Azure Multi-Factor Authentication](howto-mfa-nps-extension.md)

- [Konfigurace nastavení služby Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)