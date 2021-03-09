---
author: baanders
description: zahrnutí souboru pro nastavení místního ověřování pro DefaultAzureCredential v ukázkách digitálních vláken Azure – Úvod
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: dcef8030c47e771e5cd771dac09b5f96e3d38abd
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2021
ms.locfileid: "102473870"
---
### <a name="set-up-local-azure-credentials"></a>Nastavit místní přihlašovací údaje Azure

Tato ukázka používá [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (součást `Azure.Identity` knihovny) k ověřování uživatelů s instancí digitálních vláken Azure, když ji spustíte na místním počítači. Další informace o různých způsobech, jak se klientská aplikace může ověřit pomocí digitálních vláken Azure, najdete v tématu [*Postup: psaní kódu ověřování aplikace*](../articles/digital-twins/how-to-authenticate-client.md).

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]