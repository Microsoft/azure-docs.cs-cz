---
title: Průvodce ukázkovou infrastrukturou Azure | Dokumentace Microsoftu
description: Další informace o klíčových návrh a implementace pokyny pro nasazení do Příklad infrastruktury v Azure.
documentationcenter: ''
services: virtual-machines-linux
author: zr-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 281fc2c0-b533-45fa-81a3-728c0049c73d
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: zarhoads
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 794182e3988a353b1e305a36da0475bacdea69b8
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49469846"
---
# <a name="example-azure-infrastructure-walkthrough-for-linux-vms"></a>Průvodce ukázkovou infrastrukturou Azure pro virtuální počítače s Linuxem
Tento článek vás provede vytvoření příkladu infrastruktury aplikace. Můžeme podrobně navrhování infrastruktury pro jednoduché online úložiště, který spojuje všechny zásady a rozhodnutí týkající se vytváření názvů, skupiny dostupnosti, virtuální sítě a nástroje pro vyrovnávání zatížení a skutečného nasazení virtuálních počítačů (VM).

## <a name="example-workload"></a>Příklad úloh
Adventure Works Cycles chce, aby se k sestavení aplikace v Azure, která se skládá z online úložiště:

* Dva servery nginx spustit klienta pro front-end webové vrstvy
* Dva servery nginx zpracování dat a objednávky v aplikační vrstvě
* Dvě MongoDB servery součástí horizontálně dělené clusteru pro ukládání dat produktu a objednávky v databázové vrstvě
* Dva řadiče domény služby Active Directory pro zákazníky a dodavatele v vrstvu ověřování
* Všechny servery se nacházejí v dvě podsítě:
  * front-endové podsítě pro webové servery 
  * back endovou podsíť pro aplikační servery, MongoDB clusteru a řadiče domény

![Diagram různých vrstev infrastruktury aplikace](./media/infrastructure-example/example-tiers.png)

Zabezpečený příchozí webový provoz musí být vyrovnávání zatížení mezi webovými servery zákazníci procházení online úložiště. Pořadí zpracování provozu ve formě HTTP požadavků webové servery musí být vyrovnávání zatížení mezi servery aplikací. Kromě toho musí být navrženy infrastruktury pro zajištění vysoké dostupnosti.

Výsledný návrh musí obsahovat:

* Předplatné Azure a účet
* Jedna skupina prostředků
* Azure Managed Disks
* Virtuální síť se dvěma podsítěmi
* Skupiny dostupnosti pro virtuální počítače s podobnou roli
* Virtuální počítače

Všechny výše uvedené použijte tyto zásady vytváření názvů:

* Adventure Works Cycles používá **[IT úlohy]-[umístění]-[prostředků Azure]** jako předponu
  * V tomto příkladu "**azos**" (Azure On-line Store) je název úlohy IT a "**použít**" je umístění (východní USA 2)
* Virtuální sítě pomocí AZOS. POUŽIJTE VN **[číslo]**
* Skupiny dostupnosti použijte azos-použití-jako-**[role]**
* Názvy virtuálních počítačů použít azos-použití-vm -**[název_virtuálního_počítače]**

## <a name="azure-subscriptions-and-accounts"></a>Účtů a předplatných Azure
Adventure Works Cycles využívá svoje předplatné Enterprise s názvem předplatného Enterprise společnosti Adventure Works k vyúčtování pro danou úlohu IT.

## <a name="storage"></a>Úložiště
Adventure Works Cycles určit, že by měl použít Azure Managed Disks. Při vytváření virtuálních počítačů, se používají obou úrovní úložiště k dispozici úložiště:

* **Storage úrovně standard** pro webové servery, aplikační servery a řadiče domény a jejich datové disky.
* **Storage úrovně Premium** pro MongoDB horizontálně dělené clusterových serverů a jejich datové disky.

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
Chcete-li udržet vysokou dostupnost všechny čtyři úrovně jejich online úložiště, Adventure Works Cycles jste se rozhodli čtyři skupiny dostupnosti:

* **azos použít jako webový** pro webové servery
* **azos používání jako aplikace** pro aplikační servery
* **azos použití jako databáze** pro servery v clusteru horizontálně dělené MongoDB
* **azos použijte jako dc** pro řadiče domény

## <a name="virtual-machines"></a>Virtuální počítače
Adventure Works Cycles jste se rozhodli následující názvy pro své virtuální počítače Azure:

* **azos použití vm-web01** první webového serveru
* **azos použití vm-web02** pro druhého webového serveru
* **azos použití vm-app01** pro první server pro aplikaci
* **azos použití vm-app02** pro druhý server aplikace
* **azos použití vm-db01** prvního serveru MongoDB v clusteru
* **azos použití vm-db02** pro druhý server MongoDB v clusteru
* **azos použití vm-dc01** pro první řadič domény
* **azos použití vm-dc02** pro druhého řadiče domény

Tady je výsledné konfigurace.

![Poslední aplikační infrastruktury, které jsou nasazené v Azure](./media/infrastructure-example/example-config.png)

Tato konfigurace zahrnuje:

* Výhradně cloudový virtuální sítě se dvěma podsítěmi (front-endových a back-end)
* Služba Azure Managed Disks použití disků Standard a Premium
* Čtyři skupiny dostupnosti, jeden pro každou vrstvu online úložiště
* Virtuální počítače pro čtyři vrstvy
* Skupinu s vyrovnáváním zatížení externí pro založený na protokolu HTTPS webový provoz z Internetu do webových serverů
* Interní s vyrovnáváním zatížení pro nešifrované webový provoz z webových serverů na aplikační servery
* Jedna skupina prostředků
