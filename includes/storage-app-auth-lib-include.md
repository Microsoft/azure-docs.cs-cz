---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5e605e4ad987db16b98649b32dc96fa1620b1564
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "69011998"
---
Klientská knihovna pro [ověřování aplikací Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) pro .NET (Preview) zjednodušuje proces získávání a obnovování tokenu z vašeho kódu. Klientská knihovna pro ověřování aplikací spravuje ověřování automaticky. Knihovna používá pověření vývojáře k ověřování během místního vývoje. Použití přihlašovacích údajů pro vývojáře během místního vývoje je bezpečnější, protože nemusíte vytvářet přihlašovací údaje Azure AD nebo sdílet přihlašovací údaje mezi vývojáři. Po nasazení řešení do Azure se knihovna automaticky přepne na použití přihlašovacích údajů aplikace.

Pokud chcete použít knihovnu ověřování aplikací v aplikaci Azure Storage, nainstalujte si nejnovější balíček verze Preview z [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)a také nejnovější verzi [Azure Storage Common Client Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) a [klientskou knihovnu Azure Blob Storage. pro .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/). Do kódu přidejte následující příkazy **using** :

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```
