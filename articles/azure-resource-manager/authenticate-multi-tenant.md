---
title: Ověření mezi tenanty – Azure Resource Manageru
description: Popisuje, jak Azure Resource Manager zpracovává požadavky na ověření mezi tenanty.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: tomfitz
ms.openlocfilehash: 6554c05f40f580a6d7ae086e1d09834298f86621
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54108374"
---
# <a name="authenticate-requests-across-tenants"></a>Ověřování požadavků mezi tenanty

Při vytváření aplikace s více tenanty, budete muset zpracování žádosti o ověření prostředků, které jsou v různých tenantech. Běžný scénář, kdy je, když virtuální počítač do jednoho tenanta musí připojit k virtuální síti v jiném tenantovi. Azure Resource Manager poskytuje hodnotu hlavičky pro ukládání pomocné tokeny k ověřování požadavků na různých tenantech.

## <a name="header-values-for-authentication"></a>Hodnoty hlavičky pro ověřování

Požadavek má následující hodnoty hlavičky ověřování:

| Název hlavičky | Popis | Příklad hodnoty |
| ----------- | ----------- | ------------ |
| Autorizace | Primární token | Nosiče &lt;primární token&gt; |
| x-ms autorizace pomocných | Pomocné tokeny | Nosiče &lt;pomocné token1&gt;; EncryptedBearer &lt;pomocné token2&gt;; Nosiče &lt;pomocné token3&gt; |

Pomocné záhlaví může obsahovat až tři pomocných tokenů. 

V kódu vaší aplikace s více tenanty získání tokenu ověřování u jiných klientů a uložit je do pomocné záhlaví. Všechny tokeny musí být ze stejného uživatele nebo aplikace. Uživatel nebo aplikace musí mít pozvánku jako hosta do jiných tenantů.

## <a name="processing-the-request"></a>Zpracování žádosti

Pokud vaše aplikace odešle požadavek do Resource Manageru, žádost spuštění pod identitou z primárního tokenu. Primární token musí být platná a nevypršela. Tento token musí být z klienta, která může spravovat předplatné.

Když žádost odkazuje na prostředek z jiného tenanta, Resource Manageru zkontroluje pomocné tokeny k určení, pokud může požadavek zpracovat. Všechny pomocných tokenů v záhlaví musí být platná a nevypršela. Pokud žádné tokenu vypršela platnost, Resource Manageru vrátí kód odpovědi 401. Odpověď obsahuje klienta, ID a ID tenanta z tokenu, který není platný. Pokud pomocné záhlaví obsahuje neplatný token pro tenanta, je mezi tenanta požadavek zpracovat.

## <a name="next-steps"></a>Další postup
* Další informace o odeslání žádosti o ověření s rozhraními API Azure Resource Manageru najdete v tématu [ověřovací Resource Manageru pomocí rozhraní API pro přístup k předplatným](resource-manager-api-authentication.md).
* Další informace o tokeny, naleznete v tématu [přístupové tokeny služby Azure Active Directory](/azure/active-directory/develop/access-tokens).
