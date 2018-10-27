---
title: Odstranit adresář tenanta služby Azure Active Directory | Dokumentace Microsoftu
description: Vysvětluje, jak připravit odstranění adresáře tenanta služby Azure AD
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 06/13/2018
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: ab752799c9e64e47abe0200a26215f9e01c489f6
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139682"
---
# <a name="delete-an-azure-active-directory-tenant"></a>Odstranit tenanta služby Azure Active Directory
Při odstranění tenanta se také odstraní všechny prostředky, které jsou obsaženy v tenantovi. Je třeba připravit klienta minimalizací její přidružené prostředky před odstraněním. Jenom globální správce Azure Active Directory (Azure AD) můžete odstranit tenanta služby Azure AD z portálu.

## <a name="prepare-the-tenant-for-deletion"></a>Příprava tenanta pro odstranění

Nelze odstranit tenanta v Azure AD, dokud nebude úspěšný několik kontrol. Tyto kontroly snížit riziko, že odstranění tenanta negativně ovlivní uživatele přístup, jako je například možnost přihlásit k Office 365 nebo přístup k prostředkům v Azure. Například pokud klient přidružený k odběru se neúmyslně odstraní, pak nelze uživatelé prostředky Azure pro dané předplatné. Následující příklad vysvětluje podmínky, které jsou kontrolovány:

* V tenantovi, s výjimkou jeden globální správce, který chcete odstranit tenanta může být žádní uživatelé. Před odstraněním tenanta, musí se odstranit všechny uživatele. Pokud uživatelé používají synchronizaci z místních, synchronizace musí být vypnutá a uživatele musí být odstraněny v tenantovi cloudu pomocí webu Azure portal nebo rutin Azure Powershellu. 
* Může existovat žádné aplikace v tenantovi. Všechny aplikace musí odebrat, aby se Dal odstranit tenanta.
* Může existovat žádní poskytovatelé služby Multi-Factor authentication propojené do tenanta.
* Může být žádná předplatná pro všechny Microsoft Online Services, jako je například Microsoft Azure, Office 365 nebo Azure AD Premium přidružené k tenantovi. Například pokud se výchozí tenanta vytvořil pro vás v Azure, nelze odstranit tohoto tenanta Pokud vaše předplatné Azure se stále závisí na tohoto tenanta pro ověřování. Podobně nelze odstranit tenanta, pokud jiný uživatel k němu má přidružené předplatné. 

## <a name="delete-an-azure-ad-tenant"></a>Odstranit tenanta služby Azure AD

1. Přihlaste se k [centrum pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který je globální správce pro tenanta.

2. Vyberte **Azure Active Directory**.

3. Přepnutí na tenanta chcete odstranit.
  
  ![odstranit adresář](./media/directory-delete-howto/delete-directory-command.png)

4. Vyberte **odstranění adresáře**.
  
  ![odstranit adresář](./media/directory-delete-howto/delete-directory-list.png)

5. Pokud váš tenant nepředává jeden nebo více kontroly, že součástí odkaz na další informace o tom, jak předat. Po úspěšných všechny kontroly vyberte **odstranit** proces dokončete.

## <a name="i-have-an-expired-subscription-but-i-cant-delete-the-tenant"></a>Mám předplatné vypršelo, ale nemůžu odstranit tenanta

Při konfiguraci vašeho tenanta Azure Active Directory, může také aktivaci na základě licence předplatných pro vaši organizaci, jako je Azure Active Directory Premium P2, Office 365 Business Premium nebo Enterprise Mobility + Security E5. Těchto předplatných blokovat odstranění adresáře, dokud se plně odstraní, aby se zabránilo nechtěnému úniku. Předplatná musí být v **zajištění zrušeno** stavu, povolíte odstranění tenanta. **Konec platnosti** nebo **zrušeno** předplatné přesune **zakázané** stavu a finální fáze je **Deprovisoned** stavu. 

Co očekávat, když vyprší platnost zkušební verze předplatného Office 365, (nezahrnuje placené partnera/CSP, smlouvy Enterprise nebo multilicenčního programu) najdete v následující tabulce. Další informace o Office 365 uchování a předplatné životní cyklus dat, naleznete v tématu [co se stane s mými daty a přístupem při ukončení Moje Office 365 pro firmy předplatné?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Stav předplatného | Data | Přístup k datům
----- | ----- | -----
Aktivní (30denní zkušební verze)  | Data dostupná pro všechny    | <li>Uživatelé mají normální přístup k Office 365 souborech nebo aplikacích<li>Správci mají normální přístup k centru pro správu Office 365 a prostředky 
Vypršela platnost (30 dnů)   | Data dostupná pro všechny    | <li>Uživatelé mají normální přístup k Office 365 souborech nebo aplikacích<li>Správci mají normální přístup k centru pro správu Office 365 a prostředky
Zakázáno (30 dnů) | Data dostupná pouze správci  | <li>Uživatelé nemají přístup k Office 365 souborech nebo aplikacích<li>Správci můžou přístup k centru pro správu Office 365, ale nelze přiřadit licence nebo aktualizace uživatelů
Zruší (30 dnů po zakázáno) | Odstranění dat (automaticky odstraněn, pokud žádné služby se používá) | <li>Uživatelé nemají přístup k Office 365 souborech nebo aplikacích<li>Správci můžou přístup k centru pro správu Office 365 si kupovat a spravovat další předplatná 

Můžete vložit do předplatného **Deprovisoned** stavu odstraníme za 3 dny. pomocí Microsoft Store pro firmy centra pro správu. Tato funkce už brzy bude k centru pro správu Office 365.

1. Přihlaste se k [Microsoft Store pro firmy centra pro správu](https://businessstore.microsoft.com/manage/) pomocí účtu, který je globálním správcem tenanta. Pokud se pokoušíte odstranit tenanta "Contoso", který má počáteční výchozí doménu contoso.onmicrosoft.com, přihlásit se hlavní název uživatele, jako admin@contoso.onmicrosoft.com.

2. Přejděte **spravovat** kartě a vyberte **produktů a služeb**, zvolte předplatné, které chcete zrušit. Po kliknutí na **zrušit**, aktualizujte stránku.
  
  ![Odstranit odkaz pro odstranění předplatného](./media/directory-delete-howto/delete-command.png)
  
3. Vyberte **odstranit** k odstranění předplatného a přijali podmínky a ujednání. Všechna data budou trvale odstraněny do tří dnů. Předplatné období třídenní můžete znovu aktivovat, pokud změníte své rozhodnutí.
  
  ![podmínky a ujednání](./media/directory-delete-howto/delete-terms.png)

4. Nyní došlo ke změně stavu odběru, předplatné je označená k odstranění. Zadá předplatné **zajištění zrušeno** stavu 72 hodin.

5. Po smazali jste předplatné ve vašem tenantovi a po uplynutí 72 hodin, se můžete přihlásit zpátky do centra pro správu Azure AD znovu a že by mělo být požadované nic a žádná předplatná, která blokují odstranění vašeho tenanta. Je třeba mít tenanta Azure AD se úspěšně odstranila.
  
  ![Předejte vrácení předplatného na obrazovce odstranění](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="next-steps"></a>Další postup
[Dokumentace ke službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
