---
title: Průvodce publikováním pro nabídky kontejnerů v Azure Marketplace
description: Tento článek popisuje požadavky na publikování nabídek kontejneru v Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 11/30/2020
ms.openlocfilehash: 83c575aa40b80d9a8e39263e89a5e7860c8f8774
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95741657"
---
# <a name="publishing-guide-for-azure-container-offers"></a>Průvodce publikováním pro nabídky Azure Container

Nabídky Azure Container vám pomůžou publikovat image kontejneru do Azure Marketplace. Tento průvodce použijte k pochopení požadavků pro tento typ nabídky.

Nabídky Azure Container jsou nabídky transakcí, které se nasazují a účtují prostřednictvím Azure Marketplace. Možnost výpisu, kterou uživatel vidí, je "získat hned".

Pokud je vaše řešení image kontejneru Docker, která je nastavená jako instance kontejneru Azure na základě Kubernetes, použijte typ nabídky kontejneru Azure.

> [!NOTE]
> Instance kontejneru Azure je instance Docker, která poskytuje nejrychlejší a nejjednodušší způsob, jak spustit kontejner v Azure, aniž by bylo nutné spravovat žádné virtuální počítače a nemusíte přitom muset přijmout službu vyšší úrovně. Instance kontejnerů se dají nasadit přímo do Azure nebo orchestrovat služba Azure Kubernetes Services nebo Azure Kubernetes Service Engine.  

Microsoft v současné době podporuje bezplatných licenčních modelů (BYOL).

## <a name="container-offer-requirements"></a>Požadavky na nabídky kontejneru

| Požadavek | Podrobnosti |  
|:--- |:--- |  
| Fakturace a měření | Podpora modelu fakturace Free nebo BYOL.<br><br> |  
| Obrázek sestavený z souboru Dockerfile | Image kontejneru musí být založené na specifikaci image Docker a sestavené z souboru Dockerfile.<br> <br>Další informace o vytváření imagí Docker najdete v části "použití" tématu [souboru Dockerfile reference](https://docs.docker.com/engine/reference/builder/#usage).<br><br> |  
| Hostování v úložišti Azure Container Registry | Image kontejneru musí být hostované v úložišti Azure Container Registry.<br> <br>Další informace o práci s Azure Container Registry najdete v tématu [rychlý Start: Vytvoření privátního registru kontejnerů pomocí Azure Portal](../container-registry/container-registry-get-started-portal.md).<br><br> |  
| Označování obrázků | Image kontejneru musí obsahovat aspoň jednu značku (maximální počet značek: 16).<br><br>Další informace o označování obrázku najdete na `docker tag` stránce s [dokumentací k dokovacímu](https://docs.docker.com/engine/reference/commandline/tag) serveru.<br><br> |  

## <a name="next-steps"></a>Další kroky

- Informace o přípravě technických prostředků pro nabídku kontejneru najdete v tématu [vytvoření prostředků Azure Container Technical assets](create-azure-container-technical-assets.md).

- Pokud chcete vytvořit nabídku kontejneru Azure, přečtěte si téma [Vytvoření nabídky kontejnerů Azure v tématu Azure Marketplace](create-azure-container-offer.md) , kde najdete další informace.
