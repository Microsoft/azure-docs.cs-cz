---
title: Průvodce ukázkovou infrastrukturou Azure | Dokumentace Microsoftu
description: Další informace o klíčových návrh a implementace pokyny pro nasazení do Příklad infrastruktury v Azure.
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7032b586-e4e5-4954-952f-fdfc03fc1980
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ddbaed6704fd32f7fd4fe5a790424cbf829d2f1c
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37932853"
---
# <a name="example-azure-infrastructure-walkthrough-for-windows-vms"></a>Průvodce ukázkovou infrastrukturou Azure pro virtuální počítače s Windows
Tento článek vás provede vytvoření příkladu infrastruktury aplikace. Můžeme podrobně navrhování infrastruktury pro jednoduché online úložiště, který spojuje všechny zásady a rozhodnutí týkající se vytváření názvů, skupiny dostupnosti, virtuální sítě a nástroje pro vyrovnávání zatížení a skutečného nasazení virtuálních počítačů (VM).

## <a name="example-workload"></a>Příklad úloh
Adventure Works Cycles chce, aby se k sestavení aplikace v Azure, která se skládá z online úložiště:

* Dva servery služby IIS spustit klienta pro front-end webové vrstvy
* Dva servery služby IIS, zpracování dat a objednávky v aplikační vrstvě
* Dvě instance Microsoft SQL Server se skupinami dostupnosti AlwaysOn (dva servery SQL a určujícího uzlu majority) pro ukládání dat produktu a objednávky v databázové vrstvě
* Dva řadiče domény služby Active Directory pro zákazníky a dodavatele v vrstvu ověřování
* Všechny servery se nacházejí v dvě podsítě:
  * front-endové podsítě pro webové servery 
  * back endovou podsíť pro aplikační servery, clusteru serveru SQL a řadiče domény

![Diagram různých vrstev infrastruktury aplikace](./media/infrastructure-example/example-tiers.png)

Zabezpečený příchozí webový provoz musí být vyrovnávání zatížení mezi webovými servery zákazníci procházení online obchodu. Pořadí zpracování provozu ve formě HTTP žádosti z webových serverů musí být rozložena mezi aplikační servery. Kromě toho musí být navrženy infrastruktury pro zajištění vysoké dostupnosti.

Výsledný návrh musí obsahovat:

* Předplatné Azure a účet
* Jedna skupina prostředků
* Azure Managed Disks
* Virtuální síť se dvěma podsítěmi
* Skupiny dostupnosti pro virtuální počítače s podobnou roli
* Virtuální počítače

Všechny výše uvedené použijte tyto zásady vytváření názvů:

* Adventure Works Cycles používá **[IT úlohy]-[umístění]-[prostředků Azure]** jako předponu
  * V tomto příkladu "**azos**" (Azure Store Online) je název úlohy IT a "**použít**" je umístění (východní USA 2)
* Virtuální sítě pomocí AZOS. POUŽIJTE VN **[číslo]**
* Skupiny dostupnosti použijte azos-použití-jako-**[role]**
* Názvy virtuálních počítačů použít azos-použití-vm -**[název_virtuálního_počítače]**

## <a name="azure-subscriptions-and-accounts"></a>Účtů a předplatných Azure
Adventure Works Cycles využívá svoje předplatné Enterprise s názvem předplatného Enterprise společnosti Adventure Works k vyúčtování pro danou úlohu IT.

## <a name="storage"></a>Úložiště
Adventure Works Cycles určit, že by měl použít Azure Managed Disks. Při vytváření virtuálních počítačů, se používají obou úrovní úložiště k dispozici:

* **Storage úrovně standard** pro webové servery, aplikační servery a řadiče domény a jejich datové disky.
* **Storage úrovně Premium** pro virtuální počítače SQL serveru a jejich datové disky.

## <a name="virtual-network-and-subnets"></a>Virtuální sítě a podsítě
Vzhledem k tomu, že virtuální sítě nemusí probíhající připojení k Adventure pracovní cykly v místní síti, se rozhodli v čistě cloudové virtuální síti.

Výhradně cloudový virtuální síť vytvořili s následujícím nastavením pomocí webu Azure portal:

* Název: AZOS-použití VN01
* Umístění: Východní USA 2
* Adresní prostor virtuální sítě: 10.0.0.0/8
* První podsíť:
  * Název: front-endu
  * Adresní prostor: 10.0.1.0/24
* Druhou podsíť:
  * Název: back-endu
  * Adresní prostor: 10.0.2.0/24

## <a name="availability-sets"></a>Skupiny dostupnosti
Kvůli udržení vysoké dostupnosti všechny čtyři úrovně jejich online úložiště, Adventure Works Cycles jste se rozhodli čtyři skupiny dostupnosti:

* **azos použít jako webový** pro webové servery
* **azos používání jako aplikace** pro aplikační servery
* **azos použijte jako sql** pro servery SQL
* **azos použijte jako dc** pro řadiče domény

## <a name="virtual-machines"></a>Virtuální počítače
Adventure Works Cycles jste se rozhodli následující názvy pro své virtuální počítače Azure:

* **azos použití vm-web01** první webového serveru
* **azos použití vm-web02** pro druhého webového serveru
* **azos použití vm-app01** pro první server pro aplikaci
* **azos použití vm-app02** pro druhý server aplikace
* **azos použití vm-sql01** pro první SQL Server server v clusteru
* **azos použití vm-sql02** pro druhý SQL Server server v clusteru
* **azos použití vm-dc01** pro první řadič domény
* **azos použití vm-dc02** pro druhého řadiče domény

Tady je výsledné konfigurace.

![Poslední aplikační infrastruktury, které jsou nasazené v Azure](./media/infrastructure-example/example-config.png)

Tato konfigurace zahrnuje:

* Výhradně cloudový virtuální sítě se dvěma podsítěmi (front-endových a back-end)
* Služba Azure Managed Disks se disky Standard a Premium
* Čtyři skupiny dostupnosti, jeden pro každou vrstvu online úložiště
* Virtuální počítače pro čtyři vrstvy
* Skupinu s vyrovnáváním zatížení externí pro založený na protokolu HTTPS webový provoz z Internetu do webových serverů
* Interní s vyrovnáváním zatížení pro nešifrované webový provoz z webových serverů na aplikační servery
* Jedna skupina prostředků
