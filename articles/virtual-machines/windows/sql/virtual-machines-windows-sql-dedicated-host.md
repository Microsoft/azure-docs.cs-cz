---
title: Virtuální počítač SQL Server na vyhrazeném hostiteli Azure
description: Přečtěte si podrobnosti o spuštění virtuálního počítače SQL Server na vyhrazeném hostiteli Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: edb2d3fa670475d9b08fe05494035949181a9240
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834351"
---
# <a name="sql-server-vm-on-an-azure-dedicated-host"></a>Virtuální počítač SQL Server na vyhrazeném hostiteli Azure 

Tento článek podrobně popisuje specifika použití virtuálního počítače SQL Server s [vyhrazeným hostitelem Azure](/azure/virtual-machines/windows/dedicated-hosts). Další informace o vyhrazeném hostiteli Azure najdete v příspěvku blogu [Představujeme azure dedicated host](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/). 

## <a name="overview"></a>Přehled
[Azure Dedicated Host](/azure/virtual-machines/windows/dedicated-hosts) je služba, která poskytuje fyzické servery – schopné hostovat jeden nebo více virtuálních počítačů – vyhrazené pro jedno předplatné Azure. Vyhrazení hostitelé jsou stejné fyzické servery používané v datových centrech společnosti Microsoft, které jsou poskytovány jako prostředek. V rámci oblasti, zóny dostupnosti a domény selhání můžete zřídit vyhrazená hostitelé. Potom můžete virtuální počítače umístit přímo do zřízených hostitelů v jakékoli konfiguraci, která nejlépe vyhovuje vašim potřebám.

## <a name="limitations"></a>Omezení

- Škálovací sady virtuálních strojů nejsou aktuálně podporovány na vyhrazených hostitelích.
- Podporovány jsou následující řady virtuálních modulů: DSv3 a ESv3. 

## <a name="licensing"></a>Licencování

Můžete si vybrat mezi dvěma různými možnostmi licencování při přidávání virtuálního počítače SQL Server do vyhrazeného hostitele Azure. 

  - **Licencování virtuálních zařízení SQL:** Toto je existující možnost licencování, kde platíte za každou licenci virtuálního zařízení SQL Server jednotlivě. 
  - **Licence vyhrazených hostitelů**: Nový licenční model dostupný pro vyhrazený hostitel Azure, kde jsou licence SQL Serveru sdružené a placené na úrovni hostitele. 


Možnosti na úrovni hostitele pro použití existujících licencí serveru SQL Server: 
  - Hybridní výhoda SQL Server Enterprise Edition Azure
    - K dispozici zákazníkům s SA nebo předplatným.
    - Licencujte všechna dostupná fyzická jádra a užijte si neomezenou virtualizaci (až po maximální virtuální procesory podporované hostitelem).
        - Další informace o použití hybridní výhody Azure na azure dedicated hostitele, najdete v tématu [Azure Hybrid Benefit FAQ](https://azure.microsoft.com/pricing/hybrid-benefit/faq/). 
  - Licence SQL Serveru získané před 1.
      - SQL Server Enterprise edition má možnosti licence na úrovni hostitele i podle virtuálního zařízení. 
      - Sql Server Standard edition má k dispozici pouze možnost licence podle virtuálního připojení. 
          - Podrobnosti naleznete v [produktových podmínkách společnosti Microsoft](https://www.microsoft.com/licensing/product-licensing/products). 
  - Pokud není vybrána žádná možnost vyhrazené na úrovni hostitele serveru SQL Server, pak SQL Server AHB může být vybrána na úrovni jednotlivých virtuálních počítačů, stejně jako u víceklientských virtuálních počítačích.



## <a name="provisioning"></a>Zřizování  
Zřizování virtuálního počítače SQL Server vyhrazenému hostiteli se nijak neliší od jakéhokoli jiného virtuálního počítače Azure. Můžete tak učinit pomocí [Azure PowerShell](../dedicated-hosts-powershell.md), [portál Azure](../dedicated-hosts-portal.md)a [Azure CLI](../../linux/dedicated-hosts-cli.md).

Proces přidání existujícího virtuálního virtuálního soudu SQL Server do vyhrazeného hostitele vyžaduje výpadky, ale nebude mít vliv na data a nebude mít ztrátu dat. Nicméně všechny databáze, včetně systémových databází, by měly být zálohovány před přesunutím.

## <a name="virtualization"></a>Virtualizace 

Jednou z výhod vyhrazeného hostitele je neomezená virtualizace. Můžete mít například licence pro 64 virtuálních jader, ale můžete nakonfigurovat hostitele tak, aby měl 128 virtuálních jader, takže získáte dvojnásobek virtuálních jader, ale platíte pouze za polovinu licencí SQL Serveru. 

Vzhledem k tomu, že je to váš hostitel, máte nárok na nastavení virtualizace s poměrem 1:2. 

## <a name="faq"></a>Nejčastější dotazy

**Otázka: Jak funguje hybridní výhoda Azure pro licence Windows Server/SQL Server na vyhrazeném hostiteli Azure?**

A: Zákazníci mohou použít hodnotu svých stávajících licencí Windows Server a SQL Server s Software Assurance nebo kvalifikační předplatné licence, platit sníženou sazbu na Azure Dedicated Host pomocí Azure Hybrid Benefit. Zákazníci datových center a sql serveru SQL Server Enterprise Edition získají neomezenou virtualizaci (na hostiteli, který podléhá fyzické kapacitě základního serveru, nasadí co nejvíce virtuálních počítačů se systémem Windows Server) a používat hybridní výhody Azure.  Všechny úlohy Windows Serveru a SQL Serveru v vyhrazeném hostiteli Azure jsou také způsobilé pro rozšířené aktualizace zabezpečení pro Windows Server a SQL Server 2008/R2 bez dalších poplatků. 

## <a name="next-steps"></a>Další kroky

Další informace najdete v těchto článcích: 

* [Přehled sql serveru na virtuálním počítači se systémem Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Nejčastější dotazy pro SQL Server na virtuálním počítači se systémem Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Pokyny k cenovým hodnotě pro SQL Server na virtuálním počítači se systémem Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Poznámky k verzi pro SQL Server na virtuálním počítači se systémem Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


