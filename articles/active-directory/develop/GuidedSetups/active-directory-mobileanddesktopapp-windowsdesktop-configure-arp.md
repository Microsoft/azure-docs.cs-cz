---
title: Azure AD v2 Windows Desktop získávání zahájeno - Config | Dokumentace Microsoftu
description: Jak aplikace Windows Desktop .NET (XAML) můžete získat přístupový token a volat rozhraní API chráněné službou Azure Active Directory v2 koncový bod.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: c42f40252733f0c4fd7fdbabb49714ea537acc70
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38473957"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Informace o registraci vaší aplikace přidejte do své aplikace
V tomto kroku budete muset přidat Id aplikace do projektu.

1.  Otevřít `App.xaml.cs` a nahraďte řádek obsahující `ClientId` pomocí:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Co je další

[!INCLUDE [Test and Validate](..\..\..\..\includes\active-directory-develop-guidedsetup-windesktop-test.md)]
