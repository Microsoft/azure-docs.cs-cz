---
title: Azure VMware Solution by CloudSimple
description: Seznamte se se službou Azure VMware Solutions by CloudSimple, včetně přehledu, rychlých zprovoznění, kurzů a praktických průvodců.
author: sharaths-cs
ms.author: b-mashar
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.custom: seo-azure-migrate
keywords: vms support, azure vmware solution by cloudsimple, cloudsimple azure, vms tools, vmware documentation
ms.openlocfilehash: fffe0255bb905c0314e2ed59f5beab726e681e00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89230632"
---
# <a name="azure-vmware-solution-by-cloudsimple"></a>Azure VMware Solution by CloudSimple

Vítá vás univerzální portál, který vám pomůže s Azure VMware Solution by CloudSimple.
Na dokumentačním webu se dozvíte víc o následujících tématech:

## <a name="overview"></a>Přehled

Další informace o Azure VMware Solution by CloudSimple

* Další informace o funkcích, výhodách a scénářích využití najdete v tématu [Co je Azure VMware Solution by CloudSimple](cloudsimple-vmware-solutions-overview.md).
* Projděte si [klíčové koncepty pro správu](key-concepts.md).

## <a name="quickstart"></a>Rychlý start

Naučte se toto řešení využívat.

* Zjistěte, jak [inicializovat tuto službu a koupit kapacitu](quickstart-create-cloudsimple-service.md).
* V článku věnovaném [konfiguraci prostředí privátního cloudu](quickstart-create-private-cloud.md) se naučíte vytvořit nové prostředí VMware.
* Projděte si článek věnovaný [využití virtuálních počítačů VMware v Azure](quickstart-create-vmware-virtual-machine.md) a zjistěte, jak sjednotit správu napříč VMware a Azure.

## <a name="concepts"></a>Koncepty

Přečtěte si víc o následujících konceptech.

* [Služba CloudSimple](cloudsimple-service.md) (označuje se také jako Azure VMware Solution by CloudSimple – služba). Tento prostředek se musí vytvořit jednou pro každou oblast.
* Nákup kapacity pro vaše prostředí vytvořením jednoho nebo několika prostředků [Uzel CloudSimple](cloudsimple-node.md). Tyto prostředky se také označují jako Azure VMware Solution by CloudSimple – uzel.
* Inicializace a konfigurace prostředí VMware s využitím [privátních cloudů](cloudsimple-private-cloud.md).
* Sjednocení správy s využitím [virtuálních počítačů CloudSimple](cloudsimple-virtual-machines.md) (označují se také jako Azure VMware Solution by CloudSimple – virtuální počítač).
* Návrh základní sítě s využitím [sítí VLAN nebo podsítí](cloudsimple-vlans-subnets.md).
* Segmentace a zabezpečení základní sítě s využitím prostředku [Tabulka bran firewall](cloudsimple-firewall-tables.md).
* Zajištění zabezpečeného přístupu k vašim prostředím VMware přes WAN s využitím [služeb VPN Gateway](cloudsimple-vpn-gateways.md).
* Povolení veřejného přístupu pro úlohy s využitím [veřejné IP adresy](cloudsimple-public-ip-address.md).
* Navázání připojení k virtuálním sítím Azure a místním sítím a místním sítím pomocí [síťového připojení Azure](cloudsimple-azure-network-connection.md).
* Konfigurace cílů upozorňovacích e-mailů s využitím [správy účtů](cloudsimple-account.md).
* Zobrazení protokolů aktivit uživatelů a systému pomocí obrazovek [správy aktivit](cloudsimple-activity.md)
* Seznámení s různými [součástmi VMware](vmware-components.md).

## <a name="tutorials"></a>Kurzy

Naučte se provádět běžné úlohy, například:

* [Vytvoření služby CloudSimple](create-cloudsimple-service.md), jedné pro každou oblast, ve které chcete nasadit prostředí VMware.
* Správa základních funkcí služby na [portálu CloudSimple](access-cloudsimple-portal.md).
* Povolení kapacity a optimalizace fakturace pro vaši infrastrukturu prostřednictvím [nákupu uzlů CloudSimple](create-nodes.md).
* Správa konfigurací prostředí VMware s využitím privátních cloudů. Privátní cloudy můžete [vytvářet](create-private-cloud.md), [spravovat](manage-private-cloud.md), [zvětšovat](expand-private-cloud.md) nebo [zmenšovat](shrink-private-cloud.md).
* Zajištění jednotné správy díky [mapování předplatných Azure](azure-subscription-mapping.md).
* Monitorování aktivity uživatelů a systému prostřednictvím [stránek aktivit](monitor-activity.md)
* Konfigurace sítě pro vaše prostředí [vytvořením podsítí a jejich správou](create-vlan-subnet.md).
* Segmentace a zabezpečení vašeho prostředí s využitím [pravidel a tabulek bran firewall](firewall.md).
* Povolení příchozího internetového přístupu pro úlohy [přidělením veřejných IP adres](public-ips.md).
* Zajištění možností připojení z interních sítí nebo klientských pracovních stanic [nastavením VPN](vpn-gateway.md).
* Povolení komunikace z [místních prostředí](on-premises-connection.md) i do [virtuálních sítí Azure](virtual-network-connection.md).
* Konfigurace cílů upozornění a zobrazení celkové zakoupené kapacity v [přehledu účtů](account.md).
* Zobrazení [uživatelů](users.md), kteří využili přístup k portálu CloudSimple.
* Správa virtuálních počítačů VMware na webu Azure Portal:
    * [Vytváření virtuálních počítačů](azure-create-vm.md) na webu Azure Portal.
    * [Správa virtuálních počítačů](azure-manage-vm.md), které jste vytvořili.

## <a name="how-to-guides"></a>Návody

Tyto příručky popisují řešení s nejrůznějšími cíli, jako například:

* [Zabezpečení vašeho prostředí](private-cloud-secure.md)
* Instalace nástrojů třetích stran, povolení dalších uživatelů a zdroj externího ověřování ve vSphere s využitím [elevace oprávnění](escalate-privileges.md)
* Konfigurace přístupu k různým službám VMware prostřednictvím [konfigurace DNS v místním prostředí](on-premises-dns-setup.md)
* Povolení přidělování názvů a adres pro vaše úlohy [konfigurací DHCP a DNS pro úlohy](dns-dhcp-setup.md)
* Zjištění, jak tato služba zajišťuje zabezpečení a funkce ve vaší platformě prostřednictvím [aktualizací a upgradů](vmware-components.md#updates-and-upgrades)
* Úspora celkových nákladů na vlastnictví pro zálohy vytvořením ukázkové architektury zálohování s využitím [zálohovacího softwaru jiného výrobce, jako je Veeam](backup-workloads-veeam.md)
* Vytvoření zabezpečeného prostředí povolením šifrování neaktivních uložených dat s využitím [šifrovacího softwaru KMS jiného výrobce](vsan-encryption.md)
* Rozšíření správy Azure Active Directory (Azure AD) do VMware konfigurací [zdroje identit Azure AD](azure-ad.md)
