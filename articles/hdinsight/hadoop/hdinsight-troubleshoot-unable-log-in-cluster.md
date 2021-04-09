---
title: Nepovedlo se přihlásit ke clusteru Azure HDInsight.
description: Řešení potíží s tím, proč se nepovedlo přihlásit Apache Hadoop clusteru v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: 03657606f7e24436ff5a851fe7eef1e0c4d5dc24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944312"
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