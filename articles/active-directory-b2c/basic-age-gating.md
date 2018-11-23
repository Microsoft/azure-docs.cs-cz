---
title: Povolit věku v Azure Active Directory B2C | Dokumentace Microsoftu
description: Další informace o tom, jak identifikovat nezletilým vaši aplikaci používají.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: a9220349249315d807a9dba675f6b074ddd385fa
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2018
ms.locfileid: "52291092"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Povolit věku v Azure Active Directory B2C

>[!IMPORTANT]
>Tato funkce je ve verzi public preview. Nepoužívejte funkci aplikacích v produkčním prostředí. 
>

Věku v Azure Active Directory (Azure AD) B2C umožňuje identifikovat nezletilé osoby, které chcete používat vaši aplikaci. Můžete blokovat menší přihlašování do aplikace. Uživatele můžete také přejít zpět do aplikace a identifikovat jejich kategorie age group a jejich stav svolení rodičů. Azure AD B2C můžete zablokovat nezletilým bez svolení rodičů. Azure AD B2C můžete také nastavit aby aplikace mohla rozhodnout, co dělat s nezletilé osoby.

Po povolení věku ve vaší [tok uživatele](active-directory-b2c-reference-policies.md), uživatelé budou vyzvaní, když se narodili a jaké země žijí. Pokud se uživatel přihlásí, který nebyl dříve zadali informace, budete potřebovat k jeho zadání při příštím přihlášení. Pravidla se použijí pokaždé, když se uživatel přihlásí.

Azure AD B2C používá informace, které uživatel zadá k určení, zda jsou za. **AgeGroup** pole se pak aktualizuje v jejich účtu. Hodnota může být `null`, `Undefined`, `Minor`, `Adult`, a `NotAdult`.  **AgeGroup** a **consentProvidedForMinor** pole se následně použijí k výpočtu hodnoty **legalAgeGroupClassification**.

Věku zahrnuje dvě hodnoty stáří: věk, který už je někdo považuje za nezletilého a věk, ve kterém musí mít za svolení rodičů. Následující tabulka uvádí pravidla věku, které se používají k definování za a dílčí vyžadující souhlas.

| Země | Jméno země | Stáří menší souhlas | Vedlejší věk |
| ------- | ------------ | ----------------- | --------- |
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

## <a name="age-gating-options"></a>Možnosti ověřování věku.
 
### <a name="allowing-minors-without-parental-consent"></a>Povolení nezletilé osoby bez svolení rodičů

Pro toky uživatelů, které umožňují registrace, přihlášení nebo obojí, můžete zvolit, aby nezletilé osoby bez svolení do vaší aplikace. Nezletilé osoby bez svolení rodičů budou moci přihlásit nebo zaregistrovat jako normální a službou Azure AD B2C vydá token ID s **legalAgeGroupClassification** deklarací identity. Tato deklarace identity definuje prostředí, která uživatelé používají, jako je například shromažďování svolení rodičů a aktualizuje **consentProvidedForMinor** pole.

### <a name="blocking-minors-without-parental-consent"></a>Zablokování nezletilé osoby bez svolení rodičů

Toky uživatelů, které umožňují buď registrace, přihlášení, nebo obojí můžete k zablokování nezletilé osoby bez svolení od aplikace. Tyto možnosti jsou k dispozici pro zpracování blokovaným uživatelům v Azure AD B2C:

- Poslat JSON zpět do aplikace – tuto možnost odešle odpověď zpět do aplikace, za byl zablokován.
- Zobrazit chybovou stránku – uživatel je zobrazen na stránce informuje o tom, že nemají přístup k aplikaci.

## <a name="set-up-your-tenant-for-age-gating"></a>Nastavení klientů pro věku

Pokud chcete použít, věku v toku uživatele, musíte nakonfigurovat tenanta pro další vlastnosti.

1. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce. Vyberte adresář, který obsahuje váš tenant. 
2. Vyberte **všechny služby** v levém horním rohu webu Azure portal, vyhledejte a vyberte **Azure AD B2C**.
3. Vyberte **vlastnosti** pro vašeho tenanta v nabídce na levé straně.
2. V části **věku** části, klikněte na **konfigurovat**.
3. Počkejte na dokončení operace a nastaví vašeho tenanta se věku.

## <a name="enable-age-gating-in-your-user-flow"></a>Povolit věku ve svém toku uživatele

Po nastavený věku použití vašeho tenanta se pak můžete použít tuto funkci v [toky uživatelů](user-flow-versions.md) kde je povolena. Povolíte věku pomocí následujících kroků:

1. Vytvořte tok uživatele, který má povolené věku.
2. Když vytvoříte tok uživatele, vyberte **vlastnosti** v nabídce.
3. V **věku** vyberte **povoleno**.
4. Potom rozhodnout, jak chcete spravovat uživatele, které identifikují jako nezletilé osoby. Pro **registrace nebo přihlašování**, vyberete `Allow minors to access your application` nebo `Block minors from accessing your application`. Pokud je vybrána zablokování nezletilé osoby, můžete vybrat `Send a JSON bcak to the application` nebo `Show an error message`. 




