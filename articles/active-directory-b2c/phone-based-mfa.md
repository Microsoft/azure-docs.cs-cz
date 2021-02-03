---
title: Zabezpečení vícefaktorového ověřování pomocí telefonu v Azure AD B2C
titleSuffix: Azure AD B2C
description: Přečtěte si tipy pro zabezpečení služby Multi-Factor Authentication (MFA) ve vašem tenantovi Azure AD B2C pomocí Azure Monitorch Log Analytics sestav a výstrah. Použijte náš sešit k identifikaci podvodného telefonického ověřování a zmírnění falešných podpisů. =
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3ca73e020009817001f309ddf29c2984a8541026
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527347"
---
# <a name="securing-phone-based-multi-factor-authentication-mfa"></a>Zabezpečení vícefaktorového ověřování Multi-Factor Authentication (MFA)

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

V případě služby Azure Active Directory (Azure AD) Multi-Factor Authentication (MFA) si uživatelé mohou přijmout automatizované hlasové volání na telefonním čísle, které se registrují k ověření. Uživatelé se zlými úmysly mohou tuto metodu využít vytvořením několika účtů a voláním telefonního hovoru bez dokončení procesu registrace MFA. Tato řada neúspěšných přihlášení by mohla vyčerpat povolený počet pokusů o registraci a zabránit ostatním uživatelům v registraci nových účtů ve vašem tenantovi Azure AD B2C. K ochraně proti těmto útokům můžete použít Azure Monitor k monitorování selhání ověřování na telefonu a zmírnění falešných podpisů.

## <a name="prerequisites"></a>Požadavky

Než začnete, vytvořte [pracovní prostor Log Analytics](azure-monitor.md).

## <a name="create-a-phone-based-mfa-events-workbook"></a>Vytvoření sešitu událostí MFA založeného na telefonu

