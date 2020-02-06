---
title: Řešení Azure VMware (AVS)
description: Portál dokumentace pro řešení Azure VMware (AVS)
author: sharaths-cs
ms.author: b-mashar
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d81ea6778f3ba31d72c34334b1439994b076647c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025210"
---
# <a name="azure-vmware-solution-by-avs"></a>Řešení Azure VMware od AVS

Vítá vás univerzální portál, který vám pomůže s řešeními Azure VMware od AVS.
Na dokumentačním webu se dozvíte víc o následujících tématech:

## <a name="overview"></a>Přehled

Další informace o řešeních Azure VMware (AVS)

* Další informace o funkcích, výhodách a scénářích využití najdete v tématu [Co je řešení Azure VMware od AVS](cloudsimple-vmware-solutions-overview.md)
* Projděte si [klíčové koncepty pro správu](key-concepts.md).

## <a name="quickstart"></a>Rychlý start

Naučte se toto řešení využívat.

* Zjistěte, jak [inicializovat tuto službu a koupit kapacitu](quickstart-create-cloudsimple-service.md).
* V článku věnovaném [konfiguraci prostředí privátního cloudu AVS](quickstart-create-private-cloud.md) se naučíte vytvořit nové prostředí VMware.
* Projděte si článek věnovaný [využití virtuálních počítačů VMware v Azure](quickstart-create-vmware-virtual-machine.md) a zjistěte, jak sjednotit správu napříč VMware a Azure.

## <a name="concepts"></a>Koncepty

Přečtěte si víc o následujících konceptech.

* [Služba AVS](cloudsimple-service.md) (označovaná také jako řešení Azure VMware (AVS) – služba). Tento prostředek se musí vytvořit jednou pro každou oblast.
* Nákup kapacity pro vaše prostředí vytvořením jednoho nebo několika prostředků [Uzel AVS](cloudsimple-node.md). Tyto prostředky se také označují jako řešení Azure VMware od AVS – uzel.
* Inicializace a konfigurace prostředí VMware s využitím [privátních cloudů AVS](cloudsimple-private-cloud.md).
* Sjednocení správy s využitím [virtuálních počítačů AVS](cloudsimple-virtual-machines.md) (označují se také jako řešení Azure VMware od AVS – virtuální počítač).
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

* [Vytvoření služby AVS](create-cloudsimple-service.md), jedné pro každou oblast, ve které chcete nasadit prostředí VMware.
* Správa základních funkcí služby na [portálu AVS](access-cloudsimple-portal.md).
* Povolení kapacity a optimalizace fakturace pro vaši infrastrukturu prostřednictvím [nákupu uzlů AVS](create-nodes.md).
* Správa konfigurací prostředí VMware s využitím privátních cloudů AVS. Privátní cloudy AVS můžete [vytvářet](create-private-cloud.md), [spravovat](manage-private-cloud.md), [zvětšovat](expand-private-cloud.md) nebo [zmenšovat](shrink-private-cloud.md).
* Zajištění jednotné správy díky [mapování předplatných Azure](azure-subscription-mapping.md).
* Monitorování aktivity uživatelů a systému prostřednictvím [stránek aktivit](monitor-activity.md)
* Konfigurace sítě pro vaše prostředí [vytvořením podsítí a jejich správou](create-vlan-subnet.md).
* Segmentace a zabezpečení vašeho prostředí s využitím [pravidel a tabulek bran firewall](firewall.md).
* Povolení příchozího internetového přístupu pro úlohy [přidělením veřejných IP adres](public-ips.md).
* Zajištění možností připojení z interních sítí nebo klientských pracovních stanic [nastavením VPN](vpn-gateway.md).
* Povolení komunikace z [místních prostředí](on-premises-connection.md) i do [virtuálních sítí Azure](virtual-network-connection.md).
* Konfigurace cílů upozornění a zobrazení celkové zakoupené kapacity v [přehledu účtů](account.md).
* Zobrazení [uživatelů](users.md), kteří využili přístup k portálu AVS.
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
