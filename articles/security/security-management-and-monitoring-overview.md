---
title: Správa zabezpečení Azure a Přehled monitorování | Microsoft Docs
description: " Azure nabízí mechanismy zabezpečení, které pomáhají při správy a monitorování cloudových služeb Azure a virtuálních počítačů.  Tento článek obsahuje přehled těchto klíčových funkcí zabezpečení a služeb. "
services: security
documentationcenter: na
author: TerryLanfear
manager: StevenPo
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: terrylan
ms.openlocfilehash: 66a3a31574778bb1238498b523d51dc0d32ac22f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="azure-security-management-and-monitoring-overview"></a>Správa zabezpečení Azure a Přehled monitorování
Azure nabízí mechanismy zabezpečení, které pomáhají při správy a monitorování cloudových služeb Azure a virtuálních počítačů. Tento článek obsahuje přehled těchto klíčových funkcí zabezpečení a služeb. Jsou uvedeny odkazy na články, které poskytují podrobnosti o jednotlivých tak další informace.

Zabezpečení vašich cloudových službách společnosti Microsoft je spolupráci a sdílenou odpovědnost mezi vámi a společností Microsoft. Sdílené odpovědnost znamená, že Microsoft je odpovědná za Microsoft Azure a fyzického zabezpečení svá datového centra (pomocí ochranu zabezpečení, jako je například uzamčeném oznámení "BADGE" položku dveří, ochranné a chrání). Kromě toho Azure poskytuje silné úrovně zabezpečení cloudu ve vrstvě software, který splňuje potřeby zabezpečení, ochrany osobních údajů a dodržování předpisů jeho náročné zákazníků.

Vlastníte vašich dat a identity, odpovědnost za ochranu jejich, zabezpečení místních prostředků a zabezpečení součástí cloudu, nad kterými nemáte kontrolu. Společnost Microsoft vám poskytne kontrolních mechanismů pro zabezpečení a funkcí, které vám umožní chránit vaše data a aplikace. Vaše stupeň odpovědnost za zabezpečení je založený na typu cloudové služby.

Následující graf shrnuje rovnováhu mezi počtem odpovědnost za společnosti Microsoft a Zákazník.

![Sdílená odpovědnost][1]

Podrobnější prohlídku do správy zabezpečení, najdete v části [Správa zabezpečení v Azure](azure-security-management.md).

Zde jsou základní funkce, které chcete být popsaná v tomto článku:

* Řízení přístupu na základě rolí
* Antimalware
* Multi-factor Authentication
* ExpressRoute
* Brány virtuální sítě
* Správa privilegovaných identit
* Ochrana identit
* Security Center

## <a name="role-based-access-control"></a>Řízení přístupu na základě rolí
Řízení přístupu na základě rolí (RBAC) poskytuje přesnou správu přístupu k prostředkům Azure. Pomocí RBAC, můžete udělit osoby pouze takovou úroveň přístupu, které potřebují k provádění svých úloh.  RBAC také vám pomůže zkontrolovat, že při osoby nechte organizace se ztratit přístup k prostředkům v cloudu.

Další informace:

