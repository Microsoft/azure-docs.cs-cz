---
title: Adaptivní posílení zabezpečení sítě v Azure Security Center | Microsoft Docs
description: Naučte se používat skutečné modely provozu k posílení pravidel skupin zabezpečení sítě (NSG) a další vylepšení stav zabezpečení.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2020
ms.author: memildin
ms.openlocfilehash: 14523e814c85469aa02a860e87b86defd7823c16
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439591"
---
# <a name="improve-your-network-security-posture-with-adaptive-network-hardening"></a>Vylepšení stav zabezpečení sítě díky adaptivnímu posílení sítě
Přečtěte si, jak nakonfigurovat adaptivní posílení zabezpečení sítě v Security Center.

## <a name="availability"></a>Dostupnost
|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Obecná dostupnost (GA)|
|Stanov|Vyžaduje [Azure Defender pro servery](defender-for-servers-introduction.md) .|
|Požadované role a oprávnění:|Oprávnění k zápisu do skupin zabezpečení sítě počítače|
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![No](./media/icons/no-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov)|
|||

## <a name="what-is-adaptive-network-hardening"></a>Co je adaptivní posílení zabezpečení sítě?
Použití [skupin zabezpečení sítě (NSG)](../virtual-network/network-security-groups-overview.md) k filtrování provozu do prostředků a z nich vylepšuje zabezpečení sítě stav. Přesto však může být v některých případech, kdy je skutečný přenos toků přes NSG podmnožinou definovaných pravidel NSG. V těchto případech je možné zvýšit stav zabezpečení tím, že posílíte pravidla NSG, a to na základě skutečných schémat přenosů.

Adaptivní posílení zabezpečení sítě poskytuje doporučení k dalšímu posílení pravidel NSG. Používá algoritmus strojového učení, který má vliv na skutečný provoz, známou důvěryhodnou konfiguraci, analýzu hrozeb a další indikátory ohrožení a pak poskytuje doporučení k tomu, aby se provozoval jenom z konkrétních řazených kolekcí IP/portů.

Řekněme například, že stávající pravidlo NSG je povolit provoz z 140.20.30.10/24 na portu 22. Adaptivní posílení zabezpečení sítě může na základě analýzy provozu doporučit zúžení rozsahu, který povoluje provoz z 140.23.30.10/29, a zamítnout veškerý provoz na tento port.

>[!Note]
> Doporučení adaptivního posílení zabezpečení sítě se podporují jenom u těchto specifických portů (pro UDP i TCP): 13, 17, 19, 22, 23, 53, 69, 81, 111, 119, 123, 135, 137, 138, 139, 161, 162, 389, 445, 512, 514, 593, 636, 873, 1433, 1434, 1900, 2049 2301, 2323, 2381, 3268, 3306, 3389, 4333, 5353, 5432, 5555, 5800, 5900, 5900, 5985, 5986, 6379 , 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017, 37215


## <a name="view-and-manage-hardening-alerts-and-rules"></a>Zobrazení a správa výstrah a pravidel posílení zabezpečení

1. V nabídce Security Center otevřete řídicí panel **Azure Defender** a vyberte dlaždici adaptivního posílení zabezpečení sítě (1) nebo položku panelu přehledů, která se týká adaptivního posílení sítě (2). 

    :::image type="content" source="./media/security-center-adaptive-network-hardening/traffic-hardening.png" alt-text="Přístup k nástrojům adaptivního posílení sítě" lightbox="./media/security-center-adaptive-network-hardening/traffic-hardening.png":::

    > [!TIP]
    > Na panelu přehledů se zobrazuje procento vašich virtuálních počítačů, které se v současnosti chrání pomocí adaptivního posílení zabezpečení sítě. 

1. Na stránce s podrobnostmi o **doporučeních adaptivního posílení sítě by se měla použít doporučení pro virtuální počítače z Internetu** a vaše síťové virtuální počítače se seskupí na tři karty:
   * **Zdroje**, které nejsou v pořádku: virtuální počítače, které mají aktuálně spuštěné doporučení a výstrahy, se spouštějí pomocí algoritmu adaptivního posílení sítě. 
   * **Dobré prostředky**: virtuální počítače bez výstrah a doporučení.
   * **Nekontrolované prostředky**: virtuální počítače, na kterých se algoritmus adaptivního posílení sítě nedá spustit kvůli jednomu z následujících důvodů:
      * **Virtuální počítače jsou klasickými virtuálními** počítači: podporují se jenom Azure Resource Manager virtuální počítače.
      * **Není k dispozici dostatek dat**: aby bylo možné vygenerovat přesná doporučení pro posílení provozu, Security Center vyžaduje alespoň 30 dnů přenosu dat.
      * **Virtuální počítač není chráněný pomocí Azure Defenderu**. pro tuto funkci mají nárok jenom virtuální počítače chráněné pomocí [Azure Defenderu pro servery](defender-for-servers-introduction.md) .

    :::image type="content" source="./media/security-center-adaptive-network-hardening/recommendation-details-page.png" alt-text="Stránka podrobností doporučení posílení zabezpečení sítě doporučení by měla být aplikována na virtuální počítače s přístupem k Internetu":::

