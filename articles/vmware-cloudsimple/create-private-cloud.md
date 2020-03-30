---
title: Řešení Azure VMware od CloudSimple – vytvoření privátního cloudu CloudSimple
description: Popisuje, jak vytvořit cloudový privátní cloud pro rozšíření úloh VMware do cloudu s provozní flexibilitou a kontinuitou.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4f700ac34b6c6e2a651366bee7dd1785c608064f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024785"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Vytvoření cloudového jednoduchého privátního cloudu

Privátní cloud je izolovaný zásobník VMware, který podporuje hostitele ESXi, vCenter, vSAN a NSX. Privátní cloudy jsou spravovány prostřednictvím portálu CloudSimple. Mají svůj vlastní server vCenter ve své vlastní doméně pro správu. Zásobník běží na vyhrazených uzlech a izolovaných holé kovové hardwarové uzly.

Vytvoření privátního cloudu vám pomůže řešit řadu běžných potřeb síťové infrastruktury:

* **Růst**. Pokud jste dosáhli bodu aktualizace hardwaru pro stávající infrastrukturu, privátní cloud vám umožní expandovat bez nutnosti nových investic do hardwaru.

* **Rychlé rozšíření**. Pokud vzniknou jakékoli dočasné nebo neplánované potřeby kapacity, privátní cloud vám umožní vytvořit další kapacitu bez zpoždění.

* **Zvýšená ochrana**. S privátním cloudem se třemi nebo více uzly získáte automatickou redundanci a vysokou dostupnost.

* **Dlouhodobé potřeby infrastruktury**. Pokud jsou vaše datová centra na kapacitě nebo chcete restrukturalizovat, abyste snížili náklady, privátní cloud umožňuje vyřadit datová centra a migrovat na cloudové řešení a přitom zůstat kompatibilní s vašimi podnikovými operacemi.

Když vytvoříte privátní cloud, získáte jeden cluster vSphere a všechny virtuální ms pro správu, které jsou vytvořeny v tomto clusteru.

## <a name="before-you-begin"></a>Než začnete

Před vytvořením privátního cloudu je nutné zřídit uzly. Další informace o zřizování uzlů najdete v tématu [Zřízení uzlů pro řešení Azure VMware podle CloudSimple](create-nodes.md).

Přidělte rozsah CIDR pro podsítě vSphere/vSAN pro privátní cloud. Privátní cloud je vytvořen jako izolované prostředí zásobníku VMware (s hostiteli ESXi, vCenter, vSAN a NSX) spravované serverem vCenter. Součásti správy jsou nasazeny v síti vybrané pro podsítě vSphere/vSAN CIDR. Rozsah CIDR sítě je během nasazení rozdělen do různých podsítí. Adresní prostor podsítě vSphere/vSAN musí být jedinečný. Nesmí se překrývat s žádnou sítí, která komunikuje s prostředím CloudSimple. Sítě, které komunikují s CloudSimple patří místní sítě a virtuální sítě Azure. Další informace o podsítích vSphere/vSAN naleznete v tématu Přehled sítí VLAN a podsítí.

* Minimální předpona rozsahu řady VSphere/vSAN: /24
* Předpona rozsahu CIDR vSphere/vSAN: /21


## <a name="access-the-cloudsimple-portal"></a>Přístup k portálu CloudSimple

Přístup k [portálu CloudSimple](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Vytvoření nového privátního cloudu

1. Vyberte **Všechny služby**.
2. Vyhledejte **cloudové jednoduché služby**.
3. Vyberte službu CloudSimple, na které chcete vytvořit privátní cloud.
4. V **části Přehled**klikněte na Vytvořit **privátní cloud** a otevřete novou kartu prohlížeče pro portál CloudSimple. Pokud se zobrazí výzva, přihlaste se pomocí přihlašovacích údajů k Azure.

    ![Vytvoření privátního cloudu z Azure](media/create-private-cloud-from-azure.png)

5. Na portálu CloudSimple zadejte název privátního cloudu.
6. Vyberte **Umístění** pro svůj privátní cloud.
7. Vyberte **typ uzlu**, konzistentní s tím, co jste zřídit v Azure.
8. Zadejte **počet uzlů**.  K vytvoření privátního cloudu jsou potřeba alespoň tři uzly.

    ![Vytvořit privátní cloud – základní informace](media/create-private-cloud-basic-info.png)

9. Klikněte na **Další: Upřesnit možnosti**.
10. Zadejte rozsah CIDR pro podsítě vSphere/vSAN. Ujistěte se, že rozsah CIDR se nepřekrývá s žádnou z vašich místních nebo jiných podsítí Azure (virtuálních sítí) nebo s podsítí brány.

    **Možnosti rozsahu CIDR:** /24, /23, /22 nebo /21. Rozsah CIDR /24 podporuje až devět uzlů, rozsah CIDR /23 podporuje až 41 uzlů a rozsah CIDR /22 a /21 podporuje až 64 uzlů (maximální počet uzlů v privátním cloudu).

    > [!IMPORTANT]
    > IP adresy v rozsahu CIDR vSphere/vSAN jsou vyhrazeny pro použití infrastrukturou Privátního cloudu.  Nepoužívejte IP adresu v tomto rozsahu na žádném virtuálním počítači.

11. Klikněte na **Další: Zkontrolujte a vytvořte**.
12. Zkontrolujte nastavení. Pokud potřebujete změnit nastavení, klepněte na tlačítko **Předchozí**.
13. Klikněte na **Vytvořit**.

Proces zřizování privátního cloudu se spustí. Zřízení privátního cloudu může trvat až dvě hodiny.

Pokyny k rozšíření existujícího privátního cloudu najdete v [tématu Rozbalení privátního cloudu](expand-private-cloud.md).
