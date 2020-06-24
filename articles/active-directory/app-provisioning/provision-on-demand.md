---
title: Zřízení uživatele na vyžádání pomocí Azure Active Directory
description: Vynutit synchronizaci
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 06/23/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: 78a56b6a848139c47d7934a47decb126afe00b7a
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2020
ms.locfileid: "85297524"
---
# <a name="on-demand-provisioning"></a>Zřizování na vyžádání
Zřizování na vyžádání umožňuje zřídit uživatele v aplikaci během několika sekund. Tuto možnost můžete využít k rychlému řešení potíží s konfigurací, ověřování výrazů, které jste definovali, filtrům rozsahu testů a mnohem více. 

## <a name="how-to-use-on-demand-provisioning"></a>Jak používat zřizování na vyžádání 

1. Přihlaste se na **Azure Portal**.
2. Přejděte k **podnikovým aplikacím**.
3. Vyberte svou aplikaci a přejděte na stránku konfigurace zřizování.
4. Nakonfigurujte zřizování poskytnutím přihlašovacích údajů správce.
5. Klikněte na **zřídit na vyžádání**.
6. Vyhledat uživatele podle křestního jména, příjmení, zobrazovaného jména, hlavního názvu uživatele nebo e-mailu.
7. V dolní části stránky vyberte zřídit.

## <a name="understanding-the-provisioning-steps"></a>Porozumění krokům zřizování
Funkce zřizování na vyžádání se pokusí zobrazit kroky, které služba zřizování provádí při zřizování uživatele. K zřizování uživatele se obvykle používá pět kroků a jeden nebo více kroků níže se zobrazí v prostředí pro zřizování na vyžádání.

### <a name="step-1-test-connection"></a>Krok 1: testování připojení
Služba zřizování se pokusí o autorizaci přístupu k cílové aplikaci tím, že vytvoří žádost o "testovacího uživatele". Služba zřizování očekává odezvu, která znamená, že je ověřená, aby pokračovala v postupu zřizování. Tento krok se zobrazí jenom v případě, že v kroku dojde k selhání. Po úspěšném provedení tohoto kroku se v prostředí pro zřizování na vyžádání nezobrazuje. 

