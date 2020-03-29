---
title: Najít ukázky sítě Azure Service Fabric
description: Zde je index dostupných ukázkových aplikací service fabric mesh. Zdrojový kód v těchto příkladech ukazuje, jak dosáhnout konkrétní scénář pomocí service fabric resource model.
ms.date: 12/03/2018
ms.topic: conceptual
ms.openlocfilehash: c944ddc26f2e2d099cf9552acb8287c363d0c768
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461901"
---
# <a name="find-service-fabric-mesh-samples"></a>Najít ukázky mřížky service fabric

Tato tabulka popisuje dostupné ukázkové aplikace sítě Service Fabric. Zdrojový kód v těchto příkladech ukazuje, jak dosáhnout konkrétní scénář pomocí service fabric resource model.

Další informace o nasazení šablon přímo do Azure najdete na [stránce ukázkové šablony GitHubu.](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md)

|Ukázková šablona|Popis scénáře|Zdrojový kód|Vývojářské nástroje|
|------------|--------------------|----------|----------------------|
| [Hello World App](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | Statická webová stránka hostovaná v kontejneru. Pro Linux používá nginx, pro Windows IIS | [Zdrojový kód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | Žádné požadavky |
| [Aplikace čítačů pro svazky souborů Azure](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.md) | Uložte stav připojením svazku založeného na Azure Files uvnitř kontejneru. <br><br> **Poznámka:** Tato šablona vyžaduje, aby se již zřízené pokyny zřízené [služby](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share) | [Zdrojový kód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Nástroje pro síť Visual Studio |
| [Aplikace TodoList](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | Vytvořte aplikaci s front-endovou a back-endovou službou, která používá rozlišení založené na DNS. Používá se jako návod [zde](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore) | [Zdrojový kód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Nástroje pro síť Visual Studio |
| [Aplikace Vizuální objekty](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | Škálování a upgrade mikroslužeb v rámci aplikace. | [Zdrojový kód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Nástroje pro síť Visual Studio |
| [Hlasovací aplikace](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | Vytvoření aplikace s front-endovou a back-endovou službou, která používá rozlišení založené na DNS | [Zdrojový kód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | Visual Studio Mesh Tooling pro verzi systému Windows, VS Code / dotnet CLI lze použít pro verzi Linuxu |
| [Aplikace čítače pro spolehlivé svazky service fabric](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.sfreliablevolume.md)| Stav uklápěte připojením svazku založeného na spolehlivém disku service fabric uvnitř kontejneru.| [Zdrojový kód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Nástroje pro síť Visual Studio |
