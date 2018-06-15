---
title: Reference pro generování sestav vícefaktorového ověřování na webu Azure Portal | Dokumentace Microsoftu
description: Referenční informace ke generování sestav vícefaktorového ověřování na webu Azure Portal
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 03/16/2018
ms.author: rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: ac24b5dcf1aa5b8c616e14bcbb696c9e99b6cecf
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589062"
---
# <a name="reference-for-multi-factor-authentication-reporting-in-the-azure-portal"></a>Reference pro generování sestav vícefaktorového ověřování na webu Azure Portal

[Generování sestav Azure Active Directory (Azure AD)](active-directory-reporting-azure-portal.md) na webu [Azure Portal](https://portal.azure.com) umožňuje získat všechny informace potřebné ke zjištění stavu vašeho prostředí.

[Sestavy aktivit přihlašování](active-directory-reporting-activity-sign-ins.md) poskytují informace o využití spravovaných aplikací a o aktivitách přihlašování uživatelů, včetně informací o využívání vícefaktorového ověřování (MFA). 

Data vícefaktorového ověřování poskytují přehled o fungování vícefaktorového ověřování ve vaší organizaci. Poskytují odpovědi na následující otázky: 

- Použilo se při přihlášení vícefaktorové ověřování? 

- Jak uživatel dokončil vícefaktorové ověřování? 

- Proč se uživateli nepodařilo dokončit vícefaktorové ověřování?  

Díky shromažďování veškerých dat o přihlašování můžete mít lepší přehled o vícefaktorovém ověřování ve vaší organizaci. Tato data vám umožní zodpovědět dotazy jako: 

- Kolik uživatelů ovlivňuje vícefaktorové ověřování?  

- Kolik uživatelů nemůže dokončit vícefaktorové ověřování? 

- S jakými běžnými problémy se koncoví uživatelé vícefaktorového ověřování setkávají? 


Data jsou dostupná na webu Azure Portal a přes [rozhraní API pro generování sestav](active-directory-reporting-api-getting-started-azure-portal.md). 


## <a name="data-structure"></a>Struktura dat


Sestavy aktivit přihlašování pro vícefaktorové ověřování poskytují přístup k následujícím informacím:

**Vyžadování vícefaktorového ověřování:** Uvádí, jestli se pro přihlašování vyžaduje vícefaktorové ověřování, nebo ne. Vícefaktorové ověřování se může vyžadovat kvůli vícefaktorovému ověřování pro jednotlivé uživatele, podmíněnému přístupu nebo jiným důvodům. Možné hodnoty jsou `Yes` nebo `No`.

**Metoda vícefaktorového ověřování:** Metoda ověřování, kterou uživatel dokončil vícefaktorové ověřování. Možné hodnoty: 

- Textová zpráva 

- Oznámení mobilní aplikace 

- Telefonní hovor (telefon pro ověření) 

- Ověřovací kód z mobilní aplikace 

- Telefonní hovor (telefon do kanceláře) 

- Telefonní hovor (telefon pro alternativní ověření) 

**Podrobnosti o vícefaktorovém ověřování:** Zkrácená verze telefonního čísla, například: +X XXXXXXXX64. 

**Výsledek vícefaktorového ověřování:** Další informace o splnění nebo odepření vícefaktorového ověřování:

- Pokud bylo vícefaktorové ověřování splněno, v tomto sloupci se zobrazí informace o způsobu splnění vícefaktorového ověřování. 

- Pokud bylo vícefaktorové ověřování odepřeno, v tomto sloupci se zobrazí důvod odepření. Možné hodnoty jsou `Satisfied` nebo `Denied`. 

V následující části jsou uvedeny řetězcové hodnoty pro pole s výsledkem vícefaktorového ověřování.

## <a name="status-strings"></a>Řetězce stavu

V této části jsou uvedeny možné hodnoty řetězce stavu výsledku vícefaktorového ověřování.

### <a name="satisfied-status-strings"></a>Řetězce stavu splnění


- Azure Multi-Factor Authentication

    - dokončeno v cloudu 

    - vypršela platnost kvůli zásadám nakonfigurovaným na tenantovi 

    - zobrazena výzva k registraci 

    - splněno deklarací identity v tokenu 

    - splněno deklarací identity poskytnutou externím poskytovatelem 

    - splněno silným ověřením 

    - přeskočeno, protože tok byl spuštěn tokem přihlášení zprostředkovatele ve Windows 

    - přeskočeno kvůli heslu aplikace 

    - přeskočeno kvůli umístění 

    - přeskočeno kvůli registrovanému zařízení 
    
    - přeskočeno kvůli zapamatovanému zařízení 

    - úspěšně dokončeno 

- Přesměrováno k externímu poskytovateli vícefaktorového ověřování 

 
### <a name="denied-status-strings"></a>Řetězce stavu odepření

Služba Azure Multi-Factor Authentication byla odepřena 

- probíhá ověřování 

- duplicitní pokus o ověření 

- příliš mnohokrát byl zadán nesprávný kód 

- neplatné ověření 

- neplatný ověřovací kód z mobilní aplikace 

- chybná konfigurace 

- telefonní hovor byl přesměrován do hlasové pošty 

- telefonní číslo má neplatný formát 

- chyba služby 

- nepodařilo se spojit s telefonem uživatele 

- nepodařilo se odeslat oznámení mobilní aplikace do zařízení 

- nepodařilo se odeslat oznámení mobilní aplikace 

- uživatel odmítl ověřování 

- uživatel nereagoval na oznámení mobilní aplikace 

- uživatel nemá zaregistrované žádné metody ověřování 

- uživatel zadal nesprávný kód 

- uživatel zadal nesprávný kód PIN 

- uživatel zavěsil před dokončením ověřování 

- uživatel je blokován 

- uživatel nezadal ověřovací kód 

- uživatel nenalezen 
 
- ověřovací kód již byl použitý 



## <a name="next-steps"></a>Další kroky

Další informace najdete v článku [Generování sestav ve službě Azure Active Directory](active-directory-reporting-azure-portal.md).




























