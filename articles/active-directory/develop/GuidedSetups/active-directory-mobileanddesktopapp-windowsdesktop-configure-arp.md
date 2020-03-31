---
title: Začínáme s Azure AD v2 S plochy Windows – konfigurace | Dokumenty společnosti Microsoft
description: Jak aplikace Windows Desktop .NET (XAML) může získat přístupový token a volat rozhraní API chráněné koncovým bodem Azure Active Directory v2.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/29/2020
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: 4c757b52e7378bffe4a4954022b131b772a337cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897687"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Přidání registračních údajů aplikace do aplikace
V tomto kroku je třeba přidat ID aplikace do projektu.

1.  Otevřete `App.xaml.cs` a nahraďte `ClientId` řádek obsahující s:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Co je další

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
