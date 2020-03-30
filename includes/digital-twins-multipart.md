---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 02/07/2020
ms.custom: include file
ms.openlocfilehash: 0e7cb7e4aaa9862a2b4af51593c29793ea54dd14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77111225"
---
> [!NOTE]
> Vícedílné požadavky obvykle vyžadují tři kusy:
> * Záhlaví **typu obsahu:**
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * **Content-Dispozice**:
>   * `form-data; name="metadata"`
> * Obsah souboru, který chcete nahrát
>
> **Typ obsahu** a **Content-Dispozice** se bude lišit v závislosti na scénáři použití.

Vícedílné požadavky lze provést programově (prostřednictvím jazyka C#), prostřednictvím klienta REST nebo nástroje, jako je [Postman](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#make-a-multipart-post-request). Nástroje klienta REST mohou mít různé úrovně podpory pro složité vícedílné požadavky. Nastavení konfigurace se také může mírně lišit od nástroje k nástroji. Ověřte, který nástroj je nejvhodnější pro vaše potřeby.

> [!IMPORTANT]
> Vícedílné požadavky na azure digital twins management API mají obvykle dvě části:
> * Metadata objektu blob (například přidružený typ MIME), která je deklarována **typem obsahu** a/nebo **uspořádáním obsahu**
> * Obsah objektů Blob, který obsahuje nestrukturovaný obsah souboru, který má být nahrán
>
> Pro požadavky **PATCH** není vyžadována ani jedna z těchto dvou částí. Oba jsou požadovány pro **POST** nebo vytvořit operace.

Zdrojový [kód rychlého startu obsazenosti](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) obsahuje kompletní příklady jazyka C#, které ukazují, jak provádět vícedílné požadavky na api pro správu digitálních dvojčat Azure.