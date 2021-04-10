---
author: baanders
description: zahrnutí souboru pro DefaultAzureCredential do ukázek digitálních vláken Azure – Poznámka
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: bec2681c33108417aab1a33932c4f5f4d2ed730f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102473663"
---
>[!NOTE]
> Tato ukázka používá [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (součást `Azure.Identity` knihovny) k ověřování uživatelů s instancí digitálních vláken Azure, když ji spustíte na místním počítači. V případě tohoto typu ověřování vyhledá ukázka přihlašovací údaje Azure v rámci místního prostředí, jako je například přihlášení z místního rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) nebo v sadě Visual Studio/Visual Studio Code.
>
> Další informace o použití `DefaultAzureCredential` a dalších možnostech ověřování naleznete v tématu [*How to: Write a Code Authentication Code*](../articles/digital-twins/how-to-authenticate-client.md).