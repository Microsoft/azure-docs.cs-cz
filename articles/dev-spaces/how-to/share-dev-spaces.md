---
title: Jak sdílet Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Naučte se používat Azure Dev Spaces ke sdílení prostoru pro vývoj ve službě Azure Kubernetes s ostatními v týmu.
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s '
ms.openlocfilehash: 3cef7cd6a5113401cb1df3dd6c76e4dea3a7524d
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "86229291"
---
# <a name="share-azure-dev-spaces"></a>Sdílení Azure Dev Spaces

Pomocí Azure Dev Spaces můžete své vývojové místo sdílet s ostatními členy týmu. Každý vývojář může pracovat ve svém vlastním prostoru bez obav, že ostatní. Kromě toho můžete na jednom místě pracovat na konci testování kódu, aniž byste museli vytvářet nebo simulovat závislosti. Další informace najdete v tématu Průvodce [vývojem týmu](../team-development-nodejs.md) .

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Nastavení prostoru pro vývoj pro více vývojářů

1. Vytvořte prostor pro vývoj v Azure. Vyberte [.NET Core a vs Code](../get-started-netcore.md), [.NET Core a Visual Studio](../get-started-netcore-visualstudio.md)nebo [Node.js a vs Code](../get-started-nodejs.md). Musíte mít oprávnění vlastníka nebo přispěvatele k vybranému předplatnému Azure.
1. Zajistěte, aby měl každý člen týmu [potřebná oprávnění pro přístup k řadiči Azure dev Spaces](../troubleshooting.md#incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis). Můžete například nakonfigurovat **skupinu prostředků** ve službě Azure dev Space pro [udělení přístupu přispěvatele](../../role-based-access-control/role-assignments-portal.md) pro každého člena týmu. Skupinu prostředků pro vývojářské místo můžete kontrolovat spuštěním tohoto příkazu:`azds show-context`
1. Požádejte členy týmu, aby **vybrali prostor** pro vývoj.
   * **Příkazový řádek nebo vs Code**: Chcete-li zobrazit existující Azure dev Spaces máte přístup k: `azds space list` . Výběr prostoru pro `azds space select` vývoj:
   * **Integrované vývojové prostředí (IDE) sady Visual Studio**: Otevřete projekt v aplikaci Visual Studio, vyberte v rozevíracím seznamu nastavení spuštění možnost **Azure dev Spaces** . V dialogovém okně, které se otevře, vyberte existující cluster.

     ![Rozevírací seznam nastavení spuštění sady Visual Studio](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [informace o vývoji týmu](../team-development-nodejs.md) .
