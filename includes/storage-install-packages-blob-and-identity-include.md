---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: de79ea50d12ab322d1e28d0ad650df30ecc0c222
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806574"
---
## <a name="install-client-library-packages"></a>Instalace balíčků klientské knihovny

> [!NOTE]
> Zde uvedené příklady používají klientskou knihovnu Azure Storage verze 12. Klientská knihovna verze 12 je součástí sady Azure SDK. Další informace o Azure SDK najdete v tématu úložiště Azure SDK na [GitHubu](https://github.com/Azure/azure-sdk).

Chcete-li nainstalovat balíček úložiště objektů Blob, spusťte následující příkaz z konzoly Správce balíčků NuGet:

```powershell
Install-Package Azure.Storage.Blobs
```

Zde uvedené příklady také používají nejnovější verzi [knihovny klienta Azure Identity pro rozhraní .NET](https://www.nuget.org/packages/Azure.Identity/) k ověření pomocí přihlašovacích údajů azure ad. Chcete-li balíček nainstalovat, spusťte následující příkaz z konzoly správce balíčků NuGet:

```powershell
Install-Package Azure.Identity
```
