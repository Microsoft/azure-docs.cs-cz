---
title: Najít ukázky sítě Azure Service Fabric
description: Tady je rejstřík dostupných ukázkových aplikací pro Service Fabricovou mřížku. Zdrojový kód v těchto příkladech ukazuje, jak dosáhnout konkrétního scénáře pomocí modelu Service Fabric prostředků.
ms.date: 12/03/2018
ms.topic: conceptual
ms.openlocfilehash: 2b38cd6a6c0f3aaab4ff5b77be82aee242afef23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627024"
---
# <a name="find-service-fabric-mesh-samples"></a>Hledání ukázek Service Fabricch mřížek

> [!IMPORTANT]
> Náhled sítě Azure Service Fabric je vyřazený. Nová nasazení již nebudou povolena prostřednictvím rozhraní API pro Service Fabric sítě. Podpora stávajících nasazení bude pokračovat do 28. dubna 2021.
> 
> Podrobnosti najdete v tématu [vyřazení náhledu do sítě Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Tato tabulka obsahuje přehled dostupných ukázkových aplikací pro Service Fabricovou mřížku. Zdrojový kód v těchto příkladech ukazuje, jak dosáhnout konkrétního scénáře pomocí modelu Service Fabric prostředků.

Další informace o nasazování šablon přímo do Azure najdete na [stránce ukázkového GitHubu šablony.](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md)

|Ukázková šablona|Popis scénáře|Zdrojový kód|Vývojářské nástroje|
|------------|--------------------|----------|----------------------|
| [Aplikace Hello World](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | Statická webová stránka hostovaná v kontejneru. Pro Linux používá Nginx pro Windows IIS | [Zdrojový kód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | Žádné požadavky |
| [Aplikace čítače pro souborové svazky Azure](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.md) | Stav uložte připojením svazku založeného na službě Azure Files do kontejneru. <br><br> **Poznámka:** Tato šablona vyžaduje, aby ve sdílené složce souborů Azure byly už [pokyny](../storage/files/storage-how-to-create-file-share.md) zřízené. | [Zdrojový kód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Nástroje pro mřížku sady Visual Studio |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | Vytvořte aplikaci s front-endové a back-end službou, která používá překlad založený na DNS. Slouží jako kurz [zde](./service-fabric-mesh-tutorial-create-dotnetcore.md) | [Zdrojový kód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Nástroje pro mřížku sady Visual Studio |
| [Aplikace vizuálních objektů](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | Škálujte a upgradujte mikroslužby v rámci aplikace. | [Zdrojový kód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Nástroje pro mřížku sady Visual Studio |
| [Hlasovací aplikace](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | Vytvoření aplikace s front-endové a back-end službou, která používá rozlišení založené na DNS | [Zdrojový kód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | Nástroje pro používání sítě sady Visual Studio pro verzi Windows, VS Code nebo dotnet CLI, se dají použít pro verzi systému Linux. |
| [Aplikace čítače pro Service Fabric spolehlivé svazky](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.sfreliablevolume.md)| Uložte stav tak, že do kontejneru připojíte Service Fabric svazek na bázi spolehlivého disku.| [Zdrojový kód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Nástroje pro mřížku sady Visual Studio |
