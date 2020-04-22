---
title: Integrace Microsoft Azure s infrastrukturou Oracle Cloud Infrastructure | Dokumenty společnosti Microsoft
description: Seznamte se s řešeními, která integrují aplikace Oracle spuštěné v Microsoft Azure s databázemi v řešení Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/16/2020
ms.author: borisb
ms.custom: ''
ms.openlocfilehash: e70eedcfcdf548965b79e4a48a3a8bfa643f0396
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687429"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure"></a>Aplikační řešení Oracle integrující Microsoft Azure a Oracle Cloud Infrastructure

Společnosti Microsoft a Oracle spolupracují na poskytování připojení s nízkou latencí a vysokou propustností napříč cloudem, což vám umožní využívat to nejlepší z obou cloudů. 

Pomocí tohoto připojení napříč cloudy můžete rozdělit vícevrstvou aplikaci pro spuštění databázové vrstvy na oracle cloudové infrastruktuře (OCI) a aplikací a dalších úrovní v Microsoft Azure. Prostředí je podobné spuštění celého zásobníku řešení v jednom cloudu. 

Pokud máte zájem o úplné nasazení řešení Oracle v infrastruktuře Azure, přečtěte si informace [o ibi virtuálních počítačích Oracle a jejich nasazení v Microsoft Azure](oracle-vm-solutions.md).

## <a name="scenario-overview"></a>Přehled scénáře

Připojení mezi cloudy poskytuje řešení pro spuštění špičkových aplikací společnosti Oracle a vlastních aplikací na virtuálních počítačích Azure a zároveň využívávýhod hostovaných databázových služeb v OCI. 

> [!IMPORTANT]
> Společnost Oracle bude tyto aplikace certifikovat tak, aby se v Azure spouštěla do května 2020 při používání řešení Propojení Azure / Oracle Cloud.

Mezi aplikace, které můžete spouštět v konfiguraci mezi cloudy, patří:

* E-Business Suite
* JD Edwards EnterpriseOne
* Peoplesoft
* Maloobchodní aplikace Oracle
* Oracle Hyperion Financial Management

Následující diagram je přehled na vysoké úrovni připojeného řešení. Pro jednoduchost diagramu zobrazuje pouze aplikační vrstvu a datovou vrstvu. V závislosti na architektuře aplikace může vaše řešení zahrnovat další vrstvy, jako je webová vrstva v Azure. Další informace naleznete v následujících částech.

![Přehled řešení Azure OCI](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>Dostupnost regionu 

Připojení mezi cloudy je omezeno na následující oblasti:
* Azure – východ USA (eastus) & OCI Ashburn (USA – východ)
* Azure UK South (uksouth) & OCI London (Česká republika)
* Azure Canada Central (canadacentral) & OCI Toronto (Kanada jihovýchod)
* Azure West Europe (západní Evropa) & OCI Amsterdam (Nizozemsko severozápad)
* Azure Japan East (japaneast) & OCI Tokyo (Japonsko – východ)

## <a name="networking"></a>Sítě

Podnikoví zákazníci se často rozhodnou diverzifikovat a nasadit úlohy ve více cloudech z různých obchodních a provozních důvodů. Zákazníci propojují cloudové sítě pomocí internetu, sítě IPSec VPN nebo pomocí řešení přímého připojení poskytovatele cloudu prostřednictvím místní sítě. Propojení cloudových sítí může vyžadovat značné investice do času, peněz, návrhu, nákupu, instalace, testování a provozu. 

K řešení těchto problémů zákazníků umožnily společnosti Oracle a Microsoft integrované prostředí s více cloudy. Sítě mezi cloudy se navazují připojením okruhu [ExpressRoute](../../../expressroute/expressroute-introduction.md) v Microsoft Azure s okruhem [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) v OCI. Toto připojení je možné, pokud umístění partnerského vztahu Azure ExpressRoute je v blízkosti nebo ve stejném umístění partnerského vztahu jako OCI FastConnect. Toto nastavení umožňuje bezpečné a rychlé připojení mezi dvěma cloudy bez nutnosti zprostředkujícího poskytovatele služeb.

Pomocí ExpressRoute a FastConnect mohou zákazníci propojit virtuální síť v Azure s virtuální cloudovou sítí v OCI za předpokladu, že se privátní ip adresní prostor nepřekrývá. Partnerský vztah mezi dvěma sítěmi umožňuje prostředku ve virtuální síti komunikovat s prostředkem ve virtuální cloudové síti OCI, jako by byly obě ve stejné síti.

## <a name="network-security"></a>Zabezpečení sítě

Zabezpečení sítě je klíčovou součástí každé podnikové aplikace a je pro toto řešení s více cloudy zásadní. Veškerý provoz přes ExpressRoute a FastConnect prochází přes privátní síť. Tato konfigurace umožňuje zabezpečenou komunikaci mezi virtuální sítí Azure a virtuální cloudovou sítí Oracle. Není nutné zadat veřejnou IP adresu pro všechny virtuální počítače v Azure. Podobně nepotřebujete internetovou bránu v OCI. Veškerá komunikace probíhá přes privátní IP adresu strojů.

Kromě toho můžete nastavit [seznamy zabezpečení](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) na virtuální cloudové sítě OCI a pravidla zabezpečení (připojené ke [skupinám zabezpečení sítě](../../../virtual-network/security-overview.md)Azure). Tato pravidla slouží k řízení provozu plynulého mezi počítači ve virtuálních sítích. Pravidla zabezpečení sítě lze přidat na úrovni počítače, na úrovni podsítě a také na úrovni virtuální sítě.
 
## <a name="identity"></a>Identita

Identita je jedním z hlavních pilířů partnerství mezi společnostmi Microsoft a Oracle. Byla provedena významná práce při integraci [služby Oracle Identity Cloud Service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) s [Azure Active Directory](../../../active-directory/index.yml) (Azure AD). Azure AD je cloudová služba správy identit a přístupu od Microsoftu. Pomáhá uživatelům přihlásit se a získat přístup k různým prostředkům. Azure AD také umožňuje spravovat uživatele a jejich oprávnění.

V současné době tato integrace umožňuje spravovat v jednom centrálním umístění, což je Azure Active Directory. Azure AD synchronizuje všechny změny v adresáři s odpovídajícím adresářem Oracle a používá se pro jednotné přihlašování k řešení oracle napříč cloudy.

## <a name="next-steps"></a>Další kroky

Začínáme s [mezicloudovou sítí](configure-azure-oci-networking.md) mezi Azure a OCI. 

Další informace a dokumenty white paper o oci najdete v dokumentaci [k řešení Oracle Cloud.](https://docs.cloud.oracle.com/iaas/Content/home.htm)
