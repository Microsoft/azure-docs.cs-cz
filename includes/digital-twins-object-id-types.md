---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: 1f4cdf45fb643499f7d6ab717c6f36395ad292c8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827707"
---
@No__t-0 (nebo **typ identifikátoru objektu**) odkazuje na typ identity, která je udělena roli. Kromě typů `DeviceId` a `UserDefinedFunctionId` typy identifikátorů objektů odpovídají vlastnostem objektů Azure Active Directory.

Následující tabulka obsahuje podporované typy identifikátorů objektů v digitálních autovlákench Azure:

| Typ | Popis |
| --- | --- |
| userId | Přiřadí uživateli roli. |
| DeviceId | Přiřadí k zařízení roli. |
| DomainName | Přiřadí roli názvu domény. Každý uživatel se zadaným názvem domény má přístupová práva k odpovídající roli. |
| TenantId | Přiřadí klientovi roli. Každý uživatel, který patří do zadaného ID tenanta služby Azure AD, má přístupová práva odpovídající role. |
| servicePrincipalId | Přiřadí roli k ID instančního objektu služby. |
| UserDefinedFunctionId | Přiřadí roli k uživatelsky definované funkci (UDF). |