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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "74806574"
---
## <a name="install-client-library-packages"></a>Nainstalovat balíčky klientské knihovny

> [!NOTE]
> Zde uvedené příklady používají klientskou knihovnu Azure Storage verze 12. Klientská knihovna verze 12 je součástí sady Azure SDK. Další informace o sadě Azure SDK najdete v tématu úložiště Azure SDK na [GitHubu](https://github.com/Azure/azure-sdk).

Chcete-li nainstalovat balíček úložiště objektů blob, spusťte následující příkaz z konzoly Správce balíčků NuGet:

```powershell
Install-Package Azure.Storage.Blobs
```

Níže uvedené příklady také využívají nejnovější verzi [klientské knihovny Azure identity pro .NET](https://www.nuget.org/packages/Azure.Identity/) k ověřování pomocí přihlašovacích údajů Azure AD. Chcete-li nainstalovat balíček, spusťte následující příkaz z konzoly Správce balíčků NuGet:

```powershell
Install-Package Azure.Identity
```
