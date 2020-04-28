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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76268241"
---
`objectIdType` (Nebo **typ identifikátoru objektu**) odkazuje na typ identity, který je přidělen roli. Kromě typů `DeviceId` a `UserDefinedFunctionId` typy identifikátorů objektů odpovídají vlastnostem objektů Azure Active Directory.

Následující tabulka obsahuje podporované typy identifikátorů objektů v digitálních autovlákench Azure:

| Typ | Popis |
| --- | --- |
| UserId | Přiřadí uživateli roli. |
| DeviceId | Přiřadí k zařízení roli. |
| DomainName | Přiřadí roli názvu domény. Každý uživatel se zadaným názvem domény má přístupová práva k odpovídající roli. |
| TenantId | Přiřadí klientovi roli. Každý uživatel, který patří do zadaného ID tenanta služby Azure AD, má přístupová práva odpovídající role. |
| ServicePrincipalId | Přiřadí roli k ID instančního objektu služby. |
| UserDefinedFunctionId | Přiřadí roli k uživatelsky definované funkci (UDF). |