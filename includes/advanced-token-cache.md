---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.date: 11/05/2020
ms.author: kkrishna
ms.openlocfilehash: 174946667885debc348370ef2c6f93206890e9c1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94627999"
---
Můžete použít implementaci mezipaměti tokenů MSAL, která umožňuje aplikacím, rozhraním API a službám použít mezipaměť přístupového tokenu k tomu, aby v jejich absenci pokračovala v jednání jménem uživatelů. To je užitečné hlavně v případě, že aplikace a služby na pozadí potřebují pokračovat v práci jménem uživatele poté, co uživatel ukončí front-end webovou aplikaci.

V současné době většina procesů na pozadí používá [oprávnění aplikace](/graph/auth/auth-concepts#microsoft-graph-permissions) , když potřebují pracovat s daty uživatele, aniž by byli k dispozici k ověřování nebo opětovnému ověření. Vzhledem k tomu, že oprávnění aplikace často vyžadují souhlas správce, což vyžaduje zvýšení oprávnění, nastala zbytečné projevení, protože vývojář nemá v úmyslu získat oprávnění, která se uživateli původně poslala pro svou aplikaci.

Tato ukázka kódu na GitHubu ukazuje, jak se vyhnout nepotřebnému tření při přístupu k mezipaměti tokenů MSAL z aplikací na pozadí:

 [Přístup k mezipaměti tokenů přihlášeného uživatele z aplikací, rozhraní API a služeb na pozadí](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache)