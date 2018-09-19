---
title: Migrace mezi oblastmi Azure Data Lake Storage Gen1 | Dokumentace Microsoftu
description: Přečtěte si o Azure Data Lake Storage Gen1 migrace mezi oblastmi.
services: data-lake-store
documentationcenter: ''
author: swums
manager: amitkul
editor: swums
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: stewu
ms.openlocfilehash: 0d27ae79ab2c14cc5fd5ca81b8b7f089e7fa294e
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46126220"
---
# <a name="migrate-azure-data-lake-storage-gen1-across-regions"></a>Migrace mezi oblastmi Azure Data Lake Storage Gen1

Jak Azure Data Lake Storage Gen1 přestane být k dispozici v nové oblasti, můžete provést jednorázovou migraci, abyste mohli využívat nové oblasti. Zjistěte, co vzít v úvahu při plánování a provedení migrace.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Další informace najdete v tématu [vytvořte si bezplatný účet Azure ještě dnes](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Data Lake Storage Gen1 ve dvou různých oblastech**. Další informace najdete v tématu [Začínáme s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Azure Data Factory**. Další informace najdete v tématu [Úvod do Azure Data Factory](../data-factory/introduction.md).


## <a name="migration-considerations"></a>Aspekty migrace

Nejprve určete, která je nejvhodnější pro vaše aplikace, která zapíše čte a zpracovává data v Data Lake Storage Gen1 strategie migrace. Při výběru strategie, zvažte požadavky na dostupnost vaší aplikace a výpadků, ke které dojde během migrace. Například může být nejjednodušším přístupem k použití modelu migrace "lift and shift" cloud. V takovém případě aplikaci pozastavíte ve vaší existující oblasti při všechna data zkopírována do nové oblasti. Po dokončení kopírování obnovení aplikace v nové oblasti a pak odstraňte starý účet Data Lake Storage Gen1. Prostoje během migrace je povinný.

K omezení prostojů, můžete okamžitě začít ingestování nových dat v nové oblasti. Až budete mít minimum dat potřebných, spusťte aplikaci v nové oblasti. Na pozadí nadále kopírovat existující účet Data Lake Storage Gen1 starší data do nového účtu Data Lake Storage Gen1 v nové oblasti. Pomocí tohoto přístupu můžete provést přepínač nové oblasti s malý výpadek. Pokud všechny starší data zkopírovala, odstraňte starý účet Data Lake Storage Gen1.

Další důležité podrobnosti, které je třeba zvážit při plánování migrace jsou:

* **Objem dat**. Objem dat (v gigabajtech, počet soubory a složky a tak dále) má vliv na čas a prostředky, které potřebujete pro migraci.

* **Název účtu data Lake Storage Gen1**. Název nového účtu v nové oblasti musí být globálně jedinečný. Název účtu Data Lake Storage Gen1 staré v oblasti východní USA 2, například může být contosoeastus2.azuredatalakestore.net. Můžete pojmenovat nového účtu Data Lake Storage Gen1 v contosonortheu.azuredatalakestore.net Evropa – sever.

* **Nástroje**. Doporučujeme použít [aktivita služby Azure Data Factory kopírování](../data-factory/connector-azure-data-lake-store.md) kopírování souborů Data Lake Storage Gen1. Data Factory podporuje přesun dat s vysokým výkonem a spolehlivostí. Uvědomte si, že Data Factory kopíruje hierarchii složek a obsah souborů. Budete muset ručně použít libovolné seznamech (ACL), které můžete použít starý účet do nového účtu. Další informace, včetně cíle výkonnosti pro nejlepší scénáře, naleznete v tématu [Průvodce laděním a výkonem aktivity kopírování](../data-factory/copy-activity-performance.md). Pokud chcete data zkopírovat rychleji, potřebujete použít další jednotek pohybu dat v cloudu. Některé nástroje, jako je AdlCopy, nepodporují kopírování dat mezi oblastmi.  

* **Poplatky za šířku pásma**. [Poplatky za šířku pásma](https://azure.microsoft.com/pricing/details/bandwidth/) použít, protože data se přenáší z oblasti Azure.

* **Seznamy ACL v data**. Zabezpečení dat v nové oblasti s použitím seznamů řízení přístupu k souborům a složkám. Další informace najdete v tématu [zabezpečení dat uložených v Azure Data Lake Storage Gen1](data-lake-store-secure-data.md). Doporučujeme použít migraci k aktualizaci a upravit vaše seznamy ACL. Můžete chtít použít nastavení podobný aktuální nastavení. Můžete zobrazit seznamy ACL, které se použijí k jakémukoli souboru pomocí webu Azure portal, [rutin prostředí PowerShell](/powershell/module/azurerm.datalakestore/get-azurermdatalakestoreitempermission), nebo sad SDK.  

* **Umístění služby analýzy**. Pro zajištění nejlepšího výkonu analytickými službami, jako je Azure Data Lake Analytics nebo Azure HDInsight, by měl být ve stejné oblasti jako vaše data.  

## <a name="next-steps"></a>Další postup
* [Přehled služby Azure Data Lake Storage Gen1](data-lake-store-overview.md)
