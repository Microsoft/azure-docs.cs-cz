---
author: baanders
description: zahrnutí souboru pro DefaultAzureCredential do ukázek digitálních vláken Azure – Poznámka
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 8aa3cbb2a037e49a694192c9852eeae0215c089c
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211774"
---
>[!NOTE]
> Tato ukázka používá [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (součást `Azure.Identity` knihovny) k ověřování uživatelů s instancí digitálních vláken Azure, když ji spustíte na místním počítači. V případě tohoto typu ověřování vyhledá ukázka přihlašovací údaje Azure v rámci místního prostředí, jako je například přihlášení z místního rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) nebo v sadě Visual Studio/Visual Studio Code.
>
> Další informace o použití `DefaultAzureCredential` a dalších možnostech ověřování naleznete v tématu [*How to: Write a Code Authentication Code*](../articles/digital-twins/how-to-authenticate-client.md).