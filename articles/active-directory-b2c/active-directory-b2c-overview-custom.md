---
title: 'Služby Azure Active Directory B2C: Vlastní zásady | Microsoft Docs'
description: Téma na Azure Active Directory B2C vlastní zásady
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/04/2017
ms.author: davidmu
ms.openlocfilehash: 4c09fa5c454cee6ca9a0ed0d3fd4582b222c0c1a
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="azure-active-directory-b2c-custom-policies"></a>Azure Active Directory B2C: Vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="what-are-custom-policies"></a>Co jsou vlastní zásady?

Vlastní zásady jsou konfigurační soubory, které určují chování tenanta služby Azure AD B2C. Zatímco **integrovaných zásad** předdefinovaných na portálu Azure AD B2C nejčastějších úloh, identity, vlastní zásady dá plně upravit podle vývojář identity k dokončení near neomezený počet úloh. Pokračujte ve čtení a určí, jestli jsou pro vás ideální a váš scénář identity vlastní zásady.

## <a name="comparing-built-in-policies-and-custom-policies"></a>Porovnání zásad předdefinované a vlastní zásady

| | Předdefinované zásady | Vlastní zásady |
|-|-------------------|-----------------|
|Cíloví uživatelé | Všechny vývojáři aplikací s nebo bez znalosti identity | Odborníci na identitu: integrátorem systémů, konzultanty a týmy interní identity. Jsou tedy se OpenIDConnect toky a pochopit poskytovatelů identit a ověřování na základě deklarace identity |
|Konfigurace – metoda | Portál Azure s uživatelsky přívětivý uživatelského rozhraní | Přímo úpravy souborů XML a pak se nahrávají na portálu Azure |
|Přizpůsobení uživatelského rozhraní | Úplné přizpůsobení uživatelského rozhraní, včetně podpory HTML, CSS a javascript (vyžaduje vlastní domény)<br><br>Podpora více jazyků s vlastní řetězce | stejné |
| Vlastní nastavení atributu | Standardní a vlastní atributy | stejné |
|Token a relace správy | Vlastní token a více možností relace | stejné |
|Zprostředkovatelé identit| **Dnes**: předdefinované místní, sociálních zprostředkovatele<br><br>**Budoucí**: založených na standardech OIDC SAML, OAuth | **Dnes**: založených na standardech OIDC OAUTH, SAML<br><br>**Budoucí**: WsFed |
|Úlohy identity (příklady) | Zaregistrujte nebo Přihlaste se pomocí místního a mnoho sociálních účty<br><br>Samoobslužné resetování hesla<br><br>Úpravy profilu<br><br>Služba Multi-Factor Auth scénáře<br><br>Přizpůsobení tokeny a relace<br><br>Tok tokenu přístupu | Dokončení úloh jako integrovaných zásad pomocí poskytovatelů vlastní identitu nebo používat vlastní obory<br><br>Zřízení uživatele v jiném systému v době registrace<br><br>Odeslání Uvítacího e-mailu pomocí vlastního zprostředkovatele služby e-mailu<br><br>Použít úložiště uživatele mimo B2C<br><br>Ověření uživatele informace s důvěryhodné systému prostřednictvím rozhraní API |

## <a name="policy-files"></a>Soubory zásad

Vlastní zásady je reprezentován jako jednoho nebo několika formátu XML soubory, které odkazovat navzájem v hierarchické řetězu. Elementy XML definice: schéma deklarace identity, deklarace transformace, obsahu definice, profily poskytovatelů a technické deklarace identity a Userjourney kroků Orchestrace, mezi další prvky.

Doporučujeme použít tři typy souborů zásad:

- **ZÁKLADNÍHO souboru**, který obsahuje většinu definice a pro které Azure poskytuje kompletní příklad.  Doporučujeme, že abyste vytvořili minimální počet změn do tohoto souboru, které pomáhají při řešení potíží a dlouhodobé údržby zásad
- **soubor rozšíření** , obsahuje změny konfigurace jedinečný pro vašeho klienta
- **soubor předávající strany (RP)** tedy jednoho souboru zaměřené na úlohy, která je volána přímo z aplikace nebo služby (neboli předávající strany).  Přečtěte si článek na soubor definice zásad Další informace.  Každý úkol jedinečný vyžaduje vlastní RP a v závislosti na branding požadavky číslo může být "Celkový počet aplikací x celkový počet případů použití".


Předdefinované zásady v Azure AD B2C, postupujte podle vzoru souboru 3, které popsané výše, ale vývojář se zobrazují pouze soubor předávající strany (RP), zatímco na portálu provede změny na pozadí v souboru rozšíření.

## <a name="core-concepts-you-should-know-when-using-custom-policies"></a>Základní koncepty, které byste měli vědět při použití vlastních zásad

### <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C

Azure zákazníka identit a přístupu (CIAM) služba management. Služba zahrnuje:

1. Adresář uživatelského ve formě speciální Azure Active Directory přístupné přes Microsoft Graph a který obsahuje data o místní účty a účty federované uživatele 
2. Přístup k **Identity rozhraní Framework** která orchestruje vztah důvěryhodnosti mezi uživateli a entity a předá deklarací identity mezi nimi k dokončení úlohy správy identitě/přístup 
3. Služby tokenů zabezpečení (STS) vydávání id tokeny, aktualizace tokeny a přístup tokeny (a ekvivalentní kontrolní výrazy SAML) a ověřování je pro ochranu prostředků.

