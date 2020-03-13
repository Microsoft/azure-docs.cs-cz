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
ms.openlocfilehash: bc610fa1d7a5fa1a10db3298164404b92d5d9f85
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2020
ms.locfileid: "79139585"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Adaptivní posílení zabezpečení sítě v Azure Security Center
Přečtěte si, jak nakonfigurovat adaptivní posílení zabezpečení sítě v Azure Security Center.

## <a name="what-is-adaptive-network-hardening"></a>Co je adaptivní posílení zabezpečení sítě?
Použití [skupin zabezpečení sítě (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) k filtrování provozu do prostředků a z nich vylepšuje zabezpečení sítě stav. Přesto však může být v některých případech, kdy je skutečný přenos toků přes NSG podmnožinou definovaných pravidel NSG. V těchto případech je možné zvýšit stav zabezpečení tím, že posílíte pravidla NSG, a to na základě skutečných schémat přenosů.

Adaptivní posílení zabezpečení sítě poskytuje doporučení k dalšímu posílení pravidel NSG. Používá algoritmus strojového učení, který má vliv na skutečný provoz, známou důvěryhodnou konfiguraci, analýzu hrozeb a další indikátory ohrožení a pak poskytuje doporučení k tomu, aby se provozoval jenom z konkrétních řazených kolekcí IP/portů.

Řekněme například, že stávající pravidlo NSG je povolit provoz z 140.20.30.10/24 na portu 22. Doporučení adaptivního posílení zabezpečení sítě, které je založené na analýze, by mělo omezit rozsah a povolit provoz z 140.23.30.10/29, což je úzký rozsah IP adres, a odepřít veškerý ostatní provoz na tento port.

>[!TIP]
> Doporučení adaptivního posílení zabezpečení sítě se podporují jenom u konkrétních portů. Úplný seznam najdete v části [které porty jsou podporovány?](#which-ports-are-supported) níže. 


![Zobrazení posílení zabezpečení sítě](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Zobrazit výstrahy a pravidla adaptivního posílení zabezpečení sítě

1. V Security Center vyberte **síť** -> **adaptivní posílení zabezpečení sítě**. Síťové virtuální počítače jsou uvedené v části tři samostatné karty:
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


### Úprava pravidla <a name ="modify-rule"> </a>

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

### Přidat nové pravidlo <a name ="add-rule"> </a>

Můžete přidat pravidlo "Allow", které nedoporučuje Security Center.

> [!NOTE]
> Sem lze přidat pouze pravidla "povoleno". Pokud chcete přidat pravidla "Odepřít", můžete tak učinit přímo na NSG. Další informace najdete v tématu [Vytvoření, změna nebo odstranění skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

*Postup přidání pravidla posílení adaptivní sítě:*

1. Klikněte na **Přidat pravidlo** (nachází se v levém horním rohu).

   ![Přidat pravidlo](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. V okně **nové pravidlo** zadejte podrobnosti a klikněte na **Přidat**.

   > [!NOTE]
   > Po kliknutí na **Přidat**se pravidlo úspěšně přidalo a je uvedené s dalšími doporučenými pravidly. Nepoužíváte ho ale na NSG. Pokud ho chcete aktivovat, musíte v seznamu vybrat pravidlo a pak kliknout na **vykonat** (jak je vysvětleno v dalším kroku).

3. Chcete-li použít nové pravidlo, vyberte ze seznamu nové pravidlo a klikněte na tlačítko **vykonat**.

    ![vymáhat pravidlo](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### Odstranění pravidla <a name ="delete-rule"> </a>

V případě potřeby můžete odstranit Doporučené pravidlo pro aktuální relaci. Můžete například určit, že použití navrhovaného pravidla může blokovat legitimní provoz.

*Postup odstranění pravidla posílení adaptivní sítě pro aktuální relaci:*

1. Na kartě **pravidla** klikněte na tři tečky (...) na konci řádku pravidla a klikněte na **Odstranit**.  

    ![pravidla posílení zabezpečení](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)

 

## <a name="which-ports-are-supported"></a>Které porty jsou podporovány?

Doporučení adaptivního posílení zabezpečení sítě se podporují jenom u konkrétních portů. Tato tabulka poskytuje úplný seznam:

|Port|Protokol|Přidružená služba|
|:---:|:----:|:----|
|13|UDP|Služba Daytime|
|17|UDP|Protokol QOTD|
|19|UDP|Protokol CHARGEN|
|22|TCP|SSH|
|23|TCP|Telnet|
|53|UDP|DNS|
|69|UDP|TFTP|
|81|TCP|Potenciálně škodlivý (uzel pro ukončení operace)|
|111|TCP/UDP|RPC|
|119|TCP|PROTOKOLY|
|123|UDP|NTP|
|135|TCP/UDP|Mapovač koncových bodů; RPC DCE|
|137|TCP/UDP|Služba názvu NetBIOS|
|138|TCP/UDP|Služba datagramu rozhraní NetBIOS|
|139|TCP|Služba relace rozhraní NetBIOS|
|161|TCP/UDP|Agent|
|162|TCP/UDP|Agent|
|389|TCP|LDAP|
|445|TCP|SMB|
|512|TCP|Rexec|
|514|TCP|Vzdálené prostředí|
|593|TCP/UDP|HTTP RPC|
|636|TCP|LDAP|
|873|TCP|rsync|
|1433|TCP|MS SQL|
|1434|UDP|MS SQL|
|1900|UDP|SSDP|
|1900|UDP|SSDP|
|2049|TCP/UDP|NFS|
|2301|TCP|Služba pro správu Compaq|
|2323|TCP|3D – nfsd|
|2381|TCP|Služba pro správu Compaq|
|3268|TCP|LDAP|
|3306|TCP|MySQL|
|3389|TCP|RDP|
|4333|TCP|mSQL|
|5353|UDP|mDNS|
|5432|TCP|PostgreSQL|
|5555|TCP|Osobní Agent; HP OmniBack|
|5800|TCP|VNC|
|5900|TCP|Vzdálená framebuffer; VNC|
|5900|TCP|VNC|
|5985|TCP|Windows PowerShell|
|5986|TCP|Windows PowerShell|
|6379|TCP|Redis|
|6379|TCP|Redis|
|7000|TCP|Cassandra|
|7001|TCP|Cassandra|
|7199|TCP|Cassandra|
|8081|TCP|CosmosDB Správce proxy Sun|
|8089|TCP|Splunk|
|8545|TCP|Potenciálně škodlivý (Cryptominer)|
|9042|TCP|Cassandra|
|9160|TCP|Cassandra|
|9300|TCP|Elasticsearch|
|11211|UDP|Memcached|
|16379|TCP|Redis|
|26379|TCP|Redis|
|27017|TCP|MongoDB|
|37215|TCP|Potenciálně škodlivé|
||||