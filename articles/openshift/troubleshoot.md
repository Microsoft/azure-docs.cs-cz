---
title: Poradce při potížích s Azure Red Hat OpenShift
description: Řešení a řešení běžných problémů s Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: ee032cdf4a3f72b2cd2e7da0658effe75b6fb1fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76274935"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Řešení potíží s Azure Red Hat OpenShift

Tento článek podrobně popisuje některé běžné problémy při vytváření nebo správě clusterů Microsoft Azure Red Hat OpenShift.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Opakované pokusy o vytvoření neúspěšného clusteru

Pokud se nezdaří vytvoření clusteru `az` Azure Red Hat OpenShift pomocí příkazu CLI, opakování vytvoření bude nadále nezdaří.
Slouží `az openshift delete` k odstranění neúspěšného clusteru a vytvoření zcela nového clusteru.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Skrytá skupina prostředků clusteru Azure Red Hat OpenShift

V současné `Microsoft.ContainerService/openShiftManagedClusters` době prostředek, který se automaticky vytvoří`az openshift create` pomocí příkazu Azure CLI (příkaz) je skrytý na webu Azure Portal. V zobrazení **Skupina zdrojů** zaškrtněte **políčko Zobrazit skryté typy** a zobrazte skupinu prostředků.

![Snímek obrazovky se zaškrtávacím políčkem Skrytý typ na portálu](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>Vytvoření clusteru má za následek chybu, že nebyl nalezen žádný registrovaný zprostředkovatel prostředků.

Pokud vytvoření clusteru vede `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`k chybě, která , pak jste byli součástí náhledu a teď potřebujete [zakoupit instance vyhrazena virtuální počítač Azure](https://aka.ms/openshift/buy) používat obecně dostupný produkt. Rezervace snižuje vaše výdaje tím, že předplatí te plně spravované služby Azure. Další informace o rezervacích a o tom, jak vám ušetří peníze, najdete v části [*Co jsou rezervace Azure.*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)

## <a name="next-steps"></a>Další kroky

- Další informace o řešení potíží s OpenShiftváme najdete v Centru nápovědy Red [Hat OpenShift.](https://help.openshift.com/)

- Najděte odpovědi na [nejčastější dotazy týkající se Azure Red Hat OpenShift](openshift-faq.md).
