---
title: Kontejnery nabízejí publikování průvodce pro Azure Marketplace
description: Tento článek popisuje požadavky pro publikování kontejnerů na webu Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: 6b02714c3a62e8d11512c1cc2dfc7a75a422441d
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54076378"
---
# <a name="containers-offer-publishing-guide"></a>Kontejnery nabízejí Průvodce publikováním

Nabízí kontejner umožňují publikovat svou image kontejneru na webu Azure Marketplace. Tento průvodce vám porozumět všem požadavkům pro v rámci této nabídky. 

Jedná se o transakci nabídek, které se nasazují a prostřednictvím webu Marketplace účtují. Volání akce, které uživatel uvidí je "Získat."

Typ nabídky kontejneru použijte, pokud vaše řešení je image kontejneru Dockeru Zřizuje se jako služba kontejnerů Azure na základě Kubernetes.

>[!NOTE]
>Například na základě Kubernetes Azure container service, jako jsou služby Azure Kubernetes Service nebo Azure Container Instances, řadu zákazníků Azure pro modul runtime kontejneru na základě Kubernetes.  

Společnost Microsoft aktuálně podporuje zdarma a přenést svůj – používání vlastní licence (BYOL) licenční modely.

## <a name="containers-offer"></a>Nabídka kontejnery

| Požadavek | Podrobnosti |  
|:--- |:--- |  
| K fakturaci a měření | Podpora buď bezplatnou nebo fakturační model BYOL. |  
| Bitové kopie vytvořené ze souboru Dockerfile | Image kontejneru musí být založené na specifikaci image Dockeru a musí být sestaveny ze souboru Dockerfile.<ul> <li>Další informace o vytváření imagí dockeru, najdete v části využívání umístěného v [docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage).</li> </ul> |  
| Hostování v ACR | Image kontejneru musí být uložen v úložišti Azure Container Registry (ACR).<ul> <li>Další informace o práci s ACR najdete rychlém startu: Vytvoření registru kontejnerů pomocí stránky Azure portal v [docs.microsoft.com/azure/container-registry/container-registry-get-started-portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).</li> </ul> |  
| Označení Image | Image kontejnerů musí obsahovat alespoň 1 značky (maximální počet značek: 16).<ul> <li>Další informace o označování image, najdete na stránce značky dockeru v [docs.docker.com/engine/reference/commandline/tag](https://docs.docker.com/engine/reference/commandline/tag).</li> </ul> |  


## <a name="next-steps"></a>Další postup

Pokud jste tak již neučinili, 

- [Zaregistrujte](https://azuremarketplace.microsoft.com/sell) na webu Marketplace.

Pokud budete zaregistrováni a vytvoření nové nabídky nebo pracující na existující,

- [Přihlaste se na portál Cloud Partner](https://cloudpartner.azure.com) k vytvoření nebo dokončení vaší nabídky.
- Zobrazit [kontejnery](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) Další informace.
