---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.custom: include file
ms.openlocfilehash: 12d4278171d43fdaf8613a1c91bdbffc269adc56
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949055"
---
@No__t-0 (nebo **typ identifikátoru objektu**) odkazuje na typ identity, která je udělena roli. Kromě typů `DeviceId` a `UserDefinedFunctionId` typy identifikátorů objektů odpovídají vlastnostem objektů Azure Active Directory.

Následující tabulka obsahuje podporované typy identifikátorů objektů v digitálních autovlákench Azure:

| Typ | Popis |
| --- | --- |
| UserId | Přiřadí uživateli roli. |
| DeviceId | Přiřadí k zařízení roli. |
| DomainName | Přiřadí roli názvu domény. Každý uživatel se zadaným názvem domény má přístupová práva k odpovídající roli. |
| TenantId | Přiřadí klientovi roli. Každý uživatel, který patří do zadaného ID tenanta služby Azure AD, má přístupová práva odpovídající role. |
| ServicePrincipalId | Přiřadí roli k ID instančního objektu služby. |
| UserDefinedFunctionId | Přiřadí roli k uživatelsky definované funkci (UDF). |