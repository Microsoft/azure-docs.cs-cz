---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 11/20/2019
ms.custom: include file
ms.openlocfilehash: e46041a33c12b3fcb40e5a04de11108471e59855
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307257"
---
`objectIdType` (nebo **typ identifikátoru objektu**) odkazuje na typ identity, který je přidělen roli. Kromě typů `DeviceId` a `UserDefinedFunctionId` typy identifikátorů objektů odpovídají vlastnostem objektů Azure Active Directory.

Následující tabulka obsahuje podporované typy identifikátorů objektů v digitálních autovlákench Azure:

| Typ | Popis |
| --- | --- |
| UserId | Přiřadí uživateli roli. |
| DeviceId | Přiřadí k zařízení roli. |
| DomainName | Přiřadí roli názvu domény. Každý uživatel se zadaným názvem domény má přístupová práva k odpovídající roli. |
| TenantId | Přiřadí klientovi roli. Každý uživatel, který patří do zadaného ID tenanta služby Azure AD, má přístupová práva odpovídající role. |
| ServicePrincipalId | Přiřadí roli k ID instančního objektu služby. |
| UserDefinedFunctionId | Přiřadí roli k uživatelsky definované funkci (UDF). |