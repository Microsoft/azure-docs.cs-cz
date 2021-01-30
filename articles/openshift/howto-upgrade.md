---
title: Upgrade clusteru Azure Red Hat OpenShift
description: Přečtěte si, jak upgradovat cluster Azure Red Hat OpenShift se systémem OpenShift 4
ms.service: container-service
ms.topic: article
ms.date: 1/10/2021
author: sakthi-vetrivel
ms.author: suvetriv
keywords: ARO, OpenShift, AZ ARO, Red Hat, CLI
ms.openlocfilehash: 98ab8ff1e321929a9007c28f487d5861f6ac9930
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063831"
---
# <a name="upgrade-an-azure-red-hat-openshift-aro-cluster"></a>Upgrade clusteru Azure Red Hat OpenShift (ARO)

Součástí životního cyklu clusteru ARO je provádění pravidelných upgradů na nejnovější verzi OpenShift. Je důležité použít nejnovější verze zabezpečení nebo upgradovat, abyste získali nejnovější funkce. V tomto článku se dozvíte, jak upgradovat všechny součásti v clusteru OpenShift pomocí webové konzole OpenShift.

## <a name="before-you-begin"></a>Než začnete

Tento článek vyžaduje, abyste spustili Azure CLI verze 2.0.65, který se nachází později. Aktuální verzi zjistíte spuštěním `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) .

V tomto článku se předpokládá, že máte přístup k existujícímu clusteru Azure Red Hat OpenShift jako uživatel s `admin` oprávněními.

## <a name="check-for-available-aro-cluster-upgrades"></a>Vyhledat dostupné upgrady clusteru ARO společnosti

Z webové konzoly OpenShift vyberte možnost **Správa**  >  **Nastavení clusteru** a otevřete kartu **Podrobnosti** .

Pokud **stav aktualizace** pro váš cluster odráží **aktualizace k dispozici**, můžete cluster aktualizovat.

## <a name="upgrade-your-aro-cluster"></a>Upgrade clusteru ARO

Z webové konzoly v předchozím kroku nastavte **kanál** na správný kanál pro verzi, na kterou chcete aktualizovat, například `stable-4.5` .

Vyberte verzi, na kterou se má aktualizace aktualizovat, a pak vyberte **aktualizovat**. Uvidíte změnu stavu aktualizace na: `Update to <product-version> in progress` . Průběh aktualizace clusteru můžete zkontrolovat tak, že přejdete na indikátory průběhu u operátorů a uzlů.

## <a name="next-steps"></a>Další kroky
- [Naučte se upgradovat cluster ARO pomocí rozhraní příkazového řádku (OC).](https://docs.openshift.com/container-platform/4.6/updating/updating-cluster-between-minor.html)
- Informace o dostupných OpenShift a aktualizacích pro kontejnery a aktualizace pro kontejnerové platformy najdete v [části Seznam chyb](https://access.redhat.com/downloads/content/290/ver=4.6/rhel---8/4.6.0/x86_64/product-errata) na portálu Customer.
  
