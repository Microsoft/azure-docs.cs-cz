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
ms.openlocfilehash: c469a71ec1357dd6f260ab613fed72110d1ed880
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824349"
---
# <a name="share-azure-dev-spaces"></a>Sdílené složky Azure Dev prostory

Dev prostory Azure můžete sdílet váš prostor dev s jinými uživateli ve vašem týmu. Každý vývojář může fungovat ve vlastním bez obav z nejnovější jiné. Spolupráci z jediného místa může také povolit budete moct otestovat kód začátku do konce bez nutnosti vytvoření mocks nebo simulovat závislosti. Najdete v článku [Další informace o vývoj v týmu](../get-started-nodejs.md#learn-about-team-development) Průvodce pro další informace.

Pro nastavení dev místa pro více vývojáři:
1. Vytvořte prostor vývojářů v Azure. Zvolte [.NET Core a VS Code](../get-started-netcore.md), [.NET Core a Visual Studio](../get-started-netcore-visualstudio.md), nebo [Node.js a VS Code](../get-started-nodejs.md). Budete potřebovat vlastníkem nebo přispěvatelem přístup k vybrané předplatné Azure.
1. Nakonfigurujte místo Dev Azure **skupiny prostředků** k [udělit přístup Přispěvatel](/azure/active-directory/role-based-access-control-configure) pro každý člen týmu. Skupina prostředků vývojářů místa můžete zkontrolovat spuštěním tohoto příkazu: `azds resource list`
1. Členy týmu požádat **vyberte místo na dev** s cílem vytvořit v něm.
     * **Příkazový řádek nebo VS Code**: zobrazíte existující Dev Spacess Azure máte přístup k: `azds resource list`. Vybrat místo dev: `azds resource select`.
     * **Visual Studio IDE**: Otevřete projekt v sadě Visual Studio, vyberte **Azure Dev prostory** z spuštění nastavení rozevíracího seznamu. V dialogovém okně, které se otevře vyberte existující cluster.

![Visual Studio spusťte nastavení rozevíracího seznamu](../media/get-started-netcore-visualstudio/LaunchSettings.png)