---
title: Migrace mezi oblastmi úložiště datového jezera Azure | Dokumenty společnosti Microsoft
description: Přečtěte si o migraci mezi oblastmi pro Azure Data Lake Storage Gen1.
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
ms.openlocfilehash: 0bf0843314f38c0de28820c82e95b7921297bf40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60518469"
---
# <a name="migrate-azure-data-lake-storage-gen1-across-regions"></a>Migrace Azure Data Lake Storage Gen1 napříč oblastmi

Jako Azure Data Lake Storage Gen1 bude k dispozici v nových oblastech, můžete se rozhodnout provést jednorázovou migraci, využít výhod nové oblasti. Přečtěte si, co je třeba vzít v úvahu při plánování a dokončení migrace.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Další informace najdete v [tématu Vytvoření bezplatného účtu Azure ještě dnes](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Data Lake Storage Gen1 ve dvou různých oblastech**. Další informace najdete [v tématu Začínáme s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Azure Data Factory**. Další informace najdete v tématu [Úvod do Azure Data Factory](../data-factory/introduction.md).


## <a name="migration-considerations"></a>Požadavky na migraci

Nejprve určete strategii migrace, která nejlépe vyhovuje vaší aplikaci, která zapisuje, čte nebo zpracovává data v úložišti Data Lake Storage Gen1. Při výběru strategie zvažte požadavky na dostupnost aplikace a prostoje, ke kterým dochází během migrace. Nejjednodušším přístupem může být například použití modelu migrace do cloudu "lift-and-shift". V tomto přístupu pozastavíte aplikaci v existující oblasti, zatímco všechna data se zkopírují do nové oblasti. Po dokončení procesu kopírování obnovíte aplikaci v nové oblasti a potom odstraníte starý účet Data Lake Storage Gen1. Je vyžadována prostoj během migrace.

Chcete-li zkrátit prostoje, můžete okamžitě začít ingestovat nová data v nové oblasti. Pokud máte minimální potřebná data, spusťte aplikaci v nové oblasti. Na pozadí pokračujte v kopírování starších dat z existujícího účtu Data Lake Storage Gen1 do nového účtu Data Lake Storage Gen1 v nové oblasti. Pomocí tohoto přístupu můžete přejít do nové oblasti s malými prostoji. Po zkopírování všech starších dat odstraňte starý účet Data Lake Storage Gen1.

Další důležité podrobnosti, které je třeba zvážit při plánování migrace, jsou:

* **Objem dat**. Objem dat (v gigabajtů, počet souborů a složek a tak dále) ovlivňuje čas a prostředky, které potřebujete pro migraci.

* **Název účtu Data Lake Storage Gen1**. Nový název účtu v nové oblasti musí být globálně jedinečný. Například název starého účtu Data Lake Storage Gen1 v usa – východ 2 může být contosoeastus2.azuredatalakestore.net. Nový účet Data Lake Storage Gen1 můžete pojmenovat v severní contosonortheu.azuredatalakestore.net EU.

* **Nástroje**. Doporučujeme použít [aktivitu kopírování Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) ke kopírování souborů Data Lake Storage Gen1. Data Factory podporuje pohyb dat s vysokým výkonem a spolehlivostí. Mějte na paměti, že Data Factory zkopíruje pouze hierarchii složek a obsah souborů. Je třeba ručně použít všechny seznamy řízení přístupu (ACLs), které používáte ve starém účtu na nový účet. Další informace, včetně cílů výkonu pro nejlepší scénáře, naleznete v [tématu Kopírovat aktivitu výkonu a optimalizace průvodce](../data-factory/copy-activity-performance.md). Pokud chcete data zkopírovat rychleji, možná budete muset použít další jednotky cloudového pohybu dat. Některé další nástroje, například AdlCopy, nepodporují kopírování dat mezi oblastmi.  

* **Poplatky za šířku pásma**. [Poplatky za šířku pásma](https://azure.microsoft.com/pricing/details/bandwidth/) platí, protože data se přenášejí z oblasti Azure.

* **acl na vašich datech**. Zabezpečte data v nové oblasti použitím seznamů AC na soubory a složky. Další informace najdete [v tématu Zabezpečení dat uložených v Azure Data Lake Storage Gen1](data-lake-store-secure-data.md). Doporučujeme použít migraci k aktualizaci a úpravě aloků. Možná budete chtít použít nastavení podobná aktuálnímu nastavení. Acls, které jsou použity pro libovolný soubor pomocí portálu Azure, [rutin prostředí PowerShell](/powershell/module/az.datalakestore/get-azdatalakestoreitempermission)nebo sad SDK.  

* **Umístění analytických služeb**. Chcete-li dosáhnout nejlepšího výkonu, vaše analytické služby, jako je Azure Data Lake Analytics nebo Azure HDInsight, by měly být ve stejné oblasti jako vaše data.  

## <a name="next-steps"></a>Další kroky
* [Přehled Azure Data Lake Storage Gen1](data-lake-store-overview.md)
