---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/15/2020
ms.custom: include file
ms.openlocfilehash: cb43c8b8c952d8db6cf450a7015c22c85e7fe4b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76268241"
---
(Typ `objectIdType` **identifikátoru objektu**) odkazuje na typ identity, která je dána roli. Kromě typů `DeviceId` `UserDefinedFunctionId` a odpovídají typy identifikátorů objektů vlastnostem objektů služby Azure Active Directory.

Následující tabulka obsahuje typy podporovaných identifikátorů objektů v Azure Digital Twins:

| Typ | Popis |
| --- | --- |
| UserId | Přiřadí roli uživateli. |
| DeviceId | Přiřadí roli zařízení. |
| DomainName | Přiřadí roli názvu domény. Každý uživatel se zadaným názvem domény má přístupová práva odpovídající role. |
| TenantId | Přiřadí roli klientovi. Každý uživatel, který patří do zadaného ID klienta Azure AD, má přístupová práva odpovídající role. |
| Id serviceprincipalid | Přiřadí roli ID objektu instančního objektu. |
| UserDefinedFunctionId | Přiřadí roli uživatelem definované funkci (UDF). |