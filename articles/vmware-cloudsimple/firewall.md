---
title: Řešení Azure VMware podle CloudSimple – nastavení tabulek a pravidel brány firewall
description: Popisuje, jak nastavit tabulky a pravidla brány firewall privátního cloudu pro omezení provozu v podsítích a sítích VLAN.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 32dcfb0c6cd26b5f34afad328db8f383fa1c2a6f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895627"
---
# <a name="set-up-firewall-tables-and-rules-for-private-clouds"></a>Nastavení tabulek a pravidel brány firewall pro privátní cloudy

Tabulky brány firewall a přidružená pravidla umožňují určit omezení provozu, která se mají použít na konkrétní podsítě a sítě VLAN.

* Podsíť může být přidružená k jedné tabulce brány firewall.
* Tabulka brány firewall může být přidružena k několika podsítím.

## <a name="add-a-new-firewall-table"></a>Přidat novou tabulku brány firewall

1. [Přejděte na portál CloudSimple](access-cloudsimple-portal.md) a v postranní nabídce vyberte **síť** .
2. Vyberte **tabulky brány firewall**.
3. Vyberte **vytvořit tabulku brány firewall**.

    ![Stránka sítě VLAN/podsíť](media/firewall-tables-page.png)

4. Zadejte název tabulky.
5. Zobrazí se výchozí pravidlo pro tabulku. Kliknutím na **vytvořit nové pravidlo** vytvořte další pravidlo. Podrobnosti najdete v následujícím postupu.
6. Kliknutím na **Hotovo** uložte tabulku brány firewall.

> [!IMPORTANT]
> Pro každý privátní Cloud můžete vytvořit až dvě tabulky brány firewall.

## <a name="firewall-rules"></a>Pravidla brány firewall

Pravidla brány firewall určují, jak brána firewall zpracovává konkrétní typy provozu. Karta **pravidla** pro vybranou tabulku brány firewall obsahuje seznam všech přidružených pravidel.

![Tabulka pravidel brány firewall](media/firewall-rules-tab.png)

## <a name="create-a-firewall-rule"></a>Vytvoření pravidla brány firewall

1. Zobrazte nastavení pro vytvoření pravidla brány firewall jedním z těchto způsobů:
    * Při vytváření tabulky brány firewall klikněte na **Přidat pravidlo** .
    * Vyberte konkrétní tabulku brány firewall na stránce **> tabulky brány firewall sítě** a klikněte na **vytvořit nové pravidlo brány firewall**.
2. Pravidlo nastavte takto:
    * **Název:** Zadejte název pravidla.
    * **Priorita**. Přiřaďte k pravidlu prioritu. Jako první se spustí pravidla s nižšími čísly.
    * **Typ provozu**. Vyberte, jestli je pravidlo pro provoz privátního cloudu, Internetu nebo VPN (bez stavu) nebo pro veřejnou IP adresu (Stavový).
    * **Protokol**. Vyberte protokol, na který se vztahuje pravidlo (TCP, UDP nebo jakýkoli protokol).
    * **Směr**. Vyberte, zda je pravidlo pro příchozí nebo odchozí provoz. Musíte definovat samostatná pravidla pro příchozí a odchozí provoz.
    * **Akce**. Vyberte akci, která se má provést, pokud pravidlo souhlasí (povolit nebo odepřít).
    * **Zdroj**. Zadejte zdroje, na které se vztahuje pravidlo (blok CIDR, interní nebo libovolný zdroj).
    * **Rozsah zdrojových portů**. Zadejte rozsah portů, na které se vztahuje pravidlo.
    * **Směr**. Vyberte příchozí nebo odchozí.
    * **Cíl**. Zadejte cílové umístění, na které se vztahuje pravidlo (blok CIDR, interní nebo libovolný zdroj).
    * **Rozsah zdrojových portů**. Zadejte rozsah portů, na které se vztahuje pravidlo.

    ![Přidat pravidlo pro tabulku brány firewall](media/firewall-rule-create.png)

3. Kliknutím na **Hotovo** uložte pravidlo a přidejte ho do seznamu pravidel pro tabulku brány firewall.

> [!IMPORTANT]
> Každá tabulka brány firewall může mít až 10 příchozích pravidel a 20 odchozích pravidel. Tato omezení se dají zvýšit [kontaktováním podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="attach-vlanssubnets"></a><a name="attach-vlans-subnet"></a>Připojit sítě VLAN a podsítě

Po definování tabulky brány firewall můžete určit podsítě, na které se vztahují pravidla v tabulce.

1. Na stránce   >  **tabulky brány firewall** sítě vyberte tabulku brány firewall.
2. Otevřete kartu **připojené sítě VLAN/podsíť** .
3. Klikněte na **připojit k síti VLAN nebo podsíti**.
4. Vyberte privátní cloud a síť VLAN. Zobrazí se název přidružené podsítě a blok CIDR.
5. Klikněte na **Odeslat**.
