---
title: Zvýšení odolnosti při ověřování a autorizaci v aplikacích démona, které vyvíjíte
titleSuffix: Microsoft identity platform
description: Pokyny pro zvýšení odolnosti ověřování a autorizace v aplikaci démona pomocí platformy Microsoft Identity Platform
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: a7b8f893026bb96c8d768d2e6d07d0240ecb81fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724837"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-daemon-applications-you-develop"></a>Zvýšení odolnosti při ověřování a autorizaci v aplikacích démona, které vyvíjíte

Tento článek poskytuje pokyny k tomu, jak můžou vývojáři používat platformu Microsoft Identity Platform a Azure Active Directory ke zvýšení odolnosti aplikací démona. To zahrnuje procesy na pozadí, služby, aplikace serveru pro server a aplikace bez uživatelů.

![Aplikace démona, která volá Microsoft identity](media/resilience-daemon-app/calling-microsoft-identity.png)

## <a name="use-managed-identities-for-azure-resources"></a>Použití spravovaných identit pro prostředky Azure

Vývojáři, kteří sestavují aplikace démona v Microsoft Azure můžou používat [spravované identity pro prostředky Azure](../managed-identities-azure-resources/overview.md). Spravované identity eliminují potřebu vývojářů spravovat tajné klíče a přihlašovací údaje. Tato funkce zvyšuje odolnost proti chybám při vypršení platnosti certifikátu, chybách rotace nebo vztahu důvěryhodnosti. Má také několik integrovaných funkcí, které byly specificky určeny ke zvýšení odolnosti.

Spravované identity používají dlouhodobé přístupové tokeny a informace z identity Microsoftu k proaktivnímu získání nových tokenů ve velkém časovém intervalu, než vyprší platnost existujícího tokenu. Vaše aplikace může pokračovat v běhu při pokusu o získání nového tokenu.

Spravované identity také používají regionální koncové body pro zlepšení výkonu a odolnosti proti chybám mimo oblast. Použití regionálního koncového bodu pomáhá udržet veškerý provoz v geografické oblasti. Pokud je například prostředek Azure v WestUS2, veškerý provoz, včetně provozu generovaného společností Microsoft identity, by měl zůstat v WestUS2. Tím se eliminují možné body selhání sloučením závislostí vaší služby.

## <a name="use-the-microsoft-authentication-library"></a>Použití knihovny Microsoft Authentication Library

Vývojáři aplikací démona, kteří nepoužívají spravované identity, můžou používat [Microsoft Authentication Library (MSAL)](../develop/msal-overview.md), který umožňuje implementaci ověřování a autorizace jednoduché a automaticky používá osvědčené postupy pro odolnost. MSAL zajistí snazší proces poskytování požadovaných přihlašovacích údajů klienta. Například vaše aplikace nemusí při použití přihlašovacích údajů založených na certifikátech implementovat vytváření a podepisování JSON Web Token kontrolních výrazů.

### <a name="use-microsoftidentityweb-for-net-developers"></a>Použití Microsoft. identity. Web pro vývojáře v rozhraní .NET

Vývojáři, kteří sestavují aplikace démona v ASP.NET Core můžou používat knihovnu [Microsoft. identity. Web](../develop/microsoft-identity-web.md) . Tato knihovna je postavená na MSAL, aby bylo implementace autorizace ještě snazší pro ASP.NET Core aplikace. Zahrnuje několik strategií [mezipaměti distribuovaných tokenů](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization#distributed-token-cache) pro distribuované aplikace, které mohou běžet v několika oblastech.

## <a name="cache-and-store-tokens"></a>Cache a Store – tokeny

Pokud pro implementaci ověřování a autorizace nepoužíváte MSAL, můžete implementovat některé osvědčené postupy pro ukládání do mezipaměti a ukládání tokenů. MSAL implementuje a sleduje tyto osvědčené postupy automaticky.

Aplikace získá tokeny od poskytovatele identity, aby bylo možné aplikaci volat chráněná rozhraní API. Když vaše aplikace obdrží tokeny, odpověď obsahující tokeny obsahuje také vlastnost "Expires \_ in", která oznamuje aplikaci, jak dlouho ukládat do mezipaměti a znovu použít token. Je důležité, aby aplikace používaly vlastnost Expires \_ in k určení životnosti tokenu. Aplikace se nikdy nepokouší dekódovat přístupový token rozhraní API. Použití tokenu uloženého v mezipaměti zabraňuje zbytečnému provozu mezi vaší aplikací a identitou Microsoftu. Uživatel může zůstat přihlášený k vaší aplikaci po dobu životnosti tohoto tokenu.

## <a name="properly-handle-service-responses"></a>Správné zpracování odpovědí služby

A konečně, zatímco aplikace by měly zpracovávat všechny chybové odpovědi, existují některé odpovědi, které mohou ovlivnit odolnost. Pokud vaše aplikace obdrží kód odpovědi HTTP 429, moc velký počet požadavků, Microsoft Identity omezuje vaše požadavky. Pokud vaše aplikace bude i nadále dělat příliš mnoho požadavků, bude i nadále omezené, aby vaše aplikace přijímala tokeny. Aplikace by se neměla pokoušet získat token znovu až po uplynutí doby v sekundách v poli s odpovědí "opakovat po" po. Přijetí odpovědi 429 je často známkou toho, že aplikace neukládá do mezipaměti a správně znovu nepoužívá tokeny. Vývojáři by si měli projít, jak se tokeny ukládají do mezipaměti a znovu použijí v aplikaci.

Když aplikace obdrží kód odpovědi HTTP 5xx, aplikace nesmí zadat rychlou smyčku opakování. Je-li k dispozici, aplikace by měla pro odpověď 429 platit stejné zpracování "opakovat po". Pokud odpověď neposkytne hlavičku "opakovat po", doporučujeme, abyste implementovali exponenciální back-vypnutý pokus s prvním opakovaným pokusem o 5 sekund po odpovědi.

V případě, že se žádosti vyprší, pokud se aplikace neopakuje okamžitě. Implementujte exponenciální Back-off opakování s prvním opakováním alespoň 5 sekund po odpovědi.

## <a name="next-steps"></a>Další kroky

- [Zajištění odolnosti proti chybám při sestavování aplikací přihlašování uživatelů](resilience-client-app.md)
- [Odolnost sestavení v infrastruktuře pro správu identit a přístupu](resilience-in-infrastructure.md)
- [Odolnost sestavení v systémech CIAM](resilience-b2c.md)