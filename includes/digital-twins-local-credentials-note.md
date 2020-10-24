---
author: baanders
description: zahrnutí souboru pro DefaultAzureCredential do ukázek digitálních vláken Azure – Poznámka
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 35996595158994308a78f4d1197dcdc8f00af618
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494481"
---
>[!NOTE]
> Tato ukázka používá [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (součást `Azure.Identity` knihovny) k ověřování uživatelů s instancí digitálních vláken Azure, když ji spustíte na místním počítači. V případě tohoto typu ověřování vyhledá ukázka přihlašovací údaje Azure v rámci místního prostředí, jako je například přihlášení z místního rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) nebo v sadě Visual Studio/Visual Studio Code.
>
> Další informace o použití `DefaultAzureCredential` a dalších možnostech ověřování naleznete v tématu [*How to: Write a Code Authentication Code*](../articles/digital-twins/how-to-authenticate-client.md).