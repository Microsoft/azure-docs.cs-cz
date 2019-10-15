---
title: Odstranění adresáře služby Azure AD – Azure Active Directory | Microsoft Docs
description: Vysvětluje, jak připravit adresář služby Azure AD k odstranění, včetně samoobslužných adresářů.
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
ms.openlocfilehash: e4fb2019b0298520d6bc2400e9fc2a2732664812
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329955"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Odstranění adresáře v Azure Active Directory

Při odstranění adresáře služby Azure AD se odstraní také všechny prostředky, které jsou obsaženy v adresáři. Připraví svoji organizaci minimalizací svých přidružených prostředků, než je odstraníte. Pouze globální správce služby Azure Active Directory (Azure AD) může z portálu odstranit adresář služby Azure AD.

## <a name="prepare-the-directory"></a>Příprava adresáře

Nemůžete odstranit adresář v Azure AD, dokud ho neprojde několik kontrol. Tato kontrola snižuje riziko, že odstranění adresáře Azure AD negativně ovlivní přístup uživatelů, jako je třeba možnost přihlásit se k Office 365 nebo získat přístup k prostředkům v Azure. Pokud se například adresář přidružený k předplatnému neúmyslně odstraní, uživatelé nebudou mít přístup k prostředkům Azure pro toto předplatné. Kontrolují se následující podmínky:

* V adresáři nesmí být žádní uživatelé s výjimkou jednoho globálního správce, který má odstranit adresář. Všichni ostatní uživatelé musí být před odstraněním adresáře odstraněni. Pokud jsou uživatelé synchronizováni z místního prostředí, musí být nejprve vypnuta synchronizace a uživatelé musí být odstraněni v adresáři cloudu pomocí rutin Azure Portal nebo Azure PowerShell.
* V adresáři nesmí být žádné aplikace. Aby bylo možné odstranit adresář, musí být všechny aplikace odebrány.
* K adresáři nemůžou být připojeni žádní zprostředkovatelé služby Multi-Factor Authentication.
* K adresáři nesmí být přidruženo žádné předplatné online služby Microsoft, jako jsou Microsoft Azure, Office 365 nebo Azure AD Premium. Například pokud byl váš výchozí adresář vytvořený v Azure, nemůžete ho odstranit, pokud stále slouží k ověřování přihlášení k předplatnému služby Azure. Podobně nelze odstranit adresář, pokud k němu má přidružené předplatné jiný uživatel.

## <a name="delete-the-directory"></a>Odstranit adresář

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který je globálním správcem vaší organizace.

2. Vyberte **Azure Active Directory**.

3. Přejděte do adresáře, který chcete odstranit.
  
   ![Před odstraněním potvrdit organizaci](./media/directory-delete-howto/delete-directory-command.png)

4. Vyberte **odstranit adresář**.
  
   ![Vyberte příkaz pro odstranění organizace.](./media/directory-delete-howto/delete-directory-list.png)

5. Pokud váš adresář neprojde jednou nebo více kontrolami, máte k dispozici odkaz na Další informace o tom, jak předat. Po předání všech kontrol vyberte **Odstranit** a proces dokončete.

## <a name="if-you-cant-delete-the-directory"></a>Pokud adresář nemůžete odstranit

Když jste nakonfigurovali adresář služby Azure AD, mohli jste taky aktivovat odběry na základě licencí pro vaši organizaci, jako je Azure AD Premium P2, Office 365 Business Premium nebo Enterprise Mobility + Security E5. Aby nedocházelo k náhodné ztrátě dat, nemůžete odstranit adresář, dokud nebudou předplatná zcela odstraněna. Aby bylo možné odběry adresáře odstranit, musí být odběry v **nezřízeném** stavu. Předplatné, **jehož platnost vypršela** nebo **zrušila** , se přesune do stavu **zakázáno** a závěrečná fáze je stav **zrušeno** .

