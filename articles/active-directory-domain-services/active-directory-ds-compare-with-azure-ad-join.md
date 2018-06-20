---
title: Porovnání připojení k Azure AD a Azure Active Directory Domain Services | Microsoft Docs
description: Při rozhodování o tom mezi připojení k Azure AD a Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 31a71d36-58c1-4839-b958-80da0c6a77eb
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: maheshu
ms.openlocfilehash: 8bfc62f978b85399a64da32636627efc7ae234da
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36212549"
---
# <a name="choose-between-azure-active-directory-join-and-azure-active-directory-domain-services"></a>Zvolit připojení k Azure Active Directory a Azure Active Directory Domain Services
Tento článek popisuje rozdíly mezi připojení k Azure Active Directory (AD) a Azure AD Domain Services a pomůže vám zvolit založené na vaše případy použití.

## <a name="azure-ad-registered-and-azure-ad-joined-devices"></a>Zaregistrovat Azure AD a zařízení připojená k Azure AD
Azure AD umožňuje spravovat identity zařízení používaných ve vaší organizaci a řízení přístupu k firemním prostředkům z těchto zařízení. Uživatelé mohou registrovat svá osobní zařízení (přineste vlastní) s Azure AD, které poskytuje identitu zařízení. Azure AD můžete následně ověření zařízení, když se uživatel přihlásí ke službě Azure AD a zařízení používá pro přístup k zabezpečeným prostředkům. Navíc můžete spravovat zařízení pomocí softwaru správy mobilních zařízení (MDM), jako je Microsoft Intune. Tato funkce umožňuje omezit přístup k citlivým prostředkům ze spravovaných a zásad kompatibilních zařízení.

Můžete také připojit organizace, která zařízení do služby Azure AD. Tento mechanismus nabízí stejné výhody registrace osobní zařízení s Azure AD. Kromě toho mohou uživatelé přihlašují k zařízení pomocí svých podnikových přihlašovacích údajů. Azure AD, které jsou připojené k zařízení získáte následující výhody:
* Jednotného přihlašování (SSO) k aplikacím zabezpečeným službou Azure AD
* Enterprise vyhovovala zásadám roaming uživatelských nastavení mezi zařízeními.
* Přístup na web Windows Store pro firmy pomocí svých podnikových přihlašovacích údajů.
* Windows Hello pro firmy
* Omezený přístup k aplikacím a prostředkům ze zařízení, které jsou kompatibilní s podnikovými zásadami.

| **Typ zařízení** | **Platformy zařízení** | **Mechanismus** |
|:---| --- | --- |
| Osobní zařízení | Windows 10, iOS, Android, Mac OS | Zaregistrovat Azure AD |
| Organizace, které jsou ve vlastnictví zařízení není připojené k místní službě AD | Windows 10 | Připojené k Azure AD |
| Organizace, které jsou ve vlastnictví zařízení připojené k místní AD | Windows 10 | Hybridní připojený k Azure AD |

Na Azure AD připojený nebo zaregistrovaného zařízení, ověření uživatele se stane pomocí moderní OAuth/OpenID Connect, na základě protokolů. Tyto protokoly jsou navrženy pro práci přes internet a jsou velmi vhodné pro mobilních situacích, kde uživatelé přístup k podnikovým prostředkům odkudkoli.


## <a name="domain-join-to-azure-ad-domain-services-managed-domains"></a>Připojení k doméně spravované domény Azure AD Domain Services
Služba Azure AD Domain Services poskytuje spravované doméně AD v virtuální sítě Azure. Přidávání počítačů do této spravované doméně pomocí mechanismů tradiční připojení k doméně. Klient systému Windows (Windows 7, Windows 10) a počítačů Windows serveru je možné připojit k spravované doméně. Kromě toho můžete také připojit počítače se systémy Linux a Mac OS k spravované doméně. Když připojit počítač k doméně služby AD, uživatelé přihlásit k počítači pomocí své podnikové přihlašovací údaje. Tyto počítače můžete spravovat pomocí zásad skupiny, proto vynucování souladu se zásadami zabezpečení vaší organizace.

Na počítači připojeném k doméně ověření uživatele se stane, pomocí protokoly ověřování NTLM nebo Kerberos. Počítač připojený k doméně musí směrem pohledu na řadičích domény, spravované domény v pořadí pro ověřování uživatelů pro práci. Proto připojený k doméně počítače, musí být ve stejné virtuální síti jako spravované domény. Můžete také připojené do domény počítače musí být připojen k spravované doméně peered virtuální síti nebo prostřednictvím připojení site-to-site VPN nebo ExpressRoute. Proto tento mechanismus není skvělé přizpůsobit pro zařízení, která jsou mobilní nebo připojení k prostředkům z vnějšku podnikové sítě.

> [!NOTE]
> Technicky je možné připojit k místní pracovní stanici klienta k spravované doméně přes připojení site-to-site VPN nebo ExpressRoute. Ale pro koncového uživatele zařízení, která vám doporučujeme, že abyste použili buď registrace zařízení s Azure AD (osobní zařízení), nebo připojit zařízení k Azure AD (podnikové zařízení). Tento mechanismus funguje lépe přes internet a umožňuje koncovým uživatelům pracovat odkudkoli. Služba Azure AD Domain Services je skvělá pro Windows nebo Linux Server virtuálních počítačů nasazených v Azure virtuálních sítí, na kterých jsou nasazené aplikace.


## <a name="summary---key-differences"></a>Souhrn – hlavní rozdíly
| **Aspekt** | **Azure AD Join** | **Služba Azure AD Domain Services** |
|:---| --- | --- |
| Zařízení, které řídí | Azure AD | Spravované doméně služby Azure AD Domain Services |
| Reprezentace v adresáři | Objekty zařízení v adresáři služby Azure AD. | Počítačových objektů ve spravované doméně AAD DS. |
| Authentication | OAuth/OpenID Connect na základě protokolů | Protokol Kerberos, protokoly NTLM |
| Správa | Software správy mobilních zařízení (MDM) Intune | Zásada skupiny |
| Sítě | Funguje přes internet | Vyžaduje, aby se ve stejné virtuální síti jako spravované domény počítače.|
| Je vhodná k... | Mobilních nebo desktopových zařízení koncového uživatele | Server virtuálních počítačů nasazených v Azure |


## <a name="next-steps"></a>Další postup
### <a name="learn-more-about-azure-ad-domain-services"></a>Další informace o Azure AD Domain Services
* [Přehled služby Azure AD Domain Services](active-directory-ds-overview.md)
* [Funkce](active-directory-ds-features.md)
* [Scénáře nasazení](active-directory-ds-scenarios.md)
* [Zjistěte, pokud Azure AD Domain Services vyhovuje případy použití](active-directory-ds-comparison.md)
* [Pochopit, jak Azure AD Domain Services synchronizován s adresářem služby Azure AD](active-directory-ds-synchronization.md)

### <a name="learn-more-about-azure-ad-join"></a>Další informace o připojení ke službě Azure AD
* [Úvod do správy zařízení v Azure Active Directory](../active-directory/device-management-introduction.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Začínáme s Azure AD Domain Services
* [Povolení služby Azure AD Domain Services pomocí portálu Azure](active-directory-ds-getting-started.md)
