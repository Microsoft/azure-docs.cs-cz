---
title: Spravovat řešení Azure VMware CloudSimple privátního cloudu
description: Popisuje možnosti dostupné pro správu prostředků CloudSimple privátního cloudu a aktivita
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 05a2fb451b3acce1011c1d5f4cf17f0a865d57d0
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332641"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Správa prostředků privátního cloudu a aktivita

Privátní cloudy spravují CloudSimple portálu.  Zkontrolujte stav, dostupných prostředků, aktivita na privátní cloud a další nastavení z portálu CloudSimple.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Přístup k portálu CloudSimple

Přístup [CloudSimple portál](access-cloudsimple-portal.md).

## <a name="view-the-list-of-private-clouds"></a>Zobrazí se seznam privátních Cloudů

**Privátních Cloudů** kartě **prostředky** stránka obsahuje seznam všech privátních Cloudů ve vašem předplatném. Informace zahrnují název, číslo vSphere clusterů, umístění, aktuální stav privátního cloudu a prostředků.

![Stránka privátního cloudu](media/manage-private-cloud.png)

Vyberte privátní Cloud pro další informace a akce.

## <a name="private-cloud-summary"></a>Souhrn privátního cloudu

Zobrazte kompletní přehled vybraného privátního cloudu.  Stránka souhrnu obsahuje servery DNS, které jsou nasazeny do privátního cloudu.  Můžete nastavit DNS předává z místních serverů DNS na vaše servery DNS privátního cloudu.  Další informace o předávání DNS najdete v tématu [konfigurace služby DNS pro překlad názvů pro vCenter privátního cloudu z místního](https://docs.azure.cloudsimple.com/on-premises-dns-setup/).

![Souhrnné informace o privátního mraku](media/private-cloud-summary.png)

### <a name="available-actions"></a>Dostupné akce

* [Spuštění klienta vSphere](https://docs.azure.cloudsimple.com/vsphere-access/). Přístup k vCenter pro tento privátní Cloud.
* [Zakoupit uzly](create-nodes.md). Přidání uzlů do tohoto privátního cloudu.
* [Rozbalte](expand-private-cloud.md). Přidání uzlů do tohoto privátního cloudu.
* **Aktualizovat**. Aktualizujte informace na této stránce.
* **Odstranit**. Privátní Cloud můžete kdykoli odstranit. **Před odstraněním, ujistěte se, že jste zálohovali všechny systémy a data.** Odstraněním privátního cloudu se odstraní všechny virtuální počítače, vCenter konfiguraci a data. Klikněte na tlačítko **odstranit** v souhrnné části pro vybrané privátního cloudu. Po odstranění se vymažou všechna data privátního cloudu v zabezpečené, vysoce kompatibilní s mazání procesu.
* [Změnit oprávnění vSphere](escalate-private-cloud-privileges.md).  Zvýšení vaší oprávnění na tento privátní Cloud.

## <a name="private-cloud-vlanssubnets"></a>Private Cloud VLANS/subnets

Zobrazení seznamu definované sítě VLAN a podsítě pro vybranou privátního cloudu.  Seznam obsahuje management, vytvoření sítě VLAN a podsítě při vytvoření privátního cloudu.

![Privátní Cloud – sítě VLAN a podsítě](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Dostupné akce

* [Přidání sítě VLAN a podsítě](https://docs.azure.cloudsimple.com/create-vlan-subnet/). Přidání sítě VLAN nebo podmnožinu do tohoto privátního cloudu.

Vyberte sítě VLAN a podsítě pro následující akce
* [Připojení brány firewall tabulky](https://docs.azure.cloudsimple.com/firewall/). Připojte tabulku brány firewall na tomto privátním cloudu.
* **Upravit**
* **Odstranit** (pouze uživatelské sítě VLAN a podsítě)

## <a name="private-cloud-activity"></a>Aktivita privátního cloudu

Zobrazte následující informace o vybrané privátního cloudu.  Informace o aktivitě je filtrovaný seznam všech aktivit pro vybrané privátního cloudu.  Tato stránka zobrazuje až 25 nedávných aktivit.

* Nedávné výstrahy
* Nedávné události
* Poslední úkoly
* Poslední auditu

![Privátní Cloud – aktivita](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Stojany cloudu

Cloud stojany jsou stavební kameny nástroje privátního cloudu. Každý rack obsahuje jednotka kapacity. CloudSimple automaticky nakonfiguruje stojany cloudu na základě vašeho výběru, při vytváření nebo rozšiřování privátního cloudu.  Zobrazit úplný seznam stojany cloudu, včetně privátního cloudu, které každý přiřazen.

![Privátní Cloud – stojany cloudu](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere síť pro správu

Seznam řízení zdrojů VMware a virtuálních počítačů, které jsou aktuálně nakonfigurované na privátní Cloud. Informace zahrnují verze softwaru, plně kvalifikovaný název domény (FQDN) a IP adresu zdroje.

![Privátní Cloud – vSphere síť pro správu](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Další postup

* [Využívání virtuálních počítačů VMware v Azure](quickstart-create-vmware-virtual-machine.md)
* Další informace o [privátních Cloudů](cloudsimple-private-cloud.md)