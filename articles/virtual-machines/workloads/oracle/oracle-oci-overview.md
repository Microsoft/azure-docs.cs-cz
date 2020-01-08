---
title: Integrace Microsoft Azure s cloudovou infrastrukturou Oracle | Microsoft Docs
description: Seznamte se s řešeními, která integrují aplikace Oracle běžící na Microsoft Azure s databázemi v Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: aacba12b32e9da75c2a4b9a20c0faa235cf6836a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75459298"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure-preview"></a>Řešení aplikací Oracle integrující Microsoft Azure a Oracle cloudovou infrastrukturu (Preview)

Microsoft a Oracle mají partnerství, aby poskytovaly nízkou latenci a vysokou propustnost připojení mezi cloudy, což vám umožní využít výhod obou cloudů. 

Pomocí tohoto připojení mezi cloudy můžete rozdělit vícevrstvou aplikaci tak, aby běžela vaše databázová vrstva na platformě Oracle Cloud Infrastructure (OCI), a na Microsoft Azure aplikace a další vrstvy. Prostředí je podobné jako spuštění celého zásobníku řešení v jednom cloudu. 

> [!IMPORTANT]
> Tato funkce mezi cloudy je aktuálně ve verzi Preview a [platí pro ně omezení](#preview-limitations). Aby bylo možné vytvořit připojení s nízkou latencí mezi Azure a OCI, musí být vaše předplatné Azure nejdřív pro tuto funkci povolené. K provedení tohoto krátkého [formuláře průzkumu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyzVVsi364tClw522rL9tkpUMVFGVVFWRlhMNUlRQTVWSTEzT0dXMlRUTyQlQCN0PWcu)se musíte zaregistrovat ve verzi Preview. Jakmile se vaše předplatné zaregistruje, dostanete e-mail s potvrzením. Možnost nemůžete použít, dokud nedostanete potvrzovací e-mail. Můžete se také obrátit na zástupce Microsoftu, který bude pro tuto verzi Preview povolený. Přístup k funkci verze Preview podléhá dostupnosti a omezení Microsoftu, a to pouze podle svého uvážení. Dokončení průzkumu nezaručuje přístup. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro produkční úlohy. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobné informace o Microsoft Azure verzích Preview najdete v tématu s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) . Některé aspekty této funkce se můžou před zveřejněním změnit.

Pokud vás zajímá nasazení řešení Oracle výhradně do infrastruktury Azure, přečtěte si téma [image virtuálních počítačů Oracle a jejich nasazení na Microsoft Azure](oracle-vm-solutions.md).

## <a name="scenario-overview"></a>Přehled scénáře

Připojení mezi cloudy poskytuje řešení pro spouštění špičkových aplikací od Oracle a vašich vlastních aplikací na virtuálních počítačích Azure a současně přináší výhody hostovaných databázových služeb v rozhraní OCI. 

Mezi aplikace, které můžete spustit v konfiguraci mezi cloudy patří:

* Sada E-Business
* JD Edwards EnterpriseOne
* PeopleSoft
* Maloobchodní aplikace Oracle
* Hyperion finanční správa Oracle

Následující diagram představuje podrobný přehled připojeného řešení. Pro zjednodušení diagram zobrazuje pouze aplikační vrstvu a datovou vrstvu. V závislosti na architektuře aplikace může vaše řešení zahrnovat další úrovně, jako je například webová vrstva v Azure. Další informace najdete v následující části.

![Přehled řešení Azure OCI](media/oracle-oci-overview/crosscloud.png)

## <a name="preview-limitations"></a>Omezení verze Preview

* Připojení mezi cloudy ve verzi Preview je omezené na oblasti Azure Východní USA (eastus), Velká Británie – jih (uksouth), canadacentral (Kanada – střed) a (USA – východ), Londýn (Velká Británie – jih) a Toronto (Kanada – jihovýchod). Pro Velká Británie – jih použijte prosím doménu dostupnosti 1 (AD 1) v rozhraní OCI při nasazení připojení pro nižší latenci.

## <a name="networking"></a>Sítě

Podnikoví zákazníci se často rozsazují a nasazují úlohy do několika cloudů pro různé obchodní a provozní důvody. K diverzifikaci zákazníci propojovat cloudové sítě pomocí Internetu, IPSec VPN nebo pomocí přímého připojení poskytovatele cloudu prostřednictvím místní sítě. Propojení cloudových sítí může vyžadovat významné investice v čase, peníze, návrh, nákup, instalaci, testování a provoz. 

Pro řešení těchto výzev zákazníků Oracle a Microsoft povolili integrované prostředí pro více cloudů. Síť mezi cloudy se vytváří připojením okruhu [ExpressRoute](../../../expressroute/expressroute-introduction.md) v Microsoft Azure s okruhem [FASTCONNECT](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) v rozhraní OCI. Toto připojení je možné v případě, že umístění partnerského vztahu Azure ExpressRoute je v blízkosti nebo ve stejném umístění partnerského vztahu jako rozhraní OCI FastConnect. Tato instalace umožňuje bezpečné a rychlé připojení mezi těmito dvěma cloudy bez nutnosti poskytovatele zprostředkujících služeb.

Pomocí ExpressRoute a FastConnect můžou zákazníci vytvořit partnerský vztah k virtuální síti v Azure s virtuální cloudovou sítí v rozhraní OCI za předpokladu, že se privátní adresní prostor IP adres nepřekrývá. Partnerský vztah ke dvěma sítím umožňuje prostředku ve virtuální síti komunikovat s prostředkem ve virtuální cloudové síti OCI, jako by byly ve stejné síti.

## <a name="network-security"></a>Zabezpečení sítě

Zabezpečení sítě je zásadní součástí libovolné podnikové aplikace a je centrální k tomuto řešení pro více cloudů. Veškerý provoz procházející ExpressRoute a FastConnect se předává přes soukromou síť. Tato konfigurace umožňuje zabezpečenou komunikaci mezi virtuální sítí Azure a virtuální cloudovou sítí Oracle. Nemusíte zadávat veřejnou IP adresu pro žádné virtuální počítače v Azure. Obdobně nepotřebujete internetovou bránu v rozhraní OCI. Veškerá komunikace probíhá prostřednictvím privátní IP adresy počítačů.

Kromě toho můžete nastavit [seznamy zabezpečení](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) ve vaší virtuální cloudové síti OCI a pravidla zabezpečení (připojené ke [skupinám zabezpečení sítě](../../../virtual-network/security-overview.md)Azure). Pomocí těchto pravidel můžete řídit tok provozu mezi počítači ve virtuálních sítích. Pravidla zabezpečení sítě je možné přidat na úrovni počítače, na úrovni podsítě a také na úrovni virtuální sítě.
 
## <a name="identity"></a>Identita

Identita je jedním z hlavních pilířů partnerství mezi společnostmi Microsoft a Oracle. Pro integraci [služby Oracle identity Cloud Service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) pomocí služby [Azure Active Directory](../../../active-directory/index.yml) (Azure AD) se udělala významná práce. Azure AD je cloudová služba pro správu identit a přístupu od Microsoftu. Pomáhá uživatelům přihlašovat se k různým prostředkům a přistupovat k nim. Azure AD také umožňuje spravovat uživatele a jejich oprávnění.

V současné době tato integrace umožňuje správu v jednom centrálním umístění, které je Azure Active Directory. Azure AD synchronizuje všechny změny v adresáři s odpovídajícím adresářem Oracle a používá se pro jednotné přihlašování do řešení Oracle pro více cloudů.

## <a name="next-steps"></a>Další kroky

Začněte s mezi [cloudovou sítí](configure-azure-oci-networking.md) mezi Azure a OCI. 

Další informace a dokumenty White paper o rozhraní OCI najdete v dokumentaci ke [cloudu Oracle](https://docs.cloud.oracle.com/iaas/Content/home.htm) .
