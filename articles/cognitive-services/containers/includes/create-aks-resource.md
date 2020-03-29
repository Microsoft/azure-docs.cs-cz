---
title: Vytvoření prostředku clusteru služby Azure Kubernetes
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak vytvořit prostředek služby Azure Kubernetes Service (AKS).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 4e3102912e88ef904fed3e680f8cdd23242b1f17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383421"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Vytvoření prostředku clusteru služby Azure Kubernetes

1. Přejděte na [Azure Kubernetes Service](https://ms.portal.azure.com/#create/microsoft.aks)a vyberte **Vytvořit**.

1. Na kartě **Základy** zadejte následující informace:

    |Nastavení|Hodnota|
    |--|--|
    |Předplatné|Vyberte odpovídající předplatné.|
    |Skupina prostředků|Vyberte dostupnou skupinu prostředků.|
    |Název clusteru Kubernetes|Zadejte název (malá písmena).|
    |Region (Oblast)|Vyberte blízké místo.|
    |Kubernetes verze|Jakákoli hodnota je označena jako **(výchozí)**.|
    |Předpona názvu DNS|Vytvořeno automaticky, ale můžete přepsat.|
    |Velikost uzlu|Standardní DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Počet uzlů|Ponechejte jezdec na výchozí hodnotě.|

1. Na kartě **Scale** ponechejte **virtuální uzly** a **škálovací sady virtuálních počítačových zařízení** nastavené na výchozí hodnoty.
1. Na kartě **Ověřování** ponechejte **instanční objekt** a **povolit RBAC** nastavené na jejich výchozí hodnoty.
1. Na kartě **Síť** zadejte následující výběry:

    |Nastavení|Hodnota|
    |--|--|
    |Směrování aplikace HTTP|Ne|
    |Konfigurace sítě|Basic|

1. Na kartě **Monitorování** zkontrolujte, zda je možnost **Povolit monitorování kontejnerů** nastavena na **hodnotu Ano**, a ponechte **pracovní prostor Log Analytics** jako výchozí hodnotu.
1. Na kartě **Tagy** ponechejte dvojice názvů a hodnot prozatím prázdné.
1. Vyberte **Zkontrolovat a vytvořit**.
1. Po průchodu ověření vyberte **Vytvořit**.

> [!NOTE]
> Pokud se ověření nezdaří, může to být z důvodu chyby "Instanční objekt". Vraťte se na kartu **Ověřování** a potom se **vraťte**na možnost Revize + vytvořit , kde by mělo být ověření spuštěno a poté předat.
