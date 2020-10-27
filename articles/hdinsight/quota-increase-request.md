---
title: Požadavek na zvýšení kvóty jádra procesoru – Azure HDInsight
description: Naučte se, jak požádat o zvýšení počtu PROCESORových jader přidělených vašemu předplatnému.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/07/2020
ms.openlocfilehash: 93c07b47e03bde20d494feb1c86179cd9a24ba26
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92539407"
---
# <a name="requesting-quota-increases-for-azure-hdinsight"></a>Vyžádání zvýšení kvóty pro Azure HDInsight

Základní kvóty procesoru vám pomůžou zajistit, aby využití prostředků bylo poměrně distribuované mezi všemi zákazníky v konkrétní oblasti Azure. V některých případech ale můžou vaše podnikové požadavky vyžadovat více prostředků clusteru než vaše aktuální kvóta umožní. V takových případech můžete požádat o zvýšení kvóty jádra procesoru, abyste mohli nasadit clustery, které odpovídají vašim požadavkům na zpracování dat.

Při dosažení limitu kvóty nemůžete nasadit nové clustery nebo škálovat existující clustery přidáním dalších pracovních uzlů. Jedinou omezenou kvótou je kvóta PROCESORových jader, která existuje na úrovni oblasti pro každé předplatné. Například vaše předplatné může mít v Východní USA oblasti 30 jader na procesor, přičemž v Východní USA je povolený jiný počet jader procesoru.

## <a name="gather-required-information"></a>Shromáždit požadované informace

Pokud jste obdrželi chybu s oznámením, že jste dosáhli limitu kvóty, použijte postup popsaný v této části ke shromáždění důležitých informací a odeslání žádosti o zvýšení kvóty.

1. Určete požadovanou velikost virtuálního počítače clusteru, škálování a typ.
1. Ověřte aktuální limity kapacity kvót vašeho předplatného. Chcete-li zjistit dostupné jádra, proveďte následující kroky:

    1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
    1. Přejděte na stránku **Přehled** pro cluster HDInsight.
    1. V nabídce vlevo vyberte **omezení kvóty** . Na stránce se zobrazí počet používaných jader, počet dostupných jader a celkový počet jader.

Chcete-li požádat o zvýšení kvóty, proveďte následující kroky:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. V levé dolní části stránky vyberte **help + support** .

    ![tlačítko pro pomoc a podporu](./media/quota-increase-request/help-support-button.png)

1. Vyberte **Nová žádost o podporu** .
1. Na stránce **Nová žádost o podporu** na kartě **Základní informace** vyberte následující možnosti:

   - **Typ problému** : **omezení služby a předplatného (kvóty)**
   - **Předplatné** : předplatné, které chcete upravit.
   - **Typ kvóty** : **HDInsight**

     ![Vytvoření žádosti o podporu pro zvýšení kvóty jádra HDInsight](./media/quota-increase-request/hdinsight-quota-support-request.png)

1. Vyberte **Další: řešení >>** .
1. Na stránce **Podrobnosti** zadejte popis problému, vyberte závažnost problému, upřednostňovanou metodu kontaktu a další povinná pole. Použijte níže uvedenou šablonu, abyste zajistili, že budete poskytovat potřebné informace. Žádosti o zvýšení kvóty jsou vyhodnocovány týmem Azure Capacity, nikoli produktovým týmem HDInsight. Podrobnější informace, které zadáte, bude pravděpodobně vaše žádost schválena.

    ```
    I would like to request [SPECIFY DESIRED AMOUNT] on [DESIRED SKU] for [SUBSCRIPTION ID].
    
    My current quota on this subscription is [CURRENT QUOTA AMOUNT].
    
    I would like to use the extra cores for [DETAIL REASON].
    ```

    ![Podrobnosti o problému](./media/quota-increase-request/problem-details.png)

1. Vyberte **Další: zkontrolovat + vytvořit >>** .
1. Na kartě **Revize + vytvořit** vyberte **vytvořit** .

> [!NOTE]  
> Pokud potřebujete zvýšit kvótu jádra HDInsight v soukromé oblasti, [odešlete žádost o schválení seznamu](https://aka.ms/canaryintwhitelist).

Můžete [kontaktovat podporu a požádat o zvýšení kvóty](../azure-portal/supportability/resource-manager-core-quotas-request.md).

Existují některá pevná omezení kvót. Například jedno předplatné Azure může mít maximálně 10 000 jader. Podrobnosti o těchto omezeních najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="next-steps"></a>Další kroky

* [Nastavení clusterů ve službě HDInsight pomocí Apache Hadoop, Spark, Kafka a dalších](hdinsight-hadoop-provision-linux-clusters.md): Naučte se, jak nastavit a nakonfigurovat clustery v HDInsight.
* [Monitorování výkonu clusteru](hdinsight-key-scenarios-to-monitor.md): Přečtěte si o klíčových scénářích, jak monitorovat cluster HDInsight, který může mít vliv na kapacitu clusteru.