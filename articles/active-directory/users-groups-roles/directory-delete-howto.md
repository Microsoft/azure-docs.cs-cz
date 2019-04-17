---
title: Odstranit adresář služby Azure AD – Azure Active Directory | Dokumentace Microsoftu
description: Vysvětluje postup přípravy adresář Azure AD pro odstranění, včetně samoobslužných služeb adresářů
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91ac6b4530414850c52605bac8cb701aa2b877d4
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607292"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Odstranění adresáře v Azure Active Directory

Při odstranění adresáře služby Azure AD se také odstraní všechny prostředky, které jsou obsaženy v adresáři. Příprava organizace minimalizací její přidružené prostředky před odstraněním. Jenom globální správce Azure Active Directory (Azure AD) můžete odstranit adresář služby Azure AD z portálu.

## <a name="prepare-the-directory"></a>Příprava adresáře

Nelze odstranit adresář, v Azure AD, dokud nebude úspěšný několik kontrol. Tyto kontroly snížit riziko, že odstranění adresáře služby Azure AD negativně ovlivní uživatele přístup, jako je například možnost přihlásit k Office 365 nebo přístup k prostředkům v Azure. Například pokud je neúmyslně odstranil adresář, spojené s předplatným, by uživatelé nelze přístup k prostředků Azure pro dané předplatné. Kontrolují se následující podmínky:

* V adresáři s výjimkou jeden globální správce, který je k odstranění adresáře, může být žádní uživatelé. Všichni ostatní uživatelé musí být před odstraněním adresáře odstraněni. Pokud uživatelé používají synchronizaci z místních, pak synchronizace nejprve nutno vypnout a uživatelé musí být odstraněny v cloudu pomocí webu Azure portal nebo rutin Azure Powershellu.
* V adresáři nesmí být žádné aplikace. Všechny aplikace musí odebrat, aby se Dal odstranit adresář.
* Může existovat žádní poskytovatelé služby Multi-Factor authentication s adresářem propojení.
* K adresáři nesmí být přidruženo žádné předplatné online služby Microsoft, jako jsou Microsoft Azure, Office 365 nebo Azure AD Premium. Například pokud byl váš výchozí adresář vytvořený v Azure, nemůžete ho odstranit, pokud stále slouží k ověřování přihlášení k předplatnému služby Azure. Podobně nelze odstranit adresář, pokud k němu má přidružené předplatné jiný uživatel.

## <a name="delete-the-directory"></a>Odstranit adresář

