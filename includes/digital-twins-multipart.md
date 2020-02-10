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
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2020
ms.locfileid: "77111225"
---
> [!NOTE]
> Požadavky na více částí obvykle vyžadují tři části:
> * Hlavička **Content-Type** :
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * **Dispoziční obsah**:
>   * `form-data; name="metadata"`
> * Obsah souboru, který se má nahrát
>
> **Typ obsahu** a **dispoziční obsah** se budou lišit v závislosti na scénáři použití.

Požadavky na více částí lze provádět programově ( C#prostřednictvím) prostřednictvím klienta REST nebo nástroje, jako je například [post](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#make-a-multipart-post-request). Nástroje klienta REST mohou mít různé úrovně podpory pro komplexní požadavky na více součástí. Nastavení konfigurace se může mírně lišit od nástrojů k nástroji. Ověřte, který nástroj nejlépe vyhovuje vašim potřebám.

> [!IMPORTANT]
> Požadavky na součásti, které jsou součástí rozhraní API pro správu digitálních vláken Azure, mají obvykle dvě části:
> * Metadata objektu BLOB (například přidruženého typu MIME) deklarovaná pomocí **Content-Type** nebo **Content-Disposition**
> * Obsah objektu blob, který zahrnuje nestrukturovaný obsah souboru, který se má nahrát
>
> Pro žádosti o **opravu** nejsou nutné žádné ze dvou částí. Oba jsou vyžadovány pro operace **post** nebo Create.

[Zdrojový kód pro rychlý Start obsazení](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) obsahuje kompletní C# příklady, které demonstrují, jak provádět požadavky na více než více požadavků na rozhraní API pro správu digitálních vláken Azure.