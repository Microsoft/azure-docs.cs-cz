---
title: "Příklad infrastruktury Azure návod | Microsoft Docs"
description: "Další informace o klíčových návrhu a implementace pokyny pro nasazení infrastruktury příklad v Azure."
documentationcenter: 
services: virtual-machines-windows
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 7032b586-e4e5-4954-952f-fdfc03fc1980
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c532657951d6d0241a5d8d25a56bb237ad481567
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="example-azure-infrastructure-walkthrough-for-windows-vms"></a>Příklad infrastruktury Azure návod pro virtuální počítače Windows
Tento článek vás provede vytváření infrastruktury příklad aplikace. Jsme podrobnosti navrhování infrastruktury pro jednoduché online obchodu, která spojuje všechny pokyny a rozhodnutí, která kolem názvů, skupiny dostupnosti, virtuální sítě a nástroje pro vyrovnávání zatížení a ve skutečnosti nasazení virtuálních počítačů (VM).

## <a name="example-workload"></a>Příklad úloh
Společnosti Adventure Works Cycles chce sestavit aplikaci v Azure, který se skládá z online obchodu:

* Dva servery služby IIS se spuštěnou front-endu do vrstvy webového klienta
* Zpracování dat a objednávky v aplikační vrstvě dva servery služby IIS
* Dvě instance Microsoft SQL Server se skupinami dostupnosti AlwaysOn (dva servery SQL a určujícího uzlu většina) pro ukládání dat produktu a objednávky v databázové vrstvy
* Dva řadiče domény služby Active Directory pro účty zákazníků a všichni dodavatelé v vrstvou ověřování
* Všechny servery se nacházejí v dvě podsítě:
  * podsíť front-end pro webové servery 
  * back-end podsíť pro aplikační servery, clusteru serveru SQL a řadiče domény

![Diagram různých vrstev pro infrastrukturu aplikace](./media/infrastructure-example/example-tiers.png)

Zabezpečené příchozí webové přenosy musí být vyrovnávání zatížení mezi webovými servery jako zákazníci procházet online obchodu. Pořadí zpracování přenosů dat ve formátu HTTP požadavků z webových serverů musí být rozložena mezi servery aplikací. Kromě toho musí být navrženy infrastruktury pro vysokou dostupnost.

Výsledný návrhu musí obsahovat:

* Předplatné Azure a účet
* Jedna skupina prostředků
* Azure Managed Disks
* Virtuální síť se dvěma podsítěmi
* Sady dostupnosti pro virtuální počítače s podobnou roli
* Virtuální počítače

Všechny výše použijte tyto zásady vytváření názvů:

* Adventure Works Cycles používá **[IT zatížení]-[umístění] – [prostředků Azure]** jako předponu
  * V tomto příkladu "**azos**" (Online úložiště Azure) je název úlohy IT a "**použít**" (východní USA 2) je umístění
* Virtuální sítě pomocí AZOS. POUŽIJTE VN**[číslo]**
* Pomocí sad dostupnosti azos-použít-jako-**[role]**
* Názvy virtuálních počítačů pomocí azos-použít-vm -**[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Předplatná Azure a účty
Společnosti Adventure Works Cycles je pomocí své podnikové předplatné s názvem společnosti Adventure Works podnikové předplatné, zajistit fakturace pro tuto úlohu IT.

## <a name="storage"></a>Úložiště
Společnosti Adventure Works Cycles určit, že by měli používat Azure spravované disky. Při vytváření virtuálních počítačů, se používají oba vrstev úložiště k dispozici:

* **Standardní úložiště** pro webové servery, aplikační servery a řadiče domény a jejich datových disků.
* **Storage úrovně Premium** pro virtuální počítače serveru SQL a jejich datových disků.

## <a name="virtual-network-and-subnets"></a>Virtuální sítě a podsítě
Vzhledem k tomu, že virtuální sítě nemusí probíhající připojení k síti společnosti Adventure pracovní cykly místní, rozhodla ve virtuální síti jenom pro cloud.

Vytvářely jenom pro cloud virtuální síť s následujícím nastavením pomocí portálu Azure:

* Název: AZOS-použití VN01
* Umístění: Východní USA 2
* Adresní prostor virtuální sítě: 10.0.0.0/8
* První podsíť:
  * Název: front-endu
  * Adresní prostor: 10.0.1.0/24
* Druhou podsíť:
  * Name: BackEnd
  * Adresní prostor: 10.0.2.0/24

## <a name="availability-sets"></a>Skupiny dostupnosti
Aby se zachovala vysokou dostupnost všechny čtyři úrovně jejich online úložiště, se rozhodli společnosti Adventure Works Cycles čtyři skupiny dostupnosti:

* **azos použít jako webový** pro webové servery
* **azos používání jako aplikace** pro aplikační servery
* **azos použijte jako sql** pro servery SQL Server
* **azos použijte jako dc** pro řadiče domény

## <a name="virtual-machines"></a>Virtuální počítače
Následující názvy pro své virtuální počítače Azure se rozhodli společnosti Adventure Works Cycles:

* **azos použití virtuálních počítačů web01** pro prvního webového serveru
* **azos použití virtuálních počítačů web02** pro druhého webového serveru
* **azos použití virtuálních počítačů app01** pro první server pro aplikace
* **azos použití virtuálních počítačů app02** pro druhý server aplikace
* **azos použití virtuálních počítačů sql01** pro první server SQL Server v clusteru
* **azos použití virtuálních počítačů sql02** pro druhý server SQL Server v clusteru
* **azos použití virtuálních počítačů dc01** pro první řadič domény
* **azos použití virtuálních počítačů dc02** pro druhý řadič domény

Zde je Výsledná konfigurace.

![Infrastruktura konečné aplikace nasazené v Azure](./media/infrastructure-example/example-config.png)

Tato konfigurace zahrnuje:

* Čistě cloudové virtuální síť se dvěma podsítěmi (front-endové a back-end)
* Disky Azure spravované s disky, Standard a Premium
* Čtyři skupiny dostupnosti, jeden pro každou vrstvu úložiště online
* Virtuální počítače pro čtyři vrstvy
* Externí s vyrovnáváním zatížení pro založený na protokolu HTTPS webové přenosy z Internetu webových serverů
* K interní s vyrovnáváním zatížení pro nezašifrované webový provoz z webových serverů na aplikační servery
* Jedna skupina prostředků
