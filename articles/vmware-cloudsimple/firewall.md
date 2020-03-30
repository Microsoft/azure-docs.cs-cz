---
title: Řešení Azure VMware od CloudSimple – nastavení tabulek a pravidel brány firewall
description: Popisuje, jak nastavit tabulky a pravidla brány firewall privátního cloudu pro omezení provozu v podsítích a sítích VLAN.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 986f4b0da7254ebac3725a704f32af785c72fbcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244664"
---
# <a name="set-up-firewall-tables-and-rules-for-private-clouds"></a>Nastavení tabulek a pravidel brány firewall pro privátní cloudy

Tabulky brány firewall a přidružená pravidla umožňují určit omezení provozu, která se mají vztahovat na konkrétní podsítě a sítě VLAN.

* Podsíť může být přidružena k jedné tabulce brány firewall.
* Tabulka brány firewall může být přidružena k více podsítím.

## <a name="add-a-new-firewall-table"></a>Přidání nové tabulky brány firewall

1. [Přístup k portálu CloudSimple](access-cloudsimple-portal.md) a v boční nabídce vyberte **možnost Síť.**
2. Vyberte **položku Tabulky brány firewall**.
3. Vyberte **možnost Vytvořit tabulku brány firewall**.

    ![Stránka VLAN/podsítě](media/firewall-tables-page.png)

4. Zadejte název tabulky.
5. Je uvedeno výchozí pravidlo pro tabulku. Chcete-li vytvořit další pravidlo, klepněte na tlačítko **Vytvořit nové** pravidlo. Podrobnosti naleznete v následujícím postupu.
6. Chcete-li uložit tabulku brány firewall, klepněte na **tlačítko Hotovo.**

> [!IMPORTANT]
> V soukromém cloudu můžete vytvořit až dvě tabulky brány firewall.

## <a name="firewall-rules"></a>Pravidla brány firewall

Pravidla brány firewall určují, jak brána firewall zachází s určitými typy přenosů. Na kartě **Pravidla** pro vybranou tabulku brány firewall jsou uvedena všechna přidružená pravidla.

![Tabulka pravidel brány firewall](media/firewall-rules-tab.png)

## <a name="create-a-firewall-rule"></a>Vytvoření pravidla brány firewall

1. Zobrazení nastavení pro vytvoření pravidla brány firewall jedním z těchto způsobů:
    * Při vytváření tabulky brány firewall klikněte na **Přidat pravidlo.**
    * Na stránce **Síťové > tabulky brány firewall** vyberte konkrétní tabulku brány firewall a klepněte na tlačítko Vytvořit nové pravidlo brány **firewall**.
2. Pravidlo nastavte takto:
    * **Název**. Pojmenujte pravidlo.
    * **Priorita**. Přiřazení priority pravidlu Pravidla s nižšími čísly jsou provedena jako první.
    * **Typ provozu**. Vyberte, zda je pravidlo pro privátní cloud, internet nebo provoz VPN (bezstavový) nebo pro veřejnou IP adresu (stavovou).
    * **protokolu**. Vyberte protokol, na který se pravidlo vztahuje (TCP, UDP nebo libovolný protokol).
    * **směr**. Vyberte, zda je pravidlo pro příchozí nebo odchozí provoz. Je nutné definovat samostatná pravidla pro příchozí a odchozí provoz.
    * **Akce**. Vyberte akci, která má být vyvolená, pokud se pravidlo shoduje (povolit nebo odepřít).
    * **Zdroj**. Určete zdroje, na které se pravidlo vztahuje (blok CIDR, interní nebo jakýkoli zdroj).
    * **Rozsah zdrojového portu**. Určete rozsah portů, které podléhají pravidlu.
    * **směr**. Vyberte příchozí nebo odchozí.
    * **Cíl**. Určete cíle, na které se pravidlo vztahuje (blok CIDR, interní nebo jakýkoli zdroj).
    * **Rozsah zdrojového portu**. Určete rozsah portů, které podléhají pravidlu.

    ![Přidat pravidlo tabulky brány firewall](media/firewall-rule-create.png)

3. Klepnutím na **tlačítko Hotovo** uložte pravidlo a přidejte jej do seznamu pravidel pro tabulku brány firewall.

> [!IMPORTANT]
> Každá tabulka brány firewall může mít až 10 příchozích pravidel a 20 odchozích pravidel. Tyto limity lze zvýšit [kontaktováním podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="attach-vlanssubnets"></a><a name="attach-vlans-subnet"></a>Připojení sítí VLAN/podsítí

Po definování tabulky brány firewall můžete určit podsítě, které podléhají pravidlům v tabulce.

1. Na stránce **Tabulky síťové** > **brány firewall** vyberte tabulku brány firewall.
2. Otevřete kartu **Připojené sítě VLAN/podsíť.**
3. Klepněte na **připojit k síti VLAN/podsíti**.
4. Vyberte privátní cloud a síť VLAN. Zobrazí se přidružený název podsítě a blok CIDR.
5. Klepněte na **tlačítko Odeslat**.
