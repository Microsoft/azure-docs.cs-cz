---
title: Odstranění adresáře Azure AD – Služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Vysvětluje, jak připravit adresář Azure AD k odstranění, včetně samoobslužných adresářů
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
ms.reviewer: addimitu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47a60ed44ddf057ef983f8f76f23fd784bc3efd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961819"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Odstranění adresáře ve službě Azure Active Directory

Při odstranění adresáře Azure AD jsou odstraněny také všechny prostředky, které jsou obsaženy v adresáři. Před odstraněním připravte organizaci minimalizací přidružených prostředků. Pouze globální správce služby Azure Active Directory (Azure AD) může odstranit adresář Azure AD z portálu.

## <a name="prepare-the-directory"></a>Příprava adresáře

Adresář ve službě Azure AD nelze odstranit, dokud neprojde několika kontrolami. Tyto kontroly snižují riziko, že odstranění adresáře Azure AD negativně ovlivní přístup uživatelů, jako je například možnost přihlášení k Office 365 nebo přístup k prostředkům v Azure. Například pokud adresář přidružený k předplatnému je neúmyslně odstraněn, pak uživatelé nemají přístup k prostředkům Azure pro toto předplatné. Kontrolují se následující podmínky:

* V adresáři nemohou být žádní uživatelé kromě jednoho globálního správce, který má adresář odstranit. Všichni ostatní uživatelé musí být před odstraněním adresáře odstraněni. Pokud jsou uživatelé synchronizováni z místního prostředí, musí být synchronizace nejprve vypnutá a uživatelé musí být odstraněni v cloudovém adresáři pomocí webu Azure Portal nebo rutin Azure PowerShell.
* V adresáři nesmí být žádné aplikace. Všechny aplikace musí být odebrány před odstraněním adresáře.
* S adresářem nemohou být propojeni žádní zprostředkovatelé vícefaktorového ověřování.
* K adresáři nesmí být přidruženo žádné předplatné online služby Microsoft, jako jsou Microsoft Azure, Office 365 nebo Azure AD Premium. Například pokud byl váš výchozí adresář vytvořený v Azure, nemůžete ho odstranit, pokud stále slouží k ověřování přihlášení k předplatnému služby Azure. Podobně nelze odstranit adresář, pokud k němu má přidružené předplatné jiný uživatel.

## <a name="delete-the-directory"></a>Odstranění adresáře

