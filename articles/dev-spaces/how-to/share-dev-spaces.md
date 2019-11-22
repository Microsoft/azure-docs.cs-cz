---
title: Jak sdílet Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s '
ms.openlocfilehash: 2c85625a4f61b701bc1e8b0a4a06f71dc0989ce0
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279990"
---
# <a name="share-azure-dev-spaces"></a>Sdílení Azure Dev Spaces

Pomocí Azure Dev Spaces můžete své vývojové místo sdílet s ostatními členy týmu. Každý vývojář může pracovat ve svém vlastním prostoru bez obav, že ostatní. Kromě toho můžete na jednom místě pracovat na konci testování kódu, aniž byste museli vytvářet nebo simulovat závislosti. Další informace najdete v tématu Průvodce [vývojem týmu](../team-development-nodejs.md) .

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Nastavení prostoru pro vývoj pro více vývojářů

1. Vytvořte prostor pro vývoj v Azure. Vyberte [.NET Core a vs Code](../get-started-netcore.md), [.NET Core a Visual Studio](../get-started-netcore-visualstudio.md), nebo [Node. js a vs Code](../get-started-nodejs.md). Musíte mít oprávnění vlastníka nebo přispěvatele k vybranému předplatnému Azure.
1. Nakonfigurujte **skupinu prostředků** ve službě Azure dev Space pro [udělení přístupu přispěvatele](/azure/active-directory/role-based-access-control-configure) pro každého člena týmu. Skupinu prostředků pro vývojářské místo můžete zjistit spuštěním tohoto příkazu: `azds list-up`
1. Požádejte členy týmu, aby **vybrali prostor** pro vývoj.
   * **Příkazový řádek nebo vs Code**: Chcete-li zobrazit existující Azure dev Spaces máte přístup k: `azds space list`. Výběr prostoru pro vývoj: `azds space select`.
   * **Integrované vývojové prostředí (IDE) sady Visual Studio**: Otevřete projekt v aplikaci Visual Studio, vyberte v rozevíracím seznamu nastavení spuštění možnost **Azure dev Spaces** . V dialogovém okně, které se otevře, vyberte existující cluster.

     ![Rozevírací seznam nastavení spuštění sady Visual Studio](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [informace o vývoji týmu](../team-development-nodejs.md) .
