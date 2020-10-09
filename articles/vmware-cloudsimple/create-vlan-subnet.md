---
title: Vytváření sítí VLAN a podsítí – řešení Azure VMware podle CloudSimple
description: Řešení Azure VMware podle CloudSimple – popisuje, jak vytvářet a spravovat sítě VLAN a podsítě pro privátní cloudy a pak použít pravidla brány firewall.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0160513eb9abca54adbc3819b982348dc202c90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77565991"
---
# <a name="create-and-manage-vlanssubnets-for-your-private-clouds"></a>Vytváření a správa sítí VLAN a podsítí pro privátní cloudy

Otevřete kartu sítě VLAN/podsítě na stránce síť, abyste mohli vytvářet a spravovat sítě VLAN a podsítě pro privátní cloudy. Po vytvoření sítě VLAN nebo podsítě můžete použít pravidla brány firewall.

## <a name="create-a-vlansubnet"></a>Vytvoření sítě VLAN nebo podsítě

1. [Přejděte na portál CloudSimple](access-cloudsimple-portal.md) a v postranní nabídce vyberte **síť** .
2. Vyberte **sítě VLAN a podsítě**.
3. Klikněte na **vytvořit síť VLAN nebo podsíť**.

    ![Stránka sítě VLAN/podsíť](media/vlan-subnet-page.png)

4. Vyberte privátní cloud pro novou síť VLAN nebo podsíť.
5. Zadejte ID sítě VLAN.
6. Zadejte název podsítě.
7. Pokud chcete povolit směrování v síti VLAN (podsíti), zadejte rozsah CIDR podsítě. Ujistěte se, že rozsah CIDR se nepřekrývá s žádnou z vašich místních podsítí, podsítí Azure ani podsítí brány.
8. Klikněte na **Odeslat**.

    ![Vytvořit síť VLAN nebo podsíť](media/create-new-vlan-subnet-details.png)


> [!IMPORTANT]
> K dispozici je kvóta 30 sítí VLAN na jeden privátní cloud. Tato omezení se dají zvýšit [kontaktováním podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>Nastavení distribuované skupiny portů v vSphere pomocí informací o síti VLAN

Pokud chcete vytvořit distribuovanou skupinu portů v vSphere, postupujte podle pokynů v tématu "Přidání distribuované skupiny portů" v <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">příručce sítě vSphere</a>. Při nastavování distribuované skupiny portů zadejte informace o síti VLAN z konfigurace CloudSimple.

![Distribuovaná skupina portů](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>Vybrat tabulku brány firewall

Tabulky a přidružená pravidla brány firewall jsou definovány na stránce **tabulky brány firewall sítě >** . Pokud chcete vybrat tabulku brány firewall, která se má použít pro síť VLAN nebo podsíť pro privátní cloud, vyberte síť VLAN/podsíť na stránce **sítě VLAN/podsítě** klikněte na možnost **Příloha tabulky brány firewall** . Pokyny k nastavení tabulek brány firewall a definování pravidel najdete v tématu věnovaném [tabulkám brány firewall](firewall.md) .

![Odkaz na tabulku brány firewall](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> Podsíť může být přidružená k jedné tabulce brány firewall. Tabulka brány firewall může být přidružena k několika podsítím.

## <a name="edit-a-vlansubnet"></a>Upravit síť VLAN nebo podsíť

Pokud chcete upravit nastavení pro síť VLAN nebo podsíť, vyberte ji na stránce **sítě VLAN/podsítě** a klikněte na ikonu **Upravit** . Proveďte změny a klikněte na **Submet**.

## <a name="delete-a-vlansubnet"></a>Odstranění sítě VLAN nebo podsítě

Pokud chcete odstranit síť VLAN nebo podsíť, vyberte ji na stránce **sítě VLAN/podsítě** a klikněte na ikonu **Odstranit** . Potvrďte kliknutím na **Odstranit** .
