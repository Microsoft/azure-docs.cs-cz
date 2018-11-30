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
ms.openlocfilehash: 47d62b5ea49205a5e639d620d78466e7e4a66bca
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52581393"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Informace o registraci vaší aplikace přidejte do své aplikace
V tomto kroku budete muset přidat Id aplikace do projektu.

1.  Otevřít `App.xaml.cs` a nahraďte řádek obsahující `ClientId` pomocí:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Co je další

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
