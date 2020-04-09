---
title: Jak spravovat neaktivní uživatelské účty ve službě Azure AD | Dokumenty společnosti Microsoft
description: Informace o tom, jak zjistit a zpracovat uživatelské účty ve službě Azure AD, které jsou zastaralé
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/07/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56e44059268037cfd839fc7c877c5d6c972dead8
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886037"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>Postup: Správa neaktivních uživatelských účtů ve službě Azure AD

Ve velkých prostředích nejsou uživatelské účty vždy odstraněny, když zaměstnanci opustí organizaci. Jako správce IT chcete tyto zastaralé uživatelské účty rozpoznat a zpracovat, protože představují bezpečnostní riziko.

Tento článek vysvětluje metodu zpracování zastaralých uživatelských účtů ve službě Azure AD. 

## <a name="what-are-inactive-user-accounts"></a>Co jsou neaktivní uživatelské účty?

Neaktivní účty jsou uživatelské účty, které již členové vaší organizace nepotřebují k získání přístupu k vašim prostředkům. Jeden identifikátor klíče pro neaktivní účty je, že nebyly *použity na chvíli* k přihlášení do vašeho prostředí. Vzhledem k tomu, že neaktivní účty jsou vázány na aktivitu přihlášení, můžete použít časové razítko poslednípřihlášení, které bylo úspěšné k jejich zjištění. 

Výzvou této metody je definovat, co *na chvíli* znamená v případě vašeho prostředí. Uživatelé se například nemusí nějakou *dobu*přihlašovat k prostředí , protože jsou na dovolené. Při definování, co je vaše delta pro neaktivní uživatelské účty, je třeba faktor ve všech legitimních důvodů pro nepřihlášení do vašeho prostředí. V mnoha organizacích je rozdíl pro neaktivní uživatelské účty mezi 90 a 180 dny. 

Poslední úspěšné přihlášení poskytuje potenciální přehled o trvalé potřebě uživatele přístup k prostředkům.  Může pomoci určit, jestli je členství ve skupině nebo přístup k aplikacím stále potřeba nebo jestli by je mohl být odebrán. Pro externí správu uživatelů můžete pochopit, pokud externí uživatel je stále aktivní v rámci klienta nebo by měl být vyčištěn. 

    
## <a name="how-to-detect-inactive-user-accounts"></a>Jak zjistit neaktivní uživatelské účty

Neaktivní účty zjistíte vyhodnocením vlastnosti **lastSignInDateTime** vystavené typem prostředku **signInActivity** rozhraní **Microsoft Graph** API. Pomocí této vlastnosti můžete implementovat řešení pro následující scénáře:

- **Uživatelé podle názvu**: V tomto scénáři hledáte konkrétního uživatele podle názvu, který umožňuje vyhodnotit lastSignInDate:`https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **Uživatelé podle data**: V tomto scénáři požadujete seznam uživatelů s lastSignInDateTime před zadaným datem:`https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`






## <a name="what-you-need-to-know"></a>Co je potřeba vědět

V této části je uvedeno, co potřebujete vědět o vlastnosti lastSignInDateTime.

### <a name="how-can-i-access-this-property"></a>Jak se dostanu k této nemovitosti?

Vlastnost **lastSignInDateTime** je vystavena [typem prostředku signInActivity rozhraní](https://docs.microsoft.com/graph/api/resources/signinactivity?view=graph-rest-beta) [MICROSOFT Graph REST API](https://docs.microsoft.com/graph/overview?view=graph-rest-beta#whats-in-microsoft-graph).   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>Je vlastnost lastSignInDateTime dostupná prostřednictvím rutiny Get-AzureAdUser?

Ne.

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>Jakou edici Azure AD potřebuji pro přístup k vlastnosti?

K této vlastnosti můžete přistupovat ve všech edicích Azure AD.

### <a name="what-permission-do-i-need-to-read-the-property"></a>Jaké oprávnění potřebuji ke čtení vlastnosti?

Chcete-li si přečíst tuto vlastnost, musíte udělit následující práva: 

- AuditLogs.Read.All
- Organizace.Read.All  


### <a name="when-does-azure-ad-update-the-property"></a>Kdy Azure AD aktualizuje vlastnost?

Každé interaktivní přihlášení, které bylo úspěšné, má za následek aktualizaci podkladového úložiště dat. Úspěšné přihlášení se obvykle zobrazí v související sestavě přihlášení během 10 minut.
 

### <a name="what-does-a-blank-property-value-mean"></a>Co znamená prázdná hodnota vlastnosti?

Chcete-li generovat časové razítko lastSignInDateTime, potřebujete úspěšné přihlášení. Vzhledem k tomu, že vlastnost lastSignInDateTime je nová funkce, hodnota vlastnosti lastSignInDateTime může být prázdná, pokud:

- Poslední úspěšné přihlášení uživatele proběhlo před vydáním této funkce (1. prosince 2019).
- Ovlivněný uživatelský účet nebyl nikdy použit pro úspěšné přihlášení.

## <a name="next-steps"></a>Další kroky

* [Získání dat pomocí rozhraní API pro generování sestav Azure Active Directory s certifikáty](tutorial-access-api-with-certificates.md)
* [Odkaz na rozhraní API auditu](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Odkaz na rozhraní API sestavy přihlašovacích aktivit](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