1. Přihlaste se do [Centra pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který je globálním správcem pro vaši organizaci.

2. Vyberte **Azure Active Directory**.

3. Přepněte do adresáře, který chcete odstranit.
  
   ![Potvrzení organizace před odstraněním](./media/directory-delete-howto/delete-directory-command.png)

4. Vyberte **odstranit adresář**.
  
   ![výběr příkazu pro odstranění organizace](./media/directory-delete-howto/delete-directory-list.png)

5. Pokud adresář neprojde jednou nebo více kontrolami, máte k dispozici odkaz na další informace o tom, jak předat. Po předání všech kontrol dokončete proces výběrem možnosti **Odstranit.**

## <a name="if-you-cant-delete-the-directory"></a>Pokud se vám nepodaří odstranit adresář

Když jste nakonfigurovali adresář Azure AD, je možné, že jste aktivovali také předplatná založená na licencích pro vaši organizaci, jako je Azure AD Premium P2, Office 365 Business Premium nebo Enterprise Mobility + Security E5. Chcete-li zabránit náhodné ztrátě dat, nelze odstranit adresář, dokud odběry jsou plně odstraněny. Odběry musí být ve stavu **DeProvisioned** povolit odstranění adresáře. Předplatné **s ukončenou nebo** **zrušenou položkou** se přesune do stavu **Zakázáno** a poslední fází je stav **Zrušení zřízení.**

Co můžete očekávat při vypršení zkušební hospo- vypršení předplatného Office 365 (bez placeného partnera/csp, smlouvy Enterprise nebo multilicenčního programu), najdete v následující tabulce. Další informace o uchovávání dat Office 365 a životním cyklu předplatného najdete v tématu [Co se stane s mými daty a přístupem po ukončení předplatného Office 365 pro firmy?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3) 

Stav předplatného | Data | Přístup k údajům
----- | ----- | -----
Aktivní (30 dní pro zkušební verzi) | Data přístupná všem | Uživatelé mají normální přístup k souborům Office 365 nebo aplikacím<br>Správci mají normální přístup k Centru pro správu Microsoftu 365 a k prostředkům 
Platnost vypršela (30 dní) | Data přístupná všem| Uživatelé mají normální přístup k souborům Office 365 nebo aplikacím<br>Správci mají normální přístup k Centru pro správu Microsoftu 365 a k prostředkům
Zakázáno (30 dní) | Data přístupná pouze pro správce | Uživatelé nemají přístup k souborům Office 365 nebo aplikacím<br>Správci mají přístup k Centru pro správu Microsoftu 365, ale nemohou uživatelům přiřazovat licence ani je aktualizovat.
Zrušení zřízení (30 dní po zakázání) | Data byla smazána (automaticky odstraněna, pokud nejsou používány žádné jiné služby) | Uživatelé nemají přístup k souborům Office 365 nebo aplikacím<br>Správci mají přístup k Centru pro správu Microsoftu 365 a zakoupí a spravují další předplatná.

## <a name="delete-a-subscription"></a>Odstranění předplatného

Předplatné můžete vložit do stavu **Deprovisioned,** který má být odstraněn za tři dny pomocí Centra pro správu Microsoftu 365.

1. Přihlaste se do [Centra pro správu Microsoftu 365](https://admin.microsoft.com) pomocí účtu, který je globálním správcem ve vaší organizaci. Pokud se pokoušíte odstranit adresář Contoso, který má výchozí výchozí doménu contoso.onmicrosoft.com, admin@contoso.onmicrosoft.compřihlaste se pomocí hlavního názvu domény, například .

2. Prohlédněte si náhled nového Centra pro správu Microsoftu 365 tím, že se ujistíte, že je zapnutý přepínač **Vyzkoušet nové centrum pro správu.**

   ![Náhled nového prostředí Centra pro správu M365](./media/directory-delete-howto/preview-toggle.png)

3. Jakmile je nové Centrum pro správu povoleno, musíte předplatné před odstraněním zrušit. Vyberte **Fakturace** a vyberte **Produkty & služby**, pak u předplatného, které chcete zrušit, vyberte **Zrušit předplatné.** Budete převedeni na stránku zpětné vazby.

   ![Výběr předplatného, které chcete zrušit.](./media/directory-delete-howto/cancel-choose-subscription.png)

4. Vyplňte formulář pro zpětnou vazbu a výběrem **možnosti Zrušit předplatné** předplatné zrušíte.

   ![Příkaz Zrušit ve verzi Preview předplatného](./media/directory-delete-howto/cancel-command.png)

5. Nyní můžete odstranit předplatné. U předplatného, které chcete odstranit, vyberte **Odstranit.** Pokud nemůžete najít předplatné na stránce **Produkty & služby,** ujistěte se, že máte **stav předplatného** nastavený na **vše**.

   ![Odstranit odkaz pro odstranění předplatného](./media/directory-delete-howto/delete-command.png)

6. Chcete-li předplatné odstranit, vyberte **odstranit odběr** a přijměte podmínky. Všechna data jsou trvale smazána do tří dnů. Pokud si to rozmyslíte, můžete předplatné během třídenního období [znovu aktivovat.](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide)
  
   ![pečlivě si přečtěte podmínky](./media/directory-delete-howto/delete-terms.png)

7. Nyní se změnil stav předplatného a odběr je označen k odstranění. Předplatné přejde do stavu **Deprovisioned** o 72 hodin později.

8. Po odstranění předplatného ve vašem adresáři a 72 hodin uplynulo, můžete se přihlásit zpět do Centra pro správu Azure AD znovu a měla by existovat žádná požadovaná akce a žádné odběry blokování odstranění adresáře. Měli byste být schopni úspěšně odstranit adresář Azure AD.
  
   ![kontrola předplatného na obrazovce odstranění](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Mám zkušební předplatné, které blokuje odstranění

Existují [samoobslužné registrační produkty,](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) jako je Microsoft Power BI, Rights Management Services, Microsoft Power Apps nebo Dynamics 365, jednotliví uživatelé se můžou zaregistrovat přes Office 365, což také vytvoří uživatele typu Host pro ověřování v adresáři Azure AD. Tyto samoobslužné produkty blokují odstranění adresářů, dokud nejsou zcela odstraněny z adresáře, aby nedošlo ke ztrátě dat. Mohou být odstraněny pouze správceazure AD bez ohledu na to, zda se uživatel zaregistroval jednotlivě nebo mu byl přiřazen produkt.

Existují dva typy samoobslužných registračních produktů v tom, jak jsou přiřazeny: 

* Přiřazení na úrovni organizace: Správce Azure AD přiřadí produkt celé organizaci a uživatel může službu aktivně používat s tímto přiřazením na úrovni organizace, i když není licencován jednotlivě.
* Přiřazení na úrovni uživatele: Individuální uživatel během samoobslužné registrace v podstatě přiřadí produkt sobě bez správce. Jakmile se organizace stane správou správce (viz [převzetí nespravovaného adresáře správcem](domains-admin-takeover.md), může správce produkt přímo přiřadit uživatelům bez samoobslužné registrace.  

Když začnete odstraňovat samoobslužný registrační produkt, akce trvale odstraní data a odebere veškerý přístup uživatelů ke službě. Každému uživateli, kterému byla nabídka přiřazena jednotlivě nebo na úrovni organizace, je pak zablokováno přihlášení nebo přístup k existujícím datům. Pokud chcete zabránit ztrátě dat pomocí samoobslužného registračního produktu, jako jsou [řídicí panely Microsoft Power BI](https://docs.microsoft.com/power-bi/service-export-to-pbix) nebo konfigurace zásad [Služby správy práv](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy), ujistěte se, že jsou data zálohována a uložena jinde.

Další informace o aktuálně dostupných samoobslužných registračních produktech a službách naleznete v [tématu Dostupné samoobslužné programy](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs).

Co můžete očekávat při vypršení zkušební hospo- vypršení předplatného Office 365 (bez placeného partnera/csp, smlouvy Enterprise nebo multilicenčního programu), najdete v následující tabulce. Další informace o uchovávání dat Office 365 a životním cyklu předplatného najdete v tématu [Co se stane s mými daty a přístupem po ukončení předplatného Office 365 pro firmy?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide)

Stav produktu | Data | Přístup k údajům
------------- | ---- | --------------
Aktivní (30 dní pro zkušební verzi) | Data přístupná všem | Uživatelé mají běžný přístup k samoobslužným registračním produktům, souborům nebo aplikacím<br>Správci mají normální přístup k Centru pro správu Microsoftu 365 a k prostředkům
Odstranění | Odstraněná data | Uživatelé nemají přístup k samoobslužným registračním produktům, souborům nebo aplikacím.<br>Správci mají přístup k Centru pro správu Microsoftu 365 a zakoupí a spravují další předplatná.

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Jak můžu odstranit samoobslužný registrační produkt na webu Azure Portal?

Můžete umístit samoobslužný registrační produkt, jako je Microsoft Power BI nebo Azure Rights Management Services do stavu **Delete,** který se okamžitě odstraní na portálu Azure AD.

1. Přihlaste se do [Centra pro správu Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) pomocí účtu, který je globálním správcem v organizaci. Pokud se pokoušíte odstranit adresář Contoso, který má výchozí výchozí contoso.onmicrosoft.com domény, admin@contoso.onmicrosoft.compřihlaste se pomocí hlavního názvu domény, například .

2. Vyberte **Licence**a pak vyberte **samoobslužné registrační produkty**. Všechny samoobslužné registrační produkty můžete zobrazit odděleně od předplatných založených na sedadlech. Vyberte produkt, který chcete trvale odstranit. Tady je příklad v Microsoft Power BI:

    ![uživatelské jméno je chybně zadáno nebo nebylo nalezeno](./media/directory-delete-howto/licenses-page.png)

3. Výběrem **možnosti Odstranit** produkt odstraníte a přijmete podmínky, že data jsou okamžitě a neodvolatelně odstraněna. Tato akce odstranění odebere všechny uživatele a odebere přístup organizace k produktu. Chcete-li s odstraněním přejít vpřed, klepněte na tlačítko Ano.  

    ![uživatelské jméno je chybně zadáno nebo nebylo nalezeno](./media/directory-delete-howto/delete-product.png)

4. Pokud vyberete **možnost Ano**, bude zahájeno odstranění samoobslužného produktu. K dispozici je oznámení, které vám řekne o probíhajícím odstranění.  

    ![uživatelské jméno je chybně zadáno nebo nebylo nalezeno](./media/directory-delete-howto/progress-message.png)

5. Nyní se stav samoobslužného registračního produktu změnil na **Odstraněno**. Při aktualizaci stránky by měl být produkt odebrán ze stránky **Samoobslužné registrační produkty.**  

    ![uživatelské jméno je chybně zadáno nebo nebylo nalezeno](./media/directory-delete-howto/product-deleted.png)

6. Po odstranění všech produktů se můžete znovu přihlásit do Centra pro správu Azure AD a neměla by existovat žádná požadovaná akce a žádné produkty, které by blokovaly odstranění adresáře. Měli byste být schopni úspěšně odstranit adresář Azure AD.

    ![uživatelské jméno je chybně zadáno nebo nebylo nalezeno](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Další kroky

[Dokumentace služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
