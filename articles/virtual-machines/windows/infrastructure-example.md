---
title: Příklad postupu infrastruktury Azure
description: Přečtěte si pokyny pro návrh a implementaci klíčů pro nasazení ukázkové infrastruktury v Azure.
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7032b586-e4e5-4954-952f-fdfc03fc1980
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ab6f304d78357e261c68ebbcfcb3746844edce8a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038562"
---
# <a name="example-azure-infrastructure-walkthrough-for-windows-vms"></a>Příklad návodu k infrastruktuře Azure pro virtuální počítače s Windows
Tento článek vás provede vytvořením ukázkové aplikační infrastruktury. Podrobně popisujeme infrastrukturu jednoduchého online obchodu, která se skládá ze všech pokynů a rozhodnutí týkajících se konvencí pojmenování, skupin dostupnosti, virtuálních sítí a nástrojů pro vyrovnávání zatížení a ve skutečnosti nasazování virtuálních počítačů.

## <a name="example-workload"></a>Příklad úlohy
Adventure Works Cycles chce vytvořit aplikaci pro Online Store v Azure, která se skládá z těchto:

* Dva servery IIS s front-end klientem ve webové vrstvě
* Dva servery služby IIS zpracovávající data a objednávky v aplikační vrstvě
* Dvě instance Microsoft SQL Server se skupinami dostupnosti AlwaysOn (dva servery SQL a určující majoritní uzel) pro ukládání dat produktu a objednávek do databázové vrstvy
* Dva řadiče domény služby Active Directory pro účty zákazníků a dodavatele ve vrstvě ověřování
* Všechny servery se nacházejí ve dvou podsítích:
  * front-end podsíť pro webové servery 
  * back-endové podsítě pro aplikační servery, cluster SQL a řadiče domény

![Diagram různých vrstev pro aplikační infrastrukturu](./media/infrastructure-example/example-tiers.png)

Příchozí zabezpečený webový provoz musí být vyrovnaný k vyrovnávání zatížení mezi webovými servery, protože zákazníci procházejí online obchod. Pořadí zpracování provozu ve formě požadavků HTTP z webových serverů musí být vyvážené mezi aplikačními servery. Kromě toho musí být infrastruktura navržena pro zajištění vysoké dostupnosti.

Výsledný návrh musí zahrnovat:

* Předplatné a účet Azure
* Jedna skupina prostředků
* Spravované disky Azure
* Virtuální síť se dvěma podsítěmi
* Skupiny dostupnosti pro virtuální počítače s podobnou rolí
* Virtual Machines

U všech výše uvedených zásad vytváření názvů použijte tyto zásady:

* Adventure Works Cycles používá **[pracovní zatížení]-[location]-[prostředek Azure]** jako předponu.
  * V tomto příkladu je "**azos**" (Azure Online Store) název úlohy IT a "**use**" (východní USA 2) je umístění
* Virtuální sítě používají AZOS-USE-VN **[číslo]**
* Skupiny dostupnosti používají azos-use-as- **[role]**
* Názvy virtuálních počítačů používají azos-use-VM- **[VMName]**

## <a name="azure-subscriptions-and-accounts"></a>Předplatná a účty Azure
Adventure Works Cycles používá své podnikové předplatné s názvem Adventure Works Enterprise Subscription k poskytování fakturace za tuto úlohu IT.

## <a name="storage"></a>Úložiště
V případě cyklů Adventure Works se zjistí, že by měly používat Azure Managed Disks. Při vytváření virtuálních počítačů se používají obě dostupné úrovně úložiště:

* **Storage úrovně Standard** pro webové servery, aplikační servery a řadiče domény a jejich datové disky.
* **Premium Storage** pro virtuální počítače s SQL Server a jejich datové disky.

## <a name="virtual-network-and-subnets"></a>Virtuální sítě a podsítě
Vzhledem k tomu, že virtuální síť nepotřebuje nepřetržité připojení k místní síti Adventure Working, rozhodla se pouze s cloudovou virtuální sítí.

Vytvořili jenom cloudovou virtuální síť s následujícím nastavením pomocí Azure Portal:

* Název: AZOS-USE-VN01
* Umístění: Východní USA 2
* Adresní prostor virtuální sítě: 10.0.0.0/8
* První podsíť:
  * Název: front-end
  * Adresní prostor: 10.0.1.0/24
* Druhá podsíť:
  * Název: back-end
  * Adresní prostor: 10.0.2.0/24

## <a name="availability-sets"></a>Skupiny dostupnosti
Aby se zachovala vysoká dostupnost všech čtyř vrstev svého online obchodu, zařadí se na čtyři skupiny dostupnosti cykly Adventure Works:

* **azos – použít jako web** pro webové servery
* **azos – použít jako aplikaci** pro aplikační servery
* **azos-use-as-SQL** pro SQL servery
* **azos-use-as-DC** pro řadiče domény

## <a name="virtual-machines"></a>Virtual Machines
Na následujících názvech se u svých virtuálních počítačů Azure rozhodly Adventure Works Cycles:

* **azos-use-VM-web01** pro první webový server
* **azos-use-VM-web02** pro druhý webový server
* **azos-use-VM-app01** pro první aplikační server
* **azos-use-VM-app02** pro druhý aplikační server
* **azos-use-VM-sql01** pro první SQL Server Server v clusteru
* **azos-use-VM-sql02** pro druhý SQL Server Server v clusteru
* **azos-use-VM-DC01** pro první řadič domény
* **azos-use-VM-dc02** pro druhý řadič domény

Tady je výsledná konfigurace.

![Konečná aplikační infrastruktura nasazená v Azure](./media/infrastructure-example/example-config.png)

Tato konfigurace zahrnuje:

* Jenom cloudová virtuální síť se dvěma podsítěmi (front-endu a back-end)
* Azure Managed Disks s disky úrovně Standard i Premium
* Čtyři skupiny dostupnosti, jeden pro každou vrstvu online obchodu
* Virtuální počítače pro čtyři úrovně
* Externí sada s vyrovnáváním zatížení pro webový provoz založený na protokolu HTTPS z Internetu na webové servery
* Interní skupina s vyrovnáváním zatížení pro nešifrovaný webový provoz z webových serverů na aplikační servery
* Jedna skupina prostředků
