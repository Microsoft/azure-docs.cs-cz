---
title: Přehled monitorování a Správa zabezpečení Azure | Dokumentace Microsoftu
description: Tento článek obsahuje přehled funkcí zabezpečení a služeb, které Azure nabízí pro správu a monitorování Azure cloud services a virtual machines.
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
ms.openlocfilehash: 10c2ed359fa77ad00945ddcfbc55dc0901ba8bff
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38697101"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Přehled monitorování a Správa zabezpečení Azure
Azure nabízí mechanismy zabezpečení pro správu a monitorování Azure cloud services a virtual machines (VM). Tento článek obsahuje přehled těchto základní funkce zabezpečení a služeb. Jsou uvedeny odkazy na články, které poskytují podrobnosti o každé tak další informace.

Zabezpečení cloudových služeb Microsoftu je partnerství a sdílenou odpovědnost mezi vámi a společností Microsoft. Microsoft je zodpovědná za platformy Azure a fyzické zabezpečení svých datových center (využívala bezpečnostní ochranu, jako jsou uzamčené oznámení "BADGE" entry dveří, ohrazení a chrání). Azure poskytuje silné úrovně nad zabezpečením cloudu ve vrstvě software, který splňuje požadavky dodržování předpisů, zabezpečení a ochrany osobních údajů svých zákazníků.

Vlastníte vašich dat a identity, odpovědnost za chrání jejich zabezpečení místních prostředků a zabezpečení cloudové komponenty, nad kterými nemáte kontrolu. Microsoft nabízí ovládací prvky zabezpečení a funkce, které pomáhají chránit vaše data a aplikace. Vaše stupeň odpovědnost za zabezpečení podle typu cloudovou službu.

Následující diagram obsahuje souhrn zůstatek odpovědnost mezi společnostmi Microsoft a zákazníka.

![Sdílená odpovědnost][1]

Další informace o správě zabezpečení najdete v tématu [Správa zabezpečení v Azure](azure-security-management.md).

## <a name="role-based-access-control"></a>Řízení přístupu na základě rolí
Řízení přístupu na základě rolí (RBAC) poskytuje správu podrobné přístupu pro prostředky Azure. Pomocí RBAC můžete udělit uživatelům pouze takovou úroveň přístupu, které potřebují ke své práci. RBAC také vám pomůže zkontrolovat, že když lidí opustí organizaci, aby nepřišla o přístup k prostředkům v cloudu.

Další informace:

