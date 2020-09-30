---
title: Odstranění organizace Azure AD (tenant) – Azure Active Directory | Microsoft Docs
description: Vysvětluje, jak připravit organizaci Azure AD (tenant) k odstranění, včetně organizací samoobslužných služeb.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 05/21/2020
ms.author: curtand
ms.reviewer: addimitu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf8eeb2d1f19ca0848ae5c608ba9f0d8e9526e05
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541250"
---
# <a name="delete-a-tenant-in-azure-active-directory"></a>Odstranění tenanta v Azure Active Directory

Když se odstraní organizace Azure AD (tenant), odstraní se také všechny prostředky, které jsou v organizaci obsažené. Připraví svoji organizaci minimalizací svých přidružených prostředků, než je odstraníte. Pouze globální správce Azure Active Directory (Azure AD) může z portálu odstranit organizaci Azure AD.

## <a name="prepare-the-organization"></a>Příprava organizace

Nemůžete odstranit organizaci ve službě Azure AD, dokud ji neprojde několik kontrol. Tyto kontroly snižují riziko, že odstranění organizace Azure AD negativně ovlivní přístup uživatelů, jako je například možnost přihlásit se k Microsoft 365 nebo získat přístup k prostředkům v Azure. Pokud je například organizace přidružená k předplatnému neúmyslně odstraněna, uživatelé nebudou mít přístup k prostředkům Azure pro toto předplatné. Kontrolují se následující podmínky:

* V organizaci Azure AD (tenant) nemůžou být žádní uživatelé s výjimkou jednoho globálního správce, který má organizaci odstranit. Než bude možné organizaci odstranit, je nutné odstranit všechny ostatní uživatele. Pokud jsou uživatelé synchronizováni z místního prostředí, musí být nejprve vypnutá synchronizace a uživatelé musí být v cloudové organizaci smazáni pomocí rutin Azure Portal nebo Azure PowerShell.
* V organizaci nemůžou být žádné aplikace. Aby bylo možné organizaci odstranit, je nutné odebrat všechny aplikace.
* Nemůžete mít žádného poskytovatele služeb Multi-Factor Authentication propojeného s organizací.
* Pro žádné online služby Microsoftu, jako jsou Microsoft Azure, Microsoft 365 nebo Azure AD Premium přidružené k organizaci, nemůžete mít žádná předplatná. Pokud se například pro vás v Azure vytvořila výchozí organizace Azure AD, nemůžete tuto organizaci odstranit, pokud se vaše předplatné Azure pořád spoléhá na tuto organizaci na ověřování. Obdobně nemůžete odstranit organizaci, pokud k ní přidružit jiný uživatel předplatné.

## <a name="delete-the-organization"></a>Odstranit organizaci

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který je globálním správcem vaší organizace.

2. Vyberte **Azure Active Directory**.

3. Přepněte na organizaci, kterou chcete odstranit.
  
   ![Před odstraněním potvrdit organizaci](./media/directory-delete-howto/delete-directory-command.png)

4. Vyberte **Odstranit tenanta**.
  
   ![Vyberte příkaz pro odstranění organizace.](./media/directory-delete-howto/delete-directory-list.png)

5. Pokud vaše organizace neprojde jednou nebo více kontrolami, máte k dispozici odkaz na Další informace o tom, jak předat. Po předání všech kontrol vyberte **Odstranit** a proces dokončete.

## <a name="if-you-cant-delete-the-organization"></a>Pokud nemůžete organizaci odstranit

Když jste nakonfigurovali organizaci Azure AD, mohli jste taky aktivovat předplatné na základě licencí pro vaši organizaci, jako je Azure AD Premium P2, Microsoft 365 Business Standard nebo Enterprise Mobility + Security E5. Aby nedocházelo k náhodné ztrátě dat, nemůžete organizaci odstranit, dokud nebudou předplatná zcela odstraněna. Aby bylo možné odstranit organizaci, musí být odběry v **nezřízeném** stavu. Předplatné, **jehož platnost vypršela** nebo **zrušila** , se přesune do stavu **zakázáno** a závěrečná fáze je stav **zrušeno** .

