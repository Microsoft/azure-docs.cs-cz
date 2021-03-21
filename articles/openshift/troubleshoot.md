---
title: Řešení potíží s Azure Red Hat OpenShift
description: Řešení potíží a řešení běžných potíží s Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: af66abff3507279dd1954fd83627900578229866
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100632947"
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

Pokud při vytváření clusteru dojde k chybě, `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview` měli byste být součástí verze Preview a teď je potřeba [koupit rezervované instance virtuálních počítačů Azure](https://aka.ms/openshift/buy) , aby používaly všeobecně dostupný produkt. Rezervace snižuje vaši útratu tím, že se předem platíte za plně spravované služby Azure. Další informace o rezervacích a způsobu, jakým vám ušetří peníze, najdete v tématu [*co je Azure reservations*](../cost-management-billing/reservations/save-compute-costs-reservations.md) .

## <a name="next-steps"></a>Další kroky

- Další informace o řešení potíží s OpenShift najdete v [centru pro nápovědu Red Hat OpenShift](https://help.openshift.com/) .

- Přečtěte si odpovědi na [Nejčastější dotazy týkající se Azure Red Hat OpenShift](openshift-faq.md).