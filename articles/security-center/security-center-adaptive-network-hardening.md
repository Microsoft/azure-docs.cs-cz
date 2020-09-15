---
title: Adaptivní posílení zabezpečení sítě v Azure Security Center | Microsoft Docs
description: Naučte se používat skutečné modely provozu k posílení pravidel skupin zabezpečení sítě (NSG) a další vylepšení stav zabezpečení.
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
ms.openlocfilehash: 1f69fe027772dc2d008a567723a5b3c04f3ee51b
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378198"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Adaptivní posílení zabezpečení sítě v Azure Security Center
Přečtěte si, jak nakonfigurovat adaptivní posílení zabezpečení sítě v Azure Security Center.

## <a name="what-is-adaptive-network-hardening"></a>Co je adaptivní posílení zabezpečení sítě?
Použití [skupin zabezpečení sítě (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) k filtrování provozu do prostředků a z nich vylepšuje zabezpečení sítě stav. Přesto však může být v některých případech, kdy je skutečný přenos toků přes NSG podmnožinou definovaných pravidel NSG. V těchto případech je možné zvýšit stav zabezpečení tím, že posílíte pravidla NSG, a to na základě skutečných schémat přenosů.

Adaptivní posílení zabezpečení sítě poskytuje doporučení k dalšímu posílení pravidel NSG. Používá algoritmus strojového učení, který má vliv na skutečný provoz, známou důvěryhodnou konfiguraci, analýzu hrozeb a další indikátory ohrožení a pak poskytuje doporučení k tomu, aby se provozoval jenom z konkrétních řazených kolekcí IP/portů.

Řekněme například, že stávající pravidlo NSG je povolit provoz z 140.20.30.10/24 na portu 22. Doporučení adaptivního posílení zabezpečení sítě, které je založené na analýze, by mělo omezit rozsah a povolit provoz z 140.23.30.10/29, což je úzký rozsah IP adres, a odepřít veškerý ostatní provoz na tento port.

>[!TIP]
> Doporučení adaptivního posílení zabezpečení sítě se podporují jenom u těchto specifických portů (pro UDP i TCP): 13, 17, 19, 22, 23, 53, 69, 81, 111, 119, 123, 135, 137, 138, 139, 161, 162, 389, 445, 512, 514, 593, 636, 873, 1433, 1434, 1900, 2049 2301, 2323, 2381, 3268, 3306, 3389, 4333, 5353, 5432, 5555, 5800, 5900, 5900, 5985, 5986, 6379 , 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017, 37215


![Zobrazení posílení zabezpečení sítě](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Všeobecná dostupnost|
|Stanov|Úroveň Standard|
|Požadované role a oprávnění:|Oprávnění k zápisu do skupin zabezpečení sítě počítače|
|Cloud|![Ano](./media/icons/yes-icon.png) Komerční cloudy<br>![Ne](./media/icons/no-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov)|
|||


## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Zobrazit výstrahy a pravidla adaptivního posílení zabezpečení sítě

1. V Security Center vyberte **síť**  ->  **adaptivní posílení zabezpečení sítě**. Síťové virtuální počítače jsou uvedené v části tři samostatné karty:
   * **Zdroje**, které nejsou v pořádku: virtuální počítače, které mají aktuálně spuštěné doporučení a výstrahy, se spouštějí pomocí algoritmu adaptivního posílení sítě. 
   * **Dobré prostředky**: virtuální počítače bez výstrah a doporučení.
   * **Nekontrolované prostředky**: virtuální počítače, na kterých se algoritmus adaptivního posílení sítě nedá spustit kvůli jednomu z následujících důvodů:
      * **Virtuální počítače jsou klasickými virtuálními**počítači: podporují se jenom Azure Resource Manager virtuální počítače.
      * **Není k dispozici dostatek dat**: aby bylo možné vygenerovat přesná doporučení pro posílení provozu, Security Center vyžaduje alespoň 30 dnů přenosu dat.
      * **Virtuální počítač není chráněný pomocí ASC Standard**: pro tuto funkci mají nárok jenom virtuální počítače, které jsou nastavené na cenovou úroveň Standard Security Center.

     ![zdroje, které nejsou v pořádku](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. Na kartě **poškozené prostředky** vyberte virtuální počítač, na kterém se mají zobrazit výstrahy, a doporučená pravidla posílení zabezpečení, která se mají použít.

    ![Upozornění posílení zabezpečení](./media/security-center-adaptive-network-hardening/anh-recommendation-rules.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>Kontrola a aplikování doporučených pravidel adaptivního posílení sítě

1. Na kartě **poškozené prostředky** vyberte virtuální počítač. Zobrazí se upozornění a doporučená pravidla posílení zabezpečení.

     ![pravidla posílení zabezpečení](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > Na kartě **pravidla** jsou uvedená pravidla, která přidávají adaptivní posílení zabezpečení sítě. Karta **výstrahy** obsahuje seznam výstrah, které byly vygenerovány z důvodu provozu, do toku do prostředku, který není v povoleném rozsahu IP adres v doporučených pravidlech.

2. Pokud chcete změnit některé parametry pravidla, můžete je upravit, jak je vysvětleno v tématu [Úprava pravidla](#modify-rule).
   > [!NOTE]
   > Můžete také [Odstranit](#delete-rule) nebo [Přidat](#add-rule) pravidlo.

3. Vyberte pravidla, která chcete použít na NSG, a klikněte na **vykonat**.

      > [!NOTE]
      > Vynutilná pravidla se přidají do NSG ochrany virtuálního počítače. (Virtuální počítač může být chráněný NSG, který je přidružený k jeho síťovému rozhraní, nebo podsíti, ve které se virtuální počítač nachází, nebo obojí)

    ![vymáhat pravidla](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### <a name="modify-a-rule"></a>Úprava pravidla <a name ="modify-rule"> </a>

Možná budete chtít upravit parametry pravidla, které se doporučuje. Můžete například chtít změnit Doporučené rozsahy IP adres.

Některé důležité pokyny pro úpravu pravidla posílení adaptivní sítě:

* Můžete upravit jenom pravidla "povoluje". 
* Nemůžete změnit pravidla "Povolit", která se stanou "Odepřít". 

  > [!NOTE]
  > Vytváření a úpravy pravidel "Odepřít" se provádí přímo na NSG. Další informace najdete v tématu [Vytvoření, změna nebo odstranění skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

* Pravidlo **Odepřít všechna přenosová** pravidla jsou jediným typem pravidla odepřít, které by bylo uvedeno zde a nelze je upravit. Můžete ho ale odstranit (viz [odstranění pravidla](#delete-rule)).
  > [!NOTE]
  > V důsledku spuštění algoritmu se doporučuje pravidlo **Odepřít všechna provozu** , Security Center neidentifikuje provoz, který by měl být povolený, a to na základě stávající konfigurace NSG. Proto je doporučené pravidlo Odepřít veškerý provoz na zadaný port. Název tohoto typu pravidla se zobrazí jako*generovaný systémem*. Po vynucení tohoto pravidla bude jeho skutečný název v NSG řetězec, který se skládá z protokolu, směru provozu, ZAMÍTNUTí a náhodného čísla.

*Postup úpravy pravidla posílení adaptivní sítě:*

1. Pokud chcete změnit některé parametry pravidla, na kartě **pravidla** klikněte na tři tečky (...) na konci řádku pravidla a klikněte na **Upravit**.

   ![Upravit pravidlo](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. V okně **Upravit pravidlo** aktualizujte podrobnosti, které chcete změnit, a klikněte na **Uložit**.

   > [!NOTE]
   > Po kliknutí na **Uložit**jste úspěšně změnili pravidlo. *Nepoužíváte ho však pro NSG.* Pokud ho chcete použít, musíte vybrat pravidlo v seznamu a kliknout na **vykonat** (jak je vysvětleno v dalším kroku).

   ![Upravit pravidlo](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. Chcete-li použít aktualizované pravidlo, vyberte ze seznamu aktualizované pravidlo a klikněte na tlačítko **vykonat**.

    ![vymáhat pravidlo](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### <a name="add-a-new-rule"></a>Přidat nové pravidlo <a name ="add-rule"> </a>

Můžete přidat pravidlo "Allow", které nedoporučuje Security Center.

> [!NOTE]
> Sem lze přidat pouze pravidla "povoleno". Pokud chcete přidat pravidla "Odepřít", můžete tak učinit přímo na NSG. Další informace najdete v tématu [Vytvoření, změna nebo odstranění skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

*Postup přidání pravidla posílení adaptivní sítě:*

1. Klikněte na **Přidat pravidlo** (nachází se v levém horním rohu).

   ![přidání pravidla](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. V okně **nové pravidlo** zadejte podrobnosti a klikněte na **Přidat**.

   > [!NOTE]
   > Po kliknutí na **Přidat**se pravidlo úspěšně přidalo a je uvedené s dalšími doporučenými pravidly. Nepoužíváte ho ale na NSG. Pokud ho chcete aktivovat, musíte v seznamu vybrat pravidlo a pak kliknout na **vykonat** (jak je vysvětleno v dalším kroku).

3. Chcete-li použít nové pravidlo, vyberte ze seznamu nové pravidlo a klikněte na tlačítko **vykonat**.

    ![vymáhat pravidlo](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### <a name="delete-a-rule"></a>Odstranění pravidla <a name ="delete-rule"> </a>

V případě potřeby můžete odstranit Doporučené pravidlo pro aktuální relaci. Můžete například určit, že použití navrhovaného pravidla může blokovat legitimní provoz.

*Postup odstranění pravidla posílení adaptivní sítě pro aktuální relaci:*

1. Na kartě **pravidla** klikněte na tři tečky (...) na konci řádku pravidla a klikněte na **Odstranit**.  

    ![pravidla posílení zabezpečení](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)