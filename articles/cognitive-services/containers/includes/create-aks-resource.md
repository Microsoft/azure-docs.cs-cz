---
title: Podpora kontejnerů
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak vytvořit prostředek (AKS) azure kubernetes.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 1d68c08f6dfca74c38973af1686d614f3f10cc28
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455126"
---
## <a name="create-an-azure-kubernetes-service-aks-cluster-resource"></a>Vytvoření prostředku clusteru Azure Kubernetes Service (AKS)

1. Přejděte [vytvořit](https://ms.portal.azure.com/#create/microsoft.aks) služby Kubernetes.

1. Na **Základy** kartu, zadejte následující údaje:

    |Nastavení|Hodnota|
    |--|--|
    |Předplatné|Vyberte odpovídající předplatné|
    |Skupina prostředků|Vyberte skupinu prostředků k dispozici|
    |Název clusteru Kubernetes|Požadovaný název (malá písmena)|
    |Oblast|Vyberte nejbližší umístění|
    |Verze Kubernetes|1.12.8 (výchozí)|
    |Předpona názvu DNS|Vytvoří automaticky, ale můžete volitelně přepsat|
    |Velikost uzlu|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Počet uzlů|Ponechte výchozí hodnota posuvníku|

1. Na **škálování** kartu, ponechte *virtuální uzly* a *škálovací sady virtuálních počítačů* výchozí hodnoty.
1. Na **ověřování** kartu, ponechte *instanční objekt služby* a *povolit RBAC* výchozí hodnoty.
1. Na **sítě** kartu, zadejte následující možnosti:

    |Nastavení|Hodnota|
    |--|--|
    |Směrování aplikace HTTP|Ne|
    |Konfigurace sítě|Basic|

1. Na **monitorování** kartu, ujistěte se, že *povolit monitorování kontejnerů* je nastavena na **Ano** a nechat *pracovní prostor Log Analytics* jako jeho Výchozí hodnota
1. Na **značky** kartu, zatím nechejte nastavené dvojice název/hodnota prázdná.
1. Klikněte na tlačítko **zkontrolovat a vytvořit**.
1. Jakmile úspěšně proběhne ověření, klikněte na tlačítko **vytvořit**.

> [!NOTE]
> Pokud ověření selže, může být vypršení platnosti *instanční objekt služby* chyby. Přejděte zpět *ověřování* kartu a pak zpátky do *revize + vytvořit* kde ověření by měl spustit a pak předejte.
