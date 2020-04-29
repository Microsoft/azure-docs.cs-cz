---
title: Nabídka imagí Azure Containers | Azure Marketplace
description: Přehled procesu publikování nabídky kontejneru na Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: dsindona
ms.openlocfilehash: 81908de5c2fb3960684ed6cf37952e8815d8a5d5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82148297"
---
# <a name="containers"></a>Containers

> [!IMPORTANT]
> Od 13. dubna 2020 začneme přesouvat správu nabídek Azure Container do partnerského centra. Po dokončení migrace vytvoříte a budete spravovat své nabídky v partnerském centru. Postupujte podle pokynů v části [vytvoření kontejneru Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer) pro správu migrovaných nabídek.

<table> <tr> <td>V této části se dozvíte, jak publikovat image kontejneru do <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a>.  
Typ nabídky kontejnerů podporuje image kontejnerů Docker zřízené jako instance <a href="https://docs.microsoft.com/azure/aks/index">služby Azure Kubernetes</a> nebo <a href="https://docs.microsoft.com/azure/container-instances/container-instances-overview">Azure Container Instances</a> a hostované v úložišti <a href="https://docs.microsoft.com/azure/container-registry">Azure Container Registry</a> . </td> <td><img src="./media/container-icon.png"  alt="Azure container icon" /></td> </tr> </table>

## <a name="offer-components"></a>Komponenty nabídky

Tato část popisuje prvky publikování kontejneru a je určený jako průvodce pro vydavatele Azure Marketplace. Publikování je rozdělené do těchto hlavních částí:

- [Požadavky](./cpp-prerequisites.md) – Seznamte se s technickými a obchodními požadavky před vytvořením nebo publikováním nabídky kontejneru.
- [Vytvoření nabídky](./cpp-create-offer.md) – zobrazí seznam kroků požadovaných k vytvoření nové položky nabídky kontejneru pomocí portál partnerů cloudu.
- [Příprava technických prostředků](./cpp-create-technical-assets.md) – jak vytvořit technické prostředky pro řešení kontejneru jako nabídku Azure Marketplace.
- [Publikování nabídky](./cpp-publish-offer.md) – jak odeslat nabídku pro publikování do Azure Marketplace.

## <a name="container-publishing-process"></a>Proces publikování kontejneru

Následující diagram znázorňuje nejdůležitější kroky při publikování nabídky virtuálních počítačů.
![Postup publikování nabídky](./media/containers-offer-process.png)

Nejdůležitější kroky pro publikování nabídky kontejneru:

1. Vytvoření nabídky – poskytněte podrobné informace o této nabídce. Tyto informace zahrnují: popis nabídky, marketingové materiály, informace o podpoře a specifikace assetu.
2. Vytvořte si obchodní a technické prostředky – vytvořte si obchodní prostředky (právní dokumenty a marketingové materiály) a technické prostředky pro přidružené řešení (kontejnery imagí hostované v Azure Container Registry.
3. Vytvoření SKU – vytvořte SKU přidružené k této nabídce. Pro každou bitovou kopii, kterou plánujete publikovat, se vyžaduje jedinečná SKU.
4. Certifikace a publikování nabídky – po dokončení nabídky a technických prostředků můžete nabídku odeslat. Toto odeslání spustí proces publikování. Během tohoto procesu je řešení testováno, ověřeno, certifikováno a pak "" bude fungovat "na Azure Marketplace.

## <a name="next-steps"></a>Další kroky

Než se pustíte do tohoto postupu, musíte splnit [technické a obchodní požadavky](./cpp-prerequisites.md) na publikování kontejneru do Microsoft Azure Marketplace.
