---
title: Řešení chyb při vytváření prostředků ve službě Azure HDInsight
description: V tomto článku jsou uvedené běžné chyby a techniky zmírnění potíží s kapacitou.
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: bad934e02184c46c19dcda91e18e7c7ce2f1c884
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944650"
---
# <a name="troubleshoot-resource-creation-failures-in-azure-hdinsight"></a>Řešení chyb při vytváření prostředků ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při interakci se službou Azure HDInsight.

## <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Chyba: nasazení by překročilo kvótu 800.

V Azure platí limit kvóty 800 nasazení na skupinu prostředků. Pro každou skupinu prostředků, předplatné, účet nebo jiné obory se aplikují různé kvóty. Například v rámci předplatného může být nakonfigurované omezení počtu jader v jedné oblasti. Při pokusu o nasazení virtuálního počítače s více jádry, než je povoleno, se zobrazí chybová zpráva s oznámením, že kvóta byla překročena.

Pokud chcete tento problém vyřešit, odstraňte nasazení, která už nepotřebujete, pomocí Azure Portal, CLI nebo PowerShellu.

Další informace najdete v tématu [Řešení chyb týkajících se kvót prostředků](../azure-resource-manager/templates/error-resource-quota.md).

## <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Chyba: maximální uzel překročil dostupné jádra v této oblasti.

V rámci předplatného může být nakonfigurované omezení počtu jader v jedné oblasti. Při pokusu o nasazení prostředku s více jádry, než je povoleno, se zobrazí chybová zpráva s oznámením, že kvóta byla překročena.

Pokud chcete požádat o navýšení kvóty, postupujte následovně:

1. Otevřete [Azure Portal](https://portal.azure.com)a vyberte **help + Support (podpora**).

1. Vyberte **Nová žádost o podporu**.

1. Na kartě **základy** na nové stránce **žádosti o podporu** zadejte tyto informace:

   * **Typ problému:** Vyberte **omezení služby a předplatné (kvóty)**.
   * **Předplatné:** Vyberte předplatné, které chcete upravit.
   * **Typ kvóty:** Vyberte **HDInsight**.

Další informace najdete v tématu [Vytvoření lístku podpory kvůli navýšení počtu jader](hdinsight-capacity-planning.md#quotas).

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]