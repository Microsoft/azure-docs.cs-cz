---
title: Azure AD v2 Windows Desktop Začínáme – config
description: Jak aplikace Windows Desktop .NET (XAML) může získat přístupový token a volat rozhraní API chráněného koncovým bodem Azure Active Directory v2.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: d82f9beecb1b558fca094c31f8c6718c990debd1
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87162717"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Přidání registračních informací aplikace do aplikace
V tomto kroku je nutné přidat ID aplikace do projektu.

1.  Otevřete `App.xaml.cs` a nahraďte řádek obsahující `ClientId` :

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Co dál

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
