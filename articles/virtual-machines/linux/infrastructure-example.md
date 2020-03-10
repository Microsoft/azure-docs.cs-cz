---
title: Příklad postupu infrastruktury Azure
description: Přečtěte si pokyny pro návrh a implementaci klíčů pro nasazení ukázkové infrastruktury v Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 6040bf8862131f5a8a5564cd2f5d845fa0490a95
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944907"
---
# <a name="example-azure-infrastructure-walkthrough-for-linux-vms"></a>Příklad návodu k infrastruktuře Azure pro virtuální počítače se systémem Linux
Tento článek vás provede vytvořením ukázkové aplikační infrastruktury. Podrobně popisujeme infrastrukturu jednoduchého online úložiště, která přináší všechny pokyny a rozhodnutí týkající se konvencí pojmenování, skupin dostupnosti, virtuálních sítí a nástrojů pro vyrovnávání zatížení a skutečně nasazování virtuálních počítačů (VM).

## <a name="example-workload"></a>Příklad úlohy
Adventure Works Cycles chce v Azure vytvořit aplikaci v online úložišti, která se skládá z těchto součástí:

* Dva servery Nginx s front-end klientem ve webové vrstvě
* Dva servery Nginx zpracovávající data a objednávky v aplikační vrstvě
* Dva servery MongoDB součástí clusteru horizontálně dělené pro ukládání dat produktů a objednávek do databázové vrstvy
* Dva řadiče domény služby Active Directory pro účty zákazníků a dodavatele ve vrstvě ověřování
* Všechny servery se nacházejí ve dvou podsítích:
  * front-end podsíť pro webové servery 
  * back-endové podsítě pro aplikační servery, cluster MongoDB a řadiče domény

![Diagram různých vrstev pro aplikační infrastrukturu](./media/infrastructure-example/example-tiers.png)

Příchozí zabezpečený webový provoz musí být vyrovnaný k vyrovnávání zatížení mezi webovými servery, protože zákazníci procházejí online obchodem. Pořadí zpracování provozu ve formě požadavků HTTP z webových serverů musí být mezi aplikačními servery vyvážené vyrovnáváním zatížení. Kromě toho musí být infrastruktura navržena pro zajištění vysoké dostupnosti.

Výsledný návrh musí zahrnovat:

* Předplatné a účet Azure
* Jedna skupina prostředků
* Spravované disky Azure
* Virtuální síť se dvěma podsítěmi
* Skupiny dostupnosti pro virtuální počítače s podobnou rolí
* Virtuální počítače

U všech výše uvedených zásad vytváření názvů použijte tyto zásady:

* Adventure Works Cycles používá **[pracovní zatížení]-[location]-[prostředek Azure]** jako předponu.
  * V tomto příkladu je "**azos**" (Azure on-line Store) název úlohy IT a "**use**" (východní USA 2) je umístění
* Virtuální sítě používají AZOS-USE-VN<strong>[číslo]</strong>
* Skupiny dostupnosti používají azos-use-as- **[role]**
* Názvy virtuálních počítačů používají azos-use-VM- **[VMName]**

## <a name="azure-subscriptions-and-accounts"></a>Předplatná a účty Azure
Adventure Works Cycles používá své podnikové předplatné s názvem Adventure Works Enterprise Subscription k poskytování fakturace za tuto úlohu IT.

## <a name="storage"></a>Úložiště
V případě cyklů Adventure Works se zjistí, že by měly používat Azure Managed Disks. Při vytváření virtuálních počítačů se používají obě úrovně úložiště dostupné pro úložiště:

* **Storage úrovně Standard** pro webové servery, aplikační servery a řadiče domény a jejich datové disky.
* **Premium Storage** pro servery clusterů MongoDB horizontálně dělené a jejich datové disky.

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
Aby se zachovala vysoká dostupnost všech čtyř vrstev svého online úložiště, pak se na čtyři skupiny dostupnosti zařadí cykly Adventure Works:

* **azos – použít jako web** pro webové servery
* **azos – použít jako aplikaci** pro aplikační servery
* **azos-use-as-DB** pro servery v clusteru MongoDB horizontálně dělené
* **azos-use-as-DC** pro řadiče domény

## <a name="virtual-machines"></a>Virtuální počítače
Na následujících názvech se u svých virtuálních počítačů Azure rozhodly Adventure Works Cycles:

* **azos-use-VM-web01** pro první webový server
* **azos-use-VM-web02** pro druhý webový server
* **azos-use-VM-app01** pro první aplikační server
* **azos-use-VM-app02** pro druhý aplikační server
* **azos-use-VM-db01** pro první server MongoDB v clusteru
* **azos-use-VM-db02** pro druhý MongoDB Server v clusteru
* **azos-use-VM-DC01** pro první řadič domény
* **azos-use-VM-dc02** pro druhý řadič domény

Tady je výsledná konfigurace.

![Konečná aplikační infrastruktura nasazená v Azure](./media/infrastructure-example/example-config.png)

Tato konfigurace zahrnuje:

* Jenom cloudová virtuální síť se dvěma podsítěmi (front-endu a back-end)
* Azure Managed Disks používající disky úrovně Standard i Premium
* Čtyři skupiny dostupnosti, jeden pro každou úroveň online úložiště
* Virtuální počítače pro čtyři úrovně
* Externí sada s vyrovnáváním zatížení pro webový provoz založený na protokolu HTTPS z Internetu na webové servery
* Interní skupina s vyrovnáváním zatížení pro nešifrovaný webový provoz z webových serverů na aplikační servery
* Jedna skupina prostředků
