---
title: Adaptivní sítě posílení zabezpečení v Azure Security Center | Dokumentace Microsoftu
description: " Zjistěte, jak povolit adaptivní posílení zabezpečení sítě v Azure Security Center. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2019
ms.author: monhaber
ms.openlocfilehash: f35f410ddc039ee264fa1de317e152cb03f391b5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66241508"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Adaptivní sítě posílení zabezpečení v Azure Security Center
Zjistěte, jak nakonfigurovat adaptivní posílení zabezpečení sítě v Azure Security Center.

## <a name="what-is-adaptive-network-hardening"></a>Co je adaptivní posílení zabezpečení sítě?
Použití [zabezpečení skupiny (NSG) sítě](https://docs.microsoft.com/azure/virtual-network/security-overview) filtrovat provoz do a z prostředků, přispívá k lepšímu zabezpečení sítě. Však může i nadále existovat některých případech, ve kterých skutečný provoz pomocí skupiny zabezpečení sítě je podmnožinou pravidla NSG, které jsou definovány. V těchto případech zlepšit stav zabezpečení lze dosáhnout posílení zabezpečení pravidla NSG, na základě vzorců skutečný provoz.

Adaptivní posílení zabezpečení sítě obsahuje doporučení, která dál posiluje pravidla skupiny zabezpečení sítě. Pomocí strojového učení algoritmu, který faktory pro skutečný provoz, známé důvěryhodné konfigurace, analýze hrozeb a další ukazatele ohrožení zabezpečení, a pak poskytuje doporučení, která umožní provoz pouze z konkrétní IP adresa/port řazené kolekce členů.

Řekněme například, že je existující pravidlo skupiny zabezpečení sítě umožňující provoz z 140.20.30.10/24 na portu 22. Doporučení adaptivní sítě posílení zabezpečení společnosti, na základě analýzy, bude zúžit rozsah a povolit provoz z 140.23.30.10/29 – tedy zúžit rozsah IP a odepřít veškerý provoz na daný port.

![zobrazení posílení zabezpečení sítě](./media/security-center-adaptive-network-hardening/traffic-hardening.png)

> [!NOTE]
> Adaptivní doporučení pro posílení zabezpečení sítě jsou podporovány následující porty: 22, 3389, 21, 23, 445, 4333, 3306, 1433, 1434, 53, 20, 5985, 5986, 5432, 139, 66, 1128

## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Zobrazení výstrah adaptivní posílení zabezpečení sítě a pravidel

1. Ve službě Security Center vyberte **sítě** -> **adaptivní posílení zabezpečení sítě**. Sítě virtuálních počítačů jsou uvedeny ve třech samostatných kartách:
   * **Není v pořádku prostředky**: Virtuální počítače, které aktuálně máte doporučení a výstrahy, které byly aktivovány spuštění algoritmu adaptivní posílení zabezpečení sítě. 
   * **V pořádku prostředky**: Virtuální počítače bez upozornění a doporučení.
   * **Nezkontrolované prostředky**: Virtuální počítače, které algoritmus adaptivní posílení zabezpečení sítě nelze spustit v z jednoho z následujících důvodů:
      * **Virtuální počítače jsou klasické virtuální počítače**: Podporují se jenom virtuální počítače Azure Resource Manageru.
      * **Nedostatek dat je k dispozici**: Aby se vygenerovala doporučení pro posílení přesné provoz, Security Center vyžaduje nejméně 30 dnů od data o provozu.
      * **Virtuální počítač není chráněný standardem ASC**: Pouze virtuální počítače, které jsou nastaveny na Security Center na cenové úrovni Standard jsou vhodné pro tuto funkci.

     ![prostředky není v pořádku](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. Z **není v pořádku prostředky** kartu, vyberte virtuální počítač k zobrazení jeho výstrah a posílení zabezpečení doporučená pravidla použila.

    ![výstrahy zabezpečení](./media/security-center-adaptive-network-hardening/hardening-alerts.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>Zkontrolovat a použít adaptivní sítě posílení zabezpečení doporučená pravidla

1. Z **není v pořádku prostředky** kartu, vyberte virtuální počítač. Výstrahy a doporučené posílení zabezpečení pravidla jsou uvedeny.

     ![pravidla posílení zabezpečení](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > **Pravidla** karta obsahuje pravidla, která adaptivní posílení zabezpečení sítě doporučuje přidáte. **Výstrahy** karta obsahuje seznam výstrah, které byly generovány z důvodu provoz směřující do prostředku, který není v rozsahu IP povolený v doporučená pravidla.

2. Pokud chcete změnit některé z parametrů pravidlo, můžete ho upravit, jak je vysvětleno v [upravit pravidlo](#modify-rule).
   > [!NOTE]
   > Můžete také [odstranit](#delete-rule) nebo [přidat](#add-rule) pravidlo.

3. Vybrat pravidla, které chcete použít na skupiny zabezpečení sítě a klikněte na tlačítko **vynutit**.

      > [!NOTE]
      > Vynucené pravidla se přidají do skupiny nsg ochranu virtuálního počítače. (Virtuální počítač může chránit skupinu NSG, která je přidružená k NIC, nebo podsíť, ve kterém se tento virtuální počítač nachází, nebo obojí)

    ![vynucení pravidel](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### Upravit pravidlo  <a name ="modify-rule"> </a>

Můžete chtít změnit parametry pravidlo, které se doporučuje. Můžete například změnit doporučené rozsahy IP adres.

Některé důležité pokyny k úpravám pravidlo adaptivní posílení zabezpečení sítě:

* Můžete změnit parametry "Povolit" pouze pravidla. 
* Nelze změnit, pravidla se "Zakázat" pravidla "Povolit". 

  > [!NOTE]
  > Vytvoření a úprava "Zakázat" pravidel se provádí přímo na skupiny zabezpečení sítě pro další podrobnosti najdete v tématu [vytvořit, změnit nebo odstranit skupinu zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

* A **odepřít veškerý provoz** pravidlo je jediným typem "Zakázat" pravidlo, které by zde uvedeny, a nemůže být upraven. Můžete ho však odstranit (viz [odstranění pravidla](#delete-rule)).
  > [!NOTE]
  > A **odepřít veškerý provoz** pravidlo se doporučuje při, v důsledku spuštění algoritmus, Security Center neidentifikuje provoz, který má být povolený, na základě existující konfigurace skupiny zabezpečení sítě. Doporučená pravidla je proto chcete odepřít veškerý provoz na zadaný port. Název tohoto typu pravidla se zobrazí jako "*generována*". Po vynucení toto pravidlo, jeho skutečný název v této skupině budou řetězec skládá z protokolu, směr přenosu, "Zakázat" a náhodné číslo.

*Chcete-li upravit pravidlo adaptivní posílení zabezpečení sítě:*

1. Upravit některé parametry pravidla v **pravidla** kartu, klikněte na tři tečky (...) na konci řádku pravidlo a klikněte na tlačítko **upravit**.

   ![Upravit pravidlo](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. V **upravit pravidlo** okně Aktualizovat podrobnosti, které chcete změnit a klikněte na tlačítko **Uložit**.

   > [!NOTE]
   > Po kliknutí na tlačítko **Uložit**, úspěšně jste změnili pravidlo. *Však nebyly použity na skupiny zabezpečení sítě.* A provést tak, musíte v seznamu vyberte pravidlo a klikněte na tlačítko **vynutit** (jak je popsáno v dalším kroku).

   ![Upravit pravidlo](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. Použít aktualizované pravidlo ze seznamu, vyberte aktualizovanou pravidlo a klikněte na tlačítko **vynutit**.

    ![Vynutit pravidla](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### Přidat nové pravidlo <a name ="add-rule"> </a>

Můžete přidat pravidlo "Povolit", které se doporučuje služba Security Center.

> [!NOTE]
> Zde je možné přidat pouze "Povolit" pravidla. Pokud chcete přidat "Zakázat" pravidla, můžete provést přímo na skupiny zabezpečení sítě. Další podrobnosti najdete v tématu [vytvořit, změnit nebo odstranit skupinu zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

*Chcete-li přidat pravidlo adaptivní posílení zabezpečení sítě:*

1. Klikněte na tlačítko **přidat pravidlo** (nachází se v levém horním rohu).

   ![Přidat pravidlo](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. V **nové pravidlo** okno, zadejte podrobnosti a klikněte na tlačítko **přidat**.

   > [!NOTE]
   > Po kliknutí na tlačítko **přidat**, úspěšně jste přidali pravidlo a je uvedený s doporučená pravidla. Ale nebyly použity na skupiny zabezpečení sítě. Aktivovat, musíte v seznamu vyberte pravidlo a klikněte na tlačítko **vynutit** (jak je popsáno v dalším kroku).

3. Chcete-li použít nové pravidlo ze seznamu, vyberte nové pravidlo a klikněte na tlačítko **vynutit**.

    ![Vynutit pravidla](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### Odstranit pravidlo <a name ="delete-rule"> </a>

Pokud je to nezbytné, můžete odstranit doporučená pravidla. Například můžete určit, že použití navrhované pravidlo by mohla blokovat provoz legitimní.

*Chcete odstranit pravidlo adaptivní posílení zabezpečení sítě:*

1. V **pravidla** kartu, klikněte na tři tečky (...) na konci řádku pravidlo a klikněte na tlačítko **odstranit**.  

    ![pravidla posílení zabezpečení](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)







 

