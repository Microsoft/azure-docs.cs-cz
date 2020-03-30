---
title: Ověřování mezi tenanty
description: Popisuje, jak Azure Resource Manager zpracovává požadavky na ověření napříč klienty.
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 7a13ba6f6cbfc10c52484c45e4011da7a0d8ee4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75478822"
---
# <a name="authenticate-requests-across-tenants"></a>Ověřování požadavků napříč klienty

Při vytváření víceklientské aplikace může být nutné zpracovat požadavky na ověření pro prostředky, které jsou v různých tenantů. Běžným scénářem je, když se virtuální počítač v jednom klientovi musí připojit k virtuální síti v jiném klientovi. Azure Resource Manager poskytuje hodnotu záhlaví pro ukládání pomocných tokenů k ověření požadavků různým klientům.

## <a name="header-values-for-authentication"></a>Hodnoty záhlaví pro ověřování

Požadavek má následující hodnoty záhlaví ověřování:

| Název hlavičky | Popis | Příklad hodnoty |
| ----------- | ----------- | ------------ |
| Autorizace | Primární token | Primární &lt;žeton nosiče&gt; |
| x-ms-authorization-auxiliary | Pomocné žetony | Nosič &lt;pomocné-token1&gt;, EncryptedBearer &lt;&gt;pomocné-token2 , Nosič &lt;pomocné-token3&gt; |

Pomocná hlavička může pojmout až tři pomocné žetony. 

V kódu aplikace pro více klientů získejte ověřovací token pro ostatní klienty a uložte je do pomocných hlaviček. Všechny tokeny musí být od stejného uživatele nebo aplikace. Uživatel nebo aplikace musí být pozvánjako host k ostatním klientům.

## <a name="processing-the-request"></a>Zpracování žádosti

Když vaše aplikace odešle požadavek správce prostředků, požadavek se spustí pod identitou z primárního tokenu. Primární token musí být platný a jeho platnost nesmí být ukončena. Tento token musí být z klienta, který může spravovat předplatné.

Když požadavek odkazuje na prostředek z jiného klienta, Správce prostředků zkontroluje pomocné tokeny k určení, zda lze požadavek zpracovat. Všechny pomocné tokeny v záhlaví musí být platné a bez vypršení platnosti. Pokud vypršela platnost některého tokenu, správce prostředků vrátí kód odpovědi 401. Odpověď zahrnuje ID klienta a ID klienta z tokenu, který není platný. Pokud pomocná hlavička obsahuje platný token pro klienta, je zpracován požadavek křížového klienta.

## <a name="next-steps"></a>Další kroky

* Další informace o požadavcích na ověřování najdete v [tématu Ověřování toků a scénářů aplikací](../../active-directory/develop/authentication-flows-app-scenarios.md).
* Další informace o tokenech najdete [v tématu Tokeny přístupu služby Azure Active Directory](../../active-directory/develop/access-tokens.md).