V následující tabulce najdete informace o tom, co očekávat, když vyprší platnost zkušební verze předplatného Office 365 (včetně placeného partnera/CSP, smlouva Enterprise nebo multilicenčního programu). Další informace o uchovávání dat a životní cyklus předplatného Office 365 najdete v tématu [co se stane s daty a přístup, když předplatné Office 365 pro firmy skončí?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Stav předplatného | Data | Přístup k datům
----- | ----- | -----
Aktivní (30 dní pro zkušební období) | Data přístupná všem | Uživatelé mají normální přístup k souborům Office 365 nebo aplikacím.<br>Správci mají normální přístup k Microsoft 365 centrum pro správu a prostředky. 
Platnost vypršela (30 dní) | Data přístupná všem| Uživatelé mají normální přístup k souborům Office 365 nebo aplikacím.<br>Správci mají normální přístup k Microsoft 365 centrum pro správu a prostředky.
Zakázáno (30 dní) | Data přístupná pouze pro správce | Uživatelé nemají přístup k souborům Office 365 nebo aplikacím.<br>Správci mohou získat přístup k centru pro správu Microsoft 365, ale nemohou jim přiřazovat licence ani aktualizovat uživatele.
Zrušeno zřízení (30 dní po zakázání) | Odstraněná data (automaticky smazána, pokud se nepoužívají žádné jiné služby) | Uživatelé nemají přístup k souborům Office 365 nebo aplikacím.<br>Správci mají přístup k centru pro správu Microsoft 365 k nákupu a správě dalších předplatných.

## <a name="delete-a-subscription"></a>Odstranění předplatného

Předplatné můžete vložit do stavu **zrušeno zřízení** , který bude odstraněn během tří dnů pomocí centra pro správu Microsoft 365.

1. Přihlaste se k [centru pro správu Microsoft 365](https://admin.microsoft.com) pomocí účtu, který je globálním správcem vaší organizace. Pokud se pokoušíte odstranit adresář contoso s počáteční výchozí doménou contoso.onmicrosoft.com, přihlaste se pomocí hlavního názvu uživatele (UPN), jako je například admin@contoso.onmicrosoft.com.

2. Než budete moct odstranit předplatné, musíte ho zrušit. Vyberte **fakturace** a vyberte **produkty & Services**a pak vyberte **zrušit předplatné** u předplatného, které chcete zrušit. Zobrazí se stránka s názory.

   ![Vyberte předplatné, které chcete zrušit.](./media/directory-delete-howto/cancel-choose-subscription.png)

3. Dokončete formulář pro zpětnou vazbu a vyberte **zrušit předplatné** . tím zrušíte předplatné.

   ![Zrušit příkaz v předplatném Preview](./media/directory-delete-howto/cancel-command.png)

4. Nyní můžete odběr odstranit. U předplatného, které chcete odstranit, vyberte **Odstranit** . Pokud si předplatné nemůžete najít na stránce **produkty & Services** , ujistěte se, že máte **stav předplatného** nastavený na **vše**.

   ![Odstranit odkaz pro odstranění předplatného](./media/directory-delete-howto/delete-command.png)

5. Vyberte **Odstranit předplatné** a odstraňte předplatné a přijměte podmínky a ujednání. Všechna data se trvale odstraní do tří dní. Pokud změníte svůj názor, můžete [předplatné znovu aktivovat](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide) v průběhu tříletého období.
  
   ![pečlivě načtěte podmínky a ujednání.](./media/directory-delete-howto/delete-terms.png)

6. Nyní došlo ke změně stavu předplatného a odběr je označený k odstranění. Předplatné vstoupí v platnost **po 72** hodin později.

7. Po odstranění odběru v adresáři a uplynutí 72 hodin se můžete znovu přihlásit do centra pro správu Azure AD a nemusíte mít žádnou akci, která by neblokovala odstranění vašeho adresáře. Měli byste být schopni úspěšně odstranit adresář služby Azure AD.
  
   ![obrazovka předat kontrolu předplatného](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Mám zkušební předplatné, které blokuje odstranění

K dispozici jsou [samoobslužné produkty pro registraci](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) , jako je Microsoft Power BI, Rights Management Services, Microsoft Power apps nebo Dynamics 365, se můžou zaregistrovat jednotliví uživatelé pomocí Office 365, který taky vytvoří uživatele typu Host pro ověřování ve vašem adresáři Azure AD. . Tyto samoobslužné produkty blokují odstranění adresáře, dokud nebudou zcela odstraněny z adresáře, aby nedošlo ke ztrátě dat. Můžou je odstranit jenom správce Azure AD, ať už se uživatel zaregistroval individuálně nebo mu byl přiřazený produkt.

Existují dva typy samoobslužných registračních produktů ve způsobu jejich přiřazení: 

* Přiřazení na úrovni organizace: správce Azure AD přiřadí produkt celé organizaci a uživatel může tuto službu aktivně používat s tímto přiřazením na úrovni organizace, i když nejsou samostatně licencované.
* Přiřazení na úrovni uživatele: jednotliví uživatelé během přihlašování k samoobslužné službě v podstatě přiřadí produkt sami sobě bez správce. Jakmile bude organizace spravována správcem (viz [převzetí správce nespravovaného adresáře](domains-admin-takeover.md), může správce přímo přiřadit produkt uživatelům bez samoobslužné registrace.  

Když zahájíte odstranění samoobslužného registračního produktu, akce trvale odstraní data a odebere veškerý přístup uživatelů ke službě. Každému uživateli, kterému byla nabídka přiřazena individuálně nebo na úrovni organizace, se pak zablokuje přihlášení nebo přístup k žádným existujícím datům. Pokud chcete zabránit ztrátě dat pomocí samoobslužného registračního produktu, jako jsou například [řídicí panely Microsoft Power BI](https://docs.microsoft.com/power-bi/service-export-to-pbix) nebo [konfigurace zásad služby Rights Management Services](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy), zajistěte, aby byla data zálohována a ukládána jinde.

Další informace o aktuálně dostupných samoobslužných produktech a službách pro registraci najdete v tématu [dostupné samoobslužné programy](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs).

V následující tabulce najdete informace o tom, co očekávat, když vyprší platnost zkušební verze předplatného Office 365 (včetně placeného partnera/CSP, smlouva Enterprise nebo multilicenčního programu). Další informace o uchovávání dat a životní cyklus předplatného Office 365 najdete v tématu [co se stane s daty a přístup, když předplatné Office 365 pro firmy skončí?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide).

Stav produktu | Data | Přístup k datům
------------- | ---- | --------------
Aktivní (30 dní pro zkušební období) | Data přístupná všem | Uživatelé mají normální přístup k samoobslužnému registračnímu produktu, souborům nebo aplikacím.<br>Správci mají normální přístup k Microsoft 365 centrum pro správu a prostředky.
Odstraněno | Data odstraněna | Uživatelé nemají přístup k samoobslužnému registračnímu produktu, souborům nebo aplikacím.<br>Správci mají přístup k centru pro správu Microsoft 365 k nákupu a správě dalších předplatných.

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Jak mohu v Azure Portal odstranit samoobslužný produkt pro registraci?

Můžete vložit samoobslužný produkt pro registraci, jako je Microsoft Power BI nebo Azure Rights Management Services, do stavu **odstranění** , který se okamžitě odstraní na portálu Azure AD.

1. Přihlaste se do [centra pro správu Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)@no__t – 1with účet, který je globálním správcem organizace. Pokud se pokoušíte odstranit adresář contoso, který má počáteční výchozí doménu contoso.onmicrosoft.com, přihlaste se pomocí hlavního názvu uživatele (UPN), jako je například admin@contoso.onmicrosoft.com.

2. Vyberte **licence**a pak vyberte **samoobslužné registrace produktů**. Všechny samoobslužné produkty pro registraci se zobrazí odděleně od předplatných založených na pracovních stanicích. Vyberte produkt, který chcete trvale odstranit. Tady je příklad v Microsoft Power BI:

    ![uživatelské jméno je netypové nebo se nenašlo.](./media/directory-delete-howto/licenses-page.png)

3. Vyberte **odstranit** To odstranit produkt a přijměte podmínky, že se data odstraňují hned a neodvolatelně. Tato akce odstranění odstraní všechny uživatele a odebere přístup organizace k produktu. Kliknutím na tlačítko Ano přesunete vpřed s odstraněním.  

    ![uživatelské jméno je netypové nebo se nenašlo.](./media/directory-delete-howto/delete-product.png)

4. Vyberete-li možnost **Ano**, bude inicializováno odstranění produktu samoobslužné služby. K dispozici je oznámení, které vám sdělí, že probíhá odstraňování.  

    ![uživatelské jméno je netypové nebo se nenašlo.](./media/directory-delete-howto/progress-message.png)

5. Nyní se stav produktu samoobslužné registrace změnil na **odstraněno**. Po aktualizaci stránky by se měl produkt odebrat ze stránky **samoobslužné registrace** .  

    ![uživatelské jméno je netypové nebo se nenašlo.](./media/directory-delete-howto/product-deleted.png)

6. Po odstranění všech produktů se můžete znovu přihlásit do centra pro správu Azure AD a nemusíte nic dělat a žádné produkty neblokují odstranění adresáře. Měli byste být schopni úspěšně odstranit adresář služby Azure AD.

    ![uživatelské jméno je netypové nebo se nenašlo.](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Další kroky

[Dokumentace k Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
