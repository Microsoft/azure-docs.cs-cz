---
author: baanders
description: zahrnutí souboru pro digitální vlákna Azure – citují známé problémy s ověřováním Cloud Shell
ms.service: digital-twins
ms.topic: include
ms.date: 9/1/2020
ms.author: baanders
ms.openlocfilehash: 574ca9d42565e897e49d6800e61bb2c33717891b
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92321583"
---
>[!NOTE]
>V současné době se jedná o **známý problém** v Cloud Shell ovlivňují tyto skupiny příkazů při spuštění z *https://shell.azure.com* : `az dt route` , `az dt model` , `az dt twin` .
>
>K vyřešení můžete provést některou z následujících akcí:
> * Spusťte `az login` v Cloud Shell před spuštěním příkazu.
> * V Azure Portal otevřete podokno Cloud Shell a dokončete Cloud Shell práci.
>  :::image type="content" source="../articles/digital-twins/media/includes/portal-cloud-shell.png" alt-text="Zobrazuje se Azure Portal se zvýrazněnou ikonou Cloud Shell a Cloud Shell se zobrazuje v dolní části okna portálu.":::
> * Místo Cloud Shell použijte místní rozhraní příkazového [řádku](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) .
>
>Další podrobnosti o tomto problému najdete v tématu [*řešení potíží: známé problémy v Azure Digital autovlákna*](../articles/digital-twins/troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).