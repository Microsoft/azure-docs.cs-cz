---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 7210b05566f5cd6f3c56792bce0904b3c9b645da
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993043"
---
## <a name="set-up-authentication"></a>Nastavení ověřování

Chcete-li získat přístup ke službě, je nutné zadat klíč účtu, přístupový token nebo Azure Active Directory ověřovací token. Další informace o tomto tématu si můžete přečíst na [stránce koncept ověřování](../articles/spatial-anchors/concepts/authentication.md).

### <a name="account-keys"></a>Klíče účtu

Klíče účtu jsou přihlašovací údaje, které vaší aplikaci umožňují ověřování ve službě prostorových kotev Azure. Zamýšlené účely klíčů účtu vám pomůžou rychle začít. Hlavně ve fázi vývoje integrace aplikace s prostorovými kotvami Azure. V takovém případě můžete použít klíče účtu jejich vložením do klientských aplikací během vývoje. V průběhu vývoje se důrazně doporučuje přejít na mechanismus ověřování, který je na úrovni výroby, podporovaný přístupovými tokeny, nebo Azure Active Directory ověření uživatele. Klíč účtu pro vývoj získáte tak, že přejdete na svůj účet prostorových kotev Azure a přejdete na kartu klíče.