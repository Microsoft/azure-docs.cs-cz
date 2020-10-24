---
author: baanders
description: zahrnutí souboru pro nastavení místního ověřování pro DefaultAzureCredential v ukázkách digitálních vláken Azure – Úvod
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 56c63ee13c5e42350a2b544074a0b0527377ca4c
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494646"
---
### <a name="set-up-local-azure-credentials"></a>Nastavit místní přihlašovací údaje Azure

Tato ukázka používá [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (součást `Azure.Identity` knihovny) k ověřování uživatelů s instancí digitálních vláken Azure, když ji spustíte na místním počítači. Další informace o různých způsobech, jak se klientská aplikace může ověřit pomocí digitálních vláken Azure, najdete v tématu [*Postup: psaní ověřovacího kódu aplikace*](../articles/digital-twins/how-to-authenticate-client.md).

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]