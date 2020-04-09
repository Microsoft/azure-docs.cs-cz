---
title: Vytvoření prostředku clusteru služby Azure Kubernetes
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak vytvořit prostředek služby Azure Kubernetes Service (AKS).
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: d01245a76c5210a1843307b543f98d4f1d59d2cc
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877806"
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
    |Konfigurace sítě|Základní|

1. Na kartě **Monitorování** zkontrolujte, zda je možnost **Povolit monitorování kontejnerů** nastavena na **hodnotu Ano**, a ponechte **pracovní prostor Log Analytics** jako výchozí hodnotu.
1. Na kartě **Tagy** ponechejte dvojice názvů a hodnot prozatím prázdné.
1. Vyberte **Zkontrolovat a vytvořit**.
1. Po průchodu ověření vyberte **Vytvořit**.

> [!NOTE]
> Pokud se ověření nezdaří, může to být z důvodu chyby "Instanční objekt". Vraťte se na kartu **Ověřování** a potom se **vraťte**na možnost Revize + vytvořit , kde by mělo být ověření spuštěno a poté předat.
