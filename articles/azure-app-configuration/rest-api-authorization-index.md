---
title: Konfigurace aplikace Azure REST API – autorizace
description: Referenční stránky pro autorizaci pomocí konfigurace aplikace Azure REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 70f05799ce2856ad490937a17b456e78789088f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932638"
---
# <a name="authorization"></a>Autorizace

Autorizace odkazuje na postup sloužící k určení oprávnění, která volající má při vytváření žádosti. Existuje několik autorizačních modelů. Autorizační model, který se používá pro požadavek, závisí na použité metodě [ověřování](./rest-api-authentication-index.md) . Modely autorizace jsou uvedené níže.

## <a name="hmac"></a>HMAC

Model [autorizačního modelu](./rest-api-authorization-hmac.md) přidružený k ověřování HMAC odděluje oprávnění pouze pro čtení nebo pro čtení i zápis. Podrobnosti najdete na stránce věnované [autorizaci HMAC](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Azure Active Directory

[Autorizační model](./rest-api-authorization-azure-ad.md) přidružený k ověřování pomocí Azure Active Directory (Azure AD) používá ke kontrole oprávnění Azure RBAC. Podrobnosti najdete na stránce věnované [autorizaci Azure AD](./rest-api-authorization-azure-ad.md) .
