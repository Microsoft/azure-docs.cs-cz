---
title: Tom, jak sdílet prostory Dev Azure | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
manager: douge
ms.openlocfilehash: ca644cc412ad0c8f0e2a0781d9419fba58ed8d12
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247700"
---
# <a name="share-azure-dev-spaces"></a>Sdílené složky Azure Dev prostory

Dev prostory Azure můžete sdílet váš prostor dev s jinými uživateli ve vašem týmu. Každý vývojář může fungovat ve vlastním bez obav z nejnovější jiné. Spolupráci z jediného místa může také povolit budete moct otestovat kód začátku do konce bez nutnosti vytvoření mocks nebo simulovat závislosti. Najdete v článku [Další informace o vývoj v týmu](../get-started-nodejs.md#learn-about-team-development) Průvodce pro další informace.

Pro nastavení dev místa pro více vývojáři:
1. Vytvořte prostor vývojářů v Azure. Zvolte [.NET Core a VS Code](../get-started-netcore.md), [.NET Core a Visual Studio](../get-started-netcore-visualstudio.md), nebo [Node.js a VS Code](../get-started-nodejs.md). Budete potřebovat vlastníkem nebo přispěvatelem přístup k vybrané předplatné Azure.
1. Nakonfigurujte místo Dev Azure **skupiny prostředků** k [udělit přístup Přispěvatel](/azure/active-directory/role-based-access-control-configure) pro každý člen týmu. Skupina prostředků vývojářů místa můžete zkontrolovat spuštěním tohoto příkazu: `azds list`
1. Členy týmu požádat **vyberte místo na dev** s cílem vytvořit v něm.
     * **Příkazový řádek nebo VS Code**: zobrazíte existující prostory Dev Azure máte přístup k: `azds space list`. Vybrat místo dev: `azds space select`.
     * **Visual Studio IDE**: Otevřete projekt v sadě Visual Studio, vyberte **Azure Dev prostory** z spuštění nastavení rozevíracího seznamu. V dialogovém okně, které se otevře vyberte existující cluster.

![Visual Studio spusťte nastavení rozevíracího seznamu](../media/get-started-netcore-visualstudio/LaunchSettings.png)