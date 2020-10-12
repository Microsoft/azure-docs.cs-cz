---
title: Povolit omezení stáří v Azure Active Directory B2C | Microsoft Docs
description: Přečtěte si, jak identifikovat podverze pomocí vaší aplikace.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/13/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6bd93f9062f8446ce20436a7a04e2054aaf5be71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85386127"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Povolit omezení stáří v Azure Active Directory B2C

>[!IMPORTANT]
>Tato funkce je ve verzi Public Preview. Nepoužívejte funkci pro produkční aplikace.
>

Omezení stáří v Azure Active Directory B2C (Azure AD B2C) umožňuje identifikovat nezletilé, které chtějí používat vaši aplikaci. Můžete se rozhodnout, že nechcete, aby se v přihlašování k aplikaci zablokoval. Uživatelé se také můžou vrátit do aplikace a identifikovat jejich věkovou skupinu a jejich stav souhlasu rodičů. Azure AD B2C mohou blokovat mladistvé bez souhlasu rodičů. Je také možné nastavit Azure AD B2C, aby aplikace mohla rozhodnout o tom, co dělat s nezletilými.

Po povolení stáří v [uživatelském toku](user-flow-overview.md)se uživatelům zobrazí výzva, když byli narodili a ve které zemi nebo oblasti se v nich nacházejí. Pokud se uživatel přihlásí, že dříve nezadal informace, bude nutné ho zadat při příštím přihlášení. Pravidla se aplikují při každém přihlášení uživatele.

Azure AD B2C používá informace, které uživatel zadá, k určení, jestli se jedná o vedlejší. Pole **ageGroup** se pak ve svém účtu aktualizuje. Hodnota může být `null` , `Undefined` ,, `Minor` `Adult` a `NotAdult` .  Pole **ageGroup** a **consentProvidedForMinor** se pak použijí k výpočtu hodnoty **legalAgeGroupClassification**.

Omezení stáří zahrnuje dvě věkové hodnoty: stáří, které už někdo nepovažuje za nezletilou, a stáří, ve kterém by měl mít uživatel souhlas rodičů. V následující tabulce jsou uvedena věková pravidla, která se používají k definování vedlejších a méně závažného souhlasu.

| Země/oblast | Název země nebo oblasti | Věk pro menší souhlas | Menší stáří |
| -------------- | ------------------- | ----------------- | --------- |
| Výchozí | Žádné | Žádné | 18 |
| AE | Spojené arabské emiráty | Žádné | 21 |
| AT | Rakousko | 14 | 18 |
| BE | Belgie | 14 | 18 |
| BG | Bulharsko | 16 | 18 |
| BH | Bahrajn | Žádné | 21 |
| CM | Kamerun | Žádné | 21 |
| CY | Kypr | 16 | 18 |
| CZ | Česká republika | 16 | 18 |
| DE | Německo | 16 | 18 |
| DK | Dánsko | 16 | 18 |
| EE | Estonsko | 16 | 18 |
| EG | Egypt | Žádné | 21 |
| ES | Španělsko | 13 | 18 |
| FR | Francie | 16 | 18 |
| GB | Spojené království | 13 | 18 |
| GR | Řecko | 16 | 18 |
| HR | Chorvatsko | 16 | 18 |
| HU | Maďarsko | 16 | 18 |
| IE | Irsko | 13 | 18 |
| IT | Itálie | 16 | 18 |
| KR | Korejská republika | 14 | 18 |
| LT | Litva | 16 | 18 |
| LU | Lucembursko | 16 | 18 |
| LV | Lotyšsko | 16 | 18 |
| MT | Malta | 16 | 18 |
| Není k dispozici | Namibie | Žádné | 21 |
| NL | Nizozemsko | 16 | 18 |
| PL | Polsko | 13 | 18 |
| PT | Portugalsko | 16 | 18 |
| RO | Rumunsko | 16 | 18 |
| SE | Švédsko | 13 | 18 |
| SG | Singapur | Žádné | 21 |
| SI | Slovinsko | 16 | 18 |
| SK | Slovensko | 16 | 18 |
| TD | Čad | Žádné | 21 |
| TH | Thajsko | Žádné | 20 |
| TW | Tchaj-wan | Žádné | 20 |
| USA | USA | 13 | 18 |

## <a name="age-gating-options"></a>Možnosti uzavírání stáří

### <a name="allowing-minors-without-parental-consent"></a>Povolení nezletilých bez souhlasu rodičů

U uživatelských toků, které umožňují registraci, přihlašování nebo obojí, se můžete rozhodnout, že nebudete moci bez souhlasu do vaší aplikace poskytnout podverze. Nezletilých osob bez souhlasu rodičů se smějí přihlašovat nebo se zaregistrovat jako normální a Azure AD B2C vydá token ID s deklarací **legalAgeGroupClassification** . Tato deklarace definuje prostředí, které uživatelé mají, jako je například shromažďování souhlasu rodičů a aktualizace pole **consentProvidedForMinor** .

### <a name="blocking-minors-without-parental-consent"></a>Blokování nezletilých bez souhlasu rodičů

U uživatelských toků, které umožňují registraci, přihlášení nebo obojí, se můžete rozhodnout pro blokování nezletilých osob bez souhlasu z aplikace. K dispozici jsou následující možnosti pro zpracování blokovaných uživatelů v Azure AD B2C:

- Odeslat JSON zpátky do aplikace – Tato možnost pošle odpověď zpátky do aplikace, která je zablokovaná.
- Zobrazit chybovou stránku – uživateli se zobrazí stránka s přehledem, že nemají přístup k aplikaci.

## <a name="set-up-your-tenant-for-age-gating"></a>Nastavení tenanta pro věkové omezení

Pokud chcete použít omezení stáří v uživatelském toku, musíte nakonfigurovat svého tenanta, aby měl další vlastnosti.

1. V horní nabídce vyberte adresář **a filtr předplatných** a ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta. Vyberte adresář, který obsahuje vašeho tenanta.
2. V levém horním rohu Azure Portal vyberte **všechny služby** , vyhledejte a vyberte **Azure AD B2C**.
3. V nabídce na levé straně vyberte **vlastnosti** pro vašeho tenanta.
2. V části omezení **stáří** klikněte na **Konfigurovat**.
3. Počkejte na dokončení operace a váš tenant se nastaví na stáří.

## <a name="enable-age-gating-in-your-user-flow"></a>Povolení stáří v uživatelském toku

Po nastavení tenanta pro použití omezení stáří můžete tuto funkci použít v [uživatelských tocích](user-flow-versions.md) , kde je povolená. Dobu platnosti můžete povolit pomocí následujících kroků:

1. Vytvořte tok uživatele s povoleným omezením stáří.
2. Po vytvoření toku uživatele vyberte v nabídce možnost **vlastnosti** .
3. V části omezení **stáří** vyberte **povoleno**.
4. Pak se rozhodnete, jak chcete spravovat uživatele, kteří identifikují jako nezletilých. Pro **registraci nebo přihlášení**vyberte `Allow minors to access your application` nebo `Block minors from accessing your application` . Pokud je vybráno blokování nezletilých, vyberete `Send a JSON back to the application` nebo `Show an error message` .




