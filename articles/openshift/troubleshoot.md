---
title: Řešení potíží s Azure Red Hat OpenShift | Dokumentace Microsoftu
description: Řešení potíží a řešit obvyklé problémy s Azure Red Hat OpenShift
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: 7f2bdf643f12671bec3d0c087d8775844099fe9a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66306250"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Řešení potíží pro Azure Red Hat OpenShift

Tento článek podrobně popisuje některé běžné problémy při vytváření a správě clusterů Microsoft Azure Red Hat OpenShift došlo.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Opakování pokusu o vytvoření clusteru se nezdařilo

Pokud Azure Red Hat OpenShift vytváření clusteru s použitím `az` rozhraní příkazového řádku příkaz selže, to zkusíte znovu vytvořit budou i nadále nezdaří.
Použití `az openshift delete` odstranění selhání clusteru, pak vytvořte zcela nový cluster.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Skryté skupinu prostředků clusteru Azure Red Hat OpenShift

V současné době `Microsoft.ContainerService/openShiftManagedClusters` prostředek, který se automaticky vytvoří pomocí rozhraní příkazového řádku Azure (`az openshift create` příkaz) je skrytý na webu Azure Portal. V **skupiny prostředků** zobrazení, zkontrolujte **zobrazit skryté typy** zobrazíte skupinu prostředků.

![Snímek obrazovky skryté typu zaškrtávací políčko na portálu](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>Vytvoření clusteru výsledky v chybě, která se nenašel žádný zaregistrovaný poskytovatel prostředků

Při vytváření clusteru výsledky v chybě `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`, pak byly součástí verze preview a teď potřebujete [nákupní virtuálních počítačů Azure rezervované instance](https://aka.ms/openshift/buy) používat produkt všeobecně dostupná. Rezervace snižuje vaše výdaje a prostřednictvím platíte předem za plně spravovaných služeb Azure. Odkazovat na [ *co jsou Azure rezervace* ](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) Další informace o rezervacích a jak se ušetřit peníze.

## <a name="next-steps"></a>Další postup

- Zkuste [centrum pro nápovědu Red Hat OpenShift](https://help.openshift.com/) další on OpenShift při řešení potíží.

- Najděte odpovědi na [nejčastější dotazy ohledně Azure Red Hat OpenShift](openshift-faq.md).
