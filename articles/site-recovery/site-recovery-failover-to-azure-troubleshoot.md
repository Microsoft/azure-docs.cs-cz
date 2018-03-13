---
title: "Řešení potíží s převzetí služeb při selhání do Azure selhání | Microsoft Docs"
description: "Tento článek popisuje způsoby řešení běžných chyb v převzetí služeb při selhání do Azure"
services: site-recovery
documentationcenter: 
author: ponatara
manager: abhemraj
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/09/2018
ms.author: ponatara
ms.openlocfilehash: 5c94e26c4639284f7e4c53d924f16040118d996c
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="troubleshoot-errors-when-failing-over-a-virtual-machine-to-azure"></a>Řešení chyb při přebírání služeb při selhání virtuálního počítače do Azure
Zobrazí jednu z těchto chyb při provádění převzetí služeb při selhání virtuálního počítače do Azure. K odstraňování, použijte popisuje kroky pro všechny chybové stavy.


## <a name="failover-failed-with-error-id-28031"></a>Převzetí služeb při selhání s ID chyby 28031

Site Recovery se nepodařilo vytvořit nezdařené přes virtuální počítač v Azure. K tomu mohlo dojít vlivem jednoho z následujících důvodů:

* Není k dispozici dostatečnou kvótu k dispozici pro vytvoření virtuálního počítače: dostupná kvóta můžete zkontrolovat tak, že přejdete do předplatného -> využití + kvóty. Můžete otevřít [novou žádost o podporu](http://aka.ms/getazuresupport) chcete kvótu zvýšit.

* Pokoušíte se k virtuálním počítačům převzetí služeb při selhání jinou velikost rodin ve stejné skupině dostupnosti. Ujistěte se, že zvolíte stejná rodina velikost pro všechny virtuální počítače ve stejné sadě dostupnosti. Změnit velikost tak, že přejdete do nastavení výpočty a síť virtuálního počítače a potom zkuste zopakovat převzetí služeb při selhání.

* Není zásadu na předplatné, které brání vytvoření virtuálního počítače. Změňte nastavení zásad pro povolení vytvoření virtuálního počítače a potom zkuste zopakovat převzetí služeb při selhání.

## <a name="failover-failed-with-error-id-28092"></a>Převzetí služeb při selhání s ID chyby 28092

Site Recovery nemohl vytvořit síťové rozhraní pro neúspěšný přes virtuální počítač. Ujistěte se, že máte dostatečnou kvótu k dispozici pro vytvoření síťových rozhraní v rámci předplatného. Dostupná kvóta můžete zkontrolovat tak, že přejdete do předplatného -> využití + kvóty. Můžete otevřít [novou žádost o podporu](http://aka.ms/getazuresupport) chcete kvótu zvýšit. Pokud máte dostatečnou kvótu, pak může se jednat přerušované vystavování, operaci opakujte. Pokud problém přetrvá i po opakování, pak nechte komentář na konci tohoto dokumentu.  

## <a name="failover-failed-with-error-id-70038"></a>Převzetí služeb při selhání s ID chyby 70038

Site Recovery se nepodařilo vytvořit nezdařené přes klasické virtuální počítač v Azure. K tomu mohlo dojít, protože:

* Jeden z prostředků, jako jsou například virtuální sítě, která je požadována pro virtuální počítač, který se má vytvořit neexistuje. Vytvoření virtuální sítě, jak je uvedeno v nastavení výpočtů a sítě virtuálního počítače nebo změnit nastavení pro virtuální síť, která již existuje a potom zkuste zopakovat převzetí služeb při selhání.


## <a name="next-steps"></a>Další postup

Pokud potřebujete další pomoc, následně je publikovat svůj dotaz na [Site Recovery fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) nebo komentář na konci tohoto dokumentu. Máme aktivní komunitě, které byste měli mít vám pomůže.
