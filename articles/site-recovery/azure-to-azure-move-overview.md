---
title: Přesun virtuálních počítačů Azure IaaS do jiné oblasti Azure pomocí služby Azure Site Recovery | Dokumentace Microsoftu
description: Pomocí Azure Site Recovery pro přesun virtuálních počítačů Azure IaaS z jedné oblasti Azure do jiného.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 0d0009c833c313b5416998502601285e5b710a8d
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112542"
---
# <a name="move-azure-vms-to-another-region"></a>Přesun virtuálních počítačů Azure do jiné oblasti

Azure se rozrůstá výrazně spolu s zákazník základní a přidává podporu pro nové oblasti s rostoucí požadavky. Existují také nové funkce, které se přidají každý měsíc napříč službami. Kvůli tomu existují situace, když budete chtít vaše virtuální počítače přesunout do jiné oblasti nebo do zóny dostupnosti a zvyšují dostupnost.

Tento dokument vás provede různé scénáře, ve které chcete přesunout virtuální počítače a průvodce, na jak architektura by měl být nakonfigurovaný v cíli, abyste dosáhli vysoké dostupnosti. 
> [!div class="checklist"]
> * [Proč by posunula virtuálních počítačích Azure](#why-would-you-move-azure-vms)
> * [Postup přesunutí virtuálních počítačů Azure](#how-to-move-azure-vms)
> * [Typické architektury](#typical-architectures-for-a-multi-tier-deployment)
> * [Přesunout virtuální počítače, protože je v cílové oblasti](#move-azure-vms-to-another-region)
> * [Přesuňte virtuální počítače, které zvyšují dostupnost](#move-vms-to-increase-availability)


## <a name="why-would-you-move-azure-vms"></a>Proč by posunula virtuálních počítačích Azure

Zákazníci, kteří přesouvat virtuální počítače z následujících důvodů:-

- Pokud jste už nasadili v jedné oblasti a novou oblast byla přidána podpora, který je blíž ke koncovým uživatelům vaší aplikace nebo služby, budete pravděpodobně chtít **přesunout virtuální počítače, do nové oblasti je** snížit latenci. Stejný přístup nastane, pokud chcete konsolidovat předplatná nebo existují zásady správného řízení / organizace pravidla, která vyžadují, abyste přesunout. 
- Pokud byl váš počítač nasazený jako jedna instance virtuálního počítače nebo jako součást dostupnost sady a chcete zvýšit dostupnost smlouvy o úrovni služeb můžete **přesunout virtuální počítače v zóně dostupnosti**. 

## <a name="how-to-move-azure-vms"></a>Postup přesunutí virtuálních počítačů Azure
Přesun virtuálních počítačů zahrnuje následující kroky:

1. Ověření požadavků 
2. Příprava zdrojové virtuální počítače 
3. Připravte cílové oblasti 
4. Kopírování dat do cílové oblasti – technologie replikace pomocí služby Azure Site Recovery ke kopírování dat ze zdrojového virtuálního počítače do cílové oblasti
5. Otestujte konfiguraci: Dokončení jednoho replikaci, testovat konfiguraci provedením testovací převzetí služeb přes síť nevýrobní prostředí.
6. Provedení přesunu 
7. Zahodit prostředky ve zdrojové oblasti 


> [!IMPORTANT]
> Azure Site Recovery aktuálně podporuje přesun virtuálních počítačů z v oblasti do druhé a nepodporuje přesun v rámci oblasti. 

> [!NOTE]
> Podrobné informace o tomto postupu jsou uvedeny v dokumentaci pro každý scénář, jak je uvedeno tady

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Typických architekturách vícevrstvých nasazení
Níže uvedený oddíl přijmout procházení prostřednictvím nejběžnější zákazníkům nasazení architektury pro vícevrstvou aplikaci v Azure. V příkladu, které jsme se rozhodli zde je tři vrstvenou aplikaci s veřejnou IP adresu. Jednotlivé úrovně – Web, aplikace a databáze mají 2 VMs & připojeni pomocí služby Vyrovnávání zatížení do dalších vrstev. Databázová vrstva je replikace SQL Always ON mezi virtuálními počítači pro vysokou dostupnost (HA).

1.  **Jedna instance virtuálních počítačů nasazených na různých úrovních**– každý virtuální počítač ve vrstvě je nakonfigurovaný jako jedna instance virtuálního počítače, připojené prostřednictvím nástroje pro vyrovnávání zatížení do dalších vrstev. Toto je nejjednodušší konfiguraci, která zákazníkům přijmout.

       ![jeden virtuální počítače](media/move-vm-overview/regular-deployment.PNG)

2. **Virtuální počítače v jednotlivých vrstvách nasazena do skupiny dostupnosti** – každý virtuální počítač ve vrstvě je nakonfigurované ve skupině dostupnosti nastavena. [Skupiny dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) zajišťují distribuci virtuálních počítačů nasazených v Azure napříč několika izolovanými hardwarovými uzly v clusteru. To zajišťuje, že pokud dojde k selhání hardwaru nebo softwaru v rámci Azure, ovlivní to pouze dílčí část vašich virtuálních počítačů a vaše celkové řešení zůstane dostupné a funkční. 
   
      ![avset](media/move-vm-overview/AVset.PNG)

3. **Virtuální počítače v jednotlivých vrstvách nasazena do skupiny dostupnosti** – každý virtuální počítač ve vrstvě je nakonfigurovaný napříč [zóny dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview). Zónu dostupnosti, do oblasti Azure je kombinace doména selhání a aktualizační doména. Například pokud vytvoříte tři nebo více virtuálních počítačů napříč zónami tři v oblasti Azure, vaše virtuální počítače jsou účinně rozloženy na tři domény selhání a aktualizačních doménách tři. Platforma Azure rozpoznává této distribuce napříč aktualizační domény, abyste měli jistotu, že virtuální počítače v různých oblastech nejsou aktualizovány ve stejnou dobu.

      ![deploymnt zóny](media/move-vm-overview/zone.PNG)



 ## <a name="move-vms-as-is-to-a-target-region"></a>Přesunout virtuální počítače, protože je v cílové oblasti

Podle výše uvedených [architektury](#typical-architectures-for-a-multi-tier-deployment), heres jak nasazení bude vypadat po provedení přesunu jako cílové oblasti.


1. **Jedna instance virtuálních počítačů nasazených na různých úrovních** 

     ![single-zone.PNG](media/move-vm-overview/single-zone.PNG)

2. **Virtuální počítače v jednotlivých vrstvách nasazena do skupiny dostupnosti**

     ![crossregionAset.PNG](media/move-vm-overview/crossregionAset.PNG)


3. **Virtuální počítače v každé úrovni nasazené v zóně dostupnosti**
      

     ![AzoneCross.PNG](media/move-vm-overview/AzoneCross.PNG)

## <a name="move-vms-to-increase-availability"></a>Přesuňte virtuální počítače, které zvyšují dostupnost

1. **Jedna instance virtuálních počítačů nasazených na různých úrovních** 

     ![single-zone.PNG](media/move-vm-overview/single-zone.PNG)

2. **Virtuální počítače v jednotlivých vrstvách nasazena do skupiny dostupnosti** – můžete nakonfigurovat tak, aby umístěte své virtuální počítače ve skupině dostupnosti do samostatných zón dostupnosti, pokud budete chtít povolit replikaci pro virtuální počítač, pomocí Azure Site Recovery. Po dokončení operace přesunutí, bude smlouva SLA pro dostupnost 99,9 %.

     ![aset-Azone.PNG](media/move-vm-overview/aset-Azone.PNG)


## <a name="next-steps"></a>Další postup

V tomto dokumentu si přečíst o obecné pokyny pro přesouvání virtuálních počítačů. Vědět, provádění krok za krokem k tomu, další informace:


> [!div class="nextstepaction"]
> * [Přesuňte virtuální počítače Azure do jiné oblasti](azure-to-azure-tutorial-migrate.md)

> * [Přesuňte virtuální počítače Azure do zóny dostupnosti](move-azure-VMs-AVset-Azone.md)

