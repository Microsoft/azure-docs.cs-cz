---
title: Vytvoření clusterové aplikace Azure Red Hat OpenShift 4 obnovení pomocí Velero
description: Naučte se vytvářet obnovení aplikací clusteru Azure Red Hat OpenShift pomocí Velero
ms.service: container-service
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: ARO, OpenShift, AZ ARO, Red Hat, CLI
ms.custom: mvc
ms.openlocfilehash: 0cd6797bcdfadca807e25f8b3decf34bd553fc56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89470047"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-restore"></a>Vytvoření clusteru Azure Red Hat OpenShift 4 – obnovení clusterové aplikace

V tomto článku připravíte své prostředí, aby se vytvořilo obnovení clusterové aplikace Azure Red Hat OpenShift 4. Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Nastavte požadované součásti a nainstalujte potřebné nástroje.
> * Vytvoření obnovení aplikace Azure Red Hat OpenShift 4

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.6.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Než začnete

### <a name="create-an-azure-red-hat-openshift-4-application-backup"></a>Vytvoření zálohy aplikace Azure Red Hat OpenShift 4

Pokud chcete vytvořit zálohu aplikace Azure Red Hat OpenShift 4, přečtěte si téma [vytvoření zálohy Azure Red Hat OpenShift 4](./howto-create-a-backup.md) .

## <a name="restore-an-azure-red-hat-openshift-4-application"></a>Obnovení aplikace Azure Red Hat OpenShift 4

Tyto kroky vám umožní obnovit aplikaci, která byla dřív zazálohovaná pomocí Velero.
Pokud chcete zjistit, jaké zálohy jsou k dispozici pro obnovení, můžete si prohlédnout seznam záloh, které cluster aktuálně rozpoznal.  K provedení tohoto kroku budete muset spustit tento příkaz:

_(Tento krok předpokládá, že jste nainstalovali Velero do projektu s názvem "Velero")_

```bash
oc get backups -n velero
```

Jakmile budete mít zálohu, kterou chcete obnovit, budete muset provést obnovení pomocí následujícího příkazu:

```bash
velero restore create --from-backup <name of backup from above output list>
```

Tento krok vytvoří objekty Kubernetes, které se zálohovali z předchozího kroku při vytváření zálohy.

Chcete-li zobrazit stav obnovení, spusťte následující krok:

```bash
oc get restore -n velero <name of restore created previously> -o yaml
```
Když se ve fázi říká `Completed` , vaše aplikace Azure Red Hat 4 by se měla obnovit.

## <a name="next-steps"></a>Další kroky

V tomto článku se obnovila Clusterová aplikace Azure Red Hat OpenShift 4. Naučili jste se:

> [!div class="checklist"]
> * Vytvoření obnovení clusterové aplikace OpenShift v4 pomocí Velero


V dalším článku se dozvíte víc o podporovaných prostředcích Azure Red Hat OpenShift 4.

* [Podporované prostředky pro Azure Red Hat OpenShift v4](supported-resources.md)