---
title: Použití Azure Policy k aplikování konfigurace clusteru ve velkém měřítku
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Použití Azure Policy k aplikování konfigurace clusteru ve velkém měřítku
keywords: Kubernetes, oblouk, Azure, K8s, Containers
ms.openlocfilehash: 05a6665a985ef8b229ee58082dc9b2c10cdcece3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121452"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale"></a>Použití Azure Policy k aplikování konfigurace clusteru ve velkém měřítku

Azure Policy můžete použít k aplikování konfigurací ( `Microsoft.KubernetesConfiguration/sourceControlConfigurations` typu prostředku) na škále u clusterů Azure s podporou Kubernetes ( `Microsoft.Kubernetes/connectedclusters` ).

Pokud chcete použít Azure Policy, vyberte existující definici zásady a vytvořte přiřazení zásady. Při vytváření přiřazení zásady:
1. Nastavte rozsah přiřazení.
    * Oborem bude skupina prostředků nebo předplatné Azure. 
2. Nastavte parametry pro konfiguraci, která se vytvoří. 

Po vytvoření přiřazení Azure Policy modul identifikuje všechny clustery s podporou Azure ARC v rámci oboru a použije konfiguraci na jednotlivé clustery.

Pomocí několika přiřazení zásad můžete vytvořit více konfigurací, z nichž každá odkazuje na jiné úložiště Git. Například jedno úložiště pro ústředního operátora IT/cluster a další úložiště pro týmy aplikace.

## <a name="prerequisite"></a>Požadavek

Ověřte, že máte `Microsoft.Authorization/policyAssignments/write` oprávnění pro obor (předplatné nebo skupinu prostředků), kde vytvoříte toto přiřazení zásady.

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásad

1. V Azure Portal přejděte na **zásady**.
1. V části **vytváření obsahu** na bočním panelu vyberte **definice**.
1. V kategorii "Kubernetes" vyberte vestavěnou zásadu "nasadit GitOps do clusteru Kubernetes". 
1. Klikněte na **přiřadit**.
1. Nastavte **obor** na skupinu pro správu, předplatné nebo skupinu prostředků, na kterou se bude přiřazení zásady vztahovat.
    * Pokud chcete vyloučit jakékoli prostředky z oboru zásad, nastavte **vyloučení**.
1. Udělte přiřazení zásad snadno identifikovatelné **jméno** a **Popis**.
1. Zajistěte, aby bylo **vynucování zásad** nastavené na **povoleno**.
1. Vyberte **Další**.
1. Nastavte hodnoty parametrů, které se mají použít při vytváření `sourceControlConfiguration` .
1. Vyberte **Další**.
1. Povolte možnost **vytvořit úlohu nápravy**.
1. Ověřte, že je zaškrtnuté políčko **vytvořit spravovanou identitu** a že identita bude mít oprávnění **Přispěvatel** . 
    * Další informace najdete v tématu [rychlý Start k vytvoření přiřazení zásady](../../governance/policy/assign-policy-portal.md) a [napravení nevyhovujících prostředků pomocí Azure Policy článku](../../governance/policy/how-to/remediate-resources.md).
1. Vyberte **Zkontrolovat a vytvořit**.

Po vytvoření přiřazení zásady se tato konfigurace použije na nové clustery Kubernetes s podporou ARC Azure vytvořené v rámci přiřazení zásad.

U existujících clusterů budete muset ručně spustit úlohu nápravy. Tato úloha obvykle trvá 10 až 20 minut, než se přiřazení zásady projeví.

## <a name="verify-a-policy-assignment"></a>Ověření přiřazení zásady

1. V Azure Portal přejděte k jednomu z clusterů Kubernetes s povoleným ARC Azure.
1. V části **Nastavení** na bočním panelu vyberte **zásady**. 
    * V seznamu zásady byste měli vidět přiřazení zásad, které jste vytvořili dříve, se **stavem dodržování předpisů** nastaveným jako *kompatibilní*.
1. V části **Nastavení** na bočním panelu vyberte **Konfigurace**.
    * V seznamu konfigurace by se měla zobrazit konfigurace vytvořená přiřazením zásad.
1. Použijte `kubectl` k dotazování clusteru. 
    * Měl by se zobrazit obor názvů a artefakty, které byly vytvořeny pomocí prostředků konfigurace.
    * Během 5 minut (za předpokladu, že cluster má síťové připojení k Azure), byste měli vidět objekty popsané v manifestech v úložišti Git, které se vytvářejí v clusteru.

## <a name="next-steps"></a>Další kroky

[Nastavte Azure monitor pro kontejnery s povolenými clustery Kubernetes Azure ARC](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md).
