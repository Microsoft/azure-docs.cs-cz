---
title: Použití spravovaných identit ve komunikačních službách
titleSuffix: An Azure Communication Services quickstart
description: Spravované identity umožňují autorizovat přístup ke komunikačním službám Azure z aplikací běžících na virtuálních počítačích Azure, aplikacích Function App a dalších prostředcích.
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: peiliu
ms.reviewer: mikben
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: aedf54c8c958e96b2bbfa31652b4861ff452f75a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307426"
---
# <a name="use-managed-identities"></a>Použití spravovaných identit
Začněte s komunikačními službami Azure pomocí spravovaných identit. Identity komunikačních služeb a sady SDK pro SMS podporují ověřování Azure Active Directory (Azure AD) se [spravovanými identitami pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

V tomto rychlém startu se dozvíte, jak autorizovat přístup k identitám a sadám SDK SMS z prostředí Azure, které podporuje spravované identity. Také popisuje, jak testovat kód ve vývojovém prostředí.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořit účet zdarma](https://azure.microsoft.com/free)
- Aktivní prostředek služby Azure Communication Services: Pokud ho nemáte, přečtěte si téma [vytvoření prostředku komunikační služby](./create-communication-resource.md) .
- K odeslání serveru SMS budete potřebovat [telefonní číslo](./telephony-sms/get-phone-number.md).
- Spravovaná identita pro vývojové prostředí najdete v tématu [autorizace přístupu se spravovanou identitou](./managed-identity-from-cli.md) .

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity/managed-identity-python.md)]
::: zone-end

## <a name="next-steps"></a>Další kroky

- [Další informace o řízení přístupu na základě role v Azure](../../../articles/role-based-access-control/index.yml)
- [Další informace o službě Azure identity Library pro .NET](/dotnet/api/overview/azure/identity-readme)
- [Vytváření tokenů přístupu uživatele](../quickstarts/access-tokens.md)
- [Odeslání zprávy SMS](../quickstarts/telephony-sms/send.md)
- [Další informace o SMS](../concepts/telephony-sms/concepts.md)
