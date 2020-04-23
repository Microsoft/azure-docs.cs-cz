---
title: Správa řešení Azure VMware pomocí cloudového privátního cloudu
description: Popisuje funkce, které jsou k dispozici pro správu prostředků a aktivit cloudu CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 13496a18f4c99b69a5b8095caf5b74a04d1bba88
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869302"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Správa prostředků a aktivit privátního cloudu

Privátní cloudy se spravují z portálu CloudSimple.  Zkontrolujte stav, dostupné prostředky, aktivitu v privátním cloudu a další nastavení z portálu CloudSimple.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Přístup k portálu CloudSimple

Přístup k [portálu CloudSimple](access-cloudsimple-portal.md).

## <a name="view-the-list-of-private-clouds"></a>Zobrazit seznam soukromých cloudů

Na kartě **Soukromé cloudy** na stránce **Prostředky** jsou uvedeny všechny privátní cloudy ve vašem předplatném. Informace zahrnují název, počet clusterů vSphere, umístění, aktuální stav privátního cloudu a informace o prostředcích.

![Stránka Privátního cloudu](media/manage-private-cloud.png)

Vyberte privátní cloud pro další informace a akce.

## <a name="private-cloud-summary"></a>Souhrn privátního cloudu

Prohlédněte si komplexní souhrn vybraného privátního cloudu.  Souhrnná stránka obsahuje servery DNS nasazené v privátním cloudu.  Můžete nastavit předávání DNS z místních serverů DNS na servery DNS privátního cloudu.  Další informace o předávání DNS najdete [v tématu Konfigurace dns pro překlad názvů pro private cloud vCenter z místního prostředí](https://docs.microsoft.com/azure/vmware-cloudsimple/on-premises-dns-setup/).

![Souhrn privátního cloudu](media/private-cloud-summary.png)

### <a name="available-actions"></a>Dostupné akce

* [Spusťte klienta vSphere](https://docs.microsoft.com/azure/vmware-cloudsimple/vcenter-access). Přístup k virtuálnímu centru pro tento privátní cloud.
* [Nákupní uzly](create-nodes.md). Přidejte uzly do tohoto privátního cloudu.
* [Rozbalte](expand-private-cloud.md). Přidejte uzly do tohoto privátního cloudu.
* **Aktualizovat**. Aktualizujte informace na této stránce.
* **Odstranit**. Privátní cloud můžete kdykoli odstranit. **Před odstraněním se ujistěte, že jste zálohovali všechny systémy a data.** Odstraněním privátního cloudu odstraníte všechny virtuální počítače, konfiguraci vcenter a data. V souhrnné části vybraného privátního cloudu klikněte na **Odstranit.** Po odstranění jsou všechna data privátního cloudu vymazána v zabezpečeném, vysoce kompatibilním procesu vymazání.
* [Změňte oprávnění vSphere](escalate-private-cloud-privileges.md).  Eskalujte svá oprávnění v tomto privátním cloudu.

## <a name="private-cloud-vlanssubnets"></a>Privátní cloudové sítě VLANS/podsítě

Zobrazení seznamu definovaných sítí VLAN/podsítí pro vybraný privátní cloud.  Seznam obsahuje správu sítí VLAN/podsítí vytvořených při vytvoření privátního cloudu.

![Privátní cloud – sítě VLAN/podsítě](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Dostupné akce

* [Přidejte VLANS/podsítě](https://docs.microsoft.com/azure/vmware-cloudsimple/create-vlan-subnet/). Přidejte do tohoto privátního cloudu síť VLAN/podmnožinu.

Výběr sítě VLAN/podsítě pro následující akce
* [Připojte tabulku brány firewall](https://docs.microsoft.com/azure/vmware-cloudsimple/firewall/). Připojte k tomuto privátnímu cloudu tabulku brány firewall.
* **Upravit**
* **Odstranit** (pouze uživatelem definované sítě VLAN/podsítě)

## <a name="private-cloud-activity"></a>Aktivita privátního cloudu

Zobrazení následujících informací pro vybraný privátní cloud.  Informace o aktivitě jsou filtrovaným seznamem všech aktivit pro vybraný privátní cloud.  Na této stránce se zobrazuje až 25 nedávných aktivit.

* Nedávná upozornění
* Nedávné události
* Nedávné úkoly
* Nedávný audit

![Privátní cloud – aktivita](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Cloudové regály

Cloudové regály jsou stavebními kameny vašeho privátního cloudu. Každý stojan poskytuje jednotku kapacity. CloudSimple automaticky konfiguruje cloudové racky na základě vašich výběrů při vytváření nebo rozšiřování privátního cloudu.  Prohlédněte si úplný seznam cloudových racků, včetně privátního cloudu, ke kterému je každý přiřazen.

![Privátní cloud – cloudové regály](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>Síť pro správu vSphere

Seznam prostředků pro správu společnosti VMware a virtuálních počítačů, které jsou aktuálně nakonfigurované v privátním cloudu. Informace zahrnují verzi softwaru, plně kvalifikovaný název domény (FQDN) a IP adresu prostředků.

![Privátní cloud – síť pro správu vSphere](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Další kroky

* [Využívání virtuálních počítačů VMware v Azure](quickstart-create-vmware-virtual-machine.md)
* Další informace o [privátních cloudech](cloudsimple-private-cloud.md)