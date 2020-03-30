---
title: Sdílení azure dev spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Zjistěte, jak pomocí Azure Dev Spaces sdílet dev prostor ve službě Azure Kubernetes se službami Azure Kubernetes s ostatními ve vašem týmu.
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery, Helm, síť služeb, směrování sítě služeb, kubectl, k8s '
ms.openlocfilehash: 0fcb8be5107c7769af7e51dece9f190c8e7e22df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474403"
---
# <a name="share-azure-dev-spaces"></a>Sdílení Azure Dev Spaces

S Azure Dev Spaces můžete sdílet svůj dev prostor s ostatními ve vašem týmu. Každý vývojář může pracovat ve svém vlastním prostoru bez obav z lámání ostatních. Spolupráce v jednom prostoru může také umožnit testování kódu od konce do konce bez nutnosti vytvářet mocks nebo simulovat závislosti. Další informace najdete v příručce [K vývoji týmu.](../team-development-nodejs.md)

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Nastavení dev prostoru pro více vývojářů

1. Vytvořte dev prostor v Azure. Zvolte [kód .NET Core a VS](../get-started-netcore.md), [.NET Core a Visual Studio](../get-started-netcore-visualstudio.md)nebo [Node.js a VS Code](../get-started-nodejs.md). Budete muset mít přístup vlastníka nebo přispěvatele k vybranému předplatnému Azure.
1. Ujistěte se, že každý člen týmu má [příslušná oprávnění pro přístup k řadiči Azure Dev Spaces](../troubleshooting.md#incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis). Můžete například nakonfigurovat **skupinu prostředků** Azure Dev Space tak, aby [udělovala přístup přispěvatele](/azure/active-directory/role-based-access-control-configure) pro každého člena týmu. Pomocí tohoto příkazu můžete zkontrolovat skupinu prostředků dev prostoru:`azds show-context`
1. Požádejte členy týmu, aby **si vybrali prostor pro vývoj,** aby se v něm mohli rozvíjet.
   * **Příkazový řádek nebo kód VS**: Chcete-li zobrazit `azds space list`existující Azure Dev Spaces, máte přístup k: . Výběr dev mezery: `azds space select`.
   * **IDE Visual Studio**: Otevřete projekt v Sadě Visual Studio, vyberte **Azure Dev Spaces** z rozevíracího prostředí nastavení spuštění. V dialogovém okně, které se otevře, vyberte existující cluster.

     ![Rozevírací nabídka nastavení spuštění sady Visual Studio](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>Další kroky

Další informace najdete [v tématu Další](../team-development-nodejs.md) informace najdete v tématu Další informace.