[Azure AD B2C sestavy & úložiště výstrahy](https://github.com/azure-ad-b2c/siem#phone-authentication-failures) na webu GitHub obsahuje artefakty, které můžete použít k vytváření a publikování sestav, výstrah a řídicích panelů na základě protokolů Azure AD B2C. Pracovní sešit s obrázkem níže popisuje chyby související s telefonem.

### <a name="overview-tab"></a>Karta Přehled

Na kartě **Přehled** se zobrazí následující informace:

- Důvody selhání (celkový počet neúspěšných ověření pro telefon pro každý z daných důvodů)
- Blokováno kvůli špatné pověsti
- IP adresa s neúspěšnými ověřeními pro telefon (celkový počet neúspěšných ověření telefonického telefonu pro každou zadanou IP adresu)
- Telefonní čísla s IP adresou – Nezdařená ověření telefonu
- Prohlížeč (počet selhání ověřování na telefon na klientský prohlížeč)
- Operační systém (počet selhání ověřování na telefon na klientský operační systém)

![Karta Přehled](media/phone-based-mfa/overview-tab.png)

### <a name="details-tab"></a>Karta Podrobnosti

Na kartě **Podrobnosti** jsou uvedeny následující informace:

- Zásada Azure AD B2C – neúspěšná ověření telefonu
- Selhání ověřování telefonem podle telefonního čísla – časový graf (upravitelná časová osa)
- Selhání ověřování telefonem podle zásad Azure AD B2C – časový graf (upravitelná časová osa)
- Selhání ověřování telefonem podle IP adresy – časový graf (upravitelná časová osa)
- Výběrem telefonního čísla zobrazíte podrobnosti o selhání (vyberte telefonní číslo pro podrobný seznam selhání).

![Karta podrobností 1 ze 3](media/phone-based-mfa/details-tab-1.png)

![Karta podrobností 2 ze 3](media/phone-based-mfa/details-tab-2.png)

![Karta Podrobnosti 3 ze 3](media/phone-based-mfa/details-tab-3.png)

## <a name="use-the-workbook-to-identify-fraudulent-sign-ups"></a>Použití sešitu k identifikaci podvodného přihlášení

Pomocí sešitu můžete pochopit události vícefaktorového ověřování na telefonu a identifikovat potenciálně škodlivé používání služby Telephony Service.

1. Porozumět tomu, co je pro vašeho tenanta normální, pomocí zodpovězení těchto otázek:

   - Kde jsou oblasti, ze kterých očekáváte telefonický MFA?
   - Projděte si důvody pro neúspěšné pokusy o ověřování MFA (Phone-based). považují se za normální nebo očekávané?

2. Rozpoznat charakteristiky podvodného přihlášení:

   - **Založeno na umístění**: Projděte si **chyby ověřování pro telefon podle IP adresy** pro všechny účty, které jsou přidružené k umístěním, ze kterých neočekáváte, že se uživatelé chtějí zaregistrovat.

   > [!NOTE]
   > Zadaná IP adresa je přibližná oblast.

   - **Založené na rychlosti**: Prohlédněte si **neúspěšná telefonická ověření přesčas (za den)**, který označuje telefonní čísla, která vycházejí z neobvyklého počtu nezdařených pokusů o ověření telefonu za den, seřazené od nejvyšší (vlevo) do nejnižší (vpravo).

3. Pomocí postupu v následující části zmírnit podvodné přihlašování.
 

## <a name="mitigate-fraudulent-sign-ups"></a>Zmírnění falešných podpisů

Proveďte následující akce, které vám pomůžou zmírnit podvodné podepisování.

- Použijte **Doporučené** verze uživatelských toků k následujícím akcím:
     
   - [Povolte funkci](phone-authentication-user-flows.md) jednorázového hesla pro použití e-mailu pro MFA (platí pro toky registrace i přihlášení).
   - [Nakonfigurujte zásady podmíněného přístupu](conditional-access-identity-protection-setup.md) , které blokují přihlášení na základě umístění (platí jenom pro toky přihlašování, ne pro toky registrace).
   - Použijte konektory rozhraní API pro [integraci s řešením anti-bot, jako je reCAPTCHA](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) (platí pro toky registrace).

- Z rozevírací nabídky, kde si uživatel ověří své telefonní číslo (Tato změna bude platit pro budoucí přihlašování), odeberte kódy zemí, které nejsou relevantní pro vaši organizaci:
    
   1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce vašeho tenanta Azure AD B2C.

   2. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.

   3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.

   4. Vyberte tok uživatele a pak vyberte **jazyky**. Vyberte jazyk pro zeměpisnou polohu vaší organizace a otevřete panel podrobnosti o jazyku. (V tomto příkladu vybereme pro USA **angličtinu EN** ). Vyberte **stránku vícefaktorového ověřování** a pak vyberte **Stáhnout výchozí (EN)**.
 
      ![Nahrání nových přepsání ke stažení výchozích hodnot](media/phone-based-mfa/download-defaults.png)

   5. Otevřete soubor JSON, který jste stáhli v předchozím kroku. V souboru vyhledejte `DEFAULT` a nahraďte řádek řetězcem `"Value": "{\"DEFAULT\":\"Country/Region\",\"US\":\"United States\"}"` . Nezapomeňte nastavit `Overrides` na `true` .

   > [!NOTE]
   > Seznam povolených kódů zemí můžete přizpůsobit v `countryList` elementu (příklad najdete na [stránce ověřování od telefonního faktoru](localization-string-ids.md#phone-factor-authentication-page-example)).

   7. Uložte soubor JSON. Na panelu Podrobnosti o jazyku v části **nahrát nová přepsání** vyberte upravený soubor JSON, který se odešle.

   8. Zavřete panel a vyberte **Spustit tok uživatele**. V tomto příkladu potvrďte, že **USA** je jediný kód země dostupný v rozevíracím seznamu:
 
      ![Rozevírací seznam pro kód země](media/phone-based-mfa/country-code-drop-down.png)

## <a name="next-steps"></a>Další kroky

- Další informace o [identitě a podmíněném přístupu pro Azure AD B2C](conditional-access-identity-protection-overview.md) 

- Použití [podmíněného přístupu pro toky uživatelů v Azure Active Directory B2C](conditional-access-user-flow.md)