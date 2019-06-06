---
title: Integrujte Microsoft Azure s Cloudovou infrastrukturou Oracle | Dokumentace Microsoftu
description: Další informace o řešení, které integrují aplikace Oracle v Microsoft Azure s databází v Oracle cloudové infrastruktury (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: jeconnoc
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: 711fce9627537e68171c3b170121900d6b14ca1e
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743647"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure-preview"></a>Řešení aplikace Oracle integrace Microsoft Azure a Oracle cloudové infrastruktury (preview)

Microsoft a Oracle uzavřeli partnerství s cílem poskytnout s nízkou latencí a vysokou propustnost připojení cloudu, vám umožní využít to nejlepší z obou cloudy. 

Pomocí tohoto připojení cloudu, můžete dělit vícevrstvou aplikaci pro spuštění v Oracle cloudové infrastruktury (OCI), databázová vrstva a aplikace a ostatními úrovněmi v Microsoft Azure. Možnosti jsou podobné ke spouštění zásobníku celého řešení v jeden cloud. 

> [!IMPORTANT]
> Tato funkce cloudu je aktuálně ve verzi preview a některé [platí omezení](#preview-limitations). Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Některé aspekty této funkce se můžou před zveřejněním změnit.

Pokud vás zajímá nasazení řešení Oracle výhradně na infrastrukturu Azure, přečtěte si téma [imagí virtuálních počítačů Oracle a jejich nasazení v Microsoft Azure](oracle-vm-solutions.md).

## <a name="scenario-overview"></a>Přehled scénáře

Připojení cloudu poskytuje řešení pro spouštění aplikací špičkové společnosti Oracle a své vlastní aplikace na Azure virtual machines využívat výhod služeb hostovanou databází v OCI současně. 

Aplikace, které můžete spustit v konfiguraci cloudu patří:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Aplikace Oracle maloobchodního prodeje
* Oracle, Hyperion finanční správy

V následujícím diagramu je podrobný přehled připojených řešení. Pro zjednodušení diagram znázorňuje pouze aplikační vrstvu a datovou vrstvu. V závislosti na architecutre aplikace může vaše řešení obsahovat další vrstvy, jako je například webová vrstva v Azure. Další informace najdete v následující části.

![Přehled řešení Azure OCI](media/oracle-oci-overview/crosscloud.png)

## <a name="preview-limitations"></a>Omezení verze Preview

* Připojení cloudu ve verzi preview je omezená na oblasti Azure USA – východ (eastus) a oblast (USA ashburn-1) OCI Ashburn.

## <a name="networking"></a>Sítě

Podnikoví zákazníci často rozhodnete diversify a nasadit úlohy o velikosti přes více cloudy pro různé obchodní a provozních důvodů. Rozšíření, zákazníci propojení cloudových sítích pomocí Internetu, IPSec VPN nebo pomocí řešení přímé připojení k poskytovateli cloudu prostřednictvím místní sítě. Propojení cloudových sítích může vyžadovat významné investic v čas, peníze, návrh, nákup, instalace, testování a operace. 

Chcete-li vyřešit tyto problémy zákazníků, Oracle a Microsoft povolili integrované prostředí multicloudové. Cloudu sítí se navazuje pomocí připojení [ExpressRoute](../../../expressroute/expressroute-introduction.md) okruh v Microsoft Azure vedenou [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) okruh v OCI. Toto připojení je možné, kde je umístění partnerského vztahu Azure ExpressRoute v blízkosti nebo ve stejném umístění partnerského vztahu jako OCI FastConnect. Toto nastavení umožňuje rychlé, zabezpečené připojení mezi dva cloudy bez nutnosti poskytovatele zprostředkující služeb.

Pomocí ExpressRoute a FastConnect, zákazníci můžou vytvořit partnerský vztah virtuální sítě v Azure s virtuální cloudové sítě v OCI, za předpokladu, že se nepřekrývá privátní adresní prostor IP adres. Vytvoření partnerského vztahu dvě sítě umožňuje prostředků ve virtuální síti komunikovat na prostředek v cloudu virtuální sítě OCI jako, pokud jsou oba ve stejné síti.

## <a name="network-security"></a>Zabezpečení sítě

Zabezpečení sítě je zásadní součástí všechny podnikové aplikace a je toto multicloudové řešení soustředí. Všechny přenosy přes ExpressRoute a FastConnect projde přes privátní síť. Tato konfigurace umožňuje zabezpečenou komunikaci mezi virtuální sítí Azure a cloudové virtuální síti typu Oracle. Není nutné poskytnout veřejné IP adresy na všechny virtuální počítače v Azure. Podobně není nutné internetovou bránu v OCI. Veškerá komunikace probíhá přes privátní IP adresu počítače.

Kromě toho můžete nastavit [seznamy zabezpečení](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) na vaše OCI cloudové virtuální sítě a pravidel zabezpečení (připojené k Azure [skupiny zabezpečení sítě](../../../virtual-network/security-overview.md)). Pomocí těchto pravidel můžete řídit provoz mezi počítači ve virtuálních sítích. Pravidla zabezpečení sítě je možné přidat na úrovni počítače, na úrovni podsítě a na všech úrovních virtuální sítě.
 
## <a name="identity"></a>Identita

Identita je jedním z pilířů core partnerství mezi společnostmi Microsoft a Oracle. Bylo provedeno rozsáhlou integraci [Cloudová služba identit Oracle](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) s [Azure Active Directory](../../../active-directory/index.yml) (Azure AD). Azure AD je služba Microsoftu pro cloudový identit a přístupu správy. Umožňuje uživatelům přihlášení a přístup k různým prostředkům. Azure AD také umožňuje spravovat uživatele a jejich oprávnění.

V současné době Tato integrace vám umožní spravovat na jednom centrálním místě, což je Azure Active Directory. Azure AD synchronizuje všechny změny v adresáři s odpovídající adresář Oracle a slouží pro jednotné přihlašování k cloudu Oracle řešení.

## <a name="next-steps"></a>Další postup

Další informace a dokumenty White Paper o OCI, najdete v článku [Oracle cloudu](https://docs.cloud.oracle.com/iaas/Content/home.htm) dokumentaci.
