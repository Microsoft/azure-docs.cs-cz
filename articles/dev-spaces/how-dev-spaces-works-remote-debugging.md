---
title: Jak vzdálené ladění kódu pomocí Azure Dev Spaces funguje
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Popisuje procesy pro vzdálené ladění ve službě Azure Kubernetes s Azure Dev Spaces
keywords: Azure Dev Spaces, vývojářské prostory, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
ms.openlocfilehash: fd984ff6a8ebe336f76643406c0957769dbfd3da
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88213381"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>Jak vzdálené ladění kódu pomocí Azure Dev Spaces funguje

Azure Dev Spaces poskytuje několik způsobů, jak rychle iterovat a ladit aplikace Kubernetes a spolupracovat s týmem v clusteru Azure Kubernetes Service (AKS). Po spuštění projektu ve vývojovém prostoru Azure Dev Spaces poskytuje způsob, jak se připojit a ladit běžící aplikace v AKS.

Tento článek popisuje, jak funguje vzdálené ladění pomocí vývojových prostorů.

## <a name="debug-your-code"></a>Ladění kódu

Pro aplikace Java, .NET Core a Node.js můžete ladit aplikaci běžící přímo ve vývojovém prostoru pomocí Visual Studio Code nebo sady Visual Studio. Visual Studio Code a Visual Studio poskytují nástroje pro připojení k vývojovému prostoru, spuštění aplikace a připojení ladicího programu. Po spuštění `azds prep` můžete projekt otevřít v Visual Studio Code nebo v aplikaci Visual Studio. Visual Studio Code nebo Visual Studio vytvoří vlastní konfigurační soubory pro připojení, které je oddělené od spuštění `azds prep` . Z aplikace Visual Studio Code nebo Visual Studio můžete nastavit zarážky a spustit aplikaci do vývojového prostoru.

![Ladění kódu](media/get-started-node/debug-configuration-nodejs2.png)

Když spustíte aplikaci pomocí Visual Studio Code nebo sady Visual Studio pro ladění, zpracují se spouštění a připojení k vývojovému prostoru stejným způsobem jako v provozu `azds up` . Nástroje na straně klienta v Visual Studio Code a sady Visual Studio také poskytují další parametr s konkrétními informacemi pro ladění. Parametr obsahuje název bitové kopie ladicího programu, umístění ladicího programu v rámci bitové kopie ladicího programu a cílové umístění v rámci kontejneru aplikace pro připojení složky ladicího programu.

Obrázek ladicího programu se automaticky určí pomocí nástrojů na straně klienta. Používá metodu podobnou té, která se používá při generování grafu souboru Dockerfile a Helm při jeho spuštění `azds prep` . Po připojení ladicího programu k imagi aplikace je spuštěn pomocí `azds exec` .

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak Azure Dev Spaces funguje.

> [!div class="nextstepaction"]
> [Jak Azure Dev Spaces funguje](how-dev-spaces-works.md)
