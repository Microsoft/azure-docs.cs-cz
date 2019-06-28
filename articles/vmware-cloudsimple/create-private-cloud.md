---
title: Vytvoření řešení Azure VMware CloudSimple privátního cloudu
description: Popisuje, jak vytvořit CloudSimple privátního cloudu k rozšíření úloh VMware do cloudu s provozní flexibility a kontinuity podnikových procesů
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e5c03c1d8a865b792ce79e3e2b576a629b71e02c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333287"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Vytvoření privátního cloudu CloudSimple

Vytvoření privátního cloudu vám pomůžeme proniknout celou řadu běžných požadavků síťové infrastruktury:

* **Růst**. Pokud bod aktualizace hardwaru dosáhli jste vaší stávající infrastruktury, privátního cloudu umožňuje rozšířit s žádné nové hardwarových investic vyžaduje.

* **Rychlé rozšiřování**. Pokud kapacita dočasné nebo neplánované potřeby, privátního cloudu můžete vytvořit další kapacitu bez zpoždění.

* **Zvýšení ochrany**. Díky privátního cloudu tři nebo více uzlů budete mít automatickou redundanci a vysokou dostupnost ochrany.

* **Dlouhodobé infrastruktury musí**. Pokud vaše datová centra jsou plně využívá kapacitu nebo chcete provést změny struktury snížit vaše náklady, privátního cloudu umožňuje vyřadit z provozu datových center a migrace na cloudové řešení přitom kompatibilní s vaší podnikové operace.

Když vytvoříte privátní Cloud, získáte vSphere jeden cluster a všechny virtuální počítače pro správu, které jsou vytvořené v tomto clusteru.

## <a name="before-you-begin"></a>Než začnete

Uzly je potřeba poskytovat, než budete moct vytvořit privátní Cloud.  Další informace o zřizování uzlů najdete v tématu [zřízení uzlů pro řešení VMware podle CloudSimple - Azure](create-nodes.md) článku.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Přístup k portálu CloudSimple

Přístup [CloudSimple portál](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Vytvořit nový privátní Cloud

1. Na **prostředky** klikněte na **nové privátního cloudu**.

    ![Vytvoření privátního cloudu – spuštění](media/create-pc-button.png)

2. Vyberte umístění pro hostování prostředky privátního cloudu.

3. Zvolte CS28 nebo CS36 you'ev typ uzlu zřízené pro privátní Cloud. Druhou možnost zahrnuje maximální kapacitu výpočetní a paměťové prostředky.

4. Vyberte počet uzlů pro privátní Cloud. Můžete vybrat maximálně k dispozici z počtu uzlů tohoto you'ev [zřízené](create-nodes.md).

    ![Vytvoření privátního cloudu – základní nastavení](media/create-private-cloud-basic-info.png)

5. Klikněte na tlačítko **Další: Rozšířené možnosti**.

6. Zadejte rozsah CIDR. pro podsítě vSphere/síti vSAN. Ujistěte se, že rozsah CIDR, nepřekrývá s žádným z místní nebo jiných podsítích Azure (virtuální sítě) nebo s podsítí brány.  Nepoužívejte žádné rozsah CIDR definované ve virtuálních sítích Azure.
    
    **Volby rozsahu CIDR:** /24, /23, /22 nebo /21. A/24 rozsah CIDR podporuje až devět uzly, /23 rozsah CIDR podporuje až 41 uzly a /22 a /21 rozsah CIDR podporuje až 64 uzly (maximální počet uzlů v privátním cloudu).

    > [!CAUTION]
    > IP adresy v rozsahu CIDR vSphere/síť vSAN je vyhrazený pro použití v infrastruktuře privátního cloudu.  Nepoužívejte IP adresy v tomto rozsahu na jakýkoli virtuální počítač.

7. Klikněte na tlačítko **Další: Zkontrolovat a vytvořit**.

8. Zkontrolujte nastavení. Pokud potřebujete změnit libovolné nastavení, klikněte na tlačítko **předchozí**.

9. Klikněte na možnost **Vytvořit**.

Privátní Cloud, zřizování se spustí po kliknutí na vytvořit.  Můžete sledovat průběh na základě [úlohy](https://docs.azure.cloudsimple.com/activity/#tasks) stránky na portálu CloudSimple.  Zřizování může trvat 30 minut až dvou hodin.  Obdržíte e-mailu po dokončení zřizování.

## <a name="next-steps"></a>Další postup

* [Rozbalte privátního cloudu](expand-private-cloud.md)