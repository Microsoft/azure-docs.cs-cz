---
title: Zvýšení odolnosti aplikací pro ověřování a autorizaci, které vyvíjíte
titleSuffix: Microsoft identity platform
description: Přehled naší odolnosti proti chybám při vývoji aplikací pomocí Azure Active Directory a platformy Microsoft identity
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: d06e851390537bf94b59e656f84bf58fe7216410
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96317348"
---
# <a name="increase-resilience-of-authentication-and-authorization-applications-you-develop"></a>Zvýšení odolnosti aplikací pro ověřování a autorizaci, které vyvíjíte

Microsoft identity používá moderní ověřování a autorizaci založené na tokenech. To znamená, že aplikace získá tokeny od poskytovatele identity za účelem ověření uživatele a autorizaci aplikace pro volání chráněných rozhraní API.

Token je platný po určitou dobu, než aplikace musí získat nový. Nejenom zřídka volání načtení tokenu by mohlo selhat kvůli problému, jako je selhání sítě nebo infrastruktury nebo výpadek služby ověřování. V tomto dokumentu vytvoříme osnovu kroků, které může vývojář provést kvůli zvýšení odolnosti ve svých aplikacích, pokud dojde k selhání získání tokenu.

Tyto články poskytují pokyny ke zvýšení odolnosti v aplikacích pomocí platformy Microsoft identity a Azure Active Directory. K dispozici jsou pokyny pro klientské aplikace, které pracují jménem přihlášeného uživatele, a také aplikace démona, které pracují vlastním jménem. Obsahují osvědčené postupy pro používání tokenů a také volání prostředků.

- [Zajištění odolnosti proti chybám při sestavování aplikací přihlašování uživatelů](resilience-client-app.md)
- [Zajištění odolnosti proti sestavování aplikací bez uživatelů](resilience-daemon-app.md)
- [Odolnost sestavení v infrastruktuře pro správu identit a přístupu](resilience-in-infrastructure.md)
- [Odolnost sestavení v systémech CIAM](resilience-b2c.md)
