---
title: Příklad infrastruktury Azure návod | Microsoft Docs
description: Další informace o klíčových návrhu a implementace pokyny pro nasazení infrastruktury příklad v Azure.
documentationcenter: ''
services: virtual-machines-linux
author: iainfoulds
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
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ae7df08e7502fbfd500944f89a3fa6ee4806522a
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2017
ms.locfileid: "26745771"
---
# <a name="example-azure-infrastructure-walkthrough-for-linux-vms"></a>Příklad infrastruktury Azure návod pro virtuální počítače s Linuxem
Tento článek vás provede vytváření infrastruktury příklad aplikace. Jsme podrobnosti navrhování infrastruktury pro jednoduché online obchodu, která spojuje všechny pokyny a rozhodnutí, která kolem názvů, skupiny dostupnosti, virtuální sítě a nástroje pro vyrovnávání zatížení a ve skutečnosti nasazení virtuálních počítačů (VM).

## <a name="example-workload"></a>Příklad úloh
Společnosti Adventure Works Cycles chce sestavit aplikaci online úložiště v Azure, který se skládá z:

* Dva servery nginx spuštění front-endu do vrstvy webového klienta
* Dva servery nginx zpracování dat a objednávky v aplikační vrstvě
* Dva MongoDB servery součástí horizontálně dělené clusteru pro ukládání dat produktu a objednávky v databázové vrstvy
* Dva řadiče domény služby Active Directory pro účty zákazníků a všichni dodavatelé v vrstvou ověřování
* Všechny servery se nacházejí v dvě podsítě:
  * podsíť front-end pro webové servery 
  * back-end podsíť pro aplikační servery, MongoDB clusteru a řadiče domény

![Diagram různých vrstev pro infrastrukturu aplikace](./media/infrastructure-example/example-tiers.png)

Zabezpečené příchozí webové přenosy musí být vyrovnávání zatížení mezi webovými servery jako zákazníci procházet online úložiště. Pořadí zpracování přenosů dat ve formátu HTTP požadavků z webu, které servery musí být vyrovnávání zatížení mezi servery aplikací. Kromě toho musí být navrženy infrastruktury pro vysokou dostupnost.

Výsledný návrhu musí obsahovat:

* Předplatné Azure a účet
* Jedna skupina prostředků
* Azure Managed Disks
* Virtuální síť se dvěma podsítěmi
* Sady dostupnosti pro virtuální počítače s podobnou roli
* Virtuální počítače

Všechny výše použijte tyto zásady vytváření názvů:

* Adventure Works Cycles používá **[IT zatížení]-[umístění] – [prostředků Azure]** jako předponu
  * V tomto příkladu "**azos**" (online úložiště Azure) je název úlohy IT a "**použít**" (východní USA 2) je umístění
* Virtuální sítě pomocí AZOS. POUŽIJTE VN **[číslo]**
* Pomocí sad dostupnosti azos-použít-jako-**[role]**
* Názvy virtuálních počítačů pomocí azos-použít-vm -**[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Předplatná Azure a účty
Společnosti Adventure Works Cycles je pomocí své podnikové předplatné s názvem společnosti Adventure Works podnikové předplatné, zajistit fakturace pro tuto úlohu IT.

## <a name="storage"></a>Úložiště
Společnosti Adventure Works Cycles určit, že by měli používat Azure spravované disky. Při vytváření virtuálních počítačů, se používají i vrstvy úložiště k dispozici úložiště:

* **Standardní úložiště** pro webové servery, aplikační servery a řadiče domény a jejich datových disků.
* **Storage úrovně Premium** pro MongoDB horizontálně dělené clusterových serverů a jejich datových disků.

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
  * Název: back-end
  * Adresní prostor: 10.0.2.0/24

## <a name="availability-sets"></a>Skupiny dostupnosti
Aby se zachovala vysokou dostupnost všechny čtyři úrovně jejich online úložiště, se rozhodli společnosti Adventure Works Cycles čtyři skupiny dostupnosti:

* **azos použít jako webový** pro webové servery
* **azos používání jako aplikace** pro aplikační servery
* **azos použijte jako db** pro servery v clusteru horizontálně dělené MongoDB
* **azos použijte jako dc** pro řadiče domény

## <a name="virtual-machines"></a>Virtuální počítače
Následující názvy pro své virtuální počítače Azure se rozhodli společnosti Adventure Works Cycles:

* **azos použití virtuálních počítačů web01** pro prvního webového serveru
* **azos použití virtuálních počítačů web02** pro druhého webového serveru
* **azos použití virtuálních počítačů app01** pro první server pro aplikace
* **azos použití virtuálních počítačů app02** pro druhý server aplikace
* **azos použití virtuálních počítačů db01** pro první MongoDB serveru v clusteru
* **azos použití virtuálních počítačů db02** pro druhý server MongoDB v clusteru
* **azos použití virtuálních počítačů dc01** pro první řadič domény
* **azos použití virtuálních počítačů dc02** pro druhý řadič domény

Zde je Výsledná konfigurace.

![Infrastruktura konečné aplikace nasazené v Azure](./media/infrastructure-example/example-config.png)

Tato konfigurace zahrnuje:

* Čistě cloudové virtuální síť se dvěma podsítěmi (front-endové a back-end)
* Spravované disky systému Azure pomocí disků Standard a Premium
* Čtyři skupiny dostupnosti, jeden pro každou vrstvu úložiště online
* Virtuální počítače pro čtyři vrstvy
* Externí s vyrovnáváním zatížení pro založený na protokolu HTTPS webové přenosy z Internetu webových serverů
* K interní s vyrovnáváním zatížení pro nezašifrované webový provoz z webových serverů na aplikační servery
* Jedna skupina prostředků
