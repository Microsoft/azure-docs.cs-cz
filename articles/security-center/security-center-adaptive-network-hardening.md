---
title: Adaptivní posílení zabezpečení sítě v Centru zabezpečení Azure | Dokumenty společnosti Microsoft
description: Naučte se používat skutečné vzory provozu k posílení pravidel skupin zabezpečení sítě (NSG) a dále zlepšit stav zabezpečení.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/11/2020
ms.author: memildin
ms.openlocfilehash: a75be23e2e8215d86aebcfd7f4317f2f597d3c5b
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385074"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Adaptivní posílení zabezpečení sítě v Azure Security Center
Přečtěte si, jak nakonfigurovat adaptivní posílení zabezpečení sítě v Azure Security Center.

## <a name="what-is-adaptive-network-hardening"></a>Co je adaptivní posílení sítě?
Použití [skupin zabezpečení sítě (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) pro filtrování provozu do a ze zdrojů zlepšuje stav zabezpečení sítě. Stále však mohou existovat některé případy, kdy skutečný provoz protékající skupinou nsg je podmnožinou definovaných pravidel skupiny nsg. V těchto případech lze dále zlepšit stav zabezpečení posílením pravidel skupiny zabezpečení sítě na základě skutečných dopravních vzorců.

Adaptivní posílení sítě poskytuje doporučení k dalšímu posílení pravidel sítě zabezpečení sítě. Používá algoritmus strojového učení, který změřuje skutečný provoz, známou důvěryhodnou konfiguraci, analýzu hrozeb a další ukazatele ohrožení zabezpečení a pak poskytuje doporučení, která umožňují přenos pouze z konkrétních kolekcí členů PROTOKOLU IP/port.

Řekněme například, že stávající pravidlo nsg je povolit provoz od 140.20.30.10/24 na portu 22. Adaptivní síť Hardening doporučení, na základě analýzy, by bylo zúžit rozsah a umožnit provoz od 140.23.30.10/29 - což je užší rozsah IP a odepřít všechny ostatní provoz na tento port.

>[!TIP]
> Adaptivní síť kalení doporučení jsou podporovány pouze na následujících konkrétních portech (pro UDP a TCP): 13, 17, 19, 22, 23, 53, 69, 81, 111, 119, 123, 135, 137, 138, 139, 161, 162, 389, 445, 512, 514, 593, 636, 873, 1433, 1434, 1900, 2049, 2301 , 2323, 2381, 3268, 3306, 3389, 4333, 5353, 5432, 5555, 5800, 5900, 5900, 5985, 5986, 6379, 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017, 37215


![Zobrazení posílení sítě](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Zobrazení adaptivních výstrah a pravidel posílení sítě

1. V Centru zabezpečení vyberte **možnost Síťové** -> **adaptivní posílení sítě**. Síťové virtuální virtuální mích y jsou uvedeny na třech samostatných kartách:
   * **Nefunkční prostředky:** Virtuální počítače, které aktuálně mají doporučení a výstrahy, které byly spuštěny spuštěním algoritmu adaptivní sítě posílení. 
   * **Prostředky v pořádku**: Virtuální počítače bez výstrah a doporučení.
   * **Neprohledávané prostředky**: Virtuální moduly, na kterých nelze spustit algoritmus posílení zabezpečení sítě adaptivní sítě z jednoho z následujících důvodů:
      * **Virtuální počítače jsou klasické virtuální počítače:** Jsou podporované jenom virtuální počítače Azure Resource Manager.
      * **Není k dispozici dostatek dat**: Chcete-li generovat přesná doporučení pro posílení provozu, centrum zabezpečení vyžaduje alespoň 30 dní provozních dat.
      * **Virtuální počítač není chráněn standardem ASC:** Pro tuto funkci jsou způsobilé jenom virtuální počítače, které jsou nastavené na úroveň standardníceny Centra zabezpečení.

     ![nefunkční zdroje](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. Na kartě **Nefunkční prostředky** vyberte virtuální hosti, chcete-li zobrazit jeho výstrahy a doporučená pravidla posílení zabezpečení, která se mají použít.

    ![upozornění na posílení zabezpečení](./media/security-center-adaptive-network-hardening/anh-recommendation-rules.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>Kontrola a použití adaptivních síťových vytvrzování doporučených pravidel

1. Na kartě **Nefunkční prostředky** vyberte virtuální hod. Výstrahy a doporučená pravidla posílení zabezpečení jsou uvedeny.

     ![pravidla kalení](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > Na kartě **Pravidla** jsou uvedena pravidla, která adaptivní posílení sítě doporučuje přidat. Na kartě **Výstrahy** jsou uvedeny výstrahy, které byly generovány z důvodu provozu, které se přemisí do prostředku, který není v rozsahu IP povoleném v doporučených pravidlech.

2. Pokud chcete změnit některé parametry pravidla, můžete jej upravit, jak je vysvětleno v [části Změnit pravidlo](#modify-rule).
   > [!NOTE]
   > Můžete také [odstranit](#delete-rule) nebo [přidat](#add-rule) pravidlo.

3. Vyberte pravidla, která chcete použít v souboru nsg, a klepněte na tlačítko **Vynutit**.

      > [!NOTE]
      > Vynucená pravidla se přidají k nsg(s) chrání virtuální ho. (Virtuální počítač může být chráněný skupinou sítě, která je přidružena k jeho síťové křísení nebo podsíti, ve které se virtuální počítač nachází, nebo obojí)

    ![vynucovat pravidla](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### <a name="modify-a-rule"></a>Změna pravidla <a name ="modify-rule"> </a>

Můžete chtít změnit parametry pravidla, které bylo doporučeno. Můžete například změnit doporučené rozsahy IP adres.

Některé důležité pokyny pro úpravu adaptivního pravidla posílení sítě:

* Můžete upravit parametry pouze "povolit" pravidla. 
* Nelze změnit pravidla "povolit", aby se stala "odepřít" pravidla. 

  > [!NOTE]
  > Vytváření a úprava "odepřít" pravidla se provádí přímo na nsg. Další informace naleznete v [tématu Vytvoření, změna nebo odstranění skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

* A **Odepřít všechny pravidla provozu** je jediný typ "odepřít" pravidlo, které by zde uvedeny a nelze jej změnit. Můžete jej však odstranit (viz [Odstranění pravidla).](#delete-rule)
  > [!NOTE]
  > A **Odepřít všechny pravidla provozu** se doporučuje, pokud v důsledku spuštění algoritmu Security Center neidentifikuje provoz, který by měl být povolen, na základě existující konfigurace skupiny zabezpečení sítě. Proto je doporučené pravidlo odepřít veškerý provoz na zadaný port. Název tohoto typu pravidla je zobrazen jako "*Systémově vygenerovaný*". Po vynucení tohoto pravidla bude jeho skutečný název v souboru nsg řetězec složený z protokolu, směru přenosu, "DENY" a náhodného čísla.

*Úprava adaptivního pravidla posílení sítě:*

1. Chcete-li upravit některé parametry pravidla, klikněte na kartě **Pravidla** na tři tečky (...) na konci řádku pravidla a klikněte na **Upravit**.

   ![upravit pravidlo](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. V okně **Upravit pravidlo** aktualizujte podrobnosti, které chcete změnit, a klepněte na tlačítko **Uložit**.

   > [!NOTE]
   > Po klepnutí na tlačítko **Uložit**jste pravidlo úspěšně změnili. *Však jste ji nepoužili na soubor nsg.* Chcete-li jej použít, musíte vybrat pravidlo v seznamu a klepnout na **tlačítko Vynutit** (jak je vysvětleno v dalším kroku).

   ![upravit pravidlo](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. Chcete-li použít aktualizované pravidlo, vyberte ze seznamu aktualizované pravidlo a klepněte na tlačítko **Vynutit**.

    ![vynutit pravidlo](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### <a name="add-a-new-rule"></a>Přidání nového <a name ="add-rule"> </a> pravidla

Můžete přidat pravidlo "povolit", které nebylo doporučeno Centrem zabezpečení.

> [!NOTE]
> Zde lze přidat pouze pravidla "povolit". Pokud chcete přidat "odepřít" pravidla, můžete tak učinit přímo na nsg. Další informace naleznete v [tématu Vytvoření, změna nebo odstranění skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

*Přidání adaptivního pravidla posílení sítě:*

1. Klikněte na **Přidat pravidlo** (umístěné v levém horním rohu).

   ![přidat pravidlo](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. V okně **Nové pravidlo** zadejte podrobnosti a klepněte na **tlačítko Přidat**.

   > [!NOTE]
   > Po klepnutí na tlačítko **Přidat**jste pravidlo úspěšně přidali a je uvedeno s dalšími doporučenými pravidly. Však jste ji nepoužili na soubor nSG. Chcete-li jej aktivovat, musíte vybrat pravidlo v seznamu a klepnout na **tlačítko Vynutit** (jak je vysvětleno v dalším kroku).

3. Chcete-li použít nové pravidlo, vyberte ze seznamu nové pravidlo a klepněte na tlačítko **Vynutit**.

    ![vynutit pravidlo](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### <a name="delete-a-rule"></a>Odstranění pravidla <a name ="delete-rule"> </a>

V případě potřeby můžete odstranit doporučené pravidlo pro aktuální relaci. Můžete například určit, že použití navrhovaného pravidla může blokovat legitimní provoz.

*Odstranění adaptivního pravidla posílení sítě pro aktuální relaci:*

1. Na kartě **Pravidla** klikněte na tři tečky (...) na konci řádku pravidla a klikněte na **Odstranit**.  

    ![pravidla kalení](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)