* [Blogu týmu Active Directory v RBAC](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Řízení přístupu na základě rolí Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Antimalware
S Azure můžete použít antimalwarový software od hlavních dodavatelů zabezpečení, jako je například Microsoft, Symantec, Trend Micro, McAfee a Kaspersky. Tento software pomáhá chránit virtuální počítače před škodlivými soubory, adwarem a dalšími hrozbami.

Microsoft Antimalware pro Azure Cloud Services a Virtual Machines nabízí možnost instalace agenta antimalwaru pro role PaaS a virtuální počítače. Založené na System Center Endpoint Protection, tato funkce přináší prověřené místní technologie zabezpečení do cloudu.

Nabízíme také hluboce integrovány se pro společnosti Trend [Deep Security](http://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) a [SecureCloud](http://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) produkty na platformě Azure. Deep Security je antivirová řešení a SecureCloud je řešení pro šifrování. Deep Security se nasazuje uvnitř virtuální počítače prostřednictvím modelu rozšíření. Pomocí webu Azure portal uživatelského rozhraní a prostředí PowerShell můžete použít Deep Security uvnitř nové virtuální počítače, které jsou prováděných výpočtů nebo existující virtuální počítače, které jsou už nasazené.

Symantec Endpoint Protection (SEP) je také podporována na Azure. Prostřednictvím integrace portálu můžete určit, že máte v úmyslu použít září na virtuálním počítači. ZÁŘÍ se dá nainstalovat na nový virtuální počítač prostřednictvím portálu Azure portal, nebo je možné nainstalovat na existující virtuální počítač pomocí Powershellu.

Další informace:

* [Nasazování antimalwarových řešení na virtuálních počítačích Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Microsoft Antimalware pro Azure Cloud Services a Virtual Machines](azure-security-antimalware.md)
* [Jak nainstalovat a nakonfigurovat Trend Micro Deep Security jako službu na virtuálním počítači s Windows](../virtual-machines/windows/classic/install-trend.md)
* [Jak nainstalovat a nakonfigurovat Symantec Endpoint Protection na virtuálním počítači s Windows](../virtual-machines/windows/classic/install-symantec.md)
* [Nové možnosti Antimalwarové ochrany virtuálních počítačů Azure](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Azure Multi-Factor Authentication je metoda ověřování, který vyžaduje použití víc ověřovacích metod současně. Přidá velmi důležitou druhou vrstvu zabezpečení uživatelská přihlášení a transakce. 

Multi-Factor Authentication pomáhá chránit přístup k datům a aplikace a současně plní požadavky uživatelů na jednoduchý přihlašovací proces. Nabízí silné ověřování pomocí řady možností ověřování (telefonní hovor, textová zpráva nebo oznámení nebo ověřovací kód z mobilní aplikace) a tokeny OATH třetích stran.

Další informace:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Co je Azure Multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md)
* [Jak funguje ověřování Azure Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute
Azure ExpressRoute můžete rozšířit vaše místní sítě do cloudu Microsoftu přes vyhrazené soukromé připojení zajišťované poskytovatelem připojení. Pomocí ExpressRoute může vytvořit připojení ke cloudovým službám Microsoftu, jako je například Azure, Office 365 a CRM Online. Připojení může být od:

- Síti typu any-to-any (IP VPN).
- Síť Ethernet typu point-to-point.
- Virtuální křížové připojení prostřednictvím poskytovatele připojení ve společném umístění. 

Spojení ExpressRoute nevyužívají veřejný internet. Můžou nabídnout více spolehlivost, vyšší rychlost, nižší latenci a lepší zabezpečení než Typická připojení přes internet.

Další informace:

* [Technický přehled ExpressRoute](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Brány virtuální sítě
Brány sítě VPN, také jako brány virtuální sítě Azure, se používají ke směrování síťového provozu mezi virtuálními sítěmi a místními umístěními. Používají se také k posílání síťového provozu mezi virtuálními sítěmi v rámci Azure (k síťovým). Brány sítě VPN poskytují zabezpečené připojení mezi lokalitami mezi Azure a vaší infrastrukturou.

Další informace:

* [O branách VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Přehled zabezpečení sítě Azure](security-network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management
Někdy uživatelé budou muset provádět privilegované operace prostředků Azure nebo dalším aplikacím SaaS. To často znamená, že organizace svým uživatelům umožnit trvalé privilegovaný přístup v Azure Active Directory (Azure AD). 

To je rostoucí bezpečnostní riziko pro prostředky hostované v cloudu, protože organizace nemůže monitorovat dostatečně jak uživatelé pracují s jejich privilegovaný přístup. Kromě toho případě ohrožení bezpečnosti uživatelského účtu s privilegovaným přístupem tento jeden porušení zabezpečení může ovlivnit celkové zabezpečení cloudu organizace. Azure AD Privileged Identity Management pomáhá vyřešit toto riziko snížit dobu expozice oprávnění a zvyšuje přehled o využití.  

Privileged Identity Management zavádí pojem dočasné správce pro roli nebo "just in time" přístup správce. Tento druh správce je uživatel, který dokončí proces aktivace pro, která se přiřadila role je potřeba. Proces aktivace změní přiřazení uživatele k roli ve službě Azure AD z aktivní na určitou dobu aktivní období.

Další informace:

* [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)
* [Začínáme s Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>Identity Protection
Služba Azure AD Identity Protection poskytuje ucelený přehled podezřelých aktivit přihlašování a potenciálních chybách zabezpečení k ochraně vaší firmy. Ochrana identity detekuje podezřelé aktivity pro uživatele a identity oprávněními (správce), na základě signálů, jako jsou:

- Útoky hrubou silou.
- Uniklé přihlašovací údaje.
- Přihlásil z neznámých umístění a nakažených zařízení.

Tím, že poskytuje oznámení a doporučenou nápravu, Identity Protection pomáhá zmírnit rizika v reálném čase. Vypočte se závažnost rizika uživatelů. Můžete nakonfigurovat zásady umožňující automaticky chránit aplikace přístup před budoucími hrozbami.

Další informace:

* [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md)
* [Kanál 9: Azure AD a Identity Show: Identity Protection ve verzi Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Security Center
Azure Security Center pomáhá zabránit, detekci a reakce na hrozby. Poskytuje Security Center můžete zvýšit přehled a kontrolu nad zabezpečením vašich prostředků Azure. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure. To pomáhá detekovat hrozby, které jinak nevšimli a spolupracuje s řadou řešení zabezpečení.

Security Center pomáhá optimalizaci a monitorování zabezpečení vašich prostředků Azure pomocí:

* Umožňuje definovat zásady pro vaše předplatné Azure prostředky podle:
  * Požadavky na zabezpečení vaší společnosti.
  * Typu aplikací nebo citlivosti dat v každém předplatném.
* Monitorování stavu virtuálních počítačů Azure, sítě a aplikace.
* Poskytuje seznam upřednostňovaných výstrah zabezpečení, včetně výstrahy z integrovaných partnerských řešení. Poskytuje také informace, které potřebujete k rychlému prozkoumání útok a doporučení týkající se způsobu jeho řešení.

Další informace:

* [Úvod do Azure Security Center](../security-center/security-center-intro.md)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png
