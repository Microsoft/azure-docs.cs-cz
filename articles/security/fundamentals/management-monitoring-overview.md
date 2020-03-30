---
title: Funkce zabezpečení správy a monitorování – Microsoft Azure | Dokumenty společnosti Microsoft
description: Tento článek obsahuje přehled funkcí zabezpečení a služeb, které Azure poskytuje pro pomoc při správě a monitorování cloudových služeb Azure a virtuálních počítačů.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: 7ad7a29a92d25556190b4cf44f4e48158a6f0952
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73162748"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Přehled správy a monitorování Azure
Tento článek obsahuje přehled funkcí zabezpečení a služeb, které Azure poskytuje pro pomoc při správě a monitorování cloudových služeb Azure a virtuálních počítačů.

## <a name="role-based-access-control"></a>Řízení přístupu na základě rolí

Řízení přístupu na základě rolí (RBAC) poskytuje podrobnou správu přístupu pro prostředky Azure. Pomocí RBAC můžete udělit lidem pouze množství přístupu, které potřebují k provádění svých úloh. RBAC vám také může pomoci zajistit, že když lidé opustí organizaci, ztratí přístup k prostředkům v cloudu.

Další informace:

* [Blog týmu služby Active Directory o službě RBAC](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Řízení přístupu na základě role v Azure](../../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Antimalware

V Azure můžete používat antimalwarový software od hlavních dodavatelů zabezpečení, jako jsou Microsoft, Symantec, Trend Micro, McAfee a Kaspersky. Tento software pomáhá chránit vaše virtuální počítače před škodlivými soubory, adwarem a dalšími hrozbami.

Microsoft Antimalware pro Cloud Services Azure a virtuální počítače nabízí možnost nainstalovat antimalwarového agenta pro role PaaS i virtuální počítače. Tato funkce založená na ochraně koncových bodů system center přináší do cloudu osvědčenou místní technologii zabezpečení.

Nabízíme také hlubokou integraci pro produkty [Deep Security](https://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) a [SecureCloud](https://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) společnosti Trend na platformě Azure. Deep Security je antivirové řešení a SecureCloud je šifrovací řešení. Deep Security se nasazuje uvnitř virtuálních proudů prostřednictvím modelu rozšíření. Pomocí uzulinového prostředí portálu Azure a prostředí PowerShell můžete použít deep security uvnitř nových virtuálních počítačů, které se stočejí, nebo stávajících virtuálních počítačích, které už jsou nasazené.

Symantec Endpoint Protection (SEP) je také podporovánv Azure. Prostřednictvím integrace portálu můžete určit, že máte v úmyslu použít SEP na virtuálním počítači. Sep se dá nainstalovat na nový virtuální počítač přes portál Azure, nebo ho dá nainstalovat na existující virtuální počítač přes PowerShell.

Další informace:

* [Nasazování antimalwarových řešení na virtuálních počítačích Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Microsoft Antimalware pro Cloudové služby Azure a virtuální počítače](antimalware.md)
* [Jak nainstalovat a nakonfigurovat Trend Micro Deep Security jako služba na virtuálním počítači se systémem Windows](/azure/virtual-machines/windows/classic/install-trend)
* [Jak nainstalovat a nakonfigurovat službu Symantec Endpoint Protection na virtuálním počítači se systémem Windows](/azure/virtual-machines/windows/classic/install-symantec)
* [Nové antimalwarové možnosti pro ochranu virtuálních počítačů Azure](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-factor Authentication

Azure Multi-Factor Authentication je metoda ověřování, která vyžaduje použití více než jedné metody ověření. Přidá kritickou druhou vrstvu zabezpečení pro přihlášení uživatelů a transakce.

Vícefaktorové ověřování pomáhá chránit přístup k datům a aplikacím a zároveň uspokojovat požadavky uživatelů na jednoduchý proces přihlášení. Poskytuje silné ověřování prostřednictvím řady možností ověření (telefonní hovor, textová zpráva nebo oznámení nebo ověřovací kód mobilní aplikace) a tokenů OATH třetích stran.

Další informace:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Co je Azure Multi-Factor Authentication?](/azure/active-directory/authentication/multi-factor-authentication)
* [Jak funguje Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute

Pomocí Služby Azure ExpressRoute můžete rozšířit místní sítě do Cloudu Microsoftu prostřednictvím vyhrazeného privátního připojení, které usnadňuje poskytovatel připojení. Pomocí ExpressRoute můžete navázat připojení ke cloudovým službám Microsoftu, jako jsou Azure, Office 365 a CRM Online. Konektivita může být z:

* Síť any-to-any (IP VPN).
* Síť Ethernet z bodu do bodu.
* Virtuální křížové připojení prostřednictvím poskytovatele připojení v zařízení společného umístění.

Připojení ExpressRoute neprocházejí přes veřejný internet. Mohou nabídnout vyšší spolehlivost, vyšší rychlost, nižší latenci a vyšší zabezpečení než běžná připojení přes internet.

Další informace:

* [Technický přehled ExpressRoute](../../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Brány virtuální sítě

Brány VPN, nazývané také brány virtuální sítě Azure, se používají k odesílání síťového provozu mezi virtuálními sítěmi a místními umístěními. Používají se také k odesílání provozu mezi více virtuálními sítěmi v rámci Azure (síť do sítě). Brány VPN poskytují zabezpečené mezimístní připojení mezi Azure a vaší infrastrukturou.

Další informace:

* [O branách VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Přehled zabezpečení sítě Azure](network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Někdy uživatelé potřebují provádět privilegované operace v prostředcích Azure nebo jiných aplikacích SaaS. To často znamená, že organizace jim poskytují trvalý privilegovaný přístup ve službě Azure Active Directory (Azure AD).

Toto je rostoucí bezpečnostní riziko pro prostředky hostované v cloudu, protože organizace nemohou dostatečně sledovat, co tito uživatelé dělají s jejich privilegovaným přístupem. Navíc pokud dojde k ohrožení uživatelského účtu s privilegovaným přístupem, může jedno porušení ovlivnit celkové zabezpečení cloudu organizace. Azure AD Privileged Identity Management pomáhá vyřešit toto riziko snížením doby expozice oprávnění a zvýšením viditelnosti využití.  

Správa privilegovaných identit zavádí koncept dočasného správce pro roli nebo přístup správce "just in time". Tento druh správce je uživatel, který potřebuje dokončit proces aktivace pro tuto přiřazenou roli. Proces aktivace změní přiřazení uživatele na roli ve službě Azure AD z neaktivní na aktivní, za zadané časové období.

Další informace:

* [Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
* [Začínáme s aplikací Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>Identity Protection

Azure AD Identity Protection poskytuje konsolidované zobrazení podezřelých přihlašovacích aktivit a potenciálních chyb zabezpečení, které pomáhají chránit vaši firmu. Ochrana identity detekuje podezřelé aktivity pro uživatele a privilegované (správcské) identity na základě signálů, jako jsou:

* Útoky hrubou silou.
* Unikly přihlašovací údaje.
* Přihlášení z neznámých míst a infikovaných zařízení.

Poskytováním oznámení a doporučené nápravy pomáhá ochrana identity zmírnit rizika v reálném čase. Vypočítá závažnost rizika uživatele. Zásady založené na rizicích můžete nakonfigurovat tak, aby automaticky pomohly zabezpečit přístup k aplikacím před budoucími hrozbami.

Další informace:

* [Ochrana identit služby Azure Active Directory](/azure/active-directory/active-directory-identityprotection)
* [Kanál 9: Azure AD a identity Zobrazit: Ochrana identity Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Security Center

Azure Security Center pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně. Security Center vám poskytuje lepší přehled o zabezpečení vašich prostředků Azure a kontrolu nad tím, že nad tím máte kontrolu. Poskytuje integrované monitorování zabezpečení a správu zásad napříč vašimi předplatnými Azure. Pomáhá odhalovat hrozby, které by jinak mohly zůstat bez povšimnutí, a spolupracuje s širokým ekosystémem bezpečnostních řešení.

Security Center vám pomůže optimalizovat a monitorovat zabezpečení prostředků Azure takto:

* Povolení k definování zásad pro prostředky předplatného Azure podle:
  * Bezpečnostní potřeby vaší společnosti.
  * Typ aplikací nebo citlivost dat v každém předplatném.
* Sledování stavu virtuálních počítačů Azure, sítí a aplikací.
* Poskytuje seznam prioritních výstrah zabezpečení, včetně výstrah z integrovaných partnerských řešení. Poskytuje také informace, které potřebujete k rychlému prošetření útoku, a doporučení, jak jej napravit.

Další informace:

* [Úvod do Azure Security Center](../../security-center/security-center-intro.md)
* [Vylepšete své skóre zabezpečení v Azure Security Center](../../security-center/security-center-secure-score.md)

## <a name="intelligent-security-graph"></a>Graf inteligentního zabezpečení

Inteligentní graf zabezpečení poskytuje ochranu před hrozbami v produktech a službách společnosti Microsoft v reálném čase. Využívá pokročilé analýzy, které propojují obrovské množství informací o hrozbách a bezpečnostních dat, aby poskytovaly přehledy, které mohou posílit zabezpečení organizace. Společnost Microsoft používá pokročilou analýzu – zpracovává více než 450 miliard ověření měsíčně, skenuje 400 miliard e-mailů kvůli malwaru a phishingu a aktualizuje jednu miliardu zařízení – aby získal bohatší přehledy. Tyto přehledy mohou vaší organizaci pomoci rychle rozpoznat útoky a reagovat na ně.

* [Graf inteligentního zabezpečení](https://www.microsoft.com/security/intelligence)

## <a name="next-steps"></a>Další kroky
Seznamte se s [modelem sdílené odpovědnosti](shared-responsibility.md) a o tom, které úkoly zabezpečení zpracovává společnost Microsoft a které úkoly zpracováváte vy.

Další informace o správě zabezpečení najdete [v tématu Správa zabezpečení v Azure](management.md).
