---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 08/12/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: 94baeb3d459b700cc95d88fb82f995957640aab6
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012089"
---
(Nebo **typ identifikátoru objektu**) odkazuje na typ identity, který je přidělen roli. `objectIdType` Kromě typů `UserDefinedFunctionId` a typy identifikátorů objektů odpovídají vlastnostem objektů Azure Active Directory. `DeviceId`

Následující tabulka obsahuje podporované typy identifikátorů objektů v digitálních autovlákench Azure:

| type | Popis |
| --- | --- |
| UserId | Přiřadí uživateli roli. |
| DeviceId | Přiřadí k zařízení roli. |
| DomainName | Přiřadí roli názvu domény. Každý uživatel se zadaným názvem domény má přístupová práva k odpovídající roli. |
| TenantId | Přiřadí klientovi roli. Každý uživatel, který patří do zadaného ID tenanta služby Azure AD, má přístupová práva odpovídající role. |
| ServicePrincipalId | Přiřadí roli k ID instančního objektu služby. |
| UserDefinedFunctionId | Přiřadí roli k uživatelsky definované funkci (UDF). |