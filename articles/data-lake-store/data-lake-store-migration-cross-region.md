---
title: Migrace mezi oblastmi Azure Data Lake Store | Microsoft Docs
description: Další informace o migraci mezi oblastmi pro Azure Data Lake Store.
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
ms.openlocfilehash: 1199eca457c3f06fdd6a4b68a05da3210ea9a2c9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="migrate-data-lake-store-across-regions"></a>Migrovat Data Lake Store v oblastech

Jakmile Azure Data Lake Store k dispozici v nové oblastech, můžete provést jednorázovou migraci, abyste mohli využívat nové oblasti. Zjistěte, co je potřeba zvážit při plánování a provedení migrace.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Další informace najdete v tématu [vytvořit účet Azure zdarma Dnes](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Data Lake Store ve dvou různých oblastech**. Další informace najdete v tématu [Začínáme s Azure Data Lake Store](data-lake-store-get-started-portal.md).
* **Azure Data Factory**. Další informace najdete v tématu [Úvod do Azure Data Factory](../data-factory/introduction.md).


## <a name="migration-considerations"></a>Posouzení migrace

Nejdřív určete strategii migrace, která je nejvhodnější pro aplikace, který zapíše, čte nebo dat v Data Lake Store. Když zvolíte strategie, zvažte požadavky dostupnosti vaší aplikace a výpadek, k níž dojde během migrace. Nejjednodušším přístupem může třeba použít model migrace "navýšení a shift" cloudu. V tomto přístupu pozastavení při všechna data budou zkopírována do nové oblasti aplikace ve vaší stávající oblasti. Po dokončení procesu kopírování se obnovit svoji aplikaci v nové oblasti a pak odstraňte starý účet Data Lake Store. Je nutná odstávka během migrace.

Chcete-li zkrátit dobu prostojů, může být okamžitě začít, příjem nová data v oblasti nové. Pokud máte minimální dat potřebných, spusťte aplikaci v nové oblasti. Na pozadí nadále zkopírujte starší data z existující účet Data Lake Store v oblasti nové na nový účet Data Lake Store. Když použijete tuto metodu, můžete provést přepínač do nové oblasti s malým množstvím výpadky. Pokud byl zkopírován starší data, odstraňte starý účet Data Lake Store.

Další důležité podrobnosti vzít v úvahu při plánování migrace jsou:

* **Datový svazek**. Objem dat (v gigabajtech, počet souborů a složek a tak dále) má vliv na čas a prostředky, které potřebujete k migraci.

* **Název účtu data Lake Store**. Název nového účtu v nové oblasti musí být globálně jedinečný. Contosoeastus2.azuredatalakestore.net může být například název starý účet Data Lake Store v oblasti Východ USA 2. Může být název nového účtu Data Lake Store v Severní Evropa contosonortheu.azuredatalakestore.net.

* **Nástroje pro**. Doporučujeme vám, že používáte [aktivita služby Azure Data Factory kopie](../data-factory/connector-azure-data-lake-store.md) kopírovat soubory Data Lake Store. Objekt pro vytváření dat podporuje přesun dat s vysokým výkonem a spolehlivostí. Uvědomte si, že objekt pro vytváření dat zkopíruje pouze hierarchii složek a obsahu souborů. Budete muset použít ručně všech seznamech (ACL) používaných v starý účet nový účet. Další informace, včetně cíle výkonnosti pro nejlepší možný scénáře, najdete v článku [výkonu kopie aktivity a vyladění průvodce](../data-factory/copy-activity-performance.md). Pokud chcete data zkopírovat rychleji, možná budete muset používat další jednotky přesun dat v cloudu. Některé nástroje, jako je AdlCopy, nepodporují kopírování dat mezi oblastmi.  

* **Šířka pásma poplatky**. [Šířka pásma poplatky](https://azure.microsoft.com/pricing/details/bandwidth/) použít, protože data se přenáší z oblasti Azure.

* **Seznamy ACL na vaše data**. Zabezpečení dat v nové oblasti s použitím seznamů řízení přístupu k souborům a složkám. Další informace najdete v tématu [zabezpečení dat uložených v Azure Data Lake Store](data-lake-store-secure-data.md). Doporučujeme vám, že používáte migraci k aktualizaci a upravit vaše seznamy ACL. Můžete chtít použít nastavení podobná aktuální nastavení. Seznamy ACL, které se použijí na všechny soubory pomocí portálu Azure můžete zobrazit [rutiny prostředí PowerShell](/powershell/module/azurerm.datalakestore/get-azurermdatalakestoreitempermission), nebo sady SDK.  

* **Umístění služby analytics**. Pro nejlepší výkon analytické služby, jako je Azure Data Lake Analytics nebo Azure HDInsight, musí být ve stejné oblasti jako vaše data.  

## <a name="next-steps"></a>Další postup
* [Přehled Azure Data Lake Store](data-lake-store-overview.md)
