---
title: Správa privátního cloudu řešení Azure VMware Solutions (AVS)
description: V této části najdete popis dostupných možností pro správu prostředků a aktivit privátního cloudu služby AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 47bf2251f71204b99245c1a9d55ef87157c41dd8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014823"
---
# <a name="manage-avs-private-cloud-resources-and-activities"></a>Správa prostředků a aktivit privátního cloudu pro funkci AVS

Privátní cloudy služby AVS se spravují z portálu AVS. Ověřte stav, dostupné prostředky, činnost na privátním cloudu služby AVS a další nastavení z portálu služby AVS.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-avs-portal"></a>Přístup k portálu pro funkci AVS

Přístup k [portálu AVS](access-cloudsimple-portal.md).

## <a name="view-the-list-of-avs-private-clouds"></a>Zobrazit seznam privátních cloudů pro funkci AVS

Karta **soukromé cloudy služby AVS** na stránce **prostředky** obsahuje seznam všech privátních cloudů služby AVS v rámci vašeho předplatného. Informace zahrnují název, počet clusterů vSphere, umístění, aktuální stav privátního cloudu pro funkci AVS a informace o prostředku.

![Stránka privátního cloudu pro funkci AVS](media/manage-private-cloud.png)

Pokud chcete získat další informace a akce, vyberte privátní cloud služby AVS.

## <a name="avs-private-cloud-summary"></a>Souhrn privátního cloudu pro funkci AVS

Zobrazí komplexní souhrn vybraného privátního cloudu pro funkci AVS. Stránka Souhrn obsahuje servery DNS nasazené v privátním cloudu služby AVS. Předávání DNS z místních serverů DNS můžete nastavit na servery DNS privátního cloudu služby AVS. Další informace o předávání DNS najdete v tématu [Konfigurace DNS pro překlad IP adres pro službu AVS Private Cloud vCenter z místního](https://docs.azure.cloudsimple.com/on-premises-dns-setup/)prostředí.

![Souhrn privátního cloudu pro funkci AVS](media/private-cloud-summary.png)

### <a name="available-actions"></a>Dostupné akce

* [Spusťte klienta vSphere](https://docs.azure.cloudsimple.com/vsphere-access/). Přístup k vCenter pro tento privátní cloud služby AVS
* [Koupit uzly](create-nodes.md). Přidejte uzly do tohoto privátního cloudu pro funkci AVS.
* [Rozbalte](expand-private-cloud.md). Přidejte uzly do tohoto privátního cloudu pro funkci AVS.
* **Aktualizovat**. Aktualizujte informace na této stránce.
* **Odstraňte**. Privátní cloud AVS můžete kdykoli odstranit. **Před odstraněním se ujistěte, že jste zálohovali všechny systémy a data.** Odstranění privátního cloudu služby AVS odstraní všechny virtuální počítače, konfiguraci vCenter a data. V části Souhrn pro vybraný privátní cloud služby AVS klikněte na **Odstranit** . Po odstranění se všechna data privátního cloudu AVS vymažou v bezpečném a vysoce kompatibilním procesu mazání.
* [Změňte oprávnění vSphere](escalate-private-cloud-privileges.md). Eskalovat vaše oprávnění k tomuto privátnímu cloudu služby AVS.

## <a name="avs-private-cloud-vlanssubnets"></a>SÍTĚ VLAN a podsítě privátního cloudu pro funkci AVS

Zobrazí seznam definovaných sítí VLAN a podsítí pro vybraný privátní cloud služby AVS. Seznam obsahuje sítě VLAN a podsítě pro správu, které byly vytvořeny při vytváření privátního cloudu pro funkci AVS.

![Privátní cloud pro funkci AVS – sítě VLAN/podsítě](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Dostupné akce

* [Přidejte sítě VLAN/podsítě](https://docs.azure.cloudsimple.com/create-vlan-subnet/). Přidejte do tohoto privátního cloudu pro funkci AVS síť VLAN nebo podmnožinu.

Vyberte síť VLAN nebo podsíť pro následující akce.
* [Připojte tabulku brány firewall](https://docs.azure.cloudsimple.com/firewall/). Umožňuje připojit tabulku brány firewall k tomuto privátnímu cloudu funkce AVS.
* **Upravit**
* **Odstranit** (jenom uživatelsky definované sítě VLAN/podsítě)

## <a name="avs-private-cloud-activity"></a>Aktivita privátního cloudu pro funkci AVS

Zobrazí následující informace o vybraném privátním cloudu služby AVS. Informace o aktivitách jsou filtrovaný seznam všech aktivit pro vybraný privátní cloud služby AVS. Tato stránka zobrazuje až 25 posledních aktivit.

* Nedávné výstrahy
* Nedávné události
* Poslední úkoly
* Poslední audit

![Privátní cloud AVS – aktivita](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Cloudové racky

Cloudové racky představují stavební kameny vašeho privátního cloudu služby AVS. Každý stojan poskytuje jednotku kapacity. Při vytváření nebo rozšiřování privátního cloudu služby AVS automaticky konfiguruje cloudové racky na základě vašich voleb. Zobrazte úplný seznam cloudových stojanů, včetně privátního cloudu pro funkci AVS, ke kterému jsou přiřazeny.

![Privátní cloud pro funkci AVS – cloudové racky](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>Síť pro správu vSphere

Seznam prostředků správy VMware a virtuálních počítačů, které jsou aktuálně nakonfigurované v privátním cloudu pro funkci AVS Informace zahrnují verzi softwaru, plně kvalifikovaný název domény (FQDN) a IP adresu prostředků.

![Privátní cloud AVS – síť pro správu vSphere](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Další kroky

* [Využití virtuálních počítačů VMware v Azure](quickstart-create-vmware-virtual-machine.md)
* Další informace o [privátních cloudech](cloudsimple-private-cloud.md) pro funkci AVS