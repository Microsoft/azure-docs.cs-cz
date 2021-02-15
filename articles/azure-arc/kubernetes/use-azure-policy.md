---
title: Použití Azure Policy k použití konfigurací clusteru ve velkém měřítku (Preview)
services: azure-arc
ms.service: azure-arc
ms.date: 02/10/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Použití Azure Policy k aplikování konfigurace clusteru ve velkém měřítku
keywords: Kubernetes, oblouk, Azure, K8s, Containers
ms.openlocfilehash: ce9ba75e200a02654cac4c50303cc90fd0c1a5fd
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390908"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Použití Azure Policy k použití konfigurací clusteru ve velkém měřítku (Preview)

## <a name="overview"></a>Přehled

Můžete použít Azure Policy k vymáhání konkrétního `Microsoft.KubernetesConfiguration/sourceControlConfigurations` uplatnění následujících prostředků:
*  `Microsoft.Kubernetes/connectedclusters` partner.
* Prostředek s podporou GitOps `Microsoft.ContainerService/managedClusters` 

Pokud chcete použít Azure Policy, vyberte existující definici zásady a vytvořte přiřazení zásady. Při vytváření přiřazení zásady:
1. Nastavte rozsah přiřazení.
    * Oborem bude skupina prostředků nebo předplatné Azure. 
2. Nastavte parametry pro `sourceControlConfiguration` , které budou vytvořeny. 

Jakmile je přiřazení vytvořeno, modul Azure Policy identifikuje všechny `connectedCluster` `managedCluster` prostředky v rámci oboru a použije je `sourceControlConfiguration` pro každé z nich.

Můžete povolit více úložišť Git jako zdroje pravdy pro každý cluster pomocí několika přiřazení zásad. Každé přiřazení zásad se nakonfiguruje tak, aby používalo jiné úložiště Git. například jedno úložiště pro ústředního operátora IT/cluster a další úložiště pro aplikační týmy.

## <a name="prerequisite"></a>Požadavek

Ověřte, že máte `Microsoft.Authorization/policyAssignments/write` oprávnění pro obor (předplatné nebo skupinu prostředků), kde budete toto přiřazení zásad vytvářet.

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

Po vytvoření přiřazení zásady se použije `sourceControlConfiguration` pro některý z následujících prostředků, které se nacházejí v rámci rozsahu přiřazení:
* Nové `connectedCluster` prostředky.
* Nové `managedCluster` prostředky s nainstalovanými agenty GitOps. 

U existujících clusterů budete muset ručně spustit úlohu nápravy. Tato úloha obvykle trvá 10 až 20 minut, než se přiřazení zásady projeví.

## <a name="verify-a-policy-assignment"></a>Ověření přiřazení zásady

1. V Azure Portal přejděte k jednomu z vašich `connectedCluster` prostředků.
1. V části **Nastavení** na bočním panelu vyberte **zásady**. 
    * Prostředí AKS clusteru není ještě implementováno.
    * V seznamu zásady byste měli vidět přiřazení zásad, které jste vytvořili dříve, se **stavem dodržování předpisů** nastaveným jako *kompatibilní*.
1. V části **Nastavení** na bočním panelu vyberte **Konfigurace**.
    * V seznamu konfigurace by se mělo zobrazit `sourceControlConfiguration` , že přiřazení zásady bylo vytvořeno.
1. Použijte `kubectl` k dotazování clusteru. 
    * Měl by se zobrazit obor názvů a artefakty, které byly vytvořeny pomocí `sourceControlConfiguration` .
    * Během 5 minut byste měli vidět v clusteru artefakty, které jsou popsány v manifestech v nakonfigurovaném úložišti Git.

## <a name="next-steps"></a>Další kroky

* [Nastavení Azure Monitor pro kontejnery s povolenými clustery Kubernetes s obloukem](../../azure-monitor/insights/container-insights-enable-arc-enabled-clusters.md)