Azure AD B2C komunikuje s zprostředkovatelů identity jiných systémů, uživatelů a se adresáři místního uživatele v pořadí k dosažení úkol identity aplikace (například přihlášení uživatele, registraci nového uživatele, resetovat heslo). Základní platformu, která vytváří více stran vztah důvěryhodnosti a provede tyto kroky je volána rozhraní prostředí Identity a zásady (také nazývané cesty uživatele nebo zásady důvěryhodnosti framework) explicitně definuje aktéři, akce, protokolů, a pořadí kroků dokončete.

### <a name="identity-experience-framework"></a>Architektura prostředí identit

Plně konfigurovatelné řízená zásadami, cloudové platformy Azure, které orchestrují vztah důvěryhodnosti mezi entity (široce zprostředkovatelů deklarací) v standardní protokol formáty například OpenIDConnect, OAuth, SAML, WSFed a několik nestandardní ty (např. REST API-based systému systému deklarací výměnu). Vytvoří I2E uživatelsky přívětivý, whitelabelled vyskytne, která podporují HTML, CSS a javascript.  Rozhraní prostředí Identity v současné době je k dispozici pouze v rámci služby Azure AD B2C a úlohy týkající se CIAM seřazený podle priority.

### <a name="built-in-policies"></a>Předdefinované zásady

Předdefinované konfigurační soubory, které budou řídit chování Azure AD B2C provést nejvíc běžně používané identity úlohy (tj. uživatel registrace, přihlášení, resetování hesla) a interakci s důvěryhodné strany, jejichž relace je také předdefinovaná v Azure AD B2C (pro Příklad Facebook zprostředkovatele identity, LinkedIn, Account Microsoft, Google účty).  V budoucnu integrovaných zásad může taky poskytnout k přizpůsobení zprostředkovatelů identity, které jsou obvykle ve sféře enterprise, například Azure Active Directory Premium, Active Directory nebo ADFS Salesforce ID zprostředkovatele.


### <a name="custom-policies"></a>Vlastní zásady

Konfigurační soubory, které definují chování rozhraní Framework Identity ve vašem klientovi Azure AD B2C. Vlastní zásady přístupný jako jednoho nebo několika souborů XML (viz definice soubory zásad) spouštěných rámcem prostředí Identity při vyvolání předávající strana (například aplikace). Vlastní zásady můžete přímo upravovat vývojář identity k dokončení near neomezený počet úloh. Vývojáři vlastní zásady konfigurace musí definovat důvěryhodné vztahy pečlivě podrobně zahrnout koncové body metadat, přesný deklarací exchange definice a konfigurace tajné klíče, klíče a certifikáty podle potřeby jednotlivých poskytovatele identit.

## <a name="policy-file-definitions-for-identity-experience-framework-trustframeworks"></a>Soubor definice zásady pro Trustframeworks Framework prostředí Identity

### <a name="policy-files"></a>Soubory zásad

Vlastní zásady je reprezentován jako jednoho nebo několika souborů ve formátu XML odkazující na sebe navzájem v hierarchické řetězu. Elementy XML definice: schématu deklarace identity deklarací transformace, obsahu definice, profily poskytovatelů a technické deklarace identity a kroků Orchestrace cesty uživatele, mezi další prvky.  Doporučujeme použít tři typy souborů zásad:

- **ZÁKLADNÍHO souboru** obsahuje většinu definice a pro které Azure poskytuje kompletní příklad.  Doporučujeme, že abyste vytvořili minimální počet změn do tohoto souboru, které pomáhají při řešení potíží a dlouhodobé údržby zásad
- **soubor rozšíření** , obsahuje změny konfigurace jedinečný pro vašeho klienta
- **soubor předávající strany (RP)** je jeden soubor zaměřené na úlohy, která je volána přímo z aplikace nebo služby (neboli předávající strany).  Přečtěte si článek na soubor definice zásad Další informace.  Každý úkol jedinečný vyžaduje vlastní RP a v závislosti na branding požadavky číslo může být "Celkový počet aplikací x celkový počet případů použití".

![Typy souborů zásad](media/active-directory-b2c-overview-custom/active-directory-b2c-overview-custom-policy-files.png)

| Typ zásad souboru | Příklady název souboru | Doporučené použití | Dědí z |
|---------------------|--------------------|-----------------|---------------|
| ZÁKLADNÍ |TrustFrameworkBase.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_BASE1.xml | Obsahuje základní deklarace identity schématu, transformace deklarací, zprostředkovatelů deklarací identit a cesty uživatele konfigurovat tak, že Microsoft<br><br>Ujistěte se, minimální změny do tohoto souboru | Žádný |
| Rozšíření (EXT) | TrustFrameworkExtensions.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_EXT.xml | Konsolidovat všechny změny základního souboru<br><br>Zprostředkovatelé upravené deklarací<br><br>Cesty upravené uživatele<br><br>Vlastní definice vlastní schéma | ZÁKLADNÍHO souboru |
| Předávající stranu | B2C_1A_sign_up_sign_in.xml| Token tvar a relace nastavení změnit tady| Soubor Extensions(ext) |

### <a name="inheritance-model"></a>Model dědičnosti

Pokud aplikace zavolá soubor zásad RP, rozhraní Identity prostředí v B2C přidá všechny elementy z základní a pak z rozšíření a nakonec ze souboru zásad RP ke kompilaci aktuální zásady platit.  Elementy stejného typu a název v souboru RP se přepíšou nastavení v rozšíření, a rozšíření přepsání BASE.

**Předdefinované zásady** v Azure AD B2C, postupujte podle vzoru souboru 3, které popsané výše, ale vývojář se zobrazují pouze soubor předávající strany (RP), zatímco na portálu provede změny na pozadí EXTenstions souboru.  Všechny Azure AD B2C sdílí základní zásady souboru, který je pod kontrolou týmu Azure B2C a se často aktualizuje.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Začínáme s vlastní zásady](active-directory-b2c-get-started-custom.md)
