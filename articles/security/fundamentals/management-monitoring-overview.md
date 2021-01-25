---
title: Funkce zabezpečení správy a monitorování – Microsoft Azure | Microsoft Docs
description: Tento článek obsahuje přehled funkcí a služeb zabezpečení, které Azure poskytuje, aby vám pomohly při správě a monitorování cloudových služeb a virtuálních počítačů Azure.
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
ms.date: 01/24/2021
ms.author: terrylan
ms.openlocfilehash: d85b1fdd433c372bb41adec6e3d33013f19363f0
ms.sourcegitcommit: 3c8964a946e3b2343eaf8aba54dee41b89acc123
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98747169"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Přehled správy a monitorování zabezpečení Azure
Tento článek obsahuje přehled funkcí a služeb zabezpečení, které Azure poskytuje, aby vám pomohly při správě a monitorování cloudových služeb a virtuálních počítačů Azure.

## <a name="azure-role-based-access-control"></a>Řízení přístupu na základě role v Azure

Řízení přístupu na základě role v Azure (Azure RBAC) poskytuje podrobnou správu přístupu pro prostředky Azure. Pomocí Azure RBAC můžete uživatelům udělit jenom množství přístupu, který potřebují k provádění svých úloh. Azure RBAC vám taky může pomáhat zajistit, že když lidé odejdou z organizace, ztratí přístup k prostředkům v cloudu.

Další informace:

