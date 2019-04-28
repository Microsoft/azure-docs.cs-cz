---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
origin.date: 09/17/2018
ms.date: 11/05/2018
ms.author: v-junlch
ms.custom: include file
ms.openlocfilehash: 5c2bce5635dfe488c1725efdd2954ecd81cf8e79
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60300578"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Informace o registraci vaší aplikace přidejte do své aplikace
V tomto kroku budete muset přidat Id aplikace do projektu.

1. Otevřít `App.xaml.cs` a nahraďte řádek obsahující `ClientId` pomocí:

    ```csharp
    private static string ClientId = "[Enter the application Id here]";
    ```

<!-- ms.date: 11/05/2018 -->