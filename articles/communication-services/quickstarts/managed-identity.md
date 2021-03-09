---
title: Použití spravovaných identit ve komunikačních službách
titleSuffix: An Azure Communication Services quickstart
description: Spravované identity umožňují autorizovat přístup ke komunikačním službám Azure z aplikací běžících na virtuálních počítačích Azure, aplikacích Function App a dalších prostředcích.
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 2/24/2021
ms.author: peiliu
ms.reviewer: mikben
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 2aea4daddb9cccbf7f268c596fd121357cc17b6d
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102486585"
---
# <a name="use-managed-identities"></a>Použití spravovaných identit
Začněte s komunikačními službami Azure pomocí spravovaných identit. Identita komunikačních služeb a klientské knihovny SMS podporují ověřování Azure Active Directory (Azure AD) se [spravovanými identitami pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

V tomto rychlém startu se dozvíte, jak autorizovat přístup k klientským knihovnám identit a SMS z prostředí Azure, které podporuje spravované identity. Také popisuje, jak testovat kód ve vývojovém prostředí.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity-python.md)]
::: zone-end

## <a name="next-steps"></a>Další kroky

- [Další informace o řízení přístupu na základě role v Azure](../../../articles/role-based-access-control/index.yml)
- [Další informace o službě Azure identity Library pro .NET](/dotnet/api/overview/azure/identity-readme)
- [Vytváření tokenů přístupu uživatele](../quickstarts/access-tokens.md)
- [Odeslání zprávy SMS](../quickstarts/telephony-sms/send.md)
- [Další informace o SMS](../concepts/telephony-sms/concepts.md)
