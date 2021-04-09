---
title: Úvod do verzí – Azure HDInsight
description: Přečtěte si, jak funguje Správa verzí ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: 6db4c7856ebdf75d5bf94de1e3110bb25bc93e69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103493862"
---
# <a name="how-versioning-works-in-hdinsight"></a>Jak funguje Správa verzí v HDInsight

Služba HDInsight má dvě hlavní součásti: poskytovatele prostředků a Apache Hadoop komponenty, které jsou nasazeny v clusteru. 

## <a name="hdinsight-resource-provider"></a>Poskytovatel prostředků HDInsight

Prostředky v Azure zpřístupňuje poskytovatel prostředků. Poskytovatel prostředků HDInsight zodpovídá za vytváření, správu a odstraňování clusterů.

## <a name="hdinsight-images"></a>Image HDInsight

HDInsight používá Image k umístění komponent open-source softwaru (OSS), které se dají nasadit na cluster. Tyto image obsahují základní operační systém Ubuntu a základní komponenty, jako jsou Spark, Hadoop, Kafka, HBA nebo podregistr.

## <a name="versioning-in-hdinsight"></a>Správa verzí v HDInsight

Společnost Microsoft pravidelně upgraduje image a poskytovatele prostředků služby HDInsight, aby zahrnovalo nová vylepšení a funkce.

Novou verzi HDInsight je možné vytvořit, pokud platí některá z následujících podmínek:

- Významné změny nebo aktualizace funkcí poskytovatele prostředků HDInsight
- Hlavní verze součástí OSS
- Významné změny v operačním systému Ubuntu

Nová verze image se vytvoří, když je splněná některá z následujících podmínek:

- Hlavní verze nebo podverze a aktualizace komponent OSS
- Opravy nebo opravy pro komponentu v imagi

## <a name="next-steps"></a>Další kroky

- [Součásti a verze Apache v HDInsight](./hdinsight-component-versioning.md)
