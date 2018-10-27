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
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: dd74736734caddfcfb32a74a073e649c144abeb3
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142404"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Informace o registraci vaší aplikace přidejte do své aplikace

V tomto kroku budete muset přidat Id aplikace do projektu.

1. Otevřít `App.xaml.cs` a nahraďte řádek obsahující `ClientId` pomocí:

```csharp
private static string ClientId = "[Enter the application Id here]";
```