* [Blog týmu služby Active Directory ve službě Azure RBAC](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>antimalware

S Azure můžete použít antimalwarový software od hlavních dodavatelů zabezpečení, jako jsou Microsoft, Symantec, Trend Micro, McAfee a Kaspersky. Tento software pomáhá chránit vaše virtuální počítače před škodlivými soubory, adwarem a dalšími hrozbami.

Microsoft Antimalware pro Azure Cloud Services a Virtual Machines nabízí možnost instalace antimalwarového agenta pro role PaaS i pro virtuální počítače. Na základě Endpoint Protection System Center tato funkce přináší ověřenou místní technologii zabezpečení do cloudu.

Nabízíme [také rozsáhlou](https://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) integraci pro [SecureCloud](https://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) produkty trendu a na platformu Azure. Důkladné zabezpečení je antivirové řešení a SecureCloud je řešení šifrování. Hloubkové zabezpečení je nasazeno v rámci virtuálních počítačů prostřednictvím modelu rozšíření. Pomocí uživatelského rozhraní Azure Portal a PowerShellu se můžete rozhodnout použít hloubkové zabezpečení uvnitř nových virtuálních počítačů, které se prosazují, nebo existující virtuální počítače, které jsou už nasazené.

Společnost Symantec Endpoint Protection (SEP) je také podporována v Azure. Prostřednictvím integrace portálu můžete určit, že chcete na virtuálním počítači použít SEP. SEP se dá nainstalovat na nový virtuální počítač prostřednictvím Azure Portal, nebo se dá nainstalovat na existující virtuální počítač přes PowerShell.

Další informace:

* [Nasazování antimalwarových řešení na virtuálních počítačích Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Microsoft Antimalware pro Azure Cloud Services a Virtual Machines](antimalware.md)
* [Postup instalace a konfigurace Trend Micro hlubokého zabezpečení jako služby na virtuálním počítači s Windows](../../virtual-machines/extensions/trend.md)
* [Jak nainstalovat a nakonfigurovat Symantec Endpoint Protection na virtuálním počítači s Windows](../../virtual-machines/extensions/symantec.md)
* [Nové antimalwarové možnosti pro ochranu Azure Virtual Machines](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Azure AD Multi-Factor Authentication je metoda ověřování, která vyžaduje použití více než jedné metody ověřování. Přičítá se kritická druhá vrstva zabezpečení pro přihlášení a transakce uživatelů.

Multi-Factor Authentication pomáhá chránit přístup k datům a aplikacím a současně plnit požadavky uživatelů na jednoduchý proces přihlašování. Zajišťuje silné ověřování prostřednictvím řady možností ověřování (telefonní hovor, textová zpráva nebo oznámení mobilní aplikace nebo ověřovací kód) a tokeny OATH třetích stran.

Další informace:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Co je Azure AD Multi-Factor Authentication?](../../active-directory/authentication/concept-mfa-howitworks.md)
* [Jak funguje vícefaktorové ověřování Azure AD](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute

Azure ExpressRoute můžete použít k rozšiřování místních sítí do Microsoft Cloud prostřednictvím vyhrazeného privátního připojení, které usnadňuje poskytovatel připojení. Pomocí ExpressRoute můžete navázat připojení ke cloudovým službám Microsoftu, jako je Azure, Microsoft 365 a CRM Online. Připojení může být:

* Síť typu any-to-Any (IP VPN).
* Síť Ethernet typu Point-to-Point.
* Virtuální křížové připojení prostřednictvím poskytovatele připojení v zařízení společného umístění.

Připojení ExpressRoute nejdou přes veřejný Internet. Můžou nabídnout spolehlivější, rychlejší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes Internet.

Další informace:

* [Technický přehled ExpressRoute](../../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Brány virtuální sítě

Brány VPN, označované taky jako brány virtuální sítě Azure, se používají k posílání síťového provozu mezi virtuálními sítěmi a místními umístěními. Používají se také k posílání provozu mezi několika virtuálními sítěmi v rámci Azure (síť do sítě). Brány VPN poskytují zabezpečené připojení mezi různými místy mezi Azure a vaší infrastrukturou.

Další informace:

* [Informace o branách VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Přehled zabezpečení sítě v Azure](network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Někdy uživatelé potřebují provádět privilegované operace v prostředcích Azure nebo jiných aplikacích SaaS. To často znamená, že organizace jim poskytnou trvalý privilegovaný přístup v Azure Active Directory (Azure AD).

Toto je rostoucí bezpečnostní riziko pro prostředky hostované v cloudu, protože organizace nemůžou dostatečně monitorovat, co dělají uživatelé s privilegovaným přístupem. Kromě toho platí, že pokud dojde k ohrožení bezpečnosti uživatelského účtu s privilegovaným přístupem, může toto porušení ovlivnit celkové cloudové zabezpečení organizace. Azure AD Privileged Identity Management pomáhá vyřešit toto riziko tím, že snižuje dobu expozice oprávnění a zvyšuje přehlednost využití.  

Privileged Identity Management zavádí koncept dočasného správce pro roli nebo "just in time" přístup správce. Tento druh správce je uživatel, který potřebuje k dokončení procesu aktivace pro přiřazenou roli. Proces aktivace změní přiřazení uživatele k roli v Azure AD z neaktivní na aktivní pro zadané časové období.

Další informace:

* [Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
* [Začínáme s aplikací Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>Identity Protection

Azure AD Identity Protection poskytuje ucelený přehled o podezřelých přihlašovacích aktivitách a potenciálních ohroženích zabezpečení, které vám pomůžou ochránit vaši firmu. Identity Protection detekuje podezřelé aktivity uživatelů a privilegovaných identit (správců) na základě signálů, jako jsou:

* Útoky hrubou silou.
* Nevrácená pověření.
* Přihlášení z neznámých umístění a nakažených zařízení.

Díky poskytování oznámení a doporučené nápravy pomáhá ochrana identity zmírnit rizika v reálném čase. Počítá závažnost rizika uživatele. Můžete nakonfigurovat zásady založené na rizicích, které budou automaticky pomáhat chránit přístup k aplikacím před budoucími hrozbami.

Další informace:

* [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)
* [Kanál 9: Azure AD a identity show: identita Protection Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Security Center

Azure Security Center pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně. Security Center poskytuje lepší přehled o zabezpečení prostředků Azure a jejich kontrolu nad zabezpečením vašich prostředků Azure i v hybridním cloudovém prostředí. 

Security Center provádí průběžné vyhodnocování zabezpečení vašich připojených prostředků a porovnává jeho konfiguraci a nasazení s využitím [srovnávacího testu zabezpečení Azure](../benchmarks/introduction.md) za účelem poskytování podrobných doporučení zabezpečení přizpůsobených vašemu prostředí.

Security Center vám pomůže optimalizovat a monitorovat zabezpečení prostředků Azure pomocí těchto akcí:

- Vám umožní definovat zásady pro vaše prostředky předplatného Azure podle těchto pravidel:
    - Požadavky na zabezpečení vaší organizace.
    - Typ aplikací nebo citlivosti dat v každém předplatném.
    - Jakékoli oborové nebo regulativní normy nebo srovnávací testy, které se vztahují k předplatným. 
- Monitorování stavu virtuálních počítačů, sítí a aplikací Azure.
- Poskytuje seznam výstrah zabezpečení s určením priorit, včetně výstrah z integrovaných partnerských řešení. Poskytuje také informace, které potřebujete k rychlému prověření útoků a doporučení k tomu, jak je opravit.

Další informace:

* [Úvod do Azure Security Center](../../security-center/security-center-introduction.md)
* [Vylepšit vaše zabezpečené skóre v Azure Security Center](../../security-center/secure-score-security-controls.md)

## <a name="intelligent-security-graph"></a>Intelligent Security Graph

Intelligent Security Graph poskytuje ochranu před hrozbami v reálném čase v produktech a službách Microsoftu. Používá pokročilou analýzu, která propojí obrovské množství informací o hrozbách a zabezpečení dat a poskytuje přehledy, které mohou posílit zabezpečení organizace. Microsoft používá pokročilou analýzu – zpracovává více než 450 000 000 000 ověřování za měsíc, prohledává 400 000 000 000 e-mailů pro malware a útoky phishing a aktualizuje 1 000 000 000 zařízení, aby poskytoval širší přehledy. Tyto přehledy můžou vaší organizaci pomáhat rychle detekovat a reagovat na útoky.

* [Intelligent Security Graph](https://www.microsoft.com/security/intelligence)

## <a name="next-steps"></a>Další kroky
Seznamte se s [modelem sdílené zodpovědnosti](shared-responsibility.md) a o tom, které úlohy zabezpečení jsou zpracovávány společností Microsoft a které úlohy jsou zpracovávány vámi.

Další informace o správě zabezpečení najdete v tématu [Správa zabezpečení v Azure](management.md).