---
title: Jak sdílet prostory vývoj Azure | Dokumentace Microsoftu
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: iainfoulds
ms.author: iainfou
ms.date: 05/11/2018
ms.topic: article
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
ms.openlocfilehash: 11c9f07731c58f619ff109def3ca5c436efac24e
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2018
ms.locfileid: "50977766"
---
# <a name="share-azure-dev-spaces"></a>Sdílené složky Azure Dev mezery

Prostory Azure Dev můžete sdílet místa dev s ostatními ve vašem týmu. Každý Vývojář můžete pracovat ve vlastním bez obav z přerušení ostatních. Také spolupracují z jediného místa můžete povolit bez nutnosti vytvoření mocks nebo simulovat závislosti otestovat kód začátku do konce. Najdete v článku [přečtěte si víc o vývoj v týmu](../team-development-nodejs.md) Průvodce pro další informace.

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Nastavení místa vývoj pro více vývojářů

1. Vytvoření prostoru vývoj v Azure. Zvolte [.NET Core a VS Code](../get-started-netcore.md), [.NET Core a Visual Studio](../get-started-netcore-visualstudio.md), nebo [Node.js a VS Code](../get-started-nodejs.md). Musíte mít roli vlastníka nebo přispěvatele přístup do vybraného předplatného Azure.
1. Konfigurovat místa vývoj Azure **skupiny prostředků** k [přispěvatelům udělit přístup](/azure/active-directory/role-based-access-control-configure) pro každého člena týmu. Skupina prostředků dev místa můžete zkontrolovat spuštěním tohoto příkazu: `azds list-up`
1. Požádejte členy týmu o **vyberte místo dev** Pokud chcete vytvářet v ní.
     * **Příkazový řádek nebo VS Code**: Pokud chcete zobrazit stávající Azure Dev prostory máte přístup k: `azds space list`. Vyberte místo vývoj: `azds space select`.
     * **Visual Studio IDE**: Otevřete projekt v sadě Visual Studio, vyberte **Azure Dev prostory** ze spuštění nastavení rozevíracího seznamu. V dialogovém okně, které se otevře vyberte existující cluster.

    ![Visual Studio launch nastavení rozevíracího seznamu](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>Další postup

Zobrazit [přečtěte si víc o vývoj v týmu](../team-development-nodejs.md) Další informace.