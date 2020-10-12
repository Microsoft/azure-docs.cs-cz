---
title: Architektura SAP HANA v Azure (velké instance) | Microsoft Docs
description: Architektura nasazení SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fa059ec8159779969c7ef7a5f4c97f3e985993a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87082184"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Architektura SAP HANA (velké instance) v Azure

Na vysoké úrovni má řešení SAP HANA v Azure (velké instance) vrstvu aplikace SAP umístěnou na virtuálních počítačích. Vrstva databáze se nachází na hardwaru SAP TDI, který se nachází v označení velkých instancí ve stejné oblasti Azure, která je připojená ke službě Azure IaaS.

> [!NOTE]
> Vrstvu aplikace SAP nasaďte ve stejné oblasti Azure jako vrstva SAP DBMS. Toto pravidlo je dobře dokumentováno v publikovaných informacích o úlohách SAP v Azure. 

Celková architektura SAP HANA v Azure (velké instance) poskytuje hardwarovou konfiguraci s certifikací SAP TDI, což je nevirtualizovaný, špičkový a vysoce výkonný Server pro databázi SAP HANA. Nabízí taky možnost a flexibilitu Azure pro škálování prostředků aplikace SAP, aby vyhovovaly vašim potřebám.

![Přehled architektury SAP HANA v Azure (velké instance)](./media/hana-overview-architecture/image1-architecture.png)

Zobrazená architektura je rozdělená na tři části:

- **Right**: zobrazuje místní infrastrukturu, která v datových centrech spouští různé aplikace, aby koncoví uživatelé měli přístup k aplikacím LOB, jako je například SAP. V ideálním případě je tato místní infrastruktura připojená k Azure pomocí [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Center**: zobrazuje Azure IaaS a, v tomto případě použití virtuálních počítačů k hostování SAP nebo jiných aplikací, které používají SAP HANA jako systém DBMS. Menší instance HANA, které fungují s pamětí, kterou virtuální počítače poskytují, se nasazují do virtuálních počítačů společně s jejich aplikační vrstvou. Další informace o virtuálních počítačích najdete v tématu [virtuální počítače](https://azure.microsoft.com/services/virtual-machines/).

   Síťové služby Azure se používají k seskupení systémů SAP spolu s dalšími aplikacemi do virtuálních sítí. Tyto virtuální sítě se připojují k místním systémům a SAP HANA v Azure (velké instance).

   Pro aplikace a databáze SAP NetWeaver, které se podporují pro spouštění v Azure, najdete informace v tématu [podpora SAP poznámka #1928533 – aplikace SAP v Azure: podporované produkty a typy virtuálních počítačů Azure](https://launchpad.support.sap.com/#/notes/1928533). Dokumentaci k nasazení řešení SAP v Azure najdete v těchto tématech:

  -  [Používání SAP na virtuálních počítačích s Windows](./get-started.md?toc=/azure/virtual-machines/linux/toc.json)
  -  [Použití řešení SAP na virtuálních počítačích Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Left**: zobrazuje SAP HANA hardware s certifikací TDI v razítku velké instance Azure. Jednotky velkých instancí HANA jsou připojené k virtuálním sítím vašeho předplatného Azure pomocí stejné technologie jako připojení z místního prostředí do Azure. Od května 2019 byla představena optimalizace, která umožňuje komunikaci mezi jednotkami velkých instancí HANA a virtuálními počítači Azure bez zapojení brány ExpressRoute. Tato optimalizace označovaná jako rychlá cesta ExpressRoute se zobrazuje v této architektuře (červené čáry). 

Vlastní razítko Azure velké instance kombinuje tyto komponenty:

- **Výpočty**: servery, které jsou založené na různých generaci procesorů Intel Xeon, které poskytují nezbytnou výpočetní schopnost a jsou SAP HANA certifikovány.
- **Síť**: jednotné vysokorychlostní síťové prostředky, které spojují součásti výpočetní služby, úložiště a sítě LAN.
- **Storage**: infrastruktura úložiště, která je dostupná prostřednictvím sjednocené síťové infrastruktury. Konkrétní kapacita úložiště, která je k dispozici, závisí na konkrétní SAP HANA konfiguraci Azure (velké instance), která je nasazená. Kapacita úložiště je k dispozici na dalších měsíčních nákladech.

V rámci víceklientské infrastruktury velkých instancí se zákazníci nasazují jako izolovaní klienti. Při nasazení tenanta pojmenovat předplatné Azure v rámci registrace Azure. Toto předplatné Azure je ten, se kterým se velké instance HANA fakturují. Tito klienti mají vztah 1:1 k předplatnému Azure. V případě sítě je možné získat přístup k jednotce velkých instancí HANA nasazenou v jednom klientovi v jedné oblasti Azure z různých virtuálních sítí, které patří do různých předplatných Azure. Tato předplatná Azure musí patřit do stejného zápisu Azure. 

Stejně jako u virtuálních počítačů se SAP HANA v Azure (velké instance) nabízí ve více oblastech Azure. Pokud chcete nabízet možnosti zotavení po havárii, můžete se rozhodnout, že se rozhodnete. Různá razítka velkých instancí v rámci jedné geografické oblasti jsou vzájemně propojená. Například časová razítka HANA pro velká instance v USA – západ a USA – východ jsou propojená pomocí vyhrazeného síťového propojení pro replikaci obnovení po havárii. 

Stejně jako si můžete vybrat mezi různými typy virtuálních počítačů pomocí Azure Virtual Machines, můžete si vybrat z různých SKU velké instance HANA, které jsou přizpůsobené pro různé typy úloh SAP HANA. SAP aplikuje poměry soketu na procesor pro různé úlohy založené na generacích procesorů Intel. V následující tabulce jsou uvedeny typy SKU, které nabízí.

Můžete najít dostupné skladové položky SKU [dostupné pro HLI](hana-available-skus.md).

**Další kroky**
- Odkazování [síťové architektury SAP Hana (velké instance)](hana-network-architecture.md)
