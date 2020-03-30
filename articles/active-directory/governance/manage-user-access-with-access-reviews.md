---
title: Správa přístupu uživatelů pomocí kontrol přístupu – Azure AD
description: Zjistěte, jak spravovat přístup uživatelů jako členství ve skupině nebo přiřazení k aplikaci s využitím kontrol přístupu Azure Active Directory.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c307b349144974a4d38f937feeebb98f369d047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932403"
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Správa přístupu uživatelů pomocí kontrol přístupu Azure AD

S Azure Active Directory (Azure AD) můžete snadno zajistit, že uživatelé mají odpovídající přístup. Požádejte přímo uživatele nebo pracovníka s rozhodovací pravomocí o účast při kontrole přístupu a znovu certifikujte (nebo potvrďte) přístup uživatelů. Revidující se mohou vyjádřit k nutnosti dalšího přístupu pro jednotlivé uživatele na základě návrhů z Azure AD. Po dokončení kontroly přístupu můžete provést změny a ukončit přístup uživatelů, kteří ho už nepotřebují.

> [!NOTE]
> Pokud chcete kontrolovat přístup jenom u uživatelů typu host a nekontrolovat všechny typy uživatelského přístupu, přečtěte si téma věnované [správě přístupu uživatelů typu host s využitím kontroly přístupu](manage-guest-access-with-access-reviews.md). Pokud chcete kontrolovat členství uživatelů v rolích pro správu, jako je například globální správce, přečtěte si téma věnované [spuštění kontroly přístupu v Azure AD Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md).

## <a name="prerequisites"></a>Požadavky

- Azure AD Premium P2

Další informace naleznete v tématu [Licenční požadavky](access-reviews-overview.md#license-requirements).

## <a name="create-and-perform-an-access-review"></a>Vytvoření a provedení kontroly přístupu

V rámci kontroly přístupu může revidujícími být mít jednoho nebo několik uživatelů jako revidující.  

1. Vyberte skupinu ve službě Azure AD, která má několik členů. Případně vyberte aplikaci připojenou ke službě Azure AD, který má přiřazených několik uživatelů. 

2. Rozhodněte, jestli má každý uživatel kontrolovat svůj vlastní přístup, nebo jestli má jeden nebo několik uživatelů kontrolovat přístup všech.

3. Jako globální správce nebo správce uživatelů přejděte na [stránku Zásad správného řízení identit .](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)

4. Vytvořte kontrolu přístupu. Další informace naleznete [v tématu Vytvoření kontroly přístupu skupin nebo aplikací](create-access-review.md).

5. Po spuštění kontroly přístupu požádejte recenzenty, aby nám poskytli vstup. Ve výchozím nastavení každý obdrží e-mail z Azure AD s odkazem na přístupový panel, kde [kontrolují přístup ke skupinám nebo aplikacím](perform-access-review.md).

6. Pokud revidující neposkytli informace, můžete požádat službu Azure AD, aby jim odeslala připomenutí. Ve výchozím nastavení Azure AD automaticky pošle připomenutí revidujícím, kteří ještě neodpověděli, po uplynutí poloviny času.

7. Jakmile revidující poskytnou potřebné informace, zastavte kontrolu přístupu a proveďte příslušné změny. Další informace naleznete [v tématu Complete a access review of groups or applications](complete-access-review.md).


## <a name="next-steps"></a>Další kroky

[Vytvoření kontroly přístupu skupin nebo aplikací](create-access-review.md)




