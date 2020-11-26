---
title: Konfigurace aplikace Azure REST API – ověřování
description: Referenční stránky pro ověřování pomocí konfigurace aplikace Azure REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 56416009395ebf8270ad0fa8d141277424dd6d9a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183460"
---
# <a name="authentication"></a>Authentication

Všechny požadavky HTTP musí být ověřeny. Podporují se následující schémata ověřování.

## <a name="hmac"></a>HMAC

[Ověřování HMAC](./rest-api-authentication-hmac.md) používá náhodně generovaný tajný klíč k podepisování datové části požadavku. Podrobnosti o tom, jak jsou požadavky využívající tuto metodu ověřování povoleny, najdete v části [autorizace HMAC](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Azure Active Directory

[Ověřování pomocí Azure Active Directory (Azure AD)](../active-directory/authentication/overview-authentication.md) využívá nosný token, který se získá z Azure Active Directory k ověřování požadavků. Podrobnosti o tom, jak se požadavky využívající tuto metodu ověřování povolují, najdete v části [autorizace Azure AD](./rest-api-authorization-azure-ad.md) .