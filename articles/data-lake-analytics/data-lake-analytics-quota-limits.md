---
title: Upravit kvóty a omezení v Azure Data Lake Analytics
description: Zjistěte, jak upravit a zvýšení kvóty a omezení v Azure Data Lake Analytics (ADLA) účty.
services: data-lake-analytics
ms.service: data-lake-analytics
author: omidm1
ms.author: omidm
editor: jasonwhowell
manager: kfile
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: e493c45ef49d370bbf7d007e039b4102aaa3cdf0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623974"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>Upravit kvóty a omezení v Azure Data Lake Analytics

Zjistěte, jak upravit a zvýšit kvótu a omezení v Azure Data Lake Analytics (ADLA) účty. Znalost těchto mezních hodnot vám mohou pomoci porozumět chování vaší úlohy U-SQL. Všechny maximální kvóty jsou logicky, tak, že se obrátíte na podporu Azure můžete zvýšit maximální limit.

## <a name="azure-subscriptions-limits"></a>Omezení předplatných Azure

**Maximální počet ADLA účty podle předplatného podle oblastí:** 5

Pokud se pokusíte vytvořit účet šesté ADLA, bude dojde k chybě "Bylo dosaženo maximální počet účtů Data Lake Analytics povoleno (5) v oblasti pod názvem odběru". 

Pokud chcete tento limit přesahují, můžete vyzkoušet tyto možnosti:
* Zvolte jinou oblast, pokud je to vhodné
* Požádejte podporu Azure podle [otevřením lístku podpory](#increase-maximum-quota-limits) požádat o zvýšení kvóty.

## <a name="default-adla-account-limits"></a>Limity účtu ADLA výchozí

**Maximální počet jednotek Analytics (Austrálie) na účet:** 32.

Toto je maximální počet Austrálie, které můžou běžet současně ve vašem účtu. Pokud váš celkový počet spuštění Austrálie pro všechny úlohy překračuje tento limit, novější úlohy se zařadí do fronty automaticky. Příklad:

* Pokud máte pouze jednu úlohu s 32 Austrálie při odesílání druhý úloha se bude čekat ve frontě úloh, dokud první úloha dokončena.
* Pokud již máte čtyři spuštěné úlohy a každý používá 8 Austrálie při odesílání páté úlohu, která potřebuje 8 Austrálie čeká ve frontě úloh, dokud jsou 8 Austrálie k dispozici.

**Maximální počet jednotek Analytics (Austrálie) na úlohu:** 32.

Toto je výchozí hodnota maximálního počtu Austrálie, které mohou být přiřazeny každé jednotlivé úlohy ve vašem účtu. Úlohy, které jsou přiřazeny více než toto omezení se odmítne, pokud odesílatel je ovlivňován zásadu výpočetní (limit odeslání úloh), která jim poskytne další Austrálie na úlohu. Horní mez tato hodnota je AU limit pro účet.

**Maximální počet souběžných úloh U-SQL na účet:** 20

Toto je maximální počet úloh, které můžou běžet současně ve vašem účtu. Vyšší než tato hodnota novější úlohy se zařadí do fronty automaticky.

## <a name="adjust-adla-account-limits"></a>Upravit omezení ADLA účtů

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Zvolte existující účet ADLA.
3. Klikněte na **Vlastnosti**.
4. Upravit hodnoty pro **maximální Austrálie**, **maximální počet spuštěných úloh**, a **úlohy odeslání omezení** podle svých potřeb.

## <a name="increase-maximum-quota-limits"></a>Zvýšit maximální kvóty

Můžete najít další informace o Azure omezení v [specifickou pro službu Azure omezuje dokumentaci](../azure-subscription-service-limits.md#data-lake-analytics-limits).

1. Otevřete žádost o podporu na portálu Azure.

    ![Stránky portálu Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Stránky portálu Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Vyberte typ problému **kvóty**.
3. Vyberte vaše **předplatné** (ujistěte se, není "zkušební" předplatné).
4. Vyberte typ kvóty **Data Lake Analytics**.

    ![Stránky portálu Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. Na stránce problém popisují požadovanou zvyšte limit s **podrobnosti** z Proč potřebujete tuto kapacitu navíc.

    ![Stránky portálu Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Ověřte kontaktní informace a vytvořte žádost o podporu.

Společnost Microsoft nezkontroluje vaši žádost a pokusí se co nejdříve podle vašich obchodních potřeb.

## <a name="next-steps"></a>Další postup

* [Přehled služby Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Správa Azure Data Lake Analytics pomocí Azure PowerShellu](data-lake-analytics-manage-use-powershell.md)
* [Monitorování úloh Azure Data Lake Analytics a odstraňování potíží pomocí webu Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
