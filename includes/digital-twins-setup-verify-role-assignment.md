---
author: baanders
description: soubor zahrnutí pro ověření přiřazení role v nastavení digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: e651b02bf72ced58b6cba637a68ace3258514176
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405562"
---
Jedním ze způsobů, jak ověřit, že jste úspěšně nastavili přiřazení role, je zobrazení přiřazení rolí pro instanci digitálních vláken Azure v [Azure Portal](https://portal.azure.com). Přejít na stránku portálu [Azure Digital zdvojené instance](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) (můžete použít tento odkaz nebo ho vyhledat na portálu pro vyhledávání) a vybrat název instance, kterou chcete ověřit. 

Pak zobrazte všechny přiřazené role v části *řízení přístupu (IAM) > přiřazení rolí*. Uživatel by se měl zobrazit v seznamu s rolí *vlastníka digitálních vláken Azure (Preview)*. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Zobrazení přiřazení rolí pro instanci digitálních vláken Azure v Azure Portal":::