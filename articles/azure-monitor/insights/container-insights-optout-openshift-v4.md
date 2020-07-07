---
title: Postup zastavení monitorování clusteru Azure a Red Hat OpenShift v4 | Microsoft Docs
description: Tento článek popisuje, jak můžete přestat sledovat cluster Azure Red Hat OpenShift a Red Hat OpenShift verze 4 s Azure Monitor for Containers.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 768c4db8d72778b555a4f343cf2e23b8fa861991
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "82196436"
---
# <a name="how-to-stop-monitoring-your-azure-and-red-hat-openshift-v4-cluster"></a>Postup zastavení monitorování clusteru Azure a Red Hat OpenShift v4

Po povolení monitorování clusteru Azure Red Hat OpenShift a Red Hat OpenShift verze 4. x můžete zastavit monitorování clusteru pomocí Azure Monitor pro kontejnery, pokud se rozhodnete, že už ho nechcete monitorovat. Tento článek ukazuje, jak to provést.  

## <a name="how-to-stop-monitoring-using-helm"></a>Postup zastavení monitorování pomocí Helm

1. Abyste nejdřív identifikovali Azure Monitor pro vydanou verzi grafu Helm pro kontejnery nainstalované na clusteru, spusťte následující příkaz Helm.

    ```
    helm list
    ```

    Výstup bude vypadat přibližně takto:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-Containers-Release-1* představuje verzi grafu helm pro Azure monitor kontejnerů.

2. Chcete-li odstranit vydanou verzi grafu, spusťte následující příkaz Helm.

    `helm delete <releaseName>`

    Příklad:

    `helm delete azmon-containers-release-1`

    Tato akce odebere verzi z clusteru. Ověřit můžete spuštěním `helm list` příkazu:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

Dokončení změny konfigurace může trvat několik minut. Vzhledem k tomu, že Helm sleduje vaše verze, i když je odstraníte, můžete auditovat historii clusteru a dokonce zrušit odstranění verze pomocí `helm rollback` .

## <a name="next-steps"></a>Další kroky

Pokud se pracovní prostor Log Analytics vytvořil jenom pro podporu monitorování clusteru a už ho nepotřebujete, musíte ho ručně odstranit. Pokud nejste obeznámeni s tím, jak pracovní prostor odstranit, přečtěte si téma [odstranění pracovního prostoru Azure Log Analytics](../../log-analytics/log-analytics-manage-del-workspace.md).