**Tipy pro řešení potíží**
* Ujistěte se, že jste do cílové aplikace zadali platné přihlašovací údaje, jako je například tajný token a adresa URL tenanta. Požadovaná pověření se liší podle aplikace. Podrobné kurzy konfigurace najdete [tady](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list). 
* Zajistěte, aby cílová aplikace podporovala filtrování u odpovídající atributů definovaných v okně mapování atributů. Možná budete muset ověřit dokumentaci k rozhraní API, kterou poskytuje vývojář aplikace, a porozumět tak filtrům, které podporují.  
* U aplikací SCIM můžete použít nástroj, jako je například post, a zajistit tak, že aplikace odpoví na žádosti o autorizaci, jak služba Azure AD Provisioning očekává. Příklad žádosti najdete [tady](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#request-3).

### <a name="step-2-import-user"></a>Krok 2: import uživatele
V dalším kroku služba zřizování načte uživatele ze zdrojového systému. Atributy uživatele, které služba načítá, se později použijí k vyhodnocení toho, jestli je uživatel v oboru pro zřizování, kontrolu cílového systému pro existujícího uživatele a určení uživatelských atributů pro export do cílového systému. 

**Zobrazit podrobnosti**

V části zobrazení podrobností se zobrazují vlastnosti uživatele importované ze zdrojového systému (např. Azure AD).

**Tipy pro řešení potíží**
* Import uživatele může selhat, pokud v objektu uživatele ve zdrojovém systému chybí atribut pro hledání. Tuto chybu můžete vyřešit tak, že aktualizujete objekt uživatele o hodnotu pro atribut odpovídajícího atributu nebo změníte atribut odpovídajícího v konfiguraci zřizování.  
* Pokud v importovaném seznamu chybí atribut, který jste očekávali, ujistěte se, že má atribut hodnotu objektu uživatele ve zdrojovém systému. Služba zřizování aktuálně nepodporuje zřizování atributů s hodnotou null. 
* Ujistěte se, že stránka mapování konfiguračního atributu zřizování obsahuje atribut, který očekáváte. 

### <a name="step-3-determine-if-user-is-in-scope"></a>Krok 3: určení, jestli je uživatel v oboru
Dále služba zřizování určí, jestli je uživatel v [oboru](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#scoping) pro zřizování. Služba bude uvažovat o aspektech, jako je třeba to, jestli je uživatel přiřazený k aplikaci, jestli je nastavená synchronizace přiřazená nebo synchronizovaná, a filtry oborů definované v konfiguraci zřizování.  

**Zobrazit podrobnosti**

V části zobrazení podrobností se zobrazují podmínky oboru, které byly vyhodnoceny. Může se zobrazit jedna z následujících vlastností:
* **Aktivní ve zdrojovém systému** znamená, že uživatel má vlastnost ve službě Azure AD nastavenou na hodnotu true.
* **Přiřazeno k aplikaci** indikuje, že je uživatel přiřazený k aplikaci ve službě Azure AD.
* **Obor synchronizace vše** označuje, že nastavení oboru umožňuje všem uživatelům a skupinám v tenantovi.
* **Uživatel má požadovanou roli** , která indikuje, že uživatel má v aplikaci potřebné role, které se mají zřídit. 
* **Filtry oborů** se zobrazí také v případě, že jste definovali filtry oborů pro vaši aplikaci. Filtr se zobrazí v následujícím formátu: {název filtru rozsahu} {atribut oboru filtru} {obor filtru Hodnota} {Rozsah filtru Value}. 

**Tipy pro řešení potíží**
* Ujistěte se, že jste definovali platnou roli oboru. Nepoužívejte například operátor ["větší než"](https://docs.microsoft.com/azure/active-directory/app-provisioning/define-conditional-rules-for-provisioning-user-accounts#create-a-scoping-filter) s hodnotou, která není celočíselná. 
* Pokud uživatel nemá nezbytnou roli, přečtěte si tipy popsané [tady](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned#provisioning-users-assigned-to-the-default-access-role). 

### <a name="step-4-match-user-between-source-and-target"></a>Krok 4: vyhledání uživatele mezi zdrojem a cílem
V tomto kroku. Služba se pokusí porovnat uživatele, který byl načten v kroku importu, s uživatelem v cílovém systému. 

**Zobrazit podrobnosti**

Stránky zobrazit podrobnosti zobrazují vlastnosti uživatelů, které byly spárovány v cílovém systému. Vlastnosti, které vidíte v podokně kontextu, se budou lišit takto:
* Pokud v cílovém systému neodpovídají žádní uživatelé, nezobrazí se žádné vlastnosti.
* Pokud v cílovém systému odpovídá jeden uživatel, zobrazí se vlastnosti odpovídajícího uživatele z cílového systému.
* Pokud se shodují více uživatelů, zobrazí se vlastnosti obou odpovídajících uživatelů.
* Pokud je více odpovídajících atributů součástí mapování atributů, každý odpovídající atribut se vyhodnotí postupně a odpovídajícím uživatelům se zobrazí. 

**Podrobnosti o řešení potíží**
* Služba zřizování nemůže jedinečně odpovídat uživateli ve zdroji s uživatelem v cíli. To lze vyřešit zajištěním, že je shodný atribut jedinečný. 
* Zajistěte, aby cílová aplikace podporovala filtrování u atributu definovaného jako odpovídající atribut.  

### <a name="step-5-perform-action"></a>Krok 5: provedení akce
Služba zřizování nakonec provede akci, jako je vytvoření, aktualizace, odstranění nebo přeskočení uživatele. 

**Zobrazit podrobnosti**

V části Zobrazit podrobnosti se zobrazí atributy, které byly v cílové aplikaci změněny. To představuje konečný výstup aktivity zřizování služby a exportovaných atributů. Pokud se tento krok nezdaří, zobrazené atributy reprezentují atributy, které služba zřizování provedla při pokusu o změnu.  

**Tipy pro řešení potíží**
* Selhání pro export změn se může značně lišit. Běžné chyby najdete v [dokumentaci](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs#error-codes) k protokolům zřizování.


## <a name="frequently-asked-questions"></a>Nejčastější dotazy
**Potřebujete zapnout zřizování, abyste mohli používat zřizování na vyžádání?** Pro aplikace, které pro autorizaci používají dlouhý nosný token nebo uživatelské jméno a heslo, nejsou nutné žádné další kroky. Aplikace, které používají OAuth pro autorizaci: aktuálně vyžadují, aby se úloha zřizování zastavila před použitím zřizování na vyžádání. Do této kategorie patří aplikace, jako je například G Suite, box, pracoviště po Facebooku a časová rezerva. Práce probíhá, aby bylo možné spouštět zřizování na vyžádání pro všechny aplikace, aniž byste museli zastavovat zřizování. 

**Jak dlouho trvá zřizování na vyžádání?** Obvykle trvá méně než 30 sekund. 

## <a name="known-limitations"></a>Známá omezení
V současné době existuje několik známých omezení. Pošlete prosím na [UserVoice](https://aka.ms/appprovisioningfeaturerequest) , abychom vám mohli lépe určit, jaká vylepšení se mají udělat dál. Pamatujte na to, že tato omezení jsou specifická pro schopnost zřizování na vyžádání. konkrétní informace o tom, jestli aplikace podporuje zřizování skupin, odstranění atd., najdete v kurzu použití aplikace. 

* Aplikace Workday, AWS a SuccessFactors nepodporují zřizování na vyžádání.
* Zřizování skupin a rolí na vyžádání se nepodporuje.
* Zakázání nebo odstranění uživatelů a skupin se nepodporuje.

## <a name="next-steps"></a>Další kroky

* [Řešení potíží s zřizováním](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem)