1. Přihlaste se k [centrum pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který má oprávnění globálního správce pro vaši organizaci.

2. Vyberte **Azure Active Directory**.

3. Přejděte do adresáře, který chcete odstranit.
  
   ![Potvrďte organizaci před odstraněním](./media/directory-delete-howto/delete-directory-command.png)

4. Vyberte **odstranění adresáře**.
  
   ![Klepněte na příkaz Odstranit organizaci](./media/directory-delete-howto/delete-directory-list.png)

5. Pokud adresáři nepředává jeden nebo více kontroly, jste už k dispozici odkaz na další informace o tom, jak předat. Po úspěšných všechny kontroly vyberte **odstranit** proces dokončete.

## <a name="if-you-cant-delete-the-directory"></a>Pokud nemůže odstranit adresář

Při konfiguraci adresáře služby Azure AD může také aktivaci na základě licence předplatných pro vaši organizaci, jako je Azure AD Premium P2, Office 365 Business Premium nebo Enterprise Mobility + Security E5. Aby nedošlo ke ztrátě dat, nelze odstranit adresář, dokud předplatná jsou plně odstraněn. Předplatná musí být v **zajištění zrušeno** stavu, povolíte odstranění adresáře. **Konec platnosti** nebo **zrušeno** předplatné přesune **zakázané** stavu a finální fáze je **zajištění zrušeno** stavu.

Co očekávat, když vyprší platnost zkušební verze předplatného Office 365, (nezahrnuje placené partnera/CSP, smlouvy Enterprise nebo multilicenčního programu) najdete v následující tabulce. Další informace o Office 365 uchování a předplatné životní cyklus dat, naleznete v tématu [co se stane s mými daty a přístupem při ukončení Moje Office 365 pro firmy předplatné?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Stav předplatného | Data | Přístup k datům
----- | ----- | -----
Aktivní (30denní zkušební verze) | Data dostupná pro všechny | Uživatelé mají normální přístup k Office 365 souborech nebo aplikacích<br>Správci mají normální přístup k centru pro správu služeb Microsoft 365 a prostředky 
Vypršela platnost (30 dnů) | Data dostupná pro všechny| Uživatelé mají normální přístup k Office 365 souborech nebo aplikacích<br>Správci mají normální přístup k centru pro správu služeb Microsoft 365 a prostředky
Zakázáno (30 dnů) | Data dostupná pouze správci | Uživatelé nemají přístup k Office 365 souborech nebo aplikacích<br>Správci můžou přístup k centru pro správu služeb Microsoft 365, ale nelze přiřadit licence nebo aktualizace uživatelů
Zruší (30 dnů po zakázáno) | Odstranění dat (automaticky odstraněn, pokud žádné služby se používá) | Uživatelé nemají přístup k Office 365 souborech nebo aplikacích<br>Správci můžou přístup k centru pro správu služeb Microsoft 365 si kupovat a spravovat další předplatná

## <a name="delete-a-subscription"></a>Odstraní předplatné

Předplatné můžete umístit do stavu zajištění zrušeno odstranění do tří dnů pomocí centra pro správu Microsoftu 365.

1. Přihlaste se k [centra pro správu služeb Microsoft 365](https://admin.microsoft.com) pomocí účtu, který je globálním správcem ve vaší organizaci. Pokud se pokoušíte odstranit adresáře "Contoso", který má počáteční výchozí doménu contoso.onmicrosoft.com, přihlaste se pomocí UPN například admin@contoso.onmicrosoft.com.

2. Vyberte **fakturace** a vyberte **předplatná**, zvolte předplatné, které chcete zrušit. Po kliknutí na **zrušit**, aktualizujte stránku.
  
   ![Odstranit odkaz pro odstranění předplatného](./media/directory-delete-howto/delete-command.png)
  
3. Vyberte **odstranit** k odstranění předplatného a přijali podmínky a ujednání. Do tří dnů se trvale odstraní všechna data. Předplatné období třídenní můžete znovu aktivovat, pokud změníte své rozhodnutí.
  
   ![Přečtěte si pozorně podmínky a ujednání](./media/directory-delete-howto/delete-terms.png)

4. Nyní došlo ke změně stavu odběru, předplatné je označená k odstranění. Zadá předplatné **zajištění zrušeno** stavu 72 hodin.

5. Po smazali jste předplatné ve vašem adresáři a uplynulo 72 hodin, se můžete přihlásit zpátky do centra pro správu Azure AD znovu a že by mělo být žádné požadované akce a blokuje odstranění adresáře se žádná předplatná. Měli byste být schopni adresáře služby Azure AD se úspěšně odstranila.
  
   ![Předejte vrácení předplatného na obrazovce odstranění](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Mám zkušební předplatné, které blokuje odstranění

Existují [samoobslužné registrace produkty](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) jako Microsoft Power BI, Rights Management Services, Microsoft Powerapps nebo Dynamics 365, jednotlivé uživatele můžete zaregistrovat prostřednictvím Office 365, což zároveň vytvoří uživatele typu Host pro ověřování v adresáře služby Azure AD. Tyto produkty samoobslužné blokovat odstranění adresáře, dokud se plně odstraní z adresáře, aby nedošlo ke ztrátě dat. Můžete je odstranit pouze pomocí Správce služby Azure AD, zda uživatel zaregistrovaný samostatně nebo byla přiřazena produktu.

Existují dva typy produktů samoobslužné registrace v tom, jak jsou přiřazeny: 

* Přiřazení úrovní organizace: Správce Azure AD přiřadí produktu pro celou organizaci a uživatele můžete aktivně používat službu tohoto přiřazení na úrovni organizace i v případě, že nejsou licencované jednotlivě.
* Přiřazení úrovně uživatele: Jednotlivého uživatele během samoobslužnou registraci v podstatě přiřadí produktu sami bez správce. Jakmile bude organizace spravuje správce (naleznete v tématu [převzetí správce nespravovaného adresáře](domains-admin-takeover.md), správce přímo přiřadit produktu pro uživatele bez samoobslužné registrace.  

Při zahájení odstraňování samoobslužné registrace produktu akce trvale odstraní data a odebere veškerý přístup uživatele ke službě. Každý uživatel, který byl přiřazen nabídky jednotlivě nebo na úrovni organizace se pak blokuje přihlášením nebo přístupem k všechna existující data. Pokud chcete, aby se zabránilo ztrátě dat samoobslužné registrace produktu, jako jsou [Microsoft Power BI pomocí řídicích panelů](https://docs.microsoft.com/power-bi/service-export-to-pbix) nebo [konfigurace zásad služby Rights Management Services](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy), ujistěte se, že se data zálohují a uložit kamkoli.

Další informace o aktuálně k dispozici samoobslužné registrace produktech a službách najdete v tématu [dostupných programů samoobslužné](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs).

Co očekávat, když vyprší platnost zkušební verze předplatného Office 365, (nezahrnuje placené partnera/CSP, smlouvy Enterprise nebo multilicenčního programu) najdete v následující tabulce. Další informace o Office 365 uchování a předplatné životní cyklus dat, naleznete v tématu [co se stane s mými daty a přístupem při ukončení Moje Office 365 pro firmy předplatné?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide).

Stav produktu | Data | Přístup k datům
------------- | ---- | --------------
Aktivní (30denní zkušební verze) | Data dostupná pro všechny | Uživatelé mají normální přístup k samoobslužné registrace produktu, souborech nebo aplikacích<br>Správci mají normální přístup k centru pro správu služeb Microsoft 365 a prostředky
Odstraněno | Data se odstranila. | Uživatelé nemají přístup k samoobslužné registrace produktu, souborech nebo aplikacích<br>Správci můžou přístup k centru pro správu služeb Microsoft 365 si kupovat a spravovat další předplatná

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Jak mohu odstranit samoobslužné registrace produktu na webu Azure Portal?

Můžete vložit jako je Microsoft Power BI nebo služby Azure Rights Management Services do samoobslužné registrace produktu **odstranit** stav okamžitě odstranit na portálu Azure AD.

1. Přihlaste se k [centrum pro správu Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) pomocí účtu, který je globálním správcem v organizaci. Pokud se pokoušíte odstranit adresáře "Contoso", který má počáteční výchozí doménu contoso.onmicrosoft.com, přihlásit se hlavní název uživatele, jako admin@contoso.onmicrosoft.com.

2. Vyberte **licence**a pak vyberte **samoobslužné registrace produkty**. Zobrazí se všechny samoobslužné registrace produkty odděleně od odběry založené na pracovní stanici. Vyberte produkt, který chcete trvale odstranit. Tady je příklad v Microsoft Power BI:

    ![uživatelské jméno je zadáno chybně nebo nebyl nalezen](./media/directory-delete-howto/licenses-page.png)

3. Vyberte **odstranit** odstranění produktu a přijměte podmínky data odstraněna okamžitě a nevratně. Tato akce odstranění odebrat všechny uživatele a odebrat organizace k tomuto produktu přístup. Klikněte na tlačítko Ano. Pokud chcete pokračovat v odstranění.  

    ![uživatelské jméno je zadáno chybně nebo nebyl nalezen](./media/directory-delete-howto/delete-product.png)

4. Když vyberete **Ano**, aktivuje se odstranění produktu samoobslužné služby. Je oznámení, že zjistíte odstranění v průběhu.  

    ![uživatelské jméno je zadáno chybně nebo nebyl nalezen](./media/directory-delete-howto/progress-message.png)

5. Nyní stav samoobslužné registrace produktu změnil **odstraněné**. Po aktualizaci stránky produktu by měla být odebrány **samoobslužné registrace produkty** stránky.  

    ![uživatelské jméno je zadáno chybně nebo nebyl nalezen](./media/directory-delete-howto/product-deleted.png)

6. Po odstranění všech produktů znovu přihlásit zpátky do centra pro správu Azure AD a měl by existovat žádné požadované akce a blokuje odstranění adresáře se žádné produkty. Měli byste být schopni adresáře služby Azure AD se úspěšně odstranila.

    ![uživatelské jméno je zadáno chybně nebo nebyl nalezen](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Další postup

[Dokumentace ke službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