V následující tabulce najdete další informace o tom, co očekávat, když platnost předplatného Microsoft 365 zkušební verze vyprší (včetně placeného partnera/CSP, smlouva Enterprise nebo multilicencí). Další informace o Microsoft 365 uchovávání dat a životní cyklus předplatného najdete v tématu [co se stane s daty a přístup k tomu, když předplatná Microsoft 365 pro firmy končí?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Stav předplatného | Data | Přístup k datům
----- | ----- | -----
Aktivní (30 dní pro zkušební období) | Data přístupná všem | Uživatelé mají normální přístup k souborům Microsoft 365 nebo aplikacím.<br>Správci mají normální přístup k Microsoft 365 centrum pro správu a prostředky. 
Platnost vypršela (30 dní) | Data přístupná všem| Uživatelé mají normální přístup k souborům Microsoft 365 nebo aplikacím.<br>Správci mají normální přístup k Microsoft 365 centrum pro správu a prostředky.
Zakázáno (30 dní) | Data přístupná pouze pro správce | Uživatelé nemají přístup k souborům Microsoft 365 ani aplikacím.<br>Správci mohou získat přístup k centru pro správu Microsoft 365, ale nemohou jim přiřazovat licence ani aktualizovat uživatele.
Zrušeno zřízení (30 dní po zakázání) | Odstraněná data (automaticky smazána, pokud se nepoužívají žádné jiné služby) | Uživatelé nemají přístup k souborům Microsoft 365 ani aplikacím.<br>Správci mají přístup k centru pro správu Microsoft 365 k nákupu a správě dalších předplatných.

## <a name="delete-a-subscription"></a>Odstranění předplatného

Předplatné můžete vložit do stavu **zrušeno zřízení** , který bude odstraněn během tří dnů pomocí centra pro správu Microsoft 365.

1. Přihlaste se k [centru pro správu Microsoft 365](https://admin.microsoft.com) pomocí účtu, který je globálním správcem vaší organizace. Pokud se pokoušíte odstranit organizaci "contoso", která má počáteční výchozí doménu contoso.onmicrosoft.com, přihlaste se pomocí hlavního názvu uživatele (UPN), jako je například admin@contoso.onmicrosoft.com .

2. Zobrazte si náhled nového centra pro správu Microsoft 365 tím, že zajistěte, aby byl povolený přepínač **vyzkoušení nového centra pro správu** .

   ![Náhled nového prostředí centra pro správu M365](./media/directory-delete-howto/preview-toggle.png)

3. Po povolení nového centra pro správu je potřeba předplatné zrušit, aby ho bylo možné odstranit. Vyberte **fakturace** a vyberte **produkty & Services**a pak vyberte **zrušit předplatné** u předplatného, které chcete zrušit. Zobrazí se stránka s názory.

   ![Vyberte předplatné, které chcete zrušit.](./media/directory-delete-howto/cancel-choose-subscription.png)

4. Dokončete formulář pro zpětnou vazbu a vyberte **zrušit předplatné** . tím zrušíte předplatné.

   ![Zrušit příkaz v předplatném Preview](./media/directory-delete-howto/cancel-command.png)

5. Nyní můžete odběr odstranit. U předplatného, které chcete odstranit, vyberte **Odstranit** . Pokud si předplatné nemůžete najít na stránce **produkty & Services** , ujistěte se, že máte **stav předplatného** nastavený na **vše**.

   ![Odstranit odkaz pro odstranění předplatného](./media/directory-delete-howto/delete-command.png)

6. Vyberte **Odstranit předplatné** a odstraňte předplatné a přijměte podmínky a ujednání. Všechna data se trvale odstraní do tří dní. Pokud změníte svůj názor, můžete [předplatné znovu aktivovat](/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide) v průběhu tříletého období.
  
   ![pečlivě načtěte podmínky a ujednání.](./media/directory-delete-howto/delete-terms.png)

7. Nyní došlo ke změně stavu předplatného a odběr je označený k odstranění. Předplatné vstoupí v platnost **po 72** hodin později.

8. Jakmile odstraníte předplatné ve vaší organizaci a 72 hodin, můžete se znovu přihlásit do centra pro správu Azure AD a nemusíte mít žádnou akci, která by neblokovala odstranění vaší organizace. Měli byste být schopni úspěšně odstranit vaši organizaci Azure AD.
  
   ![obrazovka předat kontrolu předplatného](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Mám zkušební předplatné, které blokuje odstranění

K dispozici jsou [samoobslužné produkty pro registraci](/office365/admin/misc/self-service-sign-up?view=o365-worldwide) , jako je Microsoft Power BI, Rights Management Services, Microsoft Power apps nebo Dynamics 365, se můžou zaregistrovat jednotliví uživatelé prostřednictvím Microsoft 365, který taky vytvoří uživatele typu Host pro ověřování ve vaší organizaci Azure AD. Tyto samoobslužné produkty blokují odstranění adresáře, dokud se produkty z organizace zcela neodstraní, aby se předešlo ztrátě dat. Můžou je odstranit jenom správce Azure AD, ať už se uživatel zaregistroval individuálně nebo mu byl přiřazený produkt.

Existují dva typy samoobslužných registračních produktů ve způsobu jejich přiřazení: 

* Přiřazení na úrovni organizace: správce Azure AD přiřadí produkt celé organizaci a uživatel může tuto službu aktivně používat s tímto přiřazením na úrovni organizace, i když nejsou samostatně licencované.
* Přiřazení na úrovni uživatele: jednotliví uživatelé během přihlašování k samoobslužné službě v podstatě přiřadí produkt sami sobě bez správce. Jakmile bude organizace spravována správcem (viz [převzetí správce nespravované organizace](domains-admin-takeover.md)), může správce přímo přiřadit produkt uživatelům bez samoobslužné registrace.  

Když zahájíte odstranění samoobslužného registračního produktu, akce trvale odstraní data a odebere veškerý přístup uživatelů ke službě. Každému uživateli, kterému byla nabídka přiřazena individuálně nebo na úrovni organizace, se pak zablokuje přihlášení nebo přístup k žádným existujícím datům. Pokud chcete zabránit ztrátě dat pomocí samoobslužného registračního produktu, jako jsou například [řídicí panely Microsoft Power BI](/power-bi/service-export-to-pbix) nebo [konfigurace zásad služby Rights Management Services](/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy), zajistěte, aby byla data zálohována a ukládána jinde.

Další informace o aktuálně dostupných samoobslužných produktech a službách pro registraci najdete v tématu [dostupné samoobslužné programy](/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs).

V následující tabulce najdete další informace o tom, co očekávat, když platnost předplatného Microsoft 365 zkušební verze vyprší (včetně placeného partnera/CSP, smlouva Enterprise nebo multilicencí). Další informace o Microsoft 365 uchovávání dat a životní cyklus předplatného najdete v tématu [co se stane s daty a přístup k tomu, když předplatná Microsoft 365 pro firmy končí?](/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide).

Stav produktu | Data | Přístup k datům
------------- | ---- | --------------
Aktivní (30 dní pro zkušební období) | Data přístupná všem | Uživatelé mají normální přístup k samoobslužnému registračnímu produktu, souborům nebo aplikacím.<br>Správci mají normální přístup k Microsoft 365 centrum pro správu a prostředky.
Odstraněné | Data odstraněna | Uživatelé nemají přístup k samoobslužnému registračnímu produktu, souborům nebo aplikacím.<br>Správci mají přístup k centru pro správu Microsoft 365 k nákupu a správě dalších předplatných.

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Jak mohu v Azure Portal odstranit samoobslužný produkt pro registraci?

Můžete vložit samoobslužný produkt pro registraci, jako je Microsoft Power BI nebo Azure Rights Management Services, do stavu **odstranění** , který se okamžitě odstraní na portálu Azure AD.

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) pomocí účtu, který je globálním správcem v organizaci. Pokud se pokoušíte odstranit organizaci "contoso", která má počáteční výchozí doménu contoso.onmicrosoft.com, přihlaste se pomocí hlavního názvu uživatele (UPN), jako je například admin@contoso.onmicrosoft.com .

2. Vyberte **licence**a pak vyberte **samoobslužné registrace produktů**. Všechny samoobslužné produkty pro registraci se zobrazí odděleně od předplatných založených na pracovních stanicích. Vyberte produkt, který chcete trvale odstranit. Tady je příklad v Microsoft Power BI:

    ![Snímek obrazovky zobrazující stránku licence – samoobslužné registrace produktů.](./media/directory-delete-howto/licenses-page.png)

3. Vyberte **Odstranit** , pokud chcete produkt odstranit, a přijměte podmínky, že se data odstraňují okamžitě a nevratně. Tato akce odstranění odstraní všechny uživatele a odebere přístup organizace k produktu. Kliknutím na tlačítko Ano přesunete vpřed s odstraněním.  

    ![Snímek obrazovky se stránkou licence – samoobslužné registrace produktů pomocí okna odstranit produkt pro registraci samoobslužné služby v otevřeném okně.](./media/directory-delete-howto/delete-product.png)

4. Vyberete-li možnost **Ano**, bude inicializováno odstranění produktu samoobslužné služby. K dispozici je oznámení, které vám sdělí, že probíhá odstraňování.  

    ![Snímek obrazovky se zobrazeným oznámením "licence – samoobslužné produkty pro registraci" na stránce s informacemi o "odstranění Probíhá odstraňování".](./media/directory-delete-howto/progress-message.png)

5. Nyní se stav produktu samoobslužné registrace změnil na **odstraněno**. Po aktualizaci stránky by se měl produkt odebrat ze stránky **samoobslužné registrace** .  

    ![Snímek obrazovky se stránkou "licence – samoobslužné produkty pro registraci" s panelem "odstranění produktu samoobslužné registrace" na pravé straně.](./media/directory-delete-howto/product-deleted.png)

6. Po odstranění všech produktů se můžete znovu přihlásit do centra pro správu Azure AD a nemusíte nic dělat a žádné produkty neblokují odstranění vaší organizace. Měli byste být schopni úspěšně odstranit vaši organizaci Azure AD.

    ![uživatelské jméno je netypové nebo se nenašlo.](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Další kroky

[Dokumentace k Azure Active Directory](../index.yml)