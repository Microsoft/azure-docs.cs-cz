---
title: Řešení potíží s Azure Red Hat OpenShift
description: Řešení potíží a řešení běžných potíží s Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: ee032cdf4a3f72b2cd2e7da0658effe75b6fb1fa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "76274935"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Řešení potíží pro Azure Red Hat OpenShift

Tento článek podrobně popisuje některé běžné problémy, které se vyskytly při vytváření nebo správě Microsoft Azure clusterů Red Hat OpenShift.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Opakuje se pokus o vytvoření clusteru, který selhal.

Pokud se nedaří vytvořit cluster Azure Red Hat OpenShift pomocí `az` příkazu CLI, zkuste operaci vytvoření znovu pokračovat.
Použijte `az openshift delete` k odstranění neúspěšného clusteru a pak vytvořte zcela nový cluster.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Skrytá skupina prostředků clusteru Azure Red Hat OpenShift

V současné době `Microsoft.ContainerService/openShiftManagedClusters` je prostředek, který je automaticky vytvořený pomocí rozhraní příkazového řádku Azure CLI, `az openshift create` skrytý v Azure Portal. Pokud chcete zobrazit skupinu prostředků, v zobrazení **skupiny prostředků** zkontrolujte zobrazení **skryté typy** .

![Snímek obrazovky se zaškrtávacím políčkem skrytého typu na portálu](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>Při vytváření clusteru dojde k chybě, že se nenašel žádný registrovaný poskytovatel prostředků.

Pokud při vytváření clusteru dojde k chybě, `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview` měli byste být součástí verze Preview a teď je potřeba [koupit rezervované instance virtuálních počítačů Azure](https://aka.ms/openshift/buy) , aby používaly všeobecně dostupný produkt. Rezervace snižuje vaši útratu tím, že se předem platíte za plně spravované služby Azure. Další informace o rezervacích a způsobu, jakým vám ušetří peníze, najdete v tématu [*co je Azure reservations*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) .

## <a name="next-steps"></a>Další kroky

- Další informace o řešení potíží s OpenShift najdete v [centru pro nápovědu Red Hat OpenShift](https://help.openshift.com/) .

- Přečtěte si odpovědi na [Nejčastější dotazy týkající se Azure Red Hat OpenShift](openshift-faq.md).
