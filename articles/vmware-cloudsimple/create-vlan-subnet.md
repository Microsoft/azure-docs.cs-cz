---
title: Vytváření sítí VLAN/podsítí – řešení Azure VMware podle cloudu
description: Řešení Azure VMware by CloudSimple – popisuje, jak vytvořit a spravovat sítě VLAN/podsítě pro privátní cloudy a potom použít pravidla brány firewall.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0160513eb9abca54adbc3819b982348dc202c90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565991"
---
# <a name="create-and-manage-vlanssubnets-for-your-private-clouds"></a>Vytvoření a správa sítí VLAN/podsítí pro privátní cloudy

Otevřete kartu Sítě a podsítě na stránce Síť a vytvořte a spravujte sítě VLAN/podsítě pro privátní cloudy. Po vytvoření sítě VLAN/podsítě můžete použít pravidla brány firewall.

## <a name="create-a-vlansubnet"></a>Vytvoření sítě VLAN/podsítě

1. [Přístup k portálu CloudSimple](access-cloudsimple-portal.md) a v boční nabídce vyberte **možnost Síť.**
2. Vyberte **sítě VLAN/podsítě**.
3. Klepněte na **tlačítko Vytvořit síť VLAN/podsíť**.

    ![Stránka VLAN/podsítě](media/vlan-subnet-page.png)

4. Vyberte privátní cloud pro novou síť VLAN/podsíť.
5. Zadejte ID sítě VLAN.
6. Zadejte název podsítě.
7. Chcete-li povolit směrování v síti VLAN (podsíti), zadejte rozsah CIDR podsítě. Ujistěte se, že rozsah CIDR se nepřekrývá s žádnou z vašich místních podsítí, podsítí Azure nebo podsítě brány.
8. Klepněte na **tlačítko Odeslat**.

    ![Vytvořit síť VLAN/podsíť](media/create-new-vlan-subnet-details.png)


> [!IMPORTANT]
> K dispozici je kvóta 30 vlan ů na privátní cloud. Tyto limity lze zvýšit [kontaktováním podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>Použití informací sítě VLAN k nastavení skupiny distribuovaných portů ve vSphere

Chcete-li vytvořit distribuovanou skupinu portů ve společnosti vSphere, postupujte podle pokynů v tématu VMware "Přidat skupinu distribuovaných portů" v <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">průvodci sítí vSphere</a>. Při nastavování skupiny distribuovaných portů zadejte informace sítě VLAN z konfigurace CloudSimple.

![Distribuovaná skupina portů](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>Výběr tabulky brány firewall

Tabulky brány firewall a přidružená pravidla jsou definovány na stránce **Tabulky síťové > brány firewall.** Chcete-li vybrat tabulku brány firewall, kterou chcete použít pro síť VLAN/podsíť pro privátní cloud, vyberte položku VLAN/podsíť, na které je na stránce **Sítě VLAN/podsítě** klikněte na **položku Příloha tabulky brány firewall.** Pokyny k nastavení tabulek brány firewall a definování pravidel naleznete v tématu [Tabulky brány firewall.](firewall.md)

![Odkaz na tabulku brány firewall](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> Podsíť může být přidružena k jedné tabulce brány firewall. Tabulka brány firewall může být přidružena k více podsítím.

## <a name="edit-a-vlansubnet"></a>Úprava sítě VLAN/podsítě

Chcete-li upravit nastavení sítě VLAN/podsítě, vyberte ji na stránce **Sítě VLAN/podsítě** a klepněte na ikonu **Upravit.** Proveďte změny a klepněte na položku **Submet**.

## <a name="delete-a-vlansubnet"></a>Odstranění sítě VLAN/podsítě

Chcete-li odstranit síť VLAN/podsíť, vyberte ji na stránce **Sítě VLAN/podsítě** a klepněte na ikonu **Odstranit.** Potvrďte klepnutím na **tlačítko Odstranit.**
