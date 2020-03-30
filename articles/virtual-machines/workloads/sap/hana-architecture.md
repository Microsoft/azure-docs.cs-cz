---
title: Architektura SAP HANA v Azure (velké instance) | Dokumenty společnosti Microsoft
description: Architektura, jak nasadit SAP HANA v Azure (velké instance).
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
ms.openlocfilehash: 041da4198b0bdd040a4916008a1135aa2e2a5f7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614523"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Architektura SAP HANA (velké instance) v Azure

Na vysoké úrovni má řešení SAP HANA v Azure (velké instance) aplikační vrstvu SAP, která se iniduje ve virtuálních počítačích. Databázová vrstva je umístěna na hardwaru nakonfigurovaném SAP TDI umístěném v razítku velké instance ve stejné oblasti Azure, která je připojená k Azure IaaS.

> [!NOTE]
> Nasaďte aplikační vrstvu SAP ve stejné oblasti Azure jako vrstva SAP DBMS. Toto pravidlo je dobře zdokumentováno v publikovaných informacích o úlohách SAP v Azure. 

Celková architektura SAP HANA v Azure (velké instance) poskytuje konfiguraci hardwaru s certifikací SAP TDI, což je nevirtualizovaný, holý a vysoce výkonný server pro databázi SAP HANA. Poskytuje také možnost a flexibilitu Azure škálovat prostředky pro aplikační vrstvu SAP tak, aby vyhovovaly vašim potřebám.

![Přehled architektury SAP HANA v Azure (velké instance)](./media/hana-overview-architecture/image1-architecture.png)

Zobrazená architektura je rozdělena do tří částí:

- **Vpravo**: Zobrazuje místní infrastrukturu, která spouští různé aplikace v datových centrech, takže koncoví uživatelé mají přístup k lobovým aplikacím, jako je SAP. V ideálním případě je tato místní infrastruktura propojená s Azure s [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Centrum**: Zobrazuje Azure IaaS a v tomto případě použití virtuálních počítače k hostování SAP nebo jiných aplikací, které používají SAP HANA jako systém DBMS. Menší instance HANA, které fungují s pamětí, kterou virtuální aplikace poskytují, se nasazují ve virtuálních aplikacích společně s jejich aplikační vrstvou. Další informace o virtuálních počítačích najdete v tématu [Virtuální počítače](https://azure.microsoft.com/services/virtual-machines/).

   Síťové služby Azure se používají k seskupení systémů SAP spolu s dalšími aplikacemi do virtuálních sítí. Tyto virtuální sítě se připojují k místním systémům i k SAP HANA v Azure (velké instance).

   Pro sap NetWeaver aplikace a databáze, které jsou podporované ke spuštění v Azure, najdete v [tématu SAP Support Note #1928533 – SAP aplikace v Azure: Podporované produkty a typy virtuálních počítačů Azure](https://launchpad.support.sap.com/#/notes/1928533). Dokumentace o nasazení řešení SAP v Azure najdete v tématu:

  -  [Použití SAP na virtuálních počítačích s Windows](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Použití řešení SAP na virtuálních počítačích Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Vlevo**: Zobrazuje hardware s certifikací SAP HANA TDI v razítku velké instance Azure. Jednotky velké instance HANA jsou připojené k virtuálním sítím vašeho předplatného Azure pomocí stejné technologie jako připojení z místního prostředí do Azure. Od května 2019 byla zavedena optimalizace, která umožňuje komunikaci mezi jednotkami velké instance HANA a virtuálními počítači Azure bez zapojení brány ExpressRoute. Tato optimalizace s názvem ExpressRoute Rychlá cesta se zobrazí v této architektuře (červené čáry). 

Samotné razítko velké instance Azure kombinuje následující součásti:

- **Výpočetní technika:** Servery založené na různých generacích procesorů Intel Xeon, které poskytují potřebné výpočetní schopnosti a mají certifikaci SAP HANA.
- **Síť:** Jednotná vysokorychlostní síťová struktura, která propojuje výpočetní součásti, součásti úložiště a lan.
- **Úložiště**: Infrastruktura úložiště, ke které se přistupuje prostřednictvím jednotné síťové struktury. Konkrétní kapacita úložiště, která je k dispozici, závisí na konkrétní konfiguraci SAP HANA v Azure (velké instance), která je nasazená. Za příplatek je k dispozici větší úložná kapacita.

V rámci víceklientské infrastruktury razítka Velké instance jsou zákazníci nasazeni jako izolovaní klienti. Při nasazení klienta pojmenujete předplatné Azure v rámci registrace Azure. Toto předplatné Azure je ten, který hana velké instance se účtuje proti. Tito klienti mají vztah 1:1 k předplatnému Azure. Pro síť je možné přistupovat k jednotce velké instance HANA nasazené v jednom tenantovi v jedné oblasti Azure z různých virtuálních sítí, které patří do různých předplatných Azure. Tato předplatná Azure musí patřit ke stejné registraci Azure. 

Stejně jako u virtuálních počítačů se SAP HANA v Azure (velké instance) nabízí ve více oblastech Azure. Chcete-li nabídnout možnosti zotavení po havárii, můžete se přihlásit. Různá razítka velkých instancí v rámci jedné geopolitické oblasti jsou vzájemně propojena. Například hana velká instanci razítka v USA – západ a USA – východ jsou připojeny prostřednictvím vyhrazeného síťového propojení pro replikaci zotavení po havárii. 

Stejně jako si můžete vybrat mezi různými typy virtuálních počítačů s virtuálními počítači Azure, můžete si vybrat z různých sk. SAP používá poměry paměti a paměti a soketu procesoru pro různé úlohy založené na generacích procesorů Intel. V následující tabulce jsou uvedeny nabízené typy skladových položek.

Můžete najít dostupné skus k [dispozici sku pro HLI](hana-available-skus.md).

**Další kroky**
- Odkazovat [na architekturu sítě SAP HANA (Velké instance)](hana-network-architecture.md)