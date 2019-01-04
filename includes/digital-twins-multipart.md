---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 01/02/2019
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 1c6579776b86decb78c172578cbe55a66c05d78f
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54026549"
---
> [!NOTE]
> Požadavky na vícedílné zprávy standardu obvykle vyžadují tři údaje:
> * A **Content-Type** hlavičky:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * A **Content-Disposition**:
>   * `form-data; name="metadata"`
> * Obsah souboru k odeslání
>
> **Content-Type** a **Content-Disposition** budou lišit v závislosti na scénáři použití.

S více částmi. požadavky můžete provést prostřednictvím kódu programu (prostřednictvím C#), pomocí klienta REST nebo nástroj, jako [Postman](https://www.getpostman.com/). Nástroje klienta REST může mít různé úrovně podpory pro složité požadavky s více částmi. Ověřte, který nástroj nejlépe vyhovuje vašim potřebám.

> [!IMPORTANT]
> S více částmi. požadavky na rozhraní API Správce Dvojčat digitální Azure mají dvě části:
> * Metadata objektu BLOB (například přidružený typ MIME), která je deklarována pomocí **Content-Type** a **Content-Disposition**
> * Obsah objektu BLOB, které zahrnují nestrukturovaný obsah souboru k odeslání
>
> Ani jeden ze dvou částí, je třeba **oprava** požadavky. Jsou potřeba pro **příspěvek** nebo operace vytvoření.
