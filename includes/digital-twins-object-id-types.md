---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 12/20/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 40ab53c941a7ac619ebb09d381a4ae0450f26e8b
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53795875"
---
`objectIdType` (Nebo **identifikátor typu objektu**) odkazuje na typ identity, který je přiřazen k roli. Kromě `DeviceId` a `UserDefinedFunctionId` typy, typy identifikátorů objektu odpovídají vlastnostem objektů v Azure Active Directory.

Následující tabulka obsahuje typy podporovaných objektů identifikátor v digitální dvojče Azure:

| Typ | Popis |
| --- | --- |
| UserId | Přiřadí roli uživatele. |
| DeviceId | Role se přiřadí k zařízení. |
| DomainName | Přiřadí roli na název domény. Přístupová práva odpovídající roli má každý uživatel pomocí zadaného názvu domény. |
| TenantId | Přiřadí role tenanta. Každý uživatel, který patří do zadané ID tenanta Azure AD má přístupová práva odpovídající roli. |
| servicePrincipalId | Přiřadí identifikátor instanční objekt služby role |
| UserDefinedFunctionId | Přiřadí roli uživatelem definované funkce (UDF). |