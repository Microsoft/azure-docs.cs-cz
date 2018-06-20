---
title: Přístup ke službě Key Vault za bránou firewall | Microsoft Docs
description: Zjistěte, jak přistupovat ke službě Azure Key Vault z aplikace za bránou firewall
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 50d21774-2ee1-4212-8995-570c9de603c5
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: ambapat
ms.openlocfilehash: d017c0d9940288cb5eeaa45694b324f93b9bb144
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736244"
---
# <a name="access-azure-key-vault-behind-a-firewall"></a>Přístup ke službě Azure Key Vault za bránou firewall
### <a name="q-my-key-vault-client-application-needs-to-be-behind-a-firewall-what-ports-hosts-or-ip-addresses-should-i-open-to-enable-access-to-a-key-vault"></a>Otázka: Moje klientská aplikace trezoru klíčů musí být za bránou firewall. Které porty, hostitele nebo IP adresy je nutné otevřít, pokud chci umožnit přístup k trezoru klíčů?
Pokud chcete umožnit přístup k trezoru klíčů, musí mít klientská aplikace trezoru klíčů přístup k několika koncovým bodům pro různé funkce:

* Ověřování prostřednictvím Azure Active Directory (Azure AD)
* Správa služby Azure Key Vault. Jedná se o vytváření, čtení, aktualizaci, odstraňování a nastavování zásad přístupu prostřednictvím Azure Resource Manageru.
* Přístup k objektům (klíčům a tajným kódům) uloženým ve službě Key Vault a správa těchto objektů probíhají přes koncový bod specifický pro službu Key Vault (například [https://yourvaultname.vault.azure.net](https://yourvaultname.vault.azure.net)).  

V závislosti na vaší konfiguraci a prostředí existuje několik variant.   

## <a name="ports"></a>Porty
Veškerý provoz směřující do trezoru klíčů pro všechny tři funkce (ověřování, správa a přístup k rovině dat) prochází přes protokol HTTPS: port 443. Nicméně u seznamu CRL může občas docházet k provozu přes protokol HTTP (na portu 80). Klienti podporující protokol OCSP by se na seznam CRL dostat neměli, občas se ale můžou dostat na [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl).  

## <a name="authentication"></a>Authentication
Klientské aplikace trezoru klíčů budou kvůli ověřování potřebovat přístup ke koncovým bodům Azure Active Directory. Použitý koncový bod závisí na konfiguraci tenanta Azure AD, typu objektu zabezpečení (uživatel nebo instanční objekt) a na typu účtu (například účet Microsoft nebo pracovní nebo školní účet).  

| Typ objektu zabezpečení | Koncový bod:port |
| --- | --- |
| Uživatel používající účet Microsoft<br> (například user@hotmail.com) |**Globální:**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.cn:443<br><br>**Azure US Government:**<br> login.microsoftonline.us:443<br><br>**Azure Germany:**<br> login.microsoftonline.de:443<br><br> a <br>login.live.com:443 |
| Uživatel nebo instanční objekt používající pracovní nebo školní účet s Azure AD (například user@contoso.com) |**Globální:**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.cn:443<br><br>**Azure US Government:**<br> login.microsoftonline.us:443<br><br>**Azure Germany:**<br> login.microsoftonline.de:443 |
| Uživatel nebo instanční objekt používající pracovní nebo školní účet a službu Active Directory Federation Services (AD FS) nebo jiný federovaný koncový bod (například user@contoso.com) |Všechny koncové body pro pracovní nebo školní účet a AD FS nebo jiné federované koncové body |

Existují i další možné komplexní scénáře. Další informace najdete v tématech [Azure Active Directory Authentication Flow](/documentation/articles/active-directory-authentication-scenarios/) (Tok ověřování Azure Active Directory), [Integrating Applications with Azure Active Directory](/documentation/articles/active-directory-integrating-applications/) (Integrace aplikací s Azure Active Directory) a [Active Directory Authentication Protocols](https://msdn.microsoft.com/library/azure/dn151124.aspx) (Ověřovací protokoly Active Directory).  

## <a name="key-vault-management"></a>Správa služby Key Vault
Pro správu služby Key Vault (CRUD a nastavení zásad přístupu) je nutné, aby klientská aplikace trezoru klíčů měla přístup ke koncovému bodu Azure Resource Manageru.  

| Typ operace | Koncový bod:port |
| --- | --- |
| Operace roviny řízení služby Key Vault<br> prostřednictvím Azure Resource Manageru |**Globální:**<br> management.azure.com:443<br><br> **Azure China:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> management.microsoftazure.de:443 |
| Azure Active Directory Graph API |**Globální:**<br> graph.windows.net:443<br><br> **Azure China:**<br> graph.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> graph.windows.net:443<br><br> **Azure Germany:**<br> graph.cloudapi.de:443 |

## <a name="key-vault-operations"></a>Operace služby Key Vault
Pro všechny operace správy objektů trezoru klíčů (klíče a tajné kódy) a kryptografické operace je nutné, aby klient trezoru klíčů měl přístup ke koncovému bodu trezoru klíčů. V závislosti na umístění trezoru klíčů se bude lišit přípona DNS koncového bodu. Koncový bod trezoru klíčů je ve formátu *název_trezoru*.*přípona_dns_konkrétní_oblasti*, jak je popsáno v tabulce níže.  

| Typ operace | Koncový bod:port |
| --- | --- |
| Operace, včetně kryptografických operací na klíčích; vytváření, čtení, aktualizace nebo odstraňování klíčů a tajných kódů; nastavování nebo získávání značek a jiných atributů objektů trezoru klíčů (klíče a tajné kódy) |**Globální:**<br> &lt;název_trezoru&gt;.vault.azure.net:443<br><br> **Azure China:**<br> &lt;název_trezoru&gt;.vault.azure.cn:443<br><br> **Azure US Government:**<br> &lt;název_trezoru&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> &lt;název_trezoru&gt;.vault.microsoftazure.de:443 |

## <a name="ip-address-ranges"></a>Rozsahy IP adres
Služba Key Vault používá jiné prostředky Azure, například infrastrukturu modelu PaaS. Proto není možné poskytnout konkrétní rozsah IP adres, který budou mít koncové body služby Key Vault v určitém čase. Pokud vaše brána firewall podporuje jenom rozsahy IP adres, přečtěte si dokument [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) (Rozsahy IP adres datového centra Microsoft Azure). Ověřování a identita (Azure Active Directory) je globální služba a může v případě selhání převzít služby v jiné oblasti nebo může bez předchozího upozornění dojít k přesunu provozu. V tomto scénáři by měly být všechny rozsahy IP adres uvedené v seznamu [IP adres pro ověřování a identitu](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity_ip) přidány do brány firewall.

## <a name="next-steps"></a>Další kroky
Pokud máte dotazy ke službě Key Vault, navštivte [fóra služby Azure Key Vault](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).

