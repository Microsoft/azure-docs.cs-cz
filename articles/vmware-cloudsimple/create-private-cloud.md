---
title: Řešení Azure VMware (AVS) – vytvoření privátního cloudu služby AVS
description: Popisuje vytvoření privátního cloudu služby AVS pro rozšiřování úloh VMware do cloudu s využitím provozní flexibility a kontinuity.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 128a0a1eec03878d0deba93048c54324aab7d888
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024785"
---
# <a name="create-an-avs-private-cloud"></a>Vytvoření privátního cloudu služby AVS

Privátní cloud služby AVS je izolovaný zásobník VMware, který podporuje hostitele ESXi, vCenter, síti vSAN a NSX. Privátní cloudy pro funkci AVS jsou spravovány prostřednictvím portálu AVS. Mají svůj vlastní Server vCenter ve své vlastní doméně pro správu. Zásobník běží na vyhrazených uzlech a v izolovaných hardwarových uzlech.

Vytvoření privátního cloudu služby AVS vám pomůže vyřešit celou řadu běžných potřeb pro síťovou infrastrukturu:

* **Růst**. Pokud jste se seznámili s bodem aktualizace hardwaru pro stávající infrastrukturu, privátní cloud služby AVS umožňuje rozšíření bez nutnosti nové investice do hardwaru.

* **Rychlé rozšíření**. Pokud nastanou dočasné nebo neplánované nároky na kapacitu, privátní cloud služby AVS vám umožní vytvořit další kapacitu bez prodlení.

* **Zvýšená ochrana**. Pomocí privátního cloudu služby AVS se třemi nebo více uzly získáte automatickou ochranu redundance a vysokou dostupnost.

* **Dlouhodobé potřeby infrastruktury**. Pokud vaše datacentra mají kapacitu v kapacitě nebo chcete změnit jejich strukturu a snížit tak náklady, privátní cloud služby AVS umožňuje vyřadit datová centra a migrovat do cloudového řešení, a to i v případě, že budou kompatibilní s podnikovými operacemi.

Při vytváření privátního cloudu služby AVS získáte jeden cluster vSphere a všechny virtuální počítače pro správu vytvořené v tomto clusteru.

## <a name="before-you-begin"></a>Než začnete

Aby bylo možné vytvořit privátní cloud služby AVS, musí být uzly zřízené. Další informace o zřizovacích uzlech najdete v tématu [zřizování uzlů pro řešení Azure VMware (AVS)](create-nodes.md).

Přidělte rozsah CIDR pro podsítě vSphere/síti vSAN pro privátní cloud služby AVS. Privátní cloud služby AVS se vytvoří jako izolované prostředí VMware Stack (s hostiteli ESXi, vCenter, síti vSAN a NSX), které spravuje Server vCenter. Součásti pro správu se nasazují v síti, která je vybraná pro vSphere/síti vSAN pro podsítě CIDR. Rozsah směrování sítě je v průběhu nasazení rozdělen do různých podsítí. Adresní prostor podsítě vSphere/síti vSAN musí být jedinečný. Nesmí se překrývat s žádnou sítí, která komunikuje s prostředím AVS. Sítě, které komunikují se službou AVS, zahrnují místní sítě a virtuální sítě Azure. Další informace o podsítích vSphere/síti vSAN najdete v tématu Přehled sítí VLAN a podsítí.

* Minimální předpona rozsahu CIDR podsítě vSphere/síti vSAN:/24
* Maximální předpona rozsahu CIDR podsítě vSphere/síti vSAN:/21


## <a name="access-the-avs-portal"></a>Přístup k portálu pro funkci AVS

Přístup k [portálu AVS](access-cloudsimple-portal.md).

## <a name="create-a-new-avs-private-cloud"></a>Vytvoření nového privátního cloudu pro funkci AVS

1. Vyberte **Všechny služby**.
2. Vyhledejte **služby AVS**.
3. Vyberte službu AVS, na které chcete vytvořit privátní cloud služby AVS.
4. V **přehledu**klikněte na **vytvořit privátní cloud** a otevřete novou kartu prohlížeče pro portál AVS. Pokud se zobrazí výzva, přihlaste se pomocí přihlašovacích údajů pro přihlášení do Azure.

    ![Vytvoření privátního cloudu služby AVS z Azure](media/create-private-cloud-from-azure.png)

5. Na portálu služby AVS zadejte název vašeho privátního cloudu služby AVS.
6. Vyberte **umístění** pro váš privátní cloud služby AVS.
7. Vyberte **typ uzlu**, který se shoduje s tím, co jste zakoupili v Azure.
8. Zadejte **počet uzlů**. K vytvoření privátního cloudu služby AVS se vyžadují aspoň tři uzly.
5. Na portálu CloudSimple zadejte název vašeho privátního cloudu.
6. Vyberte **umístění** privátního cloudu.
7. Vyberte **typ uzlu**konzistentní s tím, co jste zřídili v Azure.
8. Zadejte **počet uzlů**.  K vytvoření privátního cloudu se vyžadují aspoň tři uzly.

    ![Vytvoření privátního cloudu pro funkci AVS – základní informace](media/create-private-cloud-basic-info.png)

9. Klikněte na **Další: Upřesnit možnosti**.
10. Zadejte rozsah CIDR pro podsítě vSphere/síti vSAN. Ujistěte se, že se rozsah CIDR nepřekrývá s žádnou místní nebo jinou podsítí Azure (virtuální sítě) nebo s podsítí brány.

    **Možnosti rozsahu CIDR:** /24,/23,/22 nebo/21. Rozsah CIDR v/24 podporuje až devět uzlů, rozsah CIDR/23 podporuje až 41 uzlů a rozsah CIDR/22 a/21 pro rozsah CIDR podporuje až 64 uzlů (maximální počet uzlů v privátním cloudu služby AVS).

    > [!IMPORTANT]
    > IP adresy v rozsahu vSphere/síti vSAN CIDR jsou vyhrazené pro použití v infrastruktuře privátního cloudu služby AVS. Nepoužívejte IP adresu v tomto rozsahu na žádném virtuálním počítači.

11. Klikněte na **Další: zkontrolovat a vytvořit**.
12. Zkontrolujte nastavení. Pokud potřebujete změnit nějaké nastavení, klikněte na tlačítko **Předchozí**.
13. Klikněte na **Vytvořit**.

Spustí se proces zřízení privátního cloudu AVS. Zřízení privátního cloudu služby AVS může trvat až dvě hodiny.

Pokyny k rozšíření existujícího privátního cloudu služby AVS najdete v tématu věnovaném [rozbalení privátního cloudu služby AVS](expand-private-cloud.md).
