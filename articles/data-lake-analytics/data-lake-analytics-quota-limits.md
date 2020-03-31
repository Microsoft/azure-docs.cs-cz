---
title: Úprava kvót a omezení ve službě Azure Data Lake Analytics
description: Přečtěte si, jak upravit a zvýšit kvóty a limity v účtech Azure Data Lake Analytics (ADLA).
services: data-lake-analytics
ms.service: data-lake-analytics
author: omidm1
ms.author: omidm
ms.reviewer: jasonwhowell
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: c1d7c8cc4b50682a74ac88b2113f0d44ebc55199
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644711"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>Úprava kvót a omezení ve službě Azure Data Lake Analytics

Přečtěte si, jak upravit a zvýšit kvótu a limity v účtech Azure Data Lake Analytics (ADLA). Znalost těchto omezení vám pomůže pochopit vaše chování úlohy U-SQL. Všechny limity kvót jsou měkké, takže můžete zvýšit maximální limity kontaktováním podpory Azure.

## <a name="azure-subscriptions-limits"></a>Omezení předplatných Azure

**Maximální počet účtů ADLA na jedno předplatné na oblast:** 5

Pokud se pokusíte vytvořit šestý účet ADLA, zobrazí se chyba "Dosáhli jste maximálního počtu účtů Data Lake Analytics povolených (5) v oblasti pod názvem předplatného".

Pokud chcete překročit tento limit, můžete vyzkoušet tyto možnosti:
* zvolit jiný region, pokud je to vhodné
* obraťte se na podporu Azure [otevřením lístku podpory](#increase-maximum-quota-limits) požádat o zvýšení kvóty.

## <a name="default-adla-account-limits"></a>Výchozí limity účtu ADLA

**Maximální počet analytických jednotek (AU) na účet:** 250, výchozí 32

Toto je maximální počet au, které lze spustit souběžně ve vašem účtu. Pokud celkový počet spuštěných au ve všech úlohách překročí tento limit, novější úlohy jsou zařazeny do fronty automaticky. Například:

* Pokud máte pouze jednu úlohu spuštěnou s 32 au, při odeslání druhé úlohy bude čekat ve frontě úloh, dokud nebude dokončena první úloha.
* Pokud již máte spuštěny čtyři úlohy a každá používá 8 au, při odeslání páté úlohy, která potřebuje 8 AU čeká ve frontě úloh, dokud nejsou k dispozici 8 AU.

    ![Azure Data Lake Analytics limity a kvóty stránky](./media/data-lake-analytics-quota-limits/adjust-quota-limits.png)

**Maximální počet analytických jednotek (AU) na úlohu:** 250, výchozí 32

Jedná se o maximální počet au, které lze přiřadit ke každému jednotlivému projektu ve vašem účtu. Úlohy, které jsou přiřazeny více než tento limit, budou odmítnuty, pokud není předkladatel ovlivněn zásadou výpočtu (limit odeslání úlohy), která jim poskytuje více au na úlohu. Horní mez této hodnoty je limit AU pro účet.

**Maximální počet souběžných úloh U-SQL na účet:** 20

Toto je maximální počet úloh, které lze spustit souběžně ve vašem účtu. Nad tuto hodnotu jsou novější úlohy zařazeny do fronty automaticky.

## <a name="adjust-adla-account-limits"></a>Úprava limitů účtu ADLA

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Zvolte existující účet ADLA.
3. Klikněte na **Vlastnosti**.
4. Upravte hodnoty maximální **chu ,** **maximální počet spuštěných úloh**a limity odesílání **úloh** tak, aby vyhovovaly vašim potřebám.

## <a name="increase-maximum-quota-limits"></a>Zvýšení maximálních limitů kvót

Další informace o omezeních Azure najdete v [dokumentaci k omezením specifickým pro služby Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-analytics-limits).

1. Otevřete žádost o podporu na webu Azure Portal.

    ![Stránka portálu Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Stránka portálu Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Vyberte typ problému **Kvóta**.
3. Vyberte **předplatné** (ujistěte se, že se nejedná o zkušební předplatné).
4. Vyberte typ **kvóty Data Lake Analytics**.

    ![Stránka portálu Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. Na stránce problému vysvětlete požadovaný limit zvýšení s **podrobnostmi o tom,** proč potřebujete tuto dodatečnou kapacitu.

    ![Stránka portálu Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Ověřte kontaktní informace a vytvořte žádost o podporu.

Společnost Microsoft vaši žádost zkontroluje a co nejdříve se pokusí vyhovět vašim obchodním potřebám.

## <a name="next-steps"></a>Další kroky

* [Přehled služby Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Správa Azure Data Lake Analytics pomocí Azure PowerShellu](data-lake-analytics-manage-use-powershell.md)
* [Monitorování úloh Azure Data Lake Analytics a odstraňování potíží pomocí webu Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
