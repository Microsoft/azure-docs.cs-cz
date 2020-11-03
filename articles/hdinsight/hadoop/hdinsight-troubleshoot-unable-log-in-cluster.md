---
title: Nepovedlo se přihlásit ke clusteru Azure HDInsight.
description: Řešení potíží s tím, proč se nepovedlo přihlásit Apache Hadoop clusteru v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 222c48ab2b9b718bc373e710f37f906e68bd31d4
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289022"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>Scénář: nejde se přihlásit ke clusteru Azure HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Nepovedlo se přihlásit ke clusteru Azure HDInsight.

## <a name="cause"></a>Příčina

Důvody se mohou lišit. Mějte na paměti, že při přihlašování ke clusteru nebo řídicím panelům aplikací použijte přihlašovací údaje clusteru nebo přihlašovací údaje HTTP. Ke vzdálenému připojení použijte přihlašovací údaje Vzdálené plochy nebo Secure Shell (SSH).

## <a name="resolution"></a>Řešení

Při řešení běžných problémů zkuste použít jeden nebo několik následujících kroků.

* Zkuste otevřít řídicí panel clusteru na nové kartě prohlížeče v režimu ochrany osobních údajů.

* Pokud nemůžete odvolat přihlašovací údaje SSH, můžete [přihlašovací údaje resetovat v uživatelském rozhraní Ambari](../hdinsight-administer-use-portal-linux.md#change-passwords).

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]