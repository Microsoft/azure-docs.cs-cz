---
title: SQL Server virtuálního počítače na vyhrazeném hostiteli Azure
description: Přečtěte si informace o tom, jak spustit SQL Server virtuální počítač na vyhrazeném hostiteli Azure.
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
ms.openlocfilehash: 8b29bbce1511b022def522d46c74b99967a76ea3
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204520"
---
# <a name="sql-server-vm-on-an-azure-dedicated-host"></a>SQL Server virtuálního počítače na vyhrazeném hostiteli Azure 

Tento článek podrobně popisuje konkrétní informace o použití SQL Serverho virtuálního počítače s [vyhrazeným hostitelem Azure](/azure/virtual-machines/windows/dedicated-hosts). Další informace o vyhrazeném hostiteli Azure najdete v blogovém příspěvku, který [zavádí vyhrazeného hostitele Azure](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/). 

## <a name="overview"></a>Přehled
[Vyhrazený hostitel Azure](/azure/virtual-machines/windows/dedicated-hosts) je služba, která poskytuje fyzické servery – schopné hostovat jeden nebo víc virtuálních počítačů, které jsou vyhrazené pro jedno předplatné Azure. Vyhrazení hostitelé jsou stejné fyzické servery, které se používají v datových centrech Microsoftu, které se poskytují jako prostředek. Můžete zřídit vyhrazené hostitele v rámci oblasti, zóny dostupnosti a domény selhání. Pak můžete virtuální počítače umístit přímo do zřízených hostitelů, v libovolné konfiguraci nejlépe vyhovuje vašim potřebám.


[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-common-dedicated-hosts-preview.md)]


## <a name="licensing"></a>Licencování

Při přidávání SQL Server virtuálního počítače do vyhrazeného hostitele Azure si můžete vybrat mezi dvěma různými možnostmi licencování. 

  - **Licencování virtuálních počítačů SQL**: Toto je stávající možnost licencování, kdy platíte za každou SQL Server licenci k virtuálnímu počítači jednotlivě. 
  - **Licencování vyhrazeného hostitele**: Nový model licencování dostupný pro vyhrazeného hostitele Azure, kde SQL Server licence jsou na úrovni hostitele zabalené a placené. 


Možnosti na úrovni hostitele pro používání stávajících licencí SQL Server: 
  - Edice SQL Server Enterprise Zvýhodněné hybridní využití Azure
    - K dispozici pro zákazníky s přidružením zabezpečení nebo předplatného.
    - Licencujte všechny dostupné fyzické jádra a užívejte neomezenou virtualizaci (až do maximálního počtu vCPU podporovaných hostitelem).
        - Další informace o použití Zvýhodněné hybridní využití Azure u vyhrazeného hostitele Azure najdete v části [Nejčastější dotazy k zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/faq/). 
  - SQL Server licence získané do 1. října
      - Edice SQL Server Enterprise má možnosti licence na úrovni hostitele i podle virtuálních počítačů. 
      - Pro SQL Server Standard Edition je dostupná jenom možnost licence podle virtuálních počítačů. 
          - Podrobnosti najdete v tématu věnovaném [produktovým podmínkám Microsoftu](https://www.microsoft.com/licensing/product-licensing/products). 
  - Pokud není vybraná žádná SQL Server vyhrazená možnost na úrovni hostitele, můžete SQL Server AHB vybrat na úrovni jednotlivých virtuálních počítačů, stejně jako u více tenantů virtuálních počítačů.



## <a name="provisioning"></a>Zřizování  
Zřizování virtuálního počítače s SQL Server pro vyhrazeného hostitele se neliší od žádných jiných virtuálních počítačů Azure. Můžete tak učinit pomocí [Azure PowerShell](../dedicated-hosts-powershell.md), [Azure Portal](../dedicated-hosts-portal.md)a [Azure CLI](../../linux/dedicated-hosts-cli.md).

Proces přidání stávajícího SQL Server virtuálního počítače do vyhrazeného hostitele vyžaduje výpadky, ale nebude mít vliv na data a nebude mít za následek ztrátu dat. Nicméně všechny databáze, včetně systémových databází, by se měly zálohovat před přesunem.

## <a name="virtualization"></a>Virtualizace 

Jednou z výhod vyhrazeného hostitele je neomezená virtualizace. Můžete mít například licence na 64 virtuální jádra, ale hostitele můžete nakonfigurovat tak, aby měl 128 virtuální jádra, takže získáte dvojnásobek virtuální jádra, ale platíte za polovinu SQL Server licencí. 

Vzhledem k tomu, že se jedná o vašeho hostitele, máte nárok na nastavení virtualizace s poměrem 1:2. 

## <a name="faq"></a>Nejčastější dotazy

**Otázka: Jak Zvýhodněné hybridní využití Azure pracuje pro licence Windows Server/SQL Server na vyhrazeném hostiteli Azure?**

Odpověď: Zákazníci můžou použít hodnotu svých stávajících licencí Windows serveru a SQL Server se Software Assurance nebo opravňujícími licencemi předplatného, aby platili sníženou sazbu na vyhrazeném hostiteli Azure pomocí Zvýhodněné hybridní využití Azure. Zákazníci s Windows serverem datacentra a SQL Server Enterprise Edition získají neomezenou virtualizaci (na hostiteli můžou nasadit tolik virtuálních počítačů s Windows serverem, které jsou v závislosti na fyzické kapacitě základního serveru k dispozici), pokud mají licenci na celého hostitele. a použijte Zvýhodněné hybridní využití Azure.  Všechny úlohy Windows serveru a SQL Server ve vyhrazeném hostiteli Azure mají taky nárok na rozšířené aktualizace zabezpečení pro Windows Server a SQL Server 2008/R2 bez dalších poplatků. 

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích: 

* [Přehled SQL Server na virtuálním počítači s Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Nejčastější dotazy k SQL Server na virtuálním počítači s Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Doprovodné materiály k cenách pro SQL Server na virtuálním počítači s Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Poznámky k verzi pro SQL Server na virtuálním počítači s Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


