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
ms.openlocfilehash: c1bebe46832226e822d9eeb002cb555b72a1d7fa
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441603"
---
# <a name="azure-active-directory-b2c-disable-email-verification-during-consumer-sign-up"></a>Azure Active Directory B2C: Zakázání ověření e-mailu během registrace uživatelů
Při povolení služby Azure Active Directory (Azure AD) B2C dává možnost zaregistrovat do aplikace tak, že poskytuje e-mailovou adresu a vytvoření místního účtu příjemce. Azure AD B2C zajišťuje platné e-mailové adresy vyžadováním příjemci k ověření během procesu registrace. Zabrání také škodlivý automatizovaný proces generování falešné účty pro aplikace.

Někteří vývojáři aplikace chtít přeskočit ověření e-mailu během procesu registrace a místo toho použít později ověření e-mailovou adresu uživatele. Z toho důvodu můžete nakonfigurovat Azure AD B2C k zakázání ověření e-mailu. Tím se vytvoří hladší procesu registrace a vývojářům poskytuje flexibilitu pro odlišení příjemce, kteří ověření e-mailová adresa z tito příjemci, které ještě nebyly.

Zásady registrace mají ve výchozím nastavení zapnuté ověření e-mailu. Chcete-li vypnout, postupujte následovně:

1. [Přejděte do okna s funkcemi B2C na webu Azure portal pomocí těchto kroků](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Klikněte na tlačítko **zásady registrace** nebo **zásady registrace nebo přihlášení** v závislosti na tom, co jste nakonfigurovali pro registraci.
3. Klikněte na zásadu (například "B2C_1_SiUp") a otevřete ho. Klikněte na tlačítko **upravit** v horní části okna.
4. Klikněte na tlačítko **přizpůsobení uživatelského rozhraní stránky**.
5. Klikněte na tlačítko **stránku pro přihlášení místním účtem**.
6. Klikněte na tlačítko **e-mailovou adresu** v **název** sloupci **atributy registrace** oddílu.
7. Přepnout **vyžadovat ověření** umožňuje **ne**.
8. Klikněte na tlačítko **OK** v dolní části, dokud se nedostanete **upravit zásadu** okno.
9. Klikněte na tlačítko **Uložit** v horní části okna. Je to!

> [!NOTE]
> Zakázání ověření e-mailu v procesu registrace může vést k zasílání nevyžádané pošty. Pokud zakážete výchozí hodnotu, doporučujeme přidat vlastní ověřovacího systému.
> 
> 

Máme vždy otevřít na názory a návrhy! Pokud nemají nějaké problémy s tímto tématem nebo máte doporučení k vylepšení tohoto obsahu, uvítáme vaše zpětná vazba v dolní části stránky. Pro žádosti o funkce, přidejte je do [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
