---
title: Sestavy o přístupech a použití pro Azure MFA | Dokumentace Microsoftu
description: Popisuje způsob použití funkce ověřování Azure Multi-Factor Authentication – sestavy.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: dc4cd28fe61c422f65f47c74c7cbc4686d73ab77
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628830"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Sestavy v Azure Multi-Factor Authentication

Azure Multi-Factor Authentication nabízí několik sestav, které mohou využívat vás a vaši organizaci, která je přístupná prostřednictvím webu Azure portal. V následující tabulce jsou uvedeny dostupné sestavy:

| Sestava | Umístění | Popis |
|:--- |:--- |:--- |
| Historie blokovaného uživatele | Azure AD > MFA Server > blokování a odblokování uživatelů | Zobrazuje historie žádostí o blokování nebo odblokování uživatelů. |
| Využití a odhalování výstrahy | Azure AD > přihlášení | Poskytuje informace o celkové využití: uživatelský souhrn a podrobnosti o uživateli; také historie upozornění na podvod odeslaných během období zadán. |
| Využití pro místní komponenty | Azure AD > MFA Server > Sestava aktivit | Pomocí rozšíření serveru NPS, AD FS, poskytuje informace o celkové využití pro MFA a MFA serveru. |
| Historie uživatele s jednorázovým přihlášením | Azure AD > MFA Server > jednorázové přihlášení | Poskytuje historii žádostí o obejít ověřování Multi-Factor Authentication pro uživatele. |
| Stav serveru | Azure AD > MFA Server > Stav serveru | Zobrazí stav služby Multi-Factor Authentication Server spojenou s vaším účtem. |

## <a name="view-mfa-reports"></a>Zobrazení sestav vícefaktorového ověřování

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **MFA Server**.
3. Vyberte sestavu, kterou chcete zobrazit.

   <center>![Cloud](./media/howto-mfa-reporting/report.png)</center>

## <a name="azure-ad-sign-ins-report"></a>Sestava přihlašování Azure AD

S **sestavě aktivit přihlašování** v [webu Azure portal](https://portal.azure.com), můžete získat informace, které potřebujete ke zjištění stavu vašeho prostředí.

Sestava přihlášení vám může poskytnout informace o použití spravovaných aplikací a přihlašovací aktivity uživatelů, který obsahuje informace o používání služby Multi-Factor authentication (MFA). Data vícefaktorového ověřování poskytují přehled o fungování vícefaktorového ověřování ve vaší organizaci. Poskytují odpovědi na následující otázky:

- Použilo se při přihlášení vícefaktorové ověřování?
- Jak uživatel dokončil vícefaktorové ověřování?
- Proč se uživateli nepodařilo dokončit vícefaktorové ověřování?
- Kolik uživatelů ovlivňuje vícefaktorové ověřování?
- Kolik uživatelů nemůže dokončit vícefaktorové ověřování?
- S jakými běžnými problémy se koncoví uživatelé vícefaktorového ověřování setkávají?

Tato data jsou k dispozici prostřednictvím [webu Azure portal](https://portal.azure.com) a [API pro vytváření sestav](../reports-monitoring/concept-reporting-api.md).

![Cloud](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Struktura sestavy přihlášení

Sestavy aktivit přihlašování pro vícefaktorové ověřování poskytují přístup k následujícím informacím:

**Vyžadování vícefaktorového ověřování:** Uvádí, jestli se pro přihlašování vyžaduje vícefaktorové ověřování, nebo ne. Vícefaktorové ověřování můžete vyžadovat vícefaktorové ověřování jednotlivých uživatelů, podmíněný přístup nebo z jiných důvodů. Možné hodnoty jsou **Ano** nebo **ne**.

**Výsledek vícefaktorového ověřování:** Další informace o splnění nebo odepření vícefaktorového ověřování:

- Pokud bylo vícefaktorové ověřování splněno, v tomto sloupci se zobrazí informace o způsobu splnění vícefaktorového ověřování.
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

- Pokud bylo vícefaktorové ověřování odepřeno, v tomto sloupci se zobrazí důvod odepření.
   - Služba Azure Multi-Factor Authentication byla odepřena
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

**Metoda vícefaktorového ověřování:** Metoda ověřování, kterou uživatel dokončil vícefaktorové ověřování. Možné hodnoty:

- Textová zpráva
- Oznámení mobilní aplikace
- Telefonní hovor (telefon pro ověření)
- Ověřovací kód z mobilní aplikace
- Telefonní hovor (telefon do kanceláře)
- Telefonní hovor (telefon pro alternativní ověření)

**Podrobnosti o vícefaktorovém ověřování:** Zkrácená verze telefonního čísla, například: +X XXXXXXXX64.

**Podmíněný přístup** najít informace o vytváření zásad podmíněného přístupu, které měla vliv na pokus o přihlášení, včetně:

- Název zásady
- Udělit řízení
- Ovládací prvky relací
- Výsledek

## <a name="powershell-reporting"></a>Vytváření sestav pomocí Powershellu

Identifikujte uživatele, kterým jste se zaregistrovali pro vícefaktorové ověřování pomocí Powershellu, který následuje.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identifikujte uživatele, kteří se ještě nezaregistrovali pro vícefaktorové ověřování pomocí Powershellu, který následuje.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>Další postup

* [Pro uživatele](../user-help/multi-factor-authentication-end-user.md)
* [Pokud chcete nasadit](concept-mfa-whichversion.md)
