---
title: Spuštění SQL Server virtuálního počítače na vyhrazeném hostiteli Azure
description: Přečtěte si, jak spustit SQL Server virtuální počítač na vyhrazeném hostiteli Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: bb80abc35aedcdf0b46cefa279e477739cf1df6b
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92789790"
---
# <a name="run-sql-server-vm-on-an-azure-dedicated-host"></a>Spuštění SQL Server virtuálního počítače na vyhrazeném hostiteli Azure 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tento článek podrobně popisuje konkrétní informace o použití SQL Serverho virtuálního počítače s [vyhrazeným hostitelem Azure](../../../virtual-machines/dedicated-hosts.md). Další informace o vyhrazeném hostiteli Azure najdete v blogovém příspěvku, který [zavádí vyhrazeného hostitele Azure](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/). 

## <a name="overview"></a>Přehled
[Vyhrazený hostitel Azure](../../../virtual-machines/dedicated-hosts.md) je služba, která poskytuje fyzické servery – schopné hostovat jeden nebo víc virtuálních počítačů, které jsou vyhrazené pro jedno předplatné Azure. Vyhrazení hostitelé jsou stejné fyzické servery, které se používají v datových centrech Microsoftu, které se poskytují jako prostředek. Můžete zřídit vyhrazené hostitele v rámci oblasti, zóny dostupnosti a domény selhání. Pak můžete virtuální počítače umístit přímo do zřízených hostitelů, v libovolné konfiguraci nejlépe vyhovuje vašim potřebám.

## <a name="limitations"></a>Omezení

- Ne všechny řady virtuálních počítačů jsou na vyhrazených hostitelích podporované a dostupnost řady virtuálních počítačů se v jednotlivých oblastech liší. Další informace najdete v tématu [Přehled vyhrazených hostitelů Azure](../../../virtual-machines/dedicated-hosts.md).

## <a name="licensing"></a>Licencování

Při umísťování SQL Server virtuálního počítače do vyhrazeného hostitele Azure si můžete vybrat mezi dvěma různými možnostmi licencování. 

  - **Licencování virtuálních počítačů SQL** : Jedná se o existující možnost licencování, kde platíte za každou SQL Server licenci virtuálních počítačů jednotlivě. 
  - **Licencování vyhrazených hostitelů** : nový licenční model dostupný pro vyhrazeného hostitele Azure, kde SQL Server licence jsou zabalené a placené pro na úrovni hostitele. 


Možnosti na úrovni hostitele pro používání stávajících licencí SQL Server: 
  - Zvýhodněné hybridní využití Azure edice SQL Server Enterprise (AHB)
    - K dispozici pro zákazníky s přidružením zabezpečení nebo předplatného.
    - Licencujte všechny dostupné fyzické jádra a užívejte neomezenou virtualizaci (až do maximálního počtu vCPU podporovaných hostitelem).
        - Další informace o použití AHB na vyhrazeném hostiteli Azure najdete v tématu [zvýhodněné hybridní využití Azure Nejčastější dotazy](https://azure.microsoft.com/pricing/hybrid-benefit/faq/). 
  - SQL Server licence získané do 1. října
      - Edice SQL Server Enterprise má možnosti licence na úrovni hostitele i podle virtuálních počítačů. 
      - Edice SQL Server Standard má k dispozici pouze možnost licence k virtuálnímu počítači. 
          - Podrobnosti najdete v tématu [Microsoft Product terms](https://www.microsoft.com/licensing/product-licensing/products). 
  - Pokud není vybraná žádná SQL Server vyhrazená možnost na úrovni hostitele, můžete na úrovni jednotlivých virtuálních počítačů vybrat SQL Server AHB, stejně jako u více tenantů virtuálních počítačů.



## <a name="provisioning"></a>Zřizování  
Zřizování virtuálního počítače s SQL Server pro vyhrazeného hostitele se neliší od žádných jiných virtuálních počítačů Azure. Můžete tak učinit pomocí [Azure PowerShell](../../../virtual-machines/windows/dedicated-hosts-powershell.md), [Azure Portal](../../../virtual-machines/dedicated-hosts-portal.md)a [Azure CLI](../../../virtual-machines/linux/dedicated-hosts-cli.md).

Proces přidání stávajícího SQL Server virtuálního počítače do vyhrazeného hostitele vyžaduje výpadky, ale nebude mít vliv na data a nebude mít za následek ztrátu dat. Nicméně všechny databáze, včetně systémových databází, by se měly zálohovat před přesunem.

## <a name="virtualization"></a>Virtualizace 

Jednou z výhod vyhrazeného hostitele je neomezená virtualizace. Například můžete mít licence na 64 virtuální jádra, ale hostitele můžete nakonfigurovat tak, aby měl 128 virtuální jádra, takže získáte dvojnásobek virtuální jádra, ale platíte jenom polovinu toho, co byste chtěli SQL Server licencí. 

Vzhledem k tomu, že je to váš hostitel, máte nárok na nastavení virtualizace s poměrem 1:2. 

## <a name="faq"></a>Nejčastější dotazy

**Otázka: jak Zvýhodněné hybridní využití Azure fungují pro licence Windows Server/SQL Server na vyhrazeném hostiteli Azure?**

Odpověď: zákazníci můžou použít hodnotu svých stávajících licencí Windows Server a SQL Server se Software Assurance nebo opravňujícími licencemi předplatného, aby platili sníženou sazbu na vyhrazeném hostiteli Azure pomocí Zvýhodněné hybridní využití Azure. Zákazníci s Windows serverem datacentra a SQL Server Enterprise Edition získají neomezenou virtualizaci (na hostiteli můžou nasadit tolik virtuálních počítačů s Windows serverem, aby mohli na hostiteli splňovat fyzickou kapacitu základního serveru), když si zadají licenci na celého hostitele a používají Zvýhodněné hybridní využití Azure.  Všechny úlohy Windows serveru a SQL Server ve vyhrazeném hostiteli Azure mají taky nárok na rozšířené aktualizace zabezpečení pro Windows Server a SQL Server 2008/R2 bez dalších poplatků. 

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích: 

* [Přehled SQL Server na virtuálním počítači s Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Nejčastější dotazy k SQL Server na virtuálním počítači s Windows](frequently-asked-questions-faq.md)
* [Doprovodné materiály k cenách pro SQL Server na virtuálním počítači s Windows](pricing-guidance.md)
* [Poznámky k verzi pro SQL Server na virtuálním počítači s Windows](doc-changes-updates-release-notes.md)