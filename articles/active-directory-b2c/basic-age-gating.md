---
title: Povolení věkového nárůstu ve službě Azure Active Directory B2C | Dokumenty společnosti Microsoft
description: Přečtěte si, jak pomocí aplikace identifikovat nezletilé.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 56cbeb8e8fe21f4b39c2f5c6af43e83ae330e5d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189969"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Povolení věkového gazeření ve službě Azure Active Directory B2C

>[!IMPORTANT]
>Tato funkce je ve verzi Public Preview. Nepoužívejte funkci pro produkční aplikace.
>

Věkové gating v Azure Active Directory B2C (Azure AD B2C) umožňuje identifikovat nezletilé osoby, které chtějí používat vaši aplikaci. Můžete zablokovat nezletilého z přihlášení do aplikace. Uživatelé se také mohou vrátit k aplikaci a identifikovat svou věkovou skupinu a stav souhlasu rodičů. Azure AD B2C můžete blokovat nezletilé osoby bez souhlasu rodičů. Azure AD B2C můžete také nastavit tak, aby aplikace mohla rozhodnout, co dělat s nezletilými.

Po povolení věkového omezení v [toku uživatelů](user-flow-overview.md)jsou uživatelé dotázáni, kdy se narodili a v jaké zemi nebo oblasti žijí. Pokud se uživatel přihlásí, který informace ještě nezadal, bude je muset zadat při příštím přihlášení. Pravidla se použijí při každém přihlášení uživatele.

Azure AD B2C používá informace, které uživatel zadá k identifikaci, zda jsou nezletilé. Pole **ageGroup** se pak aktualizuje v jejich účtu. Hodnota může `null`být `Undefined` `Minor`, `Adult`, `NotAdult`, a .  Pole **ageGroup** a **consentProvidedForMinor** se pak používají k výpočtu hodnoty **legalAgeGroupClassification**.

Věkové změny zahrnují dvě věkové hodnoty: věk, kdy někdo již není považován za nezletilou osobu, a věk, ve kterém musí mít nezletilá osoba souhlas rodičů. V následující tabulce jsou uvedena věková pravidla, která se používají k definování nezletilé a nezletilé osoby vyžadující souhlas.

| Země/region | Název země nebo oblasti | Věk menšího souhlasu | Nezletilý věk |
| -------------- | ------------------- | ----------------- | --------- |
| Výchozí | Žádný | Žádný | 18 |
| AE | Spojené arabské emiráty | Žádný | 21 |
| AT | Rakousko | 14 | 18 |
| BE | Belgie | 14 | 18 |
| BG | Bulharsko | 16 | 18 |
| BH | Bahrajn | Žádný | 21 |
| CM | Kamerun | Žádný | 21 |
| CY | Kypr | 16 | 18 |
| CZ | Česká republika | 16 | 18 |
| DE | Německo | 16 | 18 |
| DK | Dánsko | 16 | 18 |
| EE | Estonsko | 16 | 18 |
| EG | Egypt | Žádný | 21 |
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
| Není k dispozici | Namibie | Žádný | 21 |
| NL | Nizozemsko | 16 | 18 |
| PL | Polsko | 13 | 18 |
| PT | Portugalsko | 16 | 18 |
| RO | Rumunsko | 16 | 18 |
| SE | Švédsko | 13 | 18 |
| SG | Singapur | Žádný | 21 |
| SI | Slovinsko | 16 | 18 |
| SK | Slovensko | 16 | 18 |
| TD | Čad | Žádný | 21 |
| TH | Thajsko | Žádný | 20 |
| TW | Tchaj-wan | Žádný | 20 |
| USA | Spojené státy | 13 | 18 |

## <a name="age-gating-options"></a>Možnosti věkového vzpěračů

### <a name="allowing-minors-without-parental-consent"></a>Povolení nezletilých osob bez souhlasu rodičů

Pro toky uživatelů, které umožňují registraci, přihlášení nebo obojí, můžete povolit nezletilé osoby bez souhlasu do vaší aplikace. Nezletilí bez souhlasu rodičů se mohou přihlásit nebo zaregistrovat jako normální a Azure AD B2C vydá token ID s **deklarací legalAgeGroupClassification.** Toto tvrzení definuje prostředí, které uživatelé mají, jako je například shromažďování souhlasu rodičů a aktualizace **pole ConsentProvidedForMinor.**

### <a name="blocking-minors-without-parental-consent"></a>Blokování nezletilých bez souhlasu rodičů

Pro toky uživatelů, které umožňují registraci, přihlášení nebo obojí, můžete blokovat nezletilé osoby bez souhlasu aplikace. Následující možnosti jsou k dispozici pro zpracování blokovaných uživatelů v Azure AD B2C:

- Odeslat JSON zpět do aplikace - tato možnost odešle odpověď zpět do aplikace, která byla blokována nezletilého.
- Zobrazit chybovou stránku – uživateli se zobrazí stránka informující, že nemá přístup k aplikaci.

## <a name="set-up-your-tenant-for-age-gating"></a>Nastavení tenanta pro věkové nastavení

Chcete-li použít věkové gazece v toku uživatele, musíte nakonfigurovat klienta mít další vlastnosti.

1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C výběrem directory **+ předplatné** filtr v horní nabídce. Vyberte adresář, který obsahuje vašeho klienta.
2. V levém horním rohu portálu Azure vyberte **Všechny služby,** vyhledejte a vyberte **Azure AD B2C**.
3. V nabídce vlevo vyberte **Vlastnosti** pro vašeho tenanta.
2. V části **Age gating** klikněte na **Configure**.
3. Počkejte na dokončení operace a váš tenant bude nastaven pro věkové nastavení.

## <a name="enable-age-gating-in-your-user-flow"></a>Povolení věkového omezení v uživatelském toku

Po nastavení vašeho tenanta pro použití věkové hodu můžete tuto funkci použít v [tocích uživatelů,](user-flow-versions.md) kde je povolena. Můžete povolit věkové vzponí ní s následujícími kroky:

1. Vytvořte tok uživatele, který má povoleno věkové gating.
2. Po vytvoření toku uživatele vyberte **vlastnosti** v nabídce.
3. V části **Věk gatingu** vyberte **Povoleno**.
4. Poté se rozhodnete, jak chcete spravovat uživatele, kteří se identifikují jako nezletilí. V **případě registrace nebo přihlášení**vyberete `Block minors from accessing your application` `Allow minors to access your application` nebo . Pokud je vybrána možnost `Send a JSON back to the application` blokování `Show an error message`nezletilých osob, vyberete nebo .




