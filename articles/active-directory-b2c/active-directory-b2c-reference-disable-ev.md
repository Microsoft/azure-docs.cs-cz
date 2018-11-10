---
title: Zakázání ověření e-mailu během příjemce registrace v Azure Active Directory B2C | Dokumentace Microsoftu
description: Téma ukazuje, jak k zakázání ověření e-mailu během registrace v Azure Active Directory B2C příjemce.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 2/06/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e36dd19aa020b8cb2a623cda904cf7fa8a0b26da
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51004585"
---
# <a name="disable-email-verification-during-consumer-sign-up-in-azure-active-directory-b2c"></a>Zakázání ověření e-mailu během registrace v Azure Active Directory B2C příjemce 
Při povolení služby Azure Active Directory (Azure AD) B2C dává možnost zaregistrovat do aplikace tak, že poskytuje e-mailovou adresu a vytvoření místního účtu příjemce. Azure AD B2C zajišťuje platné e-mailové adresy vyžadováním příjemci k ověření během procesu registrace. Zabrání také škodlivý automatizovaný proces generování falešné účty pro aplikace.

Někteří vývojáři aplikace chtít přeskočit ověření e-mailu během procesu registrace a místo toho použít později ověření e-mailovou adresu uživatele. Z toho důvodu můžete nakonfigurovat Azure AD B2C k zakázání ověření e-mailu. Tím se vytvoří hladší procesu registrace a vývojářům poskytuje flexibilitu pro odlišení příjemce, kteří ověření e-mailová adresa z tito příjemci, které ještě nebyly.

Zásady registrace mají ve výchozím nastavení zapnuté ověření e-mailu. Chcete-li vypnout, postupujte následovně:

1. Klikněte na tlačítko **zásady registrace** nebo **zásady registrace nebo přihlášení** v závislosti na tom, co jste nakonfigurovali pro registraci.
2. Klikněte na zásadu (například "B2C_1_SiUp") a otevřete ho. 
3. Klikněte na tlačítko **upravit** v horní části okna.
4. Klikněte na tlačítko **přizpůsobení uživatelského rozhraní stránky**.
5. Klikněte na tlačítko **stránku pro přihlášení místním účtem**.
6. Klikněte na tlačítko **e-mailovou adresu** v **název** sloupci **atributy registrace** oddílu.
7. Přepnout **vyžadovat ověření** umožňuje **ne**.
8. Klikněte na tlačítko **OK** v dolní části, dokud se nedostanete **upravit zásadu** okno.
9. V horní části okna klikněte na **Uložit**. Je to!

> [!NOTE]
> Zakázání ověření e-mailu v procesu registrace může vést k zasílání nevyžádané pošty. Pokud zakážete výchozí hodnotu, doporučujeme přidat vlastní ověřovacího systému.
> 
>