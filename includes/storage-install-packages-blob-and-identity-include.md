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
ms.openlocfilehash: 0adf1280fa50e9ee594f3025dff70786f5ba2199
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666154"
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

Další informace o tom, jak ověřit pomocí klientské knihovny Azure identity z Azure Storage, najdete v části s názvem **ověřování pomocí knihovny identit Azure** v tématu [autorizace přístupu k objektům blob a frontám pomocí Azure Active Directory a spravovaných identit pro prostředky Azure](/azure/storage/common/storage-auth-aad-msi?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library).