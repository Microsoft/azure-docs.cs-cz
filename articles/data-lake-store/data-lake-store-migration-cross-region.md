---
title: Azure Data Lake Storage Gen1 migrace mezi oblastmi | Microsoft Docs
description: Zjistěte, co je třeba vzít v úvahu při plánování a dokončení migrace pro Azure Data Lake Storage Gen1 tak, jak budou k dispozici v nových oblastech.
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
ms.openlocfilehash: ba28d767c11c15b2dd70eeed2b39e13b084a7500
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88191341"
---
# <a name="migrate-azure-data-lake-storage-gen1-across-regions"></a>Migrace Azure Data Lake Storage Gen1 napříč oblastmi

Jak Azure Data Lake Storage Gen1 bude k dispozici v nových oblastech, můžete zvolit jednorázovou migraci, abyste mohli využít nové oblasti. Zjistěte, co je třeba vzít v úvahu při plánování a dokončení migrace.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Další informace najdete v tématu [Vytvoření bezplatného účtu Azure ještě dnes](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Data Lake Storage Gen1 ve dvou různých oblastech**. Další informace najdete v tématu [Začínáme s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Azure Data Factory**. Další informace najdete v tématu [Úvod do Azure Data Factory](../data-factory/introduction.md).


## <a name="migration-considerations"></a>Posouzení migrace

Nejprve Identifikujte strategii migrace, která je nejvhodnější pro vaši aplikaci, která zapisuje, čte nebo zpracovává data v Data Lake Storage Gen1. Když zvolíte strategii, zvažte požadavky na dostupnost vaší aplikace a prostoje, ke kterým dojde během migrace. Nejjednodušším způsobem může být například použití modelu migrace v cloudu "navýšení a posunutí". V tomto přístupu pozastavíte aplikaci v existující oblasti a všechna vaše data se zkopírují do nové oblasti. Po dokončení procesu kopírování obnovíte aplikaci v nové oblasti a pak odstraníte starý účet Data Lake Storage Gen1. Vyžaduje se výpadky během migrace.

Pro snížení prostojů můžete okamžitě začít ingestovat nová data v nové oblasti. Když máte potřebná minimální data, spusťte aplikaci v nové oblasti. Na pozadí pokračujte zkopírováním starších dat z existujícího účtu Data Lake Storage Gen1 do nového účtu Data Lake Storage Gen1 v nové oblasti. Pomocí tohoto přístupu můžete udělat přepínač na nové oblasti s malým prostojem. Po zkopírování všech starších dat Data Lake Storage Gen1 odstranit starý účet.

Další důležité informace, které je potřeba vzít v úvahu při plánování migrace:

* **Objem dat**: Objem dat (v gigabajtech, počtu souborů a složek atd.) má vliv na čas a prostředky, které pro migraci potřebujete.

* **Data Lake Storage Gen1 název účtu**. Nový název účtu v nové oblasti musí být globálně jedinečný. Například název starého účtu Data Lake Storage Gen1 v Východní USA 2 může být contosoeastus2.azuredatalakestore.net. Nový účet Data Lake Storage Gen1 můžete pojmenovat v severním EU contosonortheu.azuredatalakestore.net.

* **Nástroje**. Pro kopírování Data Lake Storage Gen1 souborů doporučujeme použít [aktivitu Azure Data Factory kopírování](../data-factory/connector-azure-data-lake-store.md) . Data Factory podporuje přesun dat s vysokým výkonem a spolehlivostí. Pamatujte, že Data Factory kopíruje pouze hierarchii složek a obsah souborů. Musíte ručně použít všechny seznamy řízení přístupu (ACL), které použijete ve starém účtu k novému účtu. Další informace, včetně cílů výkonu pro nejlepší scénáře, najdete v [Průvodci výkonem a laděním aktivity kopírování](../data-factory/copy-activity-performance.md). Pokud chcete data rychleji zkopírovat, možná budete muset použít další jednotky přesunu cloudových dat. Některé jiné nástroje, například AdlCopy, nepodporují kopírování dat mezi oblastmi.  

* **Poplatky za šířku pásma**. [Poplatky za šířku pásma](https://azure.microsoft.com/pricing/details/bandwidth/) se vztahují na data přenášená mimo oblast Azure.

* **Seznamy ACL pro vaše data**. Zabezpečte svá data v nové oblasti použitím seznamů ACL na soubory a složky. Další informace najdete v tématu [zabezpečení dat uložených v Azure Data Lake Storage Gen1](data-lake-store-secure-data.md). K aktualizaci a úpravě seznamů ACL doporučujeme použít migraci. Je možné, že budete chtít použít nastavení podobná vašemu aktuálnímu nastavení. Seznamy řízení přístupu, které jsou použity pro libovolný soubor, můžete zobrazit pomocí Azure Portal, [rutin prostředí PowerShell](/powershell/module/az.datalakestore/get-azdatalakestoreitempermission)nebo sad SDK.  

* **Umístění analytických služeb**. Pro dosažení nejlepšího výkonu by vaše analytické služby, jako je Azure Data Lake Analytics nebo Azure HDInsight, měly být ve stejné oblasti jako vaše data.  

## <a name="next-steps"></a>Další kroky
* [Přehled Azure Data Lake Storage Gen1](data-lake-store-overview.md)
