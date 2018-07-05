---
title: Kontrola přístupu Azure AD pomocí kontrol přístupu | Dokumentace Microsoftu
description: Zjistěte, jak kontrolovat přístup pomocí kontrol přístupu Azure Active Directory.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance-reports
ms.date: 09/19/2017
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 52d832de10de93c5bd4b91df821f96845b3b21be
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446635"
---
# <a name="review-access-with-azure-ad-access-reviews"></a>Zkontrolovat přístup s Azure AD kontroly přístupu

Azure Active Directory (Azure AD) zjednodušuje podnikům správu přístupu k aplikacím a členy skupin ve službě Azure AD a dalších Microsoft Online Services s funkci s názvem přístup kontroly. Možná jste dostali e-mailu od Microsoftu, která vás vyzve k kontrola přístupu pro členy skupiny nebo uživatelé s přístupem k aplikaci. 

## <a name="open-an-access-review"></a>Otevřete kontroly přístupu

Kontroly přístupu zobrazíte výběrem odkazu v e-mailu. Pokud nemáte k dispozici v e-mailu, můžete vyhledat kontrol přístupu pomocí následujících kroků:

1. Přihlaste se [přístupový panel služby Azure AD](https://myapps.microsoft.com).

2. Vyberte symbol uživatele v pravém horním rohu stránky, která zobrazuje název a výchozí organizace. Pokud je uveden více než jedné organizace, vyberte organizace, která požadované kontroly přístupu.

3. Pokud dlaždice s popiskem **kontrol přístupu** je na pravé straně stránky vyberte ji. Pokud není zobrazena na dlaždici, neexistují žádné kontroly přístupu k provedení pro danou organizaci a v tuto chvíli není potřeba žádná akce.

## <a name="fill-out-an-access-review"></a>Vyplňte kontroly přístupu

Když vyberete kontroly přístupu ze seznamu, uvidíte jména uživatelů, kteří potřebují ke kontrole. Může se zobrazit pouze jeden název – vlastní – Pokud se požadavek na kontrolovat svůj vlastní přístup.

Pro každý řádek v seznamu můžete rozhodnout, zda Schvalte nebo zamítněte přístup daného uživatele. Vyberte řádek a vyberte, jestli chcete schválit nebo zamítnout. (Pokud si nejste jisti, že uživatel, můžete určit, že příliš.)

Revidující můžou vyžadovat, abyste zadali odůvodnění pro schválení přístup nebo členství ve skupině.

## <a name="next-steps"></a>Další postup

Odepření přístupu uživatele odebrán okamžitě. Je možné odebrat, když je revize dokončena nebo když správce zastaví revizi. Pokud chcete změnit vaše odpověď a schválit dříve odepřených uživatelů nebo zamítnout dříve schválené uživatele, vyberte řádek, resetovat odpovědi a vyberte nová odpověď. Tento krok můžete provést, dokud se nedokončí kontroly přístupu.



