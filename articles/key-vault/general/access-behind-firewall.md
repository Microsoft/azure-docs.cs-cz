---
title: Přístup k Key Vault za bránou firewall Azure Key Vault | Microsoft Docs
description: Přečtěte si informace o portech, hostitelích nebo IP adresách, které se mají otevřít, a povolením klientské aplikace trezoru klíčů za bránou firewall pro přístup k trezoru klíčů
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 1ab5ae7bf9f1d13458e3bbeeec564fe642eb3303
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88588724"
---
# <a name="access-azure-key-vault-behind-a-firewall"></a>Přístup ke službě Azure Key Vault za bránou firewall

## <a name="what-ports-hosts-or-ip-addresses-should-i-open-to-enable-my-key-vault-client-application-behind-a-firewall-to-access-key-vault"></a>Jaké porty, hostitele nebo IP adresy mám otevřít a povolit tak aplikaci klienta trezoru klíčů za bránou firewall pro přístup k trezoru klíčů?

Pokud chcete umožnit přístup k trezoru klíčů, musí mít klientská aplikace trezoru klíčů přístup k několika koncovým bodům pro různé funkce:

* Ověřování prostřednictvím Azure Active Directory (Azure AD)
* Správa služby Azure Key Vault. Jedná se o vytváření, čtení, aktualizaci, odstraňování a nastavování zásad přístupu prostřednictvím Azure Resource Manageru.
* Přístup k objektům (klíčům a tajným kódům) uloženým ve službě Key Vault a správa těchto objektů probíhají přes koncový bod specifický pro službu Key Vault (například `https://yourvaultname.vault.azure.net`).  

V závislosti na vaší konfiguraci a prostředí existuje několik variant.

## <a name="ports"></a>Porty

Veškerý provoz směřující do trezoru klíčů pro všechny tři funkce (ověřování, správa a přístup k rovině dat) prochází přes protokol HTTPS: port 443. Nicméně u seznamu CRL může občas docházet k provozu přes protokol HTTP (na portu 80). Klienti, kteří podporují protokol OCSP, by neměli mít přístup k seznamu CRL, ale můžou se [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl) k nim občas  

## <a name="authentication"></a>Authentication

Klientské aplikace trezoru klíčů budou kvůli ověřování potřebovat přístup ke koncovým bodům Azure Active Directory. Použitý koncový bod závisí na konfiguraci tenanta Azure AD, typu objektu zabezpečení (uživatel nebo instanční objekt) a na typu účtu (například účet Microsoft nebo pracovní nebo školní účet).  

| Typ objektu zabezpečení | Koncový bod:port |
| --- | --- |
| Uživatel používající účet Microsoft<br> (například user@hotmail.com) |**Globální**<br> login.microsoftonline.com:443<br><br> **Azure Čína:**<br> login.chinacloudapi.cn:443<br><br>**Státní správa USA Azure:**<br> login.microsoftonline.us:443<br><br>**Azure Německo:**<br> login.microsoftonline.de:443<br><br> a <br>login.live.com:443 |
| Uživatel nebo instanční objekt používající pracovní nebo školní účet s Azure AD (například user@contoso.com) |**Globální**<br> login.microsoftonline.com:443<br><br> **Azure Čína:**<br> login.chinacloudapi.cn:443<br><br>**Státní správa USA Azure:**<br> login.microsoftonline.us:443<br><br>**Azure Německo:**<br> login.microsoftonline.de:443 |
| Uživatel nebo instanční objekt používající pracovní nebo školní účet a službu Active Directory Federation Services (AD FS) nebo jiný federovaný koncový bod (například user@contoso.com) |Všechny koncové body pro pracovní nebo školní účet a AD FS nebo jiné federované koncové body |

