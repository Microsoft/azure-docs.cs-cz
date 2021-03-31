---
title: Ukázky kódu pro konektory rozhraní API pro toky uživatelů – Azure AD
description: Ukázky kódu pro konektory rozhraní API v toku samoobslužné registrace pro Azure Active Directory externích identit.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3995395cfd6256689bedc7a4a3c83effc65c0b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87905873"
---
# <a name="samples-for-external-identities-self-service-sign-up"></a>Ukázky pro samoobslužné registrace externích identit

Následující tabulky obsahují odkazy na ukázky kódu pro využití webových rozhraní API ve vašich uživatelských tocích samoobslužné registrace pomocí [konektorů rozhraní API](api-connectors-overview.md).

## <a name="api-connector-azure-function-quickstarts"></a>Průvodce rychlým startem pro konektory API Azure

| Ukázka                                                                                                                          | Popis                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | Tato ukázka .NET Core Azure Functions ukazuje, jak omezit podepisování na konkrétní domény tenanta a ověřit informace zadané uživatelem. |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | Tato Node.js ukázka Azure Functions ukazuje, jak omezit podepisování na konkrétní domény tenanta a ověřit informace zadané uživatelem.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | Tato ukázka Azure Functions v Pythonu ukazuje, jak omezit podepisování na konkrétní domény tenanta a ověřit informace zadané uživatelem.    |

<!-- \| [Java](../../azure-docs-pr/articles/active-directory/b2b/invite-internal-users.md#use-the-invitation-api-to-send-a-b2b-invitation) |  The sample below illustrates how to call the invitation API to invite an internal user as a B2B user. | -->

## <a name="custom-approval-workflows"></a>Pracovní postupy vlastního schválení

| Ukázka | Popis |
|--------| ----------- |
| [Pracovní postup ručního schválení](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connectors-approvals) | Tato ukázka demonstruje kompletní pracovní postup schvalování pro správu vytvoření uživatelského účtu hosta při samoobslužném přihlášení. |

## <a name="identity-verification"></a>Ověření identity

| Ukázka                                                                                                            | Popis                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | V této ukázce se dozvíte, jak ověřit identitu uživatele v rámci samoobslužné registrace pomocí konektoru rozhraní API pro integraci s IDology. |
| [Experian](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-experian-identity-verification) | V této ukázce se dozvíte, jak ověřit identitu uživatele v rámci samoobslužné registrace pomocí konektoru rozhraní API pro integraci s Experian. |
