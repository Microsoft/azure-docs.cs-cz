---
title: Řešení Azure VMware podle CloudSimple – vytvoření privátního cloudu CloudSimple
description: Popisuje vytvoření privátního cloudu CloudSimple pro rozšiřování úloh VMware do cloudu s využitím provozní flexibility a kontinuity.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4f700ac34b6c6e2a651366bee7dd1785c608064f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77024785"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Vytvoření privátního cloudu CloudSimple

Privátní cloud je izolovaný zásobník VMware, který podporuje hostitele ESXi, vCenter, síti vSAN a NSX. Privátní cloudy se spravují prostřednictvím portálu CloudSimple. Mají svůj vlastní Server vCenter ve své vlastní doméně pro správu. Zásobník běží na vyhrazených uzlech a v izolovaných hardwarových uzlech.

Vytvoření privátního cloudu vám pomůže vyřešit celou řadu běžných potřeb pro síťovou infrastrukturu:

* **Růst**. Pokud jste se seznámili s bodem aktualizace hardwaru pro stávající infrastrukturu, privátní Cloud umožňuje rozšíření bez nutnosti nové investice do hardwaru.

* **Rychlé rozšíření**. Pokud nastanou dočasné nebo neplánované nároky na kapacitu, privátní cloud vám umožní vytvořit další kapacitu bez prodlení.

* **Zvýšená ochrana**. Pomocí privátního cloudu se třemi nebo více uzly získáte automatickou ochranu redundance a vysokou dostupnost.

* **Dlouhodobé potřeby infrastruktury**. Pokud vaše datacentra mají kapacitu v kapacitě nebo chcete změnit jejich strukturu a snížit tak náklady, privátní cloud vám umožní vyřadit datová centra a migrovat je do cloudového řešení, a to i v případě, že budou kompatibilní s podnikovými operacemi.

Při vytváření privátního cloudu získáte jeden cluster vSphere a všechny virtuální počítače pro správu, které jsou vytvořeny v tomto clusteru.

## <a name="before-you-begin"></a>Než začnete

Uzly musí být zřízené, aby bylo možné vytvořit privátní cloud. Další informace o zřizovacích uzlech najdete v tématu [zřízení uzlů pro řešení Azure VMware pomocí CloudSimple](create-nodes.md).

Přidělte rozsah CIDR pro podsítě vSphere/síti vSAN pro privátní cloud. Privátní cloud se vytvoří jako izolované prostředí VMware Stack (s hostiteli ESXi, vCenter, síti vSAN a NSX), které spravuje Server vCenter. Součásti pro správu se nasazují v síti, která je vybraná pro vSphere/síti vSAN pro podsítě CIDR. Rozsah směrování sítě je v průběhu nasazení rozdělen do různých podsítí. Adresní prostor podsítě vSphere/síti vSAN musí být jedinečný. Nesmí se překrývat s žádnou sítí, která komunikuje s prostředím CloudSimple. Sítě, které komunikují s CloudSimple, zahrnují místní sítě a virtuální sítě Azure. Další informace o podsítích vSphere/síti vSAN najdete v tématu Přehled sítí VLAN a podsítí.

* Minimální předpona rozsahu CIDR podsítě vSphere/síti vSAN:/24
* Maximální předpona rozsahu CIDR podsítě vSphere/síti vSAN:/21


## <a name="access-the-cloudsimple-portal"></a>Přístup k portálu CloudSimple

Přístup k [portálu CloudSimple](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Vytvoření nového privátního cloudu

1. Vyberte **Všechny služby**.
2. Vyhledejte **služby CloudSimple Services**.
3. Vyberte službu CloudSimple, na které chcete vytvořit privátní cloud.
4. V **přehledu**klikněte na **vytvořit privátní cloud** . otevře se nová karta prohlížeče pro CloudSimple portál. Pokud se zobrazí výzva, přihlaste se pomocí přihlašovacích údajů pro přihlášení do Azure.

    ![Vytvoření privátního cloudu z Azure](media/create-private-cloud-from-azure.png)

5. Na portálu CloudSimple zadejte název vašeho privátního cloudu.
6. Vyberte **umístění** privátního cloudu.
7. Vyberte **typ uzlu**konzistentní s tím, co jste zřídili v Azure.
8. Zadejte **počet uzlů**.  K vytvoření privátního cloudu se vyžadují aspoň tři uzly.

    ![Vytvořit privátní cloud – základní informace](media/create-private-cloud-basic-info.png)

9. Klikněte na **Další: Upřesnit možnosti**.
10. Zadejte rozsah CIDR pro podsítě vSphere/síti vSAN. Ujistěte se, že se rozsah CIDR nepřekrývá s žádnou místní nebo jinou podsítí Azure (virtuální sítě) nebo s podsítí brány.

    **Možnosti rozsahu CIDR:** /24,/23,/22 nebo/21. Rozsah CIDR/24 v rozsahu podporuje až devět uzlů, rozsah CIDR/23 podporuje až 41 uzlů a rozsah CIDR/22 a/21 podporuje až 64 uzlů (maximální počet uzlů v privátním cloudu).

    > [!IMPORTANT]
    > IP adresy v rozsahu vSphere/síti vSAN CIDR jsou vyhrazené pro použití v infrastruktuře privátního cloudu.  Nepoužívejte IP adresu v tomto rozsahu na žádném virtuálním počítači.

11. Klikněte na **Další: zkontrolovat a vytvořit**.
12. Zkontrolujte nastavení. Pokud potřebujete změnit nějaké nastavení, klikněte na tlačítko **Předchozí**.
13. Klikněte na **Vytvořit**.

Spustí se proces zřizování privátního cloudu. Zřízení privátního cloudu může trvat až dvě hodiny.

Pokyny k rozšíření existujícího privátního cloudu najdete v tématu věnovaném [rozšíření privátního cloudu](expand-private-cloud.md).
