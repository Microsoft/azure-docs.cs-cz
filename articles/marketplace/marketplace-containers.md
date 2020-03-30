---
title: Průvodce publikováním nabídek kontejnerů pro Azure Marketplace
description: Tento článek popisuje požadavky na publikování kontejnerů na webu Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: dsindona
ms.openlocfilehash: 882d428d7a8a9b56408aaa68964fa1e36c7cc7c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277302"
---
# <a name="containers-offer-publishing-guide"></a>Průvodce publikováním nabídek kontejnerů

Nabídky kontejnerů vám pomůžou publikovat image kontejneru na Azure Marketplace. V této příručce porozumíte požadavkům na tuto nabídku. 

Jedná se o nabídky transakcí, které jsou nasazeny a účtovány prostřednictvím webu Marketplace. Výzva k akci, kterou uživatel vidí, je "Get It Now".

Typ nabídky Container použijte, když vaše řešení je image kontejneru Dockeru zřízená jako služba kontejneru Azure založená na Kubernetes.

>[!NOTE]
>Například kontejnerová služba Azure založená na Kubernetes, jako je služba Azure Kubernetes Service nebo Azure Container Instances, volba zákazníků Azure pro runtime kontejnerů založených na Kubernetes.  

Společnost Microsoft v současné době podporuje bezplatné licenční modely (BYOL) s vlastní licencí.

## <a name="containers-offer"></a>Nabídka kontejnerů

| Požadavek | Podrobnosti |  
|:--- |:--- |  
| Fakturace a měření | Podporujte bezplatný nebo BYOL fakturační model. |  
| Obrázek vytvořený z Dockerfile | Image kontejneru musí být založeny na specifikaci image Dockeru a musí být sestaveny z dockerfile.<ul> <li>Další informace o vytváření iniciátorů naleznete v části Využití na [docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage).</li> </ul> |  
| Hosting v ACR | Inaly kontejnerů musí být hostované v úložišti registru kontejnerů Azure (ACR).<ul> <li>Další informace o práci s ACR najdete na úvodním panelu: Vytvoření registru kontejnerů pomocí stránky portálu Azure umístěné na [docs.microsoft.com/azure/container-registry/container-registry-get-started-portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).</li> </ul> |  
| Označování obrázků | Image kontejneru musí obsahovat alespoň 1 značku (maximální počet značek: 16).<ul> <li>Další informace o označování bitové kopie najdete na stránce značky dockeru umístěné na [docs.docker.com/engine/reference/commandline/tag](https://docs.docker.com/engine/reference/commandline/tag).</li> </ul> |  

## <a name="next-steps"></a>Další kroky

Pokud jste tak ještě neučinili, 

- [Zaregistrujte se](https://azuremarketplace.microsoft.com/sell) na trhu.

Pokud jste zaregistrováni a vytváříte novou nabídku nebo pracujete na stávající nabídce,

- [Přihlaste se na portál cloudových partnerů](https://cloudpartner.azure.com) a vytvořte nebo dokončete svou nabídku.
- Další informace naleznete v [tématu Kontejnery.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer)