* [Blog týmu Active Directory na RBAC](http://i1.blogs.technet.com/b/ad/archive/2015/10/12/azure-rbac-is-ga.aspx)
* [Řízení přístupu Azure na základě rolí](../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Antimalware
S Azure můžete použít antimalwarový software od dodavatelů hlavní zabezpečení, jako je Microsoft, Symantec, Trend Micro, McAfee a Kaspersky k ochraně vašich virtuálních počítačů ze škodlivých souborů, adwaru a dalšími hrozbami.

Antimalware od Microsoftu je k dispozici možnost nainstalovat agenta antimalwarových pro rolí PaaS a virtuální počítače. Podle toho, System Center Endpoint Protection, tato funkce přináší Principy místní technologie zabezpečení do cloudu.

Nabízíme těsná integrace pro je Trend [hloubkové zabezpečení](http://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/)™ a [SecureCloud](http://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/)™ produkty ve platformy Azure. DeepSecurity je antivirový řešení a SecureCloud je řešení pro šifrování. DeepSecurity je nasadit do virtuálních počítačů pomocí model rozšíření. Pomocí portálu uživatelského rozhraní a prostředí PowerShell, můžete použít DeepSecurity uvnitř nové virtuální počítače, které jsou právě spuštěné, nebo existující virtuální počítače, které jsou už nasazené.

Symantec koncového bodu ochrany (září) je podporováno také v Azure. Prostřednictvím integrace portálu můžete zadat zákazníků, mají v úmyslu použít září ve virtuálním počítači. ZÁŘÍ se dá nainstalovat na úplně nového virtuálního počítače prostřednictvím portálu Azure nebo se dá nainstalovat na existující virtuální počítač pomocí prostředí PowerShell.

Další informace:

* [Nasazování antimalwarových řešení na virtuálních počítačích Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Antimalware od Microsoftu pro cloudové služby Azure a virtuální počítače](azure-security-antimalware.md)
* [Postup instalace a konfigurace Trend Micro hluboké Security jako služba na virtuální počítač s Windows](../virtual-machines/windows/classic/install-trend.md)
* [Postup instalace a konfigurace Symantec Endpoint Protection na virtuální počítač s Windows](../virtual-machines/windows/classic/install-symantec.md)
* [Nové možnosti Antimalwarové ochrany virtuálních počítačů Azure – McAfee Endpoint Protection](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-factor Authentication
Azure Multi-Factor authentication (MFA) je metoda ověřování, který vyžaduje použití víc než jednu metodu ověřování a přidá velmi důležitou druhou vrstvu zabezpečení uživatelská přihlášení a transakce. MFA pomáhá chránit přístup k datům a aplikacím při splnění požadavků uživatelů pro jednoduchý proces přihlášení. Zajišťuje silné ověřování přes celou řadu možností ověření – telefonní hovor, textová zpráva nebo mobilní aplikace oznámení nebo ověřovací kód a třetích stran tokeny OATH.

Další informace:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Co je Azure Multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md)
* [Jak funguje Azure Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute
Microsoft Azure ExpressRoute umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes vyhrazené soukromé připojení zajišťované poskytovatelem připojení. Pomocí ExpressRoute může vytvořit připojení ke cloudovým službám Microsoftu, jako je například Microsoft Azure, Office 365 a CRM Online. Co se týká připojení, může se jednat o síť typu any-to-any (IP VPN), síť Ethernet typu point-to-point nebo virtuální křížové připojení prostřednictvím poskytovatele připojení ve společném umístění. Připojení ExpressRoute se nepřenášejí prostřednictvím veřejného internetu. To dovoluje připojením ExpressRoute poskytovat větší spolehlivost, vyšší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes internet.

Další informace:

* [Technický přehled ExpressRoute](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Brány virtuální sítě
Brány sítě VPN, označované taky jako Azure brány virtuální sítě, se používají k posílání síťového provozu mezi virtuálními sítěmi a místními umístění. Používají se také k posílání provozu mezi více virtuálních sítí v rámci Azure (VNet-to-VNet).  Brány sítě VPN poskytují zabezpečení mezi různými místy připojení mezi Azure a infrastruktury.

Další informace:

* [O branách VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Přehled zabezpečení sítě Azure](security-network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management
Uživatelé někdy potřebovat provádět privilegované operace v prostředků Azure nebo jiné aplikace SaaS. To často znamená, že organizace mají a umožnit jim trvalé privilegovaný přístup v Azure Active Directory (Azure AD). Je to rostoucí bezpečnostní riziko pro hostované cloudové prostředky, proto organizace nelze monitorovat dostatečně co tito uživatelé pracují s jejich privilegovaný přístup.
Navíc pokud uživatelský účet s privilegovaného přístupu je ohrožen, že jeden porušení zabezpečení by mohlo mít vliv celkového zabezpečení cloudu. Azure AD Privileged Identity Management pomáhá vyřešit toto riziko snížit čas ohrožení oprávnění a zvýšení získat přehled o využití.  

Správa privilegovaných identit zavádí koncepci dočasný správce pro roli nebo "pouze v čase" přístup správce, který je uživatel, který musí dokončit proces aktivace pro tento přiřazenou roli. Proces aktivace změní přiřazení uživatele k roli ve službě Azure AD z neaktivní na aktivní, pro zadané časové období, například 8 hodin.

Další informace:

* [Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md)
* [Začínáme s Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-getting-started.md)

## <a name="identity-protection"></a>Identity Protection
Ochrany identit Azure Active Directory (AD) poskytuje ucelený přehled podezřelé aktivity přihlášení a potenciální ohrožení zabezpečení k ochraně vaší firmy. Ochrana identity zjištění podezřelých aktivit pro uživatele a identity oprávněními (správce), podle signály jako útoku hrubou silou úniku přihlašovacích údajů a přihlášení z neznámých míst a nakažených zařízení.

Zadáním oznámení a doporučené nápravy Identity Protection pomáhá zmírnit rizika v reálném čase. Počítá závažnost riziko uživatelů a můžete nakonfigurovat na základě riziko zásady automaticky pomáhají chránit aplikaci přístup z budoucích hrozeb.

Další informace:

* [Ochrany identit Azure Active Directory](../active-directory/active-directory-identityprotection.md)
* [Kanál 9: Azure AD a Identity zobrazení: Identity Protection verze Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Security Center
Azure Security Center pomáhá zabránit, zjistit a reagovat na hrozby a poskytuje že vyšší přehled a kontrolu nad, zabezpečení vašich prostředků Azure. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, kterých byste si jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

Security Center pomáhá optimalizovat a monitorování zabezpečení vašich prostředků Azure pomocí:

* Umožňuje definovat zásady pro vaše předplatné Azure prostředky podle potřeb zabezpečení vaší společnosti a podle typu aplikací nebo citlivosti dat v každém předplatném.
* Monitorování stavu virtuální počítače Azure, sítě a aplikace.
* Poskytuje seznam upřednostňovaných výstrah zabezpečení, včetně výstrahy z integrovaných partnerských řešení, spolu s informací, které potřebujete k rychlému prozkoumání a doporučeními týkajícími se řešení útoku.

Další informace:

* [Úvod do Azure Security Center](../security-center/security-center-intro.md)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png
