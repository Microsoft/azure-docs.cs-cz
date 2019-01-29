---
title: Porovnání připojení ke službě Azure AD a Azure Active Directory Domain Services | Dokumentace Microsoftu
description: Rozhodování mezi připojení ke službě Azure AD a Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 31a71d36-58c1-4839-b958-80da0c6a77eb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: ergreenl
ms.openlocfilehash: d4f50ea89f2623d387fb77acb09e609def547468
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55178932"
---
# <a name="choose-between-azure-active-directory-join-and-azure-active-directory-domain-services"></a>Výběr mezi připojení k Azure Active Directory a Azure Active Directory Domain Services
Tento článek popisuje rozdíly mezi připojení ke službě Azure Active Directory (AD) a Azure AD Domain Services a pomůže vám zvolit založené na případy použití.

## <a name="azure-ad-registered-and-azure-ad-joined-devices"></a>Azure AD zaregistrované a zařízení připojená k Azure AD
Azure AD umožňuje spravovat identity zařízení používaná ve vaší organizaci a řízení přístupu k firemním prostředkům z těchto zařízení. Uživatelé můžou registrovat svoje osobní zařízení (přineste vlastní) s Azure AD, která poskytuje identitu zařízení. Azure AD můžete následně ověření zařízení, když uživatel přihlásí ke službě Azure AD a používá zařízení pro přístup k zabezpečeným prostředkům. Kromě toho můžete spravovat zařízení s využitím softwaru správy mobilních zařízení (MDM), jako je Microsoft Intune. Tato funkce umožňuje omezit přístup k citlivým prostředkům ze zařízení spravovaná a zásadám.

Můžete také připojit k organizaci zařízení do služby Azure AD. Tento mechanismus nabízí stejné výhody jako registrace osobních zařízení s Azure AD. Uživatelé mohou také přihlásit k zařízení pomocí svých podnikových přihlašovacích údajů. Podpora k zařízením Azure AD, které jsou připojené k získáte následující výhody:
* Jednotného přihlašování (SSO) k aplikacím, které jsou zabezpečené pomocí Azure AD
* Podnikový zásadám roaming uživatelská nastavení mezi zařízeními.
* Přístup k Windows Store pro firmy pomocí svých firemních přihlašovacích údajů.
* Windows Hello pro firmy
* Omezený přístup k aplikacím a prostředkům ze zařízení dodržovalo firemní zásady.

| **Typ zařízení** | **Platformy zařízení** | **Mechanismus** |
|:---| --- | --- |
| Osobní zařízení | Windows 10, iOS, Android, Mac OS | Registrováno v Azure AD |
| Organizace, které jsou ve vlastnictví zařízení není připojené k místní AD | Windows 10 | Připojeno k Azure AD |
| Organizace, které jsou připojené k místní zařízení ve vlastnictví firmy AD | Windows 10 | Připojená k hybridní službě Azure AD |

V Azure AD připojen nebo registrovaná zařízení, ověření uživatele se stane pomocí moderních OAuth/OpenID Connect na základě protokolů. Tyto protokoly jsou navrženy pro práci v síti internet a jsou skvělé pro mobilní scénáře, ve kterém uživatelé přístup k firemním prostředkům z libovolného místa.


## <a name="domain-join-to-azure-ad-domain-services-managed-domains"></a>Připojení k doméně do spravované domény Azure AD Domain Services
Azure AD Domain Services poskytuje spravovanou doménu AD ve službě Azure virtual network. Připojení počítačů k této spravované doméně pomocí mechanismů tradiční připojení k doméně. Klient Windows (Windows 7, Windows 10) a počítače s Windows serverem může být připojen k spravované doméně. Kromě toho můžete také připojit počítače s Linuxem a Mac OS ke spravované doméně. Po připojení počítače k doméně služby AD, uživatelé mohou přihlásit k počítači pomocí svých podnikových přihlašovacích údajů. Tyto počítače můžete spravovat pomocí zásad skupiny, tedy vynucování dodržování předpisů se zásadami zabezpečení vaší organizace.

Na počítači připojeném k doméně dojde ověřování uživatelů pomocí ověřování protokolů NTLM nebo Kerberos. Na počítači připojeném k doméně musí dohlednost služby k řadičům domény spravované doméně, aby pro ověřování uživatelů pro práci. Proto připojený k doméně počítače musí být ve stejné virtuální síti jako spravovanou doménu. Alternativně připojený k doméně počítače musí být připojené ke spravované doméně přes partnerské virtuální síti nebo prostřednictvím připojení site-to-site VPN nebo ExpressRoute. Proto tento mechanismus není skvěle hodí k zařízení, která jsou mobilní nebo připojení k prostředkům z mimo podnikovou síť.

> [!NOTE]
> Technicky je možné připojení k pracovní stanici s místními klienta k spravované doméně přes připojení site-to-site VPN nebo ExpressRoute. Ale pro zařízení koncových uživatelů, důrazně doporučujeme, že používáte registraci zařízení v Azure AD (osobní zařízení) nebo připojit zařízení k Azure AD (zařízení). Tento mechanismus fungovala lépe přes internet a umožňuje koncovým uživatelům pracovat odkudkoli. Azure AD Domain Services se skvěle hodí pro Windows nebo Linux Server virtuální počítače nasazené ve vaší virtuální sítě Azure, na kterých jsou nasazené aplikace.


## <a name="summary---key-differences"></a>Shrnutí – klíčové rozdíly
| **Aspekt** | **Azure AD Join** | **Azure AD Domain Services** |
|:---| --- | --- |
| Řídí zařízení | Azure AD | Azure AD Domain Services managed domain |
| Reprezentace v adresáři | Objekty zařízení v adresáři Azure AD. | Počítačových objektů ve spravované doméně AAD DS. |
| Authentication | Protokoly založené na účtu OAuth/OpenID Connect | Protokol Kerberos, NTLM protokoly |
| Správa | Software správy mobilních zařízení (MDM), jako je Intune | Zásada skupiny |
| Sítě | Funguje přes internet | Vyžaduje počítače být ve stejné virtuální síti jako spravovanou doménu.|
| Velmi vhodné pro... | Desktopové nebo mobilní zařízení koncových uživatelů | Server virtuálních počítačů nasazených v Azure |


## <a name="next-steps"></a>Další postup
### <a name="learn-more-about-azure-ad-domain-services"></a>Další informace o službě Azure AD Domain Services
* [Přehled služby Azure AD Domain Services](active-directory-ds-overview.md)
* [Funkce](active-directory-ds-features.md)
* [Scénáře nasazení](active-directory-ds-scenarios.md)
* [Zjistěte, pokud Azure AD Domain Services vyhovuje případy použití](active-directory-ds-comparison.md)
* [Vysvětlení, jak Azure AD Domain Services synchronizován s adresářem Azure AD](active-directory-ds-synchronization.md)

### <a name="learn-more-about-azure-ad-join"></a>Další informace o připojení ke službě Azure AD
* [Úvod do správy zařízení v Azure Active Directory](../active-directory/device-management-introduction.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Začínáme s Azure AD Domain Services
* [Povolení služby Azure AD Domain Services pomocí webu Azure portal](active-directory-ds-getting-started.md)
