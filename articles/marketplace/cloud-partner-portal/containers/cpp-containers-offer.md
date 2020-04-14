---
title: Nabídka image azure kontejnerů | Azure Marketplace
description: Přehled procesu publikování nabídky kontejnerů na Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: dsindona
ms.openlocfilehash: cd9f98d42efcb35dbab4f3c0a06c5a11360e36b6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270345"
---
# <a name="containers"></a>Containers

> [!IMPORTANT]
> dubna 2020 začneme přesouvat správu nabídek Azure Container do Centra partnerů. Po migraci vytvoříte a spravujete nabídky v Centru partnerů. Postupujte podle pokynů v [části Vytvoření nabídky kontejneru Azure](https://aka.ms/CreateContainerOffer) ke správě migrovaných nabídek.

<table> <tr> <td>Tato část vysvětluje, jak publikovat image kontejneru na <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a>.  
Typ nabídky kontejneru podporuje iimage kontejnerů Dockeru zřízené instance <a href="https://docs.microsoft.com/azure/aks/index">služby Azure Kubernetes</a> nebo <a href="https://docs.microsoft.com/azure/container-instances/container-instances-overview">instance kontejneru Azure</a> a hostované v úložišti <a href="https://docs.microsoft.com/azure/container-registry">registru kontejnerů Azure.</a> </td> <td><img src="./media/container-icon.png"  alt="Azure container icon" /></td> </tr> </table>

## <a name="offer-components"></a>Nabídka komponent

Tato část popisuje prvky publikování kontejneru a je určena jako vodítko pro vydavatele na Azure Marketplace. Publikování je rozděleno do následujících hlavních částí:

- [Požadavky](./cpp-prerequisites.md) – zobrazí seznam technických a obchodních požadavků před vytvořením nebo publikováním nabídky kontejneru.
- [Vytvořte nabídku](./cpp-create-offer.md) – uvádí kroky potřebné k vytvoření nové položky nabídky kontejneru pomocí portálu partnerů cloudu.
- [Připravte technické prostředky](./cpp-create-technical-assets.md) – jak vytvořit technické prostředky pro řešení kontejnerů jako nabídku na Azure Marketplace.
- [Publikování nabídky](./cpp-publish-offer.md) – jak odeslat nabídku k publikování na Azure Marketplace.

## <a name="container-publishing-process"></a>Proces publikování kontejnerů

Následující diagram znázorňuje kroky vysoké úrovně při publikování nabídky virtuálního připojení.
![Postup zveřejnění nabídky](./media/containers-offer-process.png)

Kroky vysoké úrovně pro publikování nabídky kontejneru jsou:

1. Vytvořit nabídku - Poskytněte podrobné informace o nabídce. Tyto informace zahrnují: popis nabídky, marketingové materiály, informace o podpoře a specifikace aktiv.
2. Vytvořte obchodní a technické prostředky – vytvořte obchodní prostředky (právní dokumenty a marketingové materiály) a technické prostředky pro přidružené řešení (image kontejnerů hostované v registru kontejnerů Azure.
3. Vytvoření skladové položky – vytvořte skladové položky přidružené k nabídce. Pro každý obrázek, který plánujete publikovat, je vyžadována jedinečná skladová položka.
4. Certifikovat a publikovat nabídku - Po dokončení nabídky a technických prostředků můžete nabídku odeslat. Toto odeslání spustí proces publikování. Během tohoto procesu se řešení testuje, ověřuje, certifikuje a pak "přejde do provozu" na Azure Marketplace.

## <a name="next-steps"></a>Další kroky

Než začnete tyto kroky zvážit, musíte splnit [technické a obchodní požadavky](./cpp-prerequisites.md) pro publikování kontejneru na webu Microsoft Azure Marketplace.
