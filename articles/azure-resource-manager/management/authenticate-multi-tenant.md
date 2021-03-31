---
title: Ověřování mezi tenanty
description: Popisuje, jak Azure Resource Manager zpracovává žádosti o ověření napříč klienty.
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 7a13ba6f6cbfc10c52484c45e4011da7a0d8ee4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "75478822"
---
# <a name="authenticate-requests-across-tenants"></a>Ověřování žádostí napříč klienty

Při vytváření víceklientské aplikace možná budete muset zpracovat žádosti o ověření pro prostředky, které jsou v různých klientech. Běžným scénářem je, že virtuální počítač v jednom tenantovi se musí připojit k virtuální síti v jiném tenantovi. Azure Resource Manager poskytuje hodnotu hlavičky pro uložení pomocných tokenů pro ověření požadavků na různé klienty.

## <a name="header-values-for-authentication"></a>Hodnoty hlaviček pro ověřování

Požadavek má následující hodnoty hlaviček ověřování:

| Název hlavičky | Popis | Příklad hodnoty |
| ----------- | ----------- | ------------ |
| Autorizace | Primární token | &lt;Primární token nosiče&gt; |
| x-MS-Authorization – pomocná | Pomocné tokeny | Nosič &lt; pomocná – token1 &gt; , EncryptedBearer &lt; AUX-token2 &gt; , nosič &lt; pomoc – token3&gt; |

Pomocné záhlaví může obsahovat až tři pomocné tokeny. 

V kódu aplikace pro více tenantů Získejte ověřovací token pro ostatní klienty a uložte je do pomocných hlaviček. Všechny tokeny musí být ze stejného uživatele nebo aplikace. Uživatel nebo aplikace musí být pozvaní jako host pro ostatní klienty.

## <a name="processing-the-request"></a>Zpracovává se žádost.

Když aplikace pošle požadavek na Správce prostředků, žádost se spustí pod identitou z primárního tokenu. Primární token musí být platný a nesmí být v platnosti. Tento token musí být z tenanta, který může spravovat předplatné.

Když požadavek odkazuje na prostředek z jiného tenanta, Správce prostředků zkontroluje pomocné tokeny a určí, jestli se žádost dá zpracovat. Všechny pomocné tokeny v hlavičce musí být platné a neprošlé. Pokud vypršela platnost nějakého tokenu, Správce prostředků vrátí kód odpovědi 401. Odpověď zahrnuje ID klienta a ID tenanta z tokenu, který není platný. Pokud pomocné záhlaví obsahuje pro tenanta platný token, je zpracován požadavek mezi klienty.

## <a name="next-steps"></a>Další kroky

* Další informace o požadavcích na ověřování najdete v tématu [toky ověřování a scénáře použití aplikace](../../active-directory/develop/authentication-flows-app-scenarios.md).
* Další informace o tokenech najdete v tématu [Azure Active Directory Access tokens](../../active-directory/develop/access-tokens.md).
