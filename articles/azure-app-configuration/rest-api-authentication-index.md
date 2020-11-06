---
title: Konfigurace aplikace Azure REST API – ověřování
description: Referenční stránky pro ověřování pomocí konfigurace aplikace Azure REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 21a43a005b78c8916d06e97ca9d2ba21d5a585a3
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424096"
---
# <a name="authentication"></a>Authentication

Všechny požadavky HTTP musí být ověřeny. Podporují se následující schémata ověřování.

## <a name="hmac"></a>HMAC

[Ověřování HMAC](./rest-api-authentication-hmac.md) používá náhodně generovaný tajný klíč k podepisování datové části požadavku. Podrobnosti o tom, jak jsou požadavky využívající tuto metodu ověřování povoleny, najdete v části [autorizace HMAC](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Azure Active Directory

[Ověřování pomocí Azure Active Directory (Azure AD)](/azure/active-directory/authentication/overview-authentication) využívá nosný token, který se získá z Azure Active Directory k ověřování požadavků. Podrobnosti o tom, jak se požadavky využívající tuto metodu ověřování povolují, najdete v části [autorizace Azure AD](./rest-api-authorization-azure-ad.md) .
