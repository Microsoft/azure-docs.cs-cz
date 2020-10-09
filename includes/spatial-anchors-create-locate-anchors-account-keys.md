---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 9bd213b63b69a25fb2530cd8f6659abf5357616a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76694188"
---
## <a name="set-up-authentication"></a>Nastavení ověřování

Chcete-li získat přístup ke službě, je nutné zadat klíč účtu, přístupový token nebo Azure Active Directory ověřovací token. Další informace o tomto tématu si můžete přečíst na [stránce koncept ověřování](/azure/spatial-anchors/concepts/authentication).

### <a name="account-keys"></a>Klíče účtu

Klíče účtu jsou přihlašovací údaje, které vaší aplikaci umožňují ověřování ve službě prostorových kotev Azure. Zamýšlené účely klíčů účtu vám pomůžou rychle začít. Hlavně ve fázi vývoje integrace aplikace s prostorovými kotvami Azure. V takovém případě můžete použít klíče účtu jejich vložením do klientských aplikací během vývoje. V průběhu vývoje se důrazně doporučuje přejít na mechanismus ověřování, který je na úrovni výroby, podporovaný přístupovými tokeny, nebo Azure Active Directory ověření uživatele. Klíč účtu pro vývoj získáte tak, že přejdete na svůj účet prostorových kotev Azure a přejdete na kartu klíče.
