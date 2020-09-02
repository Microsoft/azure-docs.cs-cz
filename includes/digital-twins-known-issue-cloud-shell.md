---
author: baanders
description: zahrnutí souboru pro digitální vlákna Azure – citují známé problémy s ověřováním Cloud Shell
ms.service: digital-twins
ms.topic: include
ms.date: 9/1/2020
ms.author: baanders
ms.openlocfilehash: bd3a192ed493a886da427cb6d89239a4eb8e4d17
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89290089"
---
>[!NOTE]
>V současnosti se jedná o **známý problém** v Cloud Shell ovlivňují tyto skupiny příkazů: `az dt route` , `az dt model` , `az dt twin` .
>
>Chcete-li tento problém vyřešit, buď spusťte `az login` v Cloud Shell před spuštěním příkazu, nebo použijte [místní CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) místo Cloud Shell. Další podrobnosti najdete v tématu [*řešení potíží: známé problémy v Azure Digital revláken*](../articles/digital-twins/troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).