Existují i další možné komplexní scénáře. Další informace najdete v tématech [Azure Active Directory Authentication Flow](../../active-directory/develop/authentication-scenarios.md) (Tok ověřování Azure Active Directory), [Integrating Applications with Azure Active Directory](../../active-directory/develop/active-directory-how-to-integrate.md) (Integrace aplikací s Azure Active Directory) a [Active Directory Authentication Protocols](https://msdn.microsoft.com/library/azure/dn151124.aspx) (Ověřovací protokoly Active Directory).  

## <a name="key-vault-management"></a>Správa služby Key Vault

Pro správu služby Key Vault (CRUD a nastavení zásad přístupu) je nutné, aby klientská aplikace trezoru klíčů měla přístup ke koncovému bodu Azure Resource Manageru.  

| Typ operace | Koncový bod:port |
| --- | --- |
| Operace roviny řízení služby Key Vault<br> prostřednictvím Azure Resource Manageru |**Globální**<br> management.azure.com:443<br><br> **Azure Čína:**<br> management.chinacloudapi.cn:443<br><br> **Státní správa USA Azure:**<br> management.usgovcloudapi.net:443<br><br> **Azure Německo:**<br> management.microsoftazure.de:443 |
| Microsoft Graph API |**Globální**<br> graph.microsoft.com:443<br><br> **Azure Čína:**<br> graph.chinacloudapi.cn:443<br><br> **Státní správa USA Azure:**<br> graph.microsoft.com:443<br><br> **Azure Německo:**<br> graph.cloudapi.de:443 |

## <a name="key-vault-operations"></a>Operace služby Key Vault

Pro všechny operace správy objektů trezoru klíčů (klíče a tajné kódy) a kryptografické operace je nutné, aby klient trezoru klíčů měl přístup ke koncovému bodu trezoru klíčů. V závislosti na umístění trezoru klíčů se bude lišit přípona DNS koncového bodu. Koncový bod trezoru klíčů je ve formátu *název_trezoru*.*přípona_dns_konkrétní_oblasti*, jak je popsáno v tabulce níže.  

| Typ operace | Koncový bod:port |
| --- | --- |
| Operace, včetně kryptografických operací na klíčích; vytváření, čtení, aktualizace nebo odstraňování klíčů a tajných kódů; nastavování nebo získávání značek a jiných atributů objektů trezoru klíčů (klíče a tajné kódy) |**Globální**<br> &lt;název_trezoru&gt;.vault.azure.net:443<br><br> **Azure Čína:**<br> &lt;název_trezoru&gt;.vault.azure.cn:443<br><br> **Státní správa USA Azure:**<br> &lt;název_trezoru&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Německo:**<br> &lt;název_trezoru&gt;.vault.microsoftazure.de:443 |

## <a name="ip-address-ranges"></a>Rozsahy IP adres

Služba Key Vault používá jiné prostředky Azure, například infrastrukturu modelu PaaS. Proto není možné poskytnout konkrétní rozsah IP adres, který budou mít koncové body služby Key Vault v určitém čase. Pokud vaše brána firewall podporuje jenom rozsahy IP adres, přečtěte si téma Microsoft Azure dokumenty rozsahů IP adres datového centra dostupné na adrese:
* [Republik](https://www.microsoft.com/en-us/download/details.aspx?id=56519)
* [US Gov](https://www.microsoft.com/en-us/download/details.aspx?id=57063)
* [Německo](https://www.microsoft.com/en-us/download/details.aspx?id=57064)
* [Čína](https://www.microsoft.com/en-us/download/details.aspx?id=57062)

Ověřování a identita (Azure Active Directory) je globální služba a může v případě selhání převzít služby v jiné oblasti nebo může bez předchozího upozornění dojít k přesunu provozu. V tomto scénáři by měly být všechny rozsahy IP adres uvedené v seznamu [IP adres pro ověřování a identitu](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity_ip) přidány do brány firewall.

## <a name="next-steps"></a>Další kroky

Pokud máte dotazy týkající se Key Vault, přejděte na [stránku s dotazem Microsoft Q&Azure Key Vault](https://docs.microsoft.com/answers/topics/azure-key-vault.html).