1. Na kartě **poškozené prostředky** vyberte virtuální počítač, na kterém se mají zobrazit výstrahy, a doporučená pravidla posílení zabezpečení, která se mají použít.

    - Na kartě **pravidla** jsou uvedená pravidla, která umožňují adaptivní posílení zabezpečení sítě. doporučuje se přidat
    - Karta **výstrahy** obsahuje seznam výstrah, které byly vygenerovány z důvodu provozu, do toku do prostředku, který není v povoleném rozsahu IP adres v doporučených pravidlech.

1. Volitelně můžete upravit pravidla:

    - [Úprava pravidla](#modify-rule)
    - [Odstranění pravidla](#delete-rule) 
    - [Přidání pravidla](#add-rule)

3. Vyberte pravidla, která chcete použít na NSG, a klikněte na **vykonat**.

    > [!TIP]
    > Pokud se povolené zdrojové rozsahy IP adres zobrazují jako žádné, znamená to, že doporučeným pravidlem je pravidlo *odepření* , jinak se jedná o pravidlo *Povolení* .

    :::image type="content" source="./media/security-center-adaptive-network-hardening/hardening-alerts.png" alt-text="Správa pravidel posílení adaptivní sítě":::

      > [!NOTE]
      > Vynutilná pravidla se přidají do NSG ochrany virtuálního počítače. (Virtuální počítač může být chráněný NSG, který je přidružený k jeho síťovému rozhraní, nebo podsíti, ve které se virtuální počítač nachází, nebo obojí)

### <a name="modify-a-rule"></a>Úprava pravidla <a name ="modify-rule"></a>

Možná budete chtít upravit parametry pravidla, které se doporučuje. Můžete například chtít změnit Doporučené rozsahy IP adres.

Některé důležité pokyny pro úpravu pravidla posílení adaptivní sítě:

* Můžete upravit jenom pravidla "povoluje". 
* Nemůžete změnit pravidla "Povolit", která se stanou "Odepřít". 

  > [!NOTE]
  > Vytváření a úpravy pravidel "Odepřít" se provádí přímo na NSG. Další informace najdete v tématu [Vytvoření, změna nebo odstranění skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md).

* Pravidlo **Odepřít všechna přenosová** pravidla jsou jediným typem pravidla odepřít, které by bylo uvedeno zde a nelze je upravit. Můžete ho ale odstranit (viz [odstranění pravidla](#delete-rule)).
  > [!NOTE]
  > V důsledku spuštění algoritmu se doporučuje pravidlo **Odepřít všechna provozu** , Security Center neidentifikuje provoz, který by měl být povolený, a to na základě stávající konfigurace NSG. Proto je doporučené pravidlo Odepřít veškerý provoz na zadaný port. Název tohoto typu pravidla se zobrazí jako *generovaný systémem*. Po vynucení tohoto pravidla bude jeho skutečný název v NSG řetězec, který se skládá z protokolu, směru provozu, ZAMÍTNUTí a náhodného čísla.

*Postup úpravy pravidla posílení adaptivní sítě:*

1. Pokud chcete změnit některé parametry pravidla, na kartě **pravidla** klikněte na tři tečky (...) na konci řádku pravidla a klikněte na **Upravit**.

   ![Pravidla úprav s](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. V okně **Upravit pravidlo** aktualizujte podrobnosti, které chcete změnit, a klikněte na **Uložit**.

   > [!NOTE]
   > Po kliknutí na **Uložit** jste úspěšně změnili pravidlo. *Nepoužíváte ho však pro NSG.* Pokud ho chcete použít, musíte v seznamu vybrat pravidlo a vybrat možnost **vykonat** (jak je vysvětleno v dalším kroku).

   ![Výběr možnosti Uložit](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. Chcete-li použít aktualizované pravidlo, vyberte ze seznamu aktualizované pravidlo a klikněte na tlačítko **vykonat**.

    ![vymáhat pravidlo](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### <a name="add-a-new-rule"></a>Přidat nové pravidlo <a name ="add-rule"></a>

Můžete přidat pravidlo "Allow", které nedoporučuje Security Center.

> [!NOTE]
> Sem lze přidat pouze pravidla "povoleno". Pokud chcete přidat pravidla "Odepřít", můžete tak učinit přímo na NSG. Další informace najdete v tématu [Vytvoření, změna nebo odstranění skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md).

*Postup přidání pravidla posílení adaptivní sítě:*

1. Klikněte na **Přidat pravidlo** (nachází se v levém horním rohu).

   ![přidání pravidla](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. V okně **nové pravidlo** zadejte podrobnosti a klikněte na **Přidat**.

   > [!NOTE]
   > Po kliknutí na **Přidat** se pravidlo úspěšně přidalo a je uvedené s dalšími doporučenými pravidly. Nepoužíváte ho ale na NSG. Pokud ho chcete aktivovat, musíte v seznamu vybrat pravidlo a pak kliknout na **vykonat** (jak je vysvětleno v dalším kroku).

3. Chcete-li použít nové pravidlo, vyberte ze seznamu nové pravidlo a klikněte na tlačítko **vykonat**.

    ![vymáhat pravidlo](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### <a name="delete-a-rule"></a>Odstranění pravidla <a name ="delete-rule"></a>

V případě potřeby můžete odstranit Doporučené pravidlo pro aktuální relaci. Můžete například určit, že použití navrhovaného pravidla může blokovat legitimní provoz.

*Postup odstranění pravidla posílení adaptivní sítě pro aktuální relaci:*

1. Na kartě **pravidla** klikněte na tři tečky (...) na konci řádku pravidla a klikněte na **Odstranit**.  

    ![Odstranění pravidla](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)