---
title: Nabídka Azure image kontejnerů | Dokumentace Microsoftu
description: Přehled procesu k publikování nabídky kontejneru na webu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: pbutlerm
ms.openlocfilehash: e40e83e16ab2bfd43c3bb5fa38e52a778694e90e
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979745"
---
# <a name="containers"></a>Containers

<table> <tr> <td>Tato část vysvětluje, jak k publikování do image kontejneru <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a>.  
Podporuje typ nabídky kontejner imagí kontejnerů Dockeru Zřizuje se jako <a href="https://docs.microsoft.com/azure/aks/index">Azure Kubernetes Service</a> instance nebo <a href="https://docs.microsoft.com/azure/container-instances/container-instances-overview">Azure Container Instances</a> a je hostovaná v <a href="https://docs.microsoft.com/azure/container-registry">Azure Container Registry </a> úložiště. </td> <td><img src="./media/container-icon.png"  alt="Azure container icon" /></td> </tr> </table>

## <a name="offer-components"></a>Nabídka komponenty

Tato část popisuje elementy publikování kontejneru a slouží jako vodítko pro vydavatele na webu Azure Marketplace. Publikování na rozdělena na následující hlavní oddíly:

- [Požadavky](./cpp-prerequisites.md) – je uveden seznam technických a obchodních požadavků před vytvořením nebo publikování nabídky kontejneru.
- [Vytvořte svou nabídku](./cpp-create-offer.md) – seznam kroků potřebných k vytvoření nové položky nabídky kontejner pomocí portál partnerů cloudu.
- [Příprava technických prostředků](./cpp-create-technical-assets.md) – jak se vytvářejí technické materiály pro řešení kontejnerů v rámci nabídky na webu Azure Marketplace.
- [Publikování nabídky](./cpp-publish-offer.md) – jak odeslat nabídku k publikování na webu Azure Marketplace.

## <a name="container-publishing-process"></a>Proces publikování kontejneru

Následující diagram znázorňuje postup vysoké úrovně v publikování nabídky virtuálních počítačů.
![Postup publikování nabídky](./media/containers-offer-process.png)

Jsou základní kroky pro publikování nabídky kontejneru:

1. Vytvořte svou nabídku – poskytují podrobné informace týkající se nabídky. Tyto informace zahrnují: popis nabídky, marketingové materiály, informace o podpoře a specifikace asset.
2. Vytvořit obchodní a technické prostředky – vytvoření prostředků (právní dokumenty a marketingové materiály) a technických prostředků pro související řešení (imagí kontejnerů v prostředí ve službě Azure Container Registry.
3. Vytvoření skladové Položce – vytvoření skladové jednotky spojené s nabídkou. Je vyžadována pro každý obrázek, který se chystáte publikovat jedinečný skladová položka.
4. Certifikace a publikujte nabídku – po dokončení nabídka a technických prostředků, můžete odeslat nabídku. Toto odeslání spustí se proces publikování. Během tohoto procesu řešení je testována ověřen, certifikace, pak "místo pro živé" na webu Azure Marketplace.

## <a name="next-steps"></a>Další postup

Předtím, než byste zvážit tyto kroky, je nutné splnit [technické a podnikové požadavky](./cpp-prerequisites.md) pro publikování kontejneru na webu Microsoft Azure Marketplace.