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
ms.openlocfilehash: 64be872e4e76545c015378417410cbbfad95ebfd
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680657"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Použití Azure Policy k aplikování konfigurace clusteru ve velkém měřítku (Preview)

## <a name="overview"></a>Přehled

Použijte Azure Policy k vykonání každého `Microsoft.Kubernetes/connectedclusters` konkrétního prostředku s povoleným Git-OPS `Microsoft.ContainerService/managedClusters` `Microsoft.KubernetesConfiguration/sourceControlConfigurations` .  Pokud chcete použít Azure Policy vyberte existující definici zásady a vytvořte přiřazení zásady.  Při vytváření přiřazení zásad nastavíte rozsah přiřazení: Toto bude skupina prostředků nebo předplatné Azure.  Nastavili jste také parametry `sourceControlConfiguration` , které budou vytvořeny.  Po vytvoření přiřazení modul zásad identifikuje všechny `connectedCluster` `managedCluster` prostředky, které se nacházejí v rámci oboru, a použije je `sourceControlConfiguration` pro každé z nich.

Pokud používáte více úložišť Git jako zdroje pravdy pro každý cluster (například jedno úložiště pro centrálního IT/clusterový operátor a jiné úložiště pro aplikační týmy), můžete to povolit pomocí více přiřazení zásad. každé přiřazení zásady je nakonfigurované tak, aby používalo jiné úložiště Git.

## <a name="create-a-custom-policy-definition"></a>Vytvoření vlastní definice zásad

1. V Azure Portal přejděte na zásady a v části **vytváření obsahu** na bočním panelu vyberte **definice**.
2. Vyberte **+ definice zásad**.
3. Nastavte **umístění definice** na vaše předplatné nebo skupinu pro správu.  Tím se určí nejširší obor, ve kterém se dá použít definice zásady.
4. Zadejte **název** a **Popis**zásady.
5. V části Kategorie vyberte možnost **vytvořit nový**a zapsat *cluster Kubernetes – Azure ARC* .
6. V poli pro úpravy **pravidla zásad** zkopírujte nebo vložte obsah této [ukázkové definice zásady](https://raw.githubusercontent.com/Azure/arc-k8s-demo/master/policy/Ensure-GitOps-configuration-for-Kubernetes-cluster.json).
7. **Uložte**.

Tento krok pro vytvoření vlastní definice zásad nebude potřeba po dokončení práce, aby se tato předdefinovaná zásada dala provést.

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásady

1. V Azure Portal přejděte na zásady a v části **vytváření obsahu** na bočním panelu vyberte **definice**.
2. Vyhledejte definici, kterou jste právě vytvořili, a vyberte ji.
3. V akci stránky vyberte **přiřadit**.
4. Nastavte **obor** na skupinu pro správu, předplatné nebo skupinu prostředků, kde se bude přiřazení zásady vztahovat.
5. Pokud chcete vyloučit jakékoli prostředky z oboru zásad, nastavte **vyloučení**.
6. Udělte přiřazení zásad **název** a **Popis** , který můžete použít k jeho snadnému identifikaci.
7. Zajistěte, aby bylo **vynucování zásad** nastavené na *povoleno*.
8. Vyberte **Další**.
9. Nastavte hodnoty parametrů, které budou použity při vytváření `sourceControlConfiguration` .
10. Vyberte **Další**.
11. Povolte možnost **vytvořit úlohu nápravy**.
12. Zajistěte, aby se ověřilo, že je zaškrtnuté políčko **vytvořit spravovanou identitu** a že identita bude mít oprávnění **přispěvatele** .  Další informace o oprávněních, která potřebujete, najdete v [tomto dokumentu](https://docs.microsoft.com/azure/governance/policy/assign-policy-portal) a v [komentáři v tomto dokumentu](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources) .
13. Vyberte **Zkontrolovat a vytvořit**.

Po vytvoření přiřazení zásady se použije pro všechny nové `connectedCluster` prostředky (nebo `managedCluster` prostředky s nainstalovanými agenty GitOps), které se nacházejí v rámci rozsahu přiřazení `sourceControlConfiguration` .  U existujících clusterů budete muset ručně spustit úlohu nápravy.  Aby se přiřazení zásad projevilo, obvykle trvá 10-20 minut.

## <a name="verify-a-policy-assignment"></a>Ověření přiřazení zásady

1. V Azure Portal přejděte k jednomu z vašich `connectedCluster` prostředků a v části **Nastavení** na bočním panelu vyberte **zásady**. (Prostředí spravovaného uživatelského prostředí pro AKS se zatím neimplementuje, ale připravujeme.)
2. V seznamu byste měli vidět přiřazení zásad, které jste vytvořili výše, a **stav dodržování předpisů** by měl *splňovat předpisy*.
3. V části **Nastavení** na bočním panelu vyberte **Konfigurace**.
4. V seznamu byste měli vidět `sourceControlConfiguration` , že přiřazení zásady bylo vytvořeno.
5. Použití **kubectl** k dotazování clusteru: měli byste vidět obor názvů a artefakty, které vytvořil `sourceControlConfiguration` .
6. Během 5 minut byste měli vidět v clusteru artefakty, které jsou popsány v manifestech v nakonfigurovaném úložišti Git.

## <a name="next-steps"></a>Další kroky

* [Řízení konfigurace clusteru pomocí Azure Policy](./use-azure-policy.md)
