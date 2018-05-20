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
description: Rychlý vývoj Kubernetes s kontejnery a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Kubernetes služby Azure, kontejnery
manager: douge
ms.openlocfilehash: 140a487a0a605e0f03122868af0d2d12bcdba35b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="share-azure-dev-spaces"></a>Sdílené složky Azure Dev prostory

Dev prostory Azure můžete sdílet váš prostor dev s jinými uživateli ve vašem týmu. Každý vývojář může fungovat ve vlastním bez obav z nejnovější jiné. Spolupráci z jediného místa může také povolit budete moct otestovat kód začátku do konce bez nutnosti vytvoření mocks nebo simulovat závislosti. Najdete v článku [Další informace o vývoj v týmu](../get-started-nodejs.md#learn-about-team-development) Průvodce pro další informace.

Nastavení prostředí pro víc vývojáře:
1. Vytvořte prostor vývojářů v Azure. Zvolte [.NET Core a VS Code](../get-started-netcore.md), [.NET Core a Visual Studio](../get-started-netcore-visualstudio.md), nebo [Node.js a VS Code](../get-started-nodejs.md). Budete potřebovat vlastníkem nebo přispěvatelem přístup k vybrané předplatné Azure.
1. Nakonfigurujte místo Dev Azure **skupiny prostředků** k [udělit přístup Přispěvatel](/azure/active-directory/role-based-access-control-configure) pro každý člen týmu. Skupinu prostředků prostředí můžete zkontrolovat spuštěním tohoto příkazu: `azds resource list`
1. Členy týmu požádat **vyberte prostředí** s cílem vytvořit v něm.
     * **Příkazový řádek nebo VS Code**: zobrazíte existující Dev Spacess Azure máte přístup k: `azds resource list`. Vyberte prostředí: `azds resource select`.
     * **Visual Studio IDE**: Otevřete projekt v sadě Visual Studio, vyberte **Azure Dev prostory** z spuštění nastavení rozevíracího seznamu. V dialogovém okně, které se otevře vyberte existující vývojové prostředí.

![Visual Studio spusťte nastavení rozevíracího seznamu](../media/get-started-netcore-visualstudio/LaunchSettings.png)