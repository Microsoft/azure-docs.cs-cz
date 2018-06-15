---
title: Správa zabezpečení Azure a monitorování přehled | Microsoft Docs
description: Tento článek obsahuje přehled funkce zabezpečení a služeb, které poskytuje Azure, které pomáhají při správě a monitorování cloudových služeb Azure a virtuálních počítačů.
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
ms.openlocfilehash: 1e48131ff784ba5bb8d5a7dfffe8afb5ce8bb4c1
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364365"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Přehled monitorování a správu zabezpečení Azure
Azure nabízí mechanismy zabezpečení, které pomáhají při správy a monitorování cloudových služeb Azure a virtuálních počítačů (VM). Tento článek obsahuje přehled těchto klíčových funkcí zabezpečení a služeb. Jsou uvedeny odkazy na články, které poskytují podrobnosti o jednotlivých tak další informace.

Zabezpečení vašich cloudových službách společnosti Microsoft je partnerství a sdílenou odpovědnost mezi vámi a společností Microsoft. Microsoft je odpovědná za platformy Azure a fyzického zabezpečení jeho datových center (pomocí ochranu zabezpečení, jako je například uzamčeném oznámení "BADGE" entry dveří, ochranné a chrání). Azure poskytuje silné úrovně zabezpečení cloudu ve vrstvě software, který splňuje potřeby zabezpečení, ochrany osobních údajů a dodržování předpisů svým zákazníkům.

Vlastníte vašich dat a identity, odpovědnost za ochranu jejich, zabezpečení místních prostředků a zabezpečení součástí cloudu, nad kterými nemáte kontrolu. Microsoft vám dává kontrolních mechanismů pro zabezpečení a funkcí, které vám umožní chránit vaše data a aplikace. Vaše stupeň odpovědnost za zabezpečení je založený na typu cloudové služby.

Následující graf shrnuje rovnováhu mezi počtem odpovědnost mezi společností Microsoft a Zákazník.

![Sdílená odpovědnost][1]

Další informace o správě zabezpečení najdete v tématu [Správa zabezpečení v Azure](azure-security-management.md).

## <a name="role-based-access-control"></a>Řízení přístupu na základě rolí
Řízení přístupu na základě rolí (RBAC) poskytuje správu podrobné přístupu k prostředkům Azure. Pomocí RBAC můžete osoby poskytnout pouze takovou úroveň přístupu, které potřebují k provádění svých úloh. RBAC také vám pomůže zkontrolovat, že při osoby nechat organizace, jejich ztratit přístup k prostředkům v cloudu.

Další informace:

