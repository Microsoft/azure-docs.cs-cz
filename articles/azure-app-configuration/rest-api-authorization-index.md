---
title: Konfigurace aplikace Azure REST API – autorizace
description: Referenční stránky pro autorizaci pomocí konfigurace aplikace Azure REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 54f9cfab1f49837a3765c978de6deeb9e5e7d644
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424011"
---
# <a name="authorization"></a>Autorizace

Autorizace odkazuje na postup sloužící k určení oprávnění, která volající má při vytváření žádosti. Existuje několik autorizačních modelů. Autorizační model, který se používá pro požadavek, závisí na použité metodě [ověřování](./rest-api-authentication-index.md) . Modely autorizace jsou uvedené níže.

## <a name="hmac"></a>HMAC

Model [autorizačního modelu](./rest-api-authorization-hmac.md) přidružený k ověřování HMAC odděluje oprávnění pouze pro čtení nebo pro čtení i zápis. Podrobnosti najdete na stránce věnované [autorizaci HMAC](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Azure Active Directory

[Autorizační model](./rest-api-authorization-azure-ad.md) přidružený k ověřování pomocí Azure Active Directory (Azure AD) používá ke kontrole oprávnění Azure RBAC. Podrobnosti najdete na stránce věnované [autorizaci Azure AD](./rest-api-authorization-azure-ad.md) .
