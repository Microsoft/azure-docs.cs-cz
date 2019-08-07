---
title: Azure AD v2 Windows Desktop Začínáme – config | Microsoft Docs
description: Jak aplikace Windows Desktop .NET (XAML) může získat přístupový token a volat rozhraní API chráněného koncovým bodem Azure Active Directory v2.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a058024f8d6bdf7399e222c134f9f24c4ddffee8
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835560"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Přidání registračních informací aplikace do aplikace
V tomto kroku je nutné přidat ID aplikace do projektu.

1.  Otevřete `App.xaml.cs` a nahraďte řádek `ClientId` obsahující:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Co dál

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