* [Blog týmu Active Directory na RBAC](http://i1.blogs.technet.com/b/ad/archive/2015/10/12/azure-rbac-is-ga.aspx)
* [Řízení přístupu Azure na základě rolí](../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Antimalware
V Azure můžete použít antimalwarový software od dodavatelů hlavní zabezpečení, jako je Microsoft, Symantec, Trend Micro, McAfee a Kaspersky. Tento software pomáhá chránit vaše virtuální počítače ze škodlivých souborů, adwaru a dalšími hrozbami.

Antimalware od Microsoftu pro Azure Cloud Services a virtuálních počítačů nabízí možnost instalace agenta antimalwarových pro rolí PaaS a virtuální počítače. Podle toho, System Center Endpoint Protection, tato funkce přináší Principy místní technologie zabezpečení do cloudu.

Nabízíme těsná integrace pro je Trend [hloubkové zabezpečení](http://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) a [SecureCloud](http://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) produkty ve platformy Azure. Hloubkové zabezpečení je antivirový řešení a SecureCloud je řešení pro šifrování. Hloubkové zabezpečení je nasazena uvnitř virtuálních počítačů prostřednictvím model rozšíření. Pomocí portálu Azure uživatelského rozhraní a prostředí PowerShell můžete použít hloubkové zabezpečení uvnitř nové virtuální počítače, které jsou právě spuštěné, nebo existující virtuální počítače, které jsou už nasazené.

Symantec Endpoint Protection (září) je podporováno také v Azure. Prostřednictvím integrace portálu můžete určit, kterou chcete použít září na virtuálním počítači. ZÁŘÍ se dá nainstalovat na nový virtuální počítač prostřednictvím portálu Azure nebo se dá nainstalovat na existující virtuální počítač pomocí prostředí PowerShell.

Další informace:

* [Nasazování antimalwarových řešení na virtuálních počítačích Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Antimalware od Microsoftu pro cloudové služby Azure a virtuální počítače](azure-security-antimalware.md)
* [Postup instalace a konfigurace Trend Micro hluboké Security jako služba na virtuální počítač s Windows](../virtual-machines/windows/classic/install-trend.md)
* [Postup instalace a konfigurace Symantec Endpoint Protection na virtuální počítač s Windows](../virtual-machines/windows/classic/install-symantec.md)
* [Nové možnosti Antimalwarové ochrany virtuálních počítačů Azure](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Azure Multi-Factor Authentication je metoda ověřování, který vyžaduje použití víc než jednu metodu ověřování. Přidá velmi důležitou druhou vrstvu zabezpečení uživatelská přihlášení a transakce. 

Vícefaktorové ověřování pomáhá chránit přístup k datům a aplikacím při splnění požadavků uživatelů pro jednoduchý proces přihlášení. Zajišťuje silné ověřování pomocí celé řady možností ověřování (telefonní hovor, textová zpráva nebo oznámení nebo ověření kód mobilní aplikace) a tokeny OATH třetích stran.

Další informace:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Co je Azure Multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md)
* [Jak funguje Azure Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute
Azure ExpressRoute můžete použít k rozšíření místní sítě do cloudu Microsoftu přes vyhrazené soukromé připojení, která usnadňují poskytovatelem připojení. Prostřednictvím ExpressRoute může vytvořit připojení ke cloudovým službám Microsoftu, jako je například Azure, Office 365 a CRM Online. Připojení může být z:

- Síť any-to-any (IP VPN).
- Síť Ethernet typu point-to-point.
- Virtuální křížové připojení prostřednictvím poskytovatele připojení ve společném umístění. 

Připojení ExpressRoute nepřipojujte prostřednictvím veřejného Internetu. Můžete nabízejí další spolehlivost, vyšší rychlost, nižší latenci a vyšší zabezpečení než Typická připojení přes internet.

Další informace:

* [Technický přehled ExpressRoute](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Brány virtuální sítě
Brány sítě VPN, označované taky jako brány virtuální sítě Azure, se používají k posílání síťového provozu mezi virtuálními sítěmi a místními umístění. Používají se také k posílání provozu mezi více virtuálních sítí v rámci Azure (síť k síti). Brány sítě VPN poskytují zabezpečení mezi různými místy připojení mezi Azure a infrastruktury.

Další informace:

* [O branách VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Přehled zabezpečení sítě Azure](security-network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management
Uživatelé někdy potřebovat provádět privilegované operace v prostředků Azure nebo jiné aplikace SaaS. To často znamená, že organizace jim poskytnout trvalé privilegovaný přístup v Azure Active Directory (Azure AD). 

Je to rostoucí bezpečnostní riziko pro hostované cloudové prostředky, proto organizace nelze monitorovat dostatečně co tito uživatelé pracují s jejich privilegovaný přístup. Navíc pokud dojde k narušení uživatelský účet s privilegovaného přístupu daného jeden porušení může ovlivnit celkové cloudu zabezpečení organizace. Azure AD Privileged Identity Management pomáhá vyřešit toto riziko snížit čas ohrožení oprávnění a zvýšení získat přehled o využití.  

Správa privilegovaných identit zavádí koncepci dočasný správce pro roli nebo "pouze v čase" přístup správce. Tento druh správce je uživatel, který musí dokončit proces aktivace pro tento přiřazenou roli. Proces aktivace změní přiřazení uživatele k roli ve službě Azure AD z neaktivní na aktivní, pro zadané časové období.

Další informace:

* [Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md)
* [Začínáme s Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-getting-started.md)

## <a name="identity-protection"></a>Identity Protection
Azure AD Identity Protection poskytuje ucelený přehled podezřelé aktivity přihlášení a potenciální ohrožení zabezpečení k ochraně vaší firmy. Ochrana identity detekuje podezřelé aktivity uživatelů a privilegovaných (správce) identit, podle signály jako:

- Útoky hrubou silou.
- Uniklé přihlašovací údaje.
- Přihlášení připojení z neznámých míst a nakažených zařízení.

Zadáním oznámení a doporučené nápravy Identity Protection pomáhá zmírnit rizika v reálném čase. Vypočte závažnost riziko uživatele. Můžete nakonfigurovat na základě riziko zásady automaticky pomáhají chránit aplikaci přístup z budoucích hrozeb.

Další informace:

* [Ochrany identit Azure Active Directory](../active-directory/active-directory-identityprotection.md)
* [Kanál 9: Azure AD a Identity zobrazení: Identity Protection verze Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Security Center
Azure Security Center pomáhá zabránit, zjistit a reagovat na hrozby. Security Center nabízí můžete zvýšit přehled a kontrolu nad zabezpečení vašich prostředků Azure. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure. Pomáhá zjišťovat hrozby, které byste jinak nevšimli a spolupracuje s řadou řešení zabezpečení.

Security Center pomáhá optimalizovat a monitorování zabezpečení vašich prostředků Azure pomocí:

* Umožňuje definovat zásady pro vaše předplatné Azure prostředky podle:
  * Potřebuje vaše společnost zabezpečení.
  * Typ aplikací nebo citlivosti dat v každém předplatném.
* Monitorování stavu virtuální počítače Azure, sítě a aplikace.
* Poskytuje seznam upřednostňovaných výstrah zabezpečení, včetně výstrahy z integrovaných partnerských řešení. Nabízí taky informace, které potřebujete k rychlému prozkoumání útoku a doporučení na jeho řešení.

Další informace:

* [Úvod do Azure Security Center](../security-center/security-center-intro.md)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png
