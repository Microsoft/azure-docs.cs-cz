---
title: Příručka pro publikování nabídky kontejnerů pro Azure Marketplace
description: Tento článek popisuje požadavky na publikování kontejnerů na webu Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: ffdc6b22209c1d334ac7b75c7079e755a7405154
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822993"
---
# <a name="containers-offer-publishing-guide"></a>Průvodce publikováním nabídky kontejnerů

Nabídky kontejnerů vám pomůžou publikovat image kontejneru do Azure Marketplace. Tento průvodce vám pomůže pochopit požadavky této nabídky. 

Jedná se o nabídky transakcí, které se nasazují a účtují prostřednictvím webu Marketplace. Volání akce, které uživatel vidí, je "získat hned".

Typ nabídky kontejneru použijte, pokud je vaše řešení image kontejneru Docker zřízená jako Kubernetes Azure Container Service.

>[!NOTE]
>Například Kubernetes Azure Container Service, jako je Azure Kubernetes Service nebo Azure Container Instances, si můžete vybrat zákazníky Azure pro modul runtime kontejneru na bázi Kubernetes.  

Microsoft v současné době podporuje bezplatných licenčních modelů (BYOL).

## <a name="containers-offer"></a>Nabídka kontejnerů

| Požadavek | Podrobnosti |  
|:--- |:--- |  
| Fakturace a měření | Podpora modelu fakturace Free nebo BYOL. |  
| Obrázek sestavený z souboru Dockerfile | Image kontejneru musí být založené na specifikaci image Docker a musí být sestavená z souboru Dockerfile.<ul> <li>Další informace o vytváření imagí Docker najdete v části věnované použití na adrese [docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage).</li> </ul> |  
| Hostování v ACR | Image kontejneru musí být hostované v úložišti Azure Container Registry (ACR).<ul> <li>Další informace o práci s ACR najdete v rychlém startu: vytvoření registru kontejnerů pomocí stránky Azure Portal nacházející se na [docs.Microsoft.com/Azure/Container-Registry/Container-Registry-Get-Started-Portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).</li> </ul> |  
| Označení obrázku | Image kontejneru musí obsahovat alespoň 1 značku (maximální počet značek: 16).<ul> <li>Další informace o označování obrázku najdete na stránce značky Docker, která se nachází na adrese [docs.Docker.com/Engine/reference/CommandLine/tag](https://docs.docker.com/engine/reference/commandline/tag).</li> </ul> |  

## <a name="next-steps"></a>Další kroky

Pokud jste to ještě neudělali, 

- [Zaregistrujte](https://azuremarketplace.microsoft.com/sell) se na webu Marketplace.

Pokud jste zaregistrováni a vytváříte novou nabídku nebo pracujete na stávajícím typu,

- [Přihlaste se k portál partnerů cloudu](https://cloudpartner.azure.com) a vytvořte nebo dokončete vaši nabídku.
- Další informace najdete v tématu [kontejnery](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) .
