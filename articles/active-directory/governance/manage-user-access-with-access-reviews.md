---
title: Správa přístupu uživatelů pomocí kontrol přístupu Azure AD | Dokumentace Microsoftu
description: Zjistěte, jak spravovat přístup uživatelů jako členství ve skupině nebo přiřazení k aplikaci pomocí Azure Active Directory kontrol přístupu
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 656a1d387a345d9ffcc549c19a131de758e18c35
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57879986"
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Správa přístupu uživatelů pomocí kontrol přístupu Azure AD

S Azure Active Directory (Azure AD) můžete snadno zajistit, že uživatelé mají odpovídající přístup. Požádejte přímo uživatele nebo pracovníka s rozhodovací pravomocí o účast při kontrole přístupu a znovu certifikujte (nebo potvrďte) přístup uživatelů. Revidující se mohou vyjádřit k nutnosti dalšího přístupu pro jednotlivé uživatele na základě návrhů z Azure AD. Po dokončení kontroly přístupu můžete provést změny a ukončit přístup uživatelů, kteří ho už nepotřebují.

> [!NOTE]
> Pokud chcete kontrolovat přístup jenom u uživatelů typu host a nekontrolovat všechny typy uživatelského přístupu, přečtěte si téma věnované [správě přístupu uživatelů typu host s využitím kontroly přístupu](manage-guest-access-with-access-reviews.md). Pokud chcete kontrolovat členství uživatelů v rolích pro správu, jako je například globální správce, přečtěte si téma věnované [spuštění kontroly přístupu v Azure AD Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Požadavky 


Kontroly přístupu jsou k dispozici v edici Premium P2 služby Azure AD, která je součástí řešení Microsoft Enterprise Mobility + Security, E5. Další informace najdete v článku [Edice služby Azure Active Directory](../fundamentals/active-directory-whatis.md). Každý uživatel, který komunikuje s touto funkcí, včetně akcí jako vytváření kontroly, vyplnění kontroly nebo potvrzení přístupu, musí mít licenci. 

## <a name="create-and-perform-an-access-review"></a>Vytvoření a provedení kontroly přístupu

V rámci kontroly přístupu může revidujícími být mít jednoho nebo několik uživatelů jako revidující.  

1. Vyberte skupinu ve službě Azure AD, která má několik členů. Případně vyberte aplikaci připojenou ke službě Azure AD, který má přiřazených několik uživatelů. 

2. Rozhodněte, jestli má každý uživatel kontrolovat svůj vlastní přístup, nebo jestli má jeden nebo několik uživatelů kontrolovat přístup všech.

3. Jako globální správce nebo Správce uživatelů, přejděte [stránku kontrol přístupu](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

4. Vytvoření kontroly přístupu. Další informace najdete v tématu [vytvoření kontroly přístupu skupinám nebo aplikacím](create-access-review.md).

5. Když začne kontrola přístupu, požádejte revidující poskytnou potřebné informace. Ve výchozím nastavení, každá přijde e-mail od služby Azure AD s odkazem na panel přístupu, kde jsou [kontrolovat přístup skupinám nebo aplikacím](perform-access-review.md).

6. Pokud revidující neposkytli informace, můžete požádat službu Azure AD, aby jim odeslala připomenutí. Ve výchozím nastavení Azure AD automaticky pošle připomenutí revidujícím, kteří ještě neodpověděli, po uplynutí poloviny času.

7. Jakmile revidující poskytnou potřebné informace, zastavte kontrolu přístupu a proveďte příslušné změny. Další informace najdete v tématu [dokončení kontroly přístupu skupinám nebo aplikacím](complete-access-review.md).


## <a name="next-steps"></a>Další postup

[Vytvoření kontroly přístupu skupiny nebo aplikace](create-access-review.md)




