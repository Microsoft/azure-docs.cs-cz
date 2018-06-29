---
title: Kontrolujte přístup pomocí služby Azure AD přístup recenze | Microsoft Docs
description: Zjistěte, jak kontrolovat přístup pomocí služby Azure Active Directory přístup recenze.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: compliance-reports
ms.date: 09/19/2017
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: e6a109e499395e09be27770a510427038872da38
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37085119"
---
# <a name="review-access-with-azure-ad-access-reviews"></a>Kontrolujte přístup s Azure AD přístup recenze

Azure Active Directory (Azure AD) zjednodušuje podnikům správu přístup k aplikacím a členů skupin ve službě Azure AD a zkontroluje jiných služeb Microsoft Online Services s funkci přístup. Možná jste dostali e-mailu od společnosti Microsoft, který se zeptá, chcete-li zkontrolovat přístup pro členy skupiny nebo uživatelé s přístupem k aplikaci. 

## <a name="open-an-access-review"></a>Otevřete kontrola přístupu

Pokud chcete zobrazit recenze čekající přístup, vyberte odkaz v e-mailu. Pokud nemáte e-mailu, můžete vyhledat recenze přístup pomocí následujících kroků:

1. Přihlaste se [přístupový panel Azure AD](https://myapps.microsoft.com).

2. V pravém horním rohu stránky, která zobrazuje název a výchozí organizace vyberte symbol uživatele. Pokud je uveden více než jedné organizace, vyberte v organizaci, která požadovaná kontrola přístupu.

3. Pokud na dlaždici s názvem bez přípony **přístup recenze** je na pravé straně stránky, vyberte ho. Pokud dlaždice není zobrazena, neexistují žádné recenze přístup k provedení pro dané organizace a v tuto chvíli není vyžadována žádná akce.

## <a name="fill-out-an-access-review"></a>Vyplňte kontrola přístupu

Když vyberete ze seznamu Kontrola přístupu, zobrazí se jména uživatelů, kteří potřebují ke kontrole. Může se zobrazit pouze jeden název – vlastní – Pokud se požadavek ke kontrole vlastní přístup.

Pro každý řádek v seznamu můžete rozhodnout, zda schválit nebo zamítnout přístup uživatele. Vyberte řádek a vyberte, zda chcete schválit nebo zamítnout. (Pokud neznáte uživatele, můžete určit, že, příliš.)

Kontrolorovi může vyžadovat, že zadáte odůvodnění schválení nepřetržitý přístup nebo členství ve skupině.

## <a name="next-steps"></a>Další postup

Odepření přístupu uživatele se neodebere okamžitě. Může být odebrán až po dokončení kontrola nebo když se správce zastaví kontrola. Pokud chcete změnit odpověď a schválit dříve odepření uživatele nebo odepřít dříve schválené uživatele, vyberte řádek, reset odpověď a vyberte novou odpověď. Tento krok můžete provést, dokud nebude dokončeno kontrola přístupu.



