---
title: Konfigurace aplikace Azure REST API – ověřování
description: Referenční stránky pro ověřování pomocí konfigurace aplikace Azure REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 58fba309f4cf7e4fc4364d075500c02e0ed45259
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932689"
---
# <a name="authentication"></a>Authentication

Všechny požadavky HTTP musí být ověřeny. Podporují se následující schémata ověřování.

## <a name="hmac"></a>HMAC

[Ověřování HMAC](./rest-api-authentication-hmac.md) používá náhodně generovaný tajný klíč k podepisování datové části požadavku. Podrobnosti o tom, jak jsou požadavky využívající tuto metodu ověřování povoleny, najdete v části [autorizace HMAC](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Azure Active Directory

[Ověřování pomocí Azure Active Directory (Azure AD)](../active-directory/authentication/overview-authentication.md) využívá nosný token, který se získá z Azure Active Directory k ověřování požadavků. Podrobnosti o tom, jak se požadavky využívající tuto metodu ověřování povolují, najdete v části [autorizace Azure AD](./rest-api-authorization-azure-ad.md) .