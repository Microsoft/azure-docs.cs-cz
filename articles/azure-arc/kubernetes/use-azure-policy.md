---
title: Použití Azure Policy k aplikování konfigurace clusteru ve velkém měřítku (Preview)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Použití Azure Policy k aplikování konfigurace clusteru ve velkém měřítku
keywords: Kubernetes, oblouk, Azure, K8s, Containers
ms.openlocfilehash: 80ffd70b830483c548f29a029d448bb6b480a8d4
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84676195"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Použití Azure Policy k aplikování konfigurace clusteru ve velkém měřítku (Preview)

## <a name="overview"></a>Přehled

Pomocí Azure Policy vynutili použití `Microsoft.Kubernetes/connectedclusters` konkrétního prostředku nebo prostředku s povoleným Git-OPS `Microsoft.ContainerService/managedClusters` `Microsoft.KubernetesConfiguration/sourceControlConfigurations` . Pokud chcete použít Azure Policy vyberte existující definici zásady a vytvořte přiřazení zásady. Při vytváření přiřazení zásad nastavíte rozsah přiřazení: Toto bude skupina prostředků nebo předplatné Azure. Nastavili jste také parametry `sourceControlConfiguration` , které budou vytvořeny. Po vytvoření přiřazení modul zásad identifikuje všechny `connectedCluster` `managedCluster` prostředky, které se nacházejí v rámci oboru, a použije je `sourceControlConfiguration` pro každé z nich.

Pokud používáte více úložišť Git jako zdroje pravdy pro každý cluster (například jedno úložiště pro centrálního IT/clusterový operátor a jiné úložiště pro aplikační týmy), můžete to povolit pomocí více přiřazení zásad. každé přiřazení zásady je nakonfigurované tak, aby používalo jiné úložiště Git.

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásady

1. V Azure Portal přejděte na zásady a v části **vytváření obsahu** na bočním panelu vyberte **definice**.
2. V kategorii "Kubernetes" vyberte integrovanou zásadu "nasadit GitOps do clusteru Kubernetes" a klikněte na **Vybrat**.
3. Nastavte **obor** na skupinu pro správu, předplatné nebo skupinu prostředků, kde se bude přiřazení zásady vztahovat.
4. Pokud chcete vyloučit jakékoli prostředky z oboru zásad, nastavte **vyloučení**.
5. Udělte přiřazení zásad **název** a **Popis** , který můžete použít k jeho snadnému identifikaci.
6. Zajistěte, aby bylo **vynucování zásad** nastavené na *povoleno*.
7. Vyberte **Další**.
8. Nastavte hodnoty parametrů, které budou použity při vytváření `sourceControlConfiguration` .
9. Vyberte **Další**.
10. Povolte možnost **vytvořit úlohu nápravy**.
11. Zajistěte, aby se ověřilo, že je zaškrtnuté políčko **vytvořit spravovanou identitu** a že identita bude mít oprávnění **přispěvatele** . Další informace o oprávněních, která potřebujete, najdete v [tomto dokumentu](https://docs.microsoft.com/azure/governance/policy/assign-policy-portal) a v [komentáři v tomto dokumentu](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources) .
12. Vyberte **Zkontrolovat a vytvořit**.

Po vytvoření přiřazení zásady se použije pro všechny nové `connectedCluster` prostředky (nebo `managedCluster` prostředky s nainstalovanými agenty GitOps), které se nacházejí v rámci rozsahu přiřazení `sourceControlConfiguration` . U existujících clusterů budete muset ručně spustit úlohu nápravy. Aby se přiřazení zásad projevilo, obvykle trvá 10-20 minut.

## <a name="verify-a-policy-assignment"></a>Ověření přiřazení zásady

1. V Azure Portal přejděte k jednomu z vašich `connectedCluster` prostředků a v části **Nastavení** na bočním panelu vyberte **zásady**. (Prostředí spravovaného uživatelského prostředí pro AKS se zatím neimplementuje, ale připravujeme.)
2. V seznamu byste měli vidět přiřazení zásad, které jste vytvořili výše, a **stav dodržování předpisů** by měl *splňovat předpisy*.
3. V části **Nastavení** na bočním panelu vyberte **Konfigurace**.
4. V seznamu byste měli vidět `sourceControlConfiguration` , že přiřazení zásady bylo vytvořeno.
5. Použití **kubectl** k dotazování clusteru: měli byste vidět obor názvů a artefakty, které vytvořil `sourceControlConfiguration` .
6. Během 5 minut byste měli vidět v clusteru artefakty, které jsou popsány v manifestech v nakonfigurovaném úložišti Git.

## <a name="next-steps"></a>Další kroky

* [Nastavení Azure Monitor pro kontejnery s povolenými clustery Kubernetes s obloukem](./deploy-azure-monitor-for-containers.md)
