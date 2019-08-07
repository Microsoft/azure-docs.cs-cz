---
title: Vytvoření řešení Azure VMware pomocí CloudSimple privátního cloudu
description: Popisuje vytvoření privátního cloudu CloudSimple pro rozšiřování úloh VMware do cloudu s využitím provozní flexibility a kontinuity.
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 02a2bd311ea1e89a49eb12ef57a167a08eea5f98
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812249"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Vytvoření privátního cloudu CloudSimple

Vytvoření privátního cloudu vám pomůže vyřešit celou řadu běžných potřeb pro síťovou infrastrukturu:

* **Růst**. Pokud jste se seznámili s bodem aktualizace hardwaru pro stávající infrastrukturu, privátní Cloud umožňuje rozšíření bez nutnosti nové investice do hardwaru.

* **Rychlé rozšíření**. Pokud nastanou dočasné nebo neplánované nároky na kapacitu, privátní cloud vám umožní vytvořit další kapacitu bez prodlení.

* **Zvýšená ochrana**. Pomocí privátního cloudu se třemi nebo více uzly získáte automatickou ochranu redundance a vysokou dostupnost.

* **Dlouhodobé potřeby infrastruktury**. Pokud vaše datacentra mají kapacitu v kapacitě nebo chcete změnit jejich strukturu a snížit tak náklady, privátní cloud vám umožní vyřadit datová centra a migrovat je do cloudového řešení, a to i v případě, že budou kompatibilní s podnikovými operacemi.

Při vytváření privátního cloudu získáte jeden cluster vSphere a všechny virtuální počítače pro správu, které jsou vytvořeny v tomto clusteru.

## <a name="before-you-begin"></a>Před zahájením

Uzly musí být zřízené, aby bylo možné vytvořit privátní cloud.  Další informace o zřizovacích uzlech najdete v článku [zřízení uzlů pro řešení VMware podle CloudSimple – Azure](create-nodes.md) .

Přidělte rozsah CIDR pro podsítě vSphere/síti vSAN pro privátní cloud. Privátní cloud se vytvoří jako izolovaný zásobník VMware (ESXi hosts, vCenter, síti vSAN a NSX), který spravuje Server vCenter. Součásti pro správu se nasazují v síti vybrané pro směrování vSphere/síti vSAN pro sítě. Rozsah směrování sítě je v průběhu nasazení rozdělen do různých podsítí.  Adresní prostor podsítě vSphere/síti vSAN musí být jedinečný. Nesmí se překrývat s žádnou sítí, která komunikuje s prostředím CloudSimple.  Sítě, které komunikují s CloudSimple, zahrnují místní sítě a virtuální sítě Azure.  Další informace o podsítích vSphere/síti vSAN najdete v tématu [Přehled sítí VLAN a podsítí](cloudsimple-vlans-subnets.md).

* Minimální předpona rozsahu CIDR podsítě vSphere/síti vSAN:/24 
* Maximální předpona rozsahu CIDR podsítě vSphere/síti vSAN:/21

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Přístup k portálu CloudSimple

Přístup k [portálu CloudSimple](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Vytvoření nového privátního cloudu

1. Na stránce **prostředky** klikněte na **Nový privátní cloud**.

    ![Vytvoření privátního cloudu – jak začít](media/create-pc-button.png)

2. Vyberte umístění pro hostování prostředků privátního cloudu.

3. Vyberte CS28 nebo CS36 typ uzlu you'ev zřízené pro daný privátní cloud. Tato možnost zahrnuje maximální kapacitu výpočetní kapacity a paměti.

4. Vyberte počet uzlů pro privátní cloud. Můžete vybrat maximálně dostupný počet uzlů, které you'ev [zřízené](create-nodes.md).

    ![Vytvoření privátního cloudu – základní nastavení](media/create-private-cloud-basic-info.png)

5. Klikněte **na další: Rozšířené možnosti**.

6. Zadejte rozsah CIDR pro podsítě vSphere/síti vSAN. Ujistěte se, že se rozsah CIDR nepřekrývá s žádnou místní nebo jinou podsítí Azure (virtuální sítě) nebo s podsítí brány.  Nepoužívejte žádný rozsah CIDR definovaný v Azure Virtual Networks.
    
    **Možnosti rozsahu CIDR:** /24,/23,/22 nebo/21. Rozsah CIDR/24 v rozsahu podporuje až devět uzlů, rozsah CIDR/23 podporuje až 41 uzlů a rozsah CIDR/22 a/21 podporuje až 64 uzlů (maximální počet uzlů v privátním cloudu).

    > [!CAUTION]
    > IP adresy v rozsahu vSphere/síti vSAN CIDR jsou vyhrazené pro použití v infrastruktuře privátního cloudu.  Nepoužívejte IP adresu v tomto rozsahu na žádném virtuálním počítači.

7. Klikněte **na další: Zkontrolujte a vytvořte**.

8. Zkontrolujte nastavení. Pokud potřebujete změnit nějaké nastavení, klikněte na tlačítko **Předchozí**.

9. Klikněte na možnost **Vytvořit**.

Po kliknutí na vytvořit se spustí zřizování privátního cloudu.  Na portálu CloudSimple můžete sledovat stránku průběh [úloh](https://docs.azure.cloudsimple.com/activity/#tasks) .  Zřizování může trvat 30 minut až dvou hodin.  Až se zřizování dokončí, dostanete e-mail.

## <a name="next-steps"></a>Další postup

* [Rozšíření privátního cloudu](expand-private-cloud.md)