---
title: Správa řešení Azure VMware pomocí CloudSimple privátního cloudu
description: Popisuje možnosti, které jsou k dispozici pro správu prostředků privátního cloudu CloudSimple a aktivity.
author: Ajayan1008
ms.author: v-hborys
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4f2f66c2e1e2e8aa596393d4c69a757138ab5a91
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895202"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Správa prostředků a aktivit privátního cloudu

Privátní cloudy se spravují z portálu CloudSimple.  Projděte si stav, dostupné prostředky, činnost na privátním cloudu a další nastavení z portálu CloudSimple.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Přístup k portálu CloudSimple

Přístup k [portálu CloudSimple](access-cloudsimple-portal.md).

## <a name="view-the-list-of-private-clouds"></a>Zobrazit seznam privátních cloudů

Karta **privátní cloudy** na stránce **prostředky** obsahuje seznam všech privátních cloudů v rámci vašeho předplatného. Informace zahrnují název, počet clusterů vSphere, umístění, aktuální stav privátního cloudu a informace o prostředcích.

![Stránka privátního cloudu](media/manage-private-cloud.png)

Pro další informace a akce vyberte privátní cloud.

## <a name="private-cloud-summary"></a>Souhrn privátního cloudu

Zobrazí komplexní souhrn vybraného privátního cloudu.  Stránka Souhrn obsahuje servery DNS nasazené v privátním cloudu.  Předávání DNS z místních serverů DNS můžete nastavit na servery DNS privátního cloudu.  Další informace o předávání DNS najdete v tématu [Konfigurace DNS pro překlad IP adres pro privátní cloud vCenter z místního](./on-premises-dns-setup.md)prostředí.

![Souhrn privátního cloudu](media/private-cloud-summary.png)

### <a name="available-actions"></a>Dostupné akce

* [Spusťte klienta vSphere](./vcenter-access.md). Přístup k vCenter pro tento privátní cloud.
* [Koupit uzly](create-nodes.md). Přidejte uzly do tohoto privátního cloudu.
* [Rozbalte](expand-private-cloud.md). Přidejte uzly do tohoto privátního cloudu.
* **Aktualizovat** Aktualizujte informace na této stránce.
* **Delete**. Privátní Cloud můžete kdykoli odstranit. **Před odstraněním se ujistěte, že jste zálohovali všechny systémy a data.** Odstranění privátního cloudu odstraní všechny virtuální počítače, konfiguraci vCenter a data. V části Souhrn pro vybraný privátní cloud klikněte na **Odstranit** . Po odstranění se všechna data privátního cloudu vymažou v bezpečném a vysoce kompatibilním procesu mazání.
* [Změňte oprávnění vSphere](escalate-private-cloud-privileges.md).  Eskalovat vaše oprávnění v tomto privátním cloudu.

## <a name="private-cloud-vlanssubnets"></a>SÍTĚ VLAN a podsítě privátního cloudu

Zobrazí seznam definovaných sítí VLAN a podsítí pro vybraný privátní cloud.  Seznam obsahuje sítě VLAN a podsítě pro správu, které byly vytvořeny při vytváření privátního cloudu.

![Privátní cloud – sítě VLAN/podsítě](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Dostupné akce

* [Přidejte sítě VLAN/podsítě](./create-vlan-subnet.md). Přidejte k tomuto privátnímu cloudu síť VLAN nebo podmnožinu.

Vyberte síť VLAN nebo podsíť pro následující akce.
* [Připojte tabulku brány firewall](./firewall.md). Připojte k tomuto privátnímu cloudu tabulku brány firewall.
* **Upravit**
* **Odstranit** (jenom uživatelsky definované sítě VLAN/podsítě)

## <a name="private-cloud-activity"></a>Aktivita privátního cloudu

Zobrazí následující informace o vybraném privátním cloudu.  Informace o aktivitách jsou filtrovaný seznam všech aktivit pro vybraný privátní cloud.  Tato stránka zobrazuje až 25 posledních aktivit.

* Nedávné výstrahy
* Nedávné události
* Poslední úkoly
* Poslední audit

![Privátní cloud – aktivita](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Cloudové racky

Cloudové racky jsou stavebními kameny vašeho privátního cloudu. Každý stojan poskytuje jednotku kapacity. CloudSimple automaticky konfiguruje cloudové racky na základě vašich voleb při vytváření nebo rozšiřování privátního cloudu.  Zobrazte úplný seznam cloudových stojanů, včetně privátního cloudu, ke kterému jsou přiřazeny.

![Privátní cloud – cloudové racky](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>Síť pro správu vSphere

Seznam prostředků správy VMware a virtuálních počítačů, které jsou aktuálně nakonfigurované v privátním cloudu. Informace zahrnují verzi softwaru, plně kvalifikovaný název domény (FQDN) a IP adresu prostředků.

![Privátní cloud – síť pro správu vSphere](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Další kroky

* [Využívání virtuálních počítačů VMware v Azure](quickstart-create-vmware-virtual-machine.md)
* Další informace o [privátních cloudech](cloudsimple-private-cloud.md)
