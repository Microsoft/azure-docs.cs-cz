---
title: Upravit kvóty a omezení v Azure Data Lake Analytics
description: Zjistěte, jak upravit a zvýšení kvóty a omezení v účtech Azure Data Lake Analytics (ADLA).
services: data-lake-analytics
ms.service: data-lake-analytics
author: omidm1
ms.author: omidm
ms.reviewer: jasonwhowell
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 4629b52f3b2c9e351ddc2a68a40c5178a9a73950
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43048251"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>Upravit kvóty a omezení v Azure Data Lake Analytics

Zjistěte, jak upravit a zvýšení kvóty a omezení v účtech Azure Data Lake Analytics (ADLA). Znalost těchto omezení vám můžou pomoct porozumět chování vašich úloh U-SQL. Všechny kvóty jsou obnovitelné, takže můžete zvýšit maximální limity kontaktovat podporu Azure.

## <a name="azure-subscriptions-limits"></a>Omezení předplatných Azure

**Maximální počet ADLA účty na předplatné a oblast:** 5

Pokud se pokusíte vytvořit účet ADLA šestý, zobrazí se chybu "Dosáhli jste maximálního počtu účtů Data Lake Analytics povoleno (5) v oblasti pod názvem odběru". 

Pokud chcete se dostat nad rámec tohoto limitu, zkuste tyto možnosti:
* Zvolte jinou oblast, pokud je to vhodné
* Požádejte podporu Azure o [otevření lístku podpory](#increase-maximum-quota-limits) požádat o zvýšení kvóty.

## <a name="default-adla-account-limits"></a>Výchozí omezení účtu ADLA

**Maximální počet jednotek analýzy (AU) na jeden účet:** 32

Toto je maximální počet jednotek analýzy, které můžou běžet souběžně ve vašem účtu. Pokud váš celkový počet jednotek analýzy spuštěné napříč všechny úlohy překročí tento limit, novější úlohy se zařadí do fronty automaticky. Příklad:

* Pokud máte jenom jednu úlohu s 32 jednotek analýzy, když odešlete sekundy úlohy se bude čekat ve frontě úloh až po dokončení první úlohy.
* Pokud už máte čtyři úlohy spuštěné a používají 8 jednotek analýzy, když odešlete páté úlohu, která potřebuje 8 jednotek au čeká ve frontě úloh až do 8 jednotek analýzy, které jsou k dispozici.

**Maximální počet jednotek analýzy (AU) na jednu úlohu:** 32

Toto je výchozí hodnota maximálního počtu jednotek analýzy, které je možné přiřadit každé jednotlivé úlohy ve vašem účtu. Úlohy, které jsou přiřazeny více než tento limit odmítne, pokud odesílatele je ovlivněna výpočetní zásad (omezení odesílání úlohy), která jim uděluje dalších jednotek au na úlohu. Horní mez tato hodnota je limit AU pro účet.

**Maximální počet souběžných úloh U-SQL na účet:** 20

Toto je maximální počet úloh, které můžou běžet souběžně ve vašem účtu. Nad tuto hodnotu novější úlohy se zařadí do fronty automaticky.

## <a name="adjust-adla-account-limits"></a>Upravit omezení účtu ADLA

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Zvolte existující účet ADLA.
3. Klikněte na **Vlastnosti**.
4. Upravit hodnoty pro **maximální počet jednotek au**, **maximální počet běžících úloh**, a **omezení odesílání úloh** tak, aby odpovídala vašim potřebám.

## <a name="increase-maximum-quota-limits"></a>Zvýšení maximální kvóty

Můžete najít další informace o omezeních Azure v [omezeních dokumentace ke službě Azure specifické pro služby](../azure-subscription-service-limits.md#data-lake-analytics-limits).

1. Žádost o podporu na webu Azure portal.

    ![Stránka portálu Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Stránka portálu Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Typ problému vyberte **kvóty**.
3. Vyberte vaše **předplatné** (ujistěte se, že se nejedná o "zkušební verze" předplatného).
4. Vyberte typ kvóty **Data Lake Analytics**.

    ![Stránka portálu Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. Na stránce problém popisují požadované navýšení limitu s **podrobnosti** z Proč je třeba tuto dodatečnou kapacitu.

    ![Stránka portálu Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Ověřte vaše kontaktní informace a vytvořte žádost o podporu.

Microsoft nezkontroluje vaši žádost a pokouší se nejdříve podle vašich obchodních potřeb.

## <a name="next-steps"></a>Další postup

* [Přehled služby Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Správa Azure Data Lake Analytics pomocí Azure PowerShellu](data-lake-analytics-manage-use-powershell.md)
* [Monitorování úloh Azure Data Lake Analytics a odstraňování potíží pomocí webu Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
