---
author: baanders
description: zahrnutí souboru pro nastavení místního ověřování pro DefaultAzureCredential v ukázkách digitálních vláken Azure – Úvod
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 35643370d6fd313d42f954a578b73befc025c040
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011243"
---
### <a name="set-up-local-azure-credentials"></a>Nastavit místní přihlašovací údaje Azure

Tato ukázka používá [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (součást `Azure.Identity` knihovny) k ověřování uživatelů s instancí digitálních vláken Azure, když ji spustíte na místním počítači. Další informace o různých způsobech, jak se klientská aplikace může ověřit pomocí digitálních vláken Azure, najdete v tématu [*Postup: psaní kódu ověřování aplikace*](../articles/digital-twins/how-to-authenticate-client.md).

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]