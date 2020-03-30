---
title: Jak funguje vzdálené ladění kódu pomocí Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Popisuje procesy pro vzdálené ladění ve službě Azure Kubernetes pomocí Azure Dev Spaces
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
ms.openlocfilehash: a9f303ea6f954285a319be137a594f7ce127f740
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241397"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>Jak funguje vzdálené ladění kódu pomocí Azure Dev Spaces

Azure Dev Spaces vám nabízí několik způsobů, jak rychle itetovat a ladit aplikace Kubernetes a spolupracovat s týmem v clusteru Služby Azure Kubernetes (AKS). Jakmile váš projekt běží v dev prostoru, Azure Dev Spaces poskytuje způsob, jak připojit a ladit spuštěnou aplikaci v AKS.

Tento článek popisuje, jak funguje vzdálené ladění pomocí funkce Dev Spaces.

## <a name="debug-your-code"></a>Ladění kódu

Pro java, .NET Core a Node.js aplikace můžete ladit aplikace spuštěné přímo v prostoru pro vývoj pomocí Visual Studio Code nebo Visual Studio. Visual Studio Code a Visual Studio poskytují nástroje pro připojení k dev prostoru, spuštění aplikace a připojení ladicího programu. Po `azds prep`spuštění můžete otevřít projekt v kódu sady Visual Studio nebo Visual Studio. Visual Studio Code nebo Visual Studio bude generovat své vlastní `azds prep`konfigurační soubory pro připojení, které je oddělené od spuštění . V rámci Visual Studio Kód nebo Visual Studio, můžete nastavit zarážky a spustit aplikaci do svého dev prostoru.

![Ladění kódu](media/get-started-node/debug-configuration-nodejs2.png)

Když spustíte aplikaci pomocí Visual Studio Code nebo Visual Studio pro ladění, zpracovávají spouštění a `azds up`připojování k dev prostoru stejným způsobem jako spuštění . Nástroje na straně klienta v jazyce Visual Studio Code a Visual Studio poskytují také další parametr s konkrétními informacemi pro ladění. Parametr obsahuje název bitové kopie ladicího programu, umístění ladicího programu v rámci bitové kopie ladicího programu a cílové umístění v kontejneru aplikace pro připojení složky ladicího programu.

Bitová kopie ladicího programu je automaticky určena nástroji na straně klienta. Používá metodu podobnou té, která se používá během `azds prep`Dockerfile a Helm graf generovat při spuštění . Po udeření je připojen v bitové kopii `azds exec`aplikace, je spuštěn pomocí .

## <a name="next-steps"></a>Další kroky

Pokud chcete začít používat Azure Dev Spaces pro vzdálené ladění projektu, podívejte se na následující rychlé starty:

* [Rychlé itereje a ladění pomocí kódu Visual Studio a Javy][quickstart-java]
* [Rychlé itered a ladění pomocí kódu Sady Visual Studio a rozhraní .NET][quickstart-netcore]
* [Rychlé itered a ladění pomocí kódu sady Visual Studio a souboru Node.js][quickstart-node]
* [Rychlé itered a ladění pomocí sady Visual Studio a .NET Core][quickstart-vs]


[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md
