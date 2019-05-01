---
title: Zakázání ověření e-mailu během příjemce registrace v Azure Active Directory B2C | Dokumentace Microsoftu
description: Téma ukazuje, jak k zakázání ověření e-mailu během registrace v Azure Active Directory B2C příjemce.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 01d96370fc7ce601ba5626ca0f1ec28871bf311a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703345"
---
# <a name="disable-email-verification-during-consumer-sign-up-in-azure-active-directory-b2c"></a>Zakázání ověření e-mailu během registrace v Azure Active Directory B2C příjemce 
Při povolení služby Azure Active Directory (Azure AD) B2C dává možnost zaregistrovat do aplikace tak, že poskytuje e-mailovou adresu a vytvoření místního účtu příjemce. Azure AD B2C zajišťuje platné e-mailové adresy vyžadováním příjemci k ověření během procesu registrace. Zabrání také škodlivý automatizovaný proces generování falešné účty pro aplikace.

Někteří vývojáři aplikace chtít přeskočit ověření e-mailu během procesu registrace a místo toho použít později ověření e-mailovou adresu uživatele. Z toho důvodu můžete nakonfigurovat Azure AD B2C k zakázání ověření e-mailu. Tím se vytvoří hladší procesu registrace a vývojářům poskytuje flexibilitu pro odlišení příjemce, kteří ověření e-mailová adresa z tito příjemci, které ještě nebyly.

Toky registrace uživatelů mají ve výchozím nastavení zapnuté ověření e-mailu. Chcete-li vypnout, postupujte následovně:

1. Klikněte na tlačítko **toky uživatelů**.
2. Klikněte na váš tok uživatele (například "B2C_1_SiUp") a otevřete ho. 
3. Klikněte na tlačítko **rozložení stránek**.
4. Klikněte na tlačítko **stránku pro přihlášení místním účtem**.
5. Klikněte na tlačítko **e-mailovou adresu** v **název** sloupci **atributy uživatele** oddílu.
6. V části **vyžaduje ověření**vyberte **ne**.
7. V horní části okna klikněte na **Uložit**. Je to!

> [!NOTE]
> Zakázání ověření e-mailu v procesu registrace může vést k zasílání nevyžádané pošty. Pokud zakážete výchozí hodnotu, doporučujeme přidat vlastní ověřovacího systému.
> 
>
