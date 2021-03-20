---
title: Azure Active Directory B2C vlastní zásady | Microsoft Docs
description: Přečtěte si o Azure Active Directory B2C vlastních zásadách.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/06/2019
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 043cf83d804803e1b5b47d3ac51bbccaa06e4e87
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87116423"
---
# <a name="custom-policies-in-azure-active-directory-b2c"></a>Vlastní zásady v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Vlastní zásady jsou konfigurační soubory, které definují chování tenanta Azure Active Directory B2C (Azure AD B2C). Na portálu Azure AD B2C jsou předdefinované toky uživatelů pro nejběžnější úlohy identit. Vývojář identit může plně upravit vlastní zásady pro provádění různých úloh.

## <a name="comparing-user-flows-and-custom-policies"></a>Porovnání toků uživatelů a vlastních zásad

| Kontext | Toky uživatele | Vlastní zásady |
|-|-------------------|-----------------|
| Cíloví uživatelé | Všichni vývojáři aplikací s odbornými znalostmi identity nebo bez nich. | Specialisté na identity, systémy integrátorů, konzultantů a interních identit. Jsou pohodlné díky tokům OpenID Connect a pochopení zprostředkovatelů identit a ověřování založeného na deklaracích identity. |
| Metoda konfigurace | Azure Portal s uživatelsky přívětivým uživatelským rozhraním (UI). | Přímo upravování souborů XML a následné nahrávání do Azure Portal. |
| Přizpůsobení uživatelského rozhraní | Úplné přizpůsobení uživatelského rozhraní, včetně HTML, CSS a JavaScriptu.<br><br>Vícejazyčná podpora s vlastními řetězci. | Stejné |
| Přizpůsobení atributu | Standardní a vlastní atributy. | Stejné |
| Správa tokenů a relací | Vlastní token a možnosti více relací. | Stejné |
| Zprostředkovatelé identit | Předdefinovaný místní nebo sociální zprostředkovatel a většina poskytovatelů OIDC identity, jako je například federace s Azure Active Directory klienty. | OIDC založené na standardech, OAUTH a SAML.  Ověřování je také možné pomocí integrace s rozhraními REST API. |
| Úkoly identity | Zaregistrujte se nebo přihlaste pomocí místních nebo mnoha sociálních účtů.<br><br>Samoobslužné resetování hesla.<br><br>Úprava profilu<br><br>Multi-Factor Authentication.<br><br>Přizpůsobení tokenů a relací.<br><br>Toky přístupového tokenu. | Dokončete stejné úlohy jako toky uživatelů pomocí vlastních zprostředkovatelů identity nebo použijte vlastní obory.<br><br>Zřídit uživatelský účet v jiném systému v době registrace.<br><br>Poslat uvítací e-mail pomocí vlastního poskytovatele e-mailové služby.<br><br>Použijte úložiště uživatele mimo Azure AD B2C.<br><br>Pomocí rozhraní API ověřte informace poskytnuté uživatelem s důvěryhodným systémem. |

## <a name="policy-files"></a>Soubory zásad

Používají se tyto tři typy souborů zásad:

- **Základní soubor** – obsahuje většinu definicí. Doporučujeme, abyste v tomto souboru provedli minimální počet změn, které vám pomůžou s odstraňováním potíží a dlouhodobou údržbou vašich zásad.
- **Soubor rozšíření** – obsahuje jedinečné změny konfigurace vašeho tenanta.
- **Soubor předávající strany (RP)** – jeden soubor zaměřený na úlohy, který je vyvolán přímo pomocí aplikace nebo služby (také označovaný jako předávající strana). Každý jedinečný úkol vyžaduje svůj vlastní RP a v závislosti na požadavcích na branding může být číslo "celkem aplikací x celkový počet případů použití".

Tok uživatelů v Azure AD B2C postupujte podle výše popsaného vzoru souboru, ale vývojář uvidí pouze soubor RP, zatímco Azure Portal provede změny v pozadí souboru rozšíření.

I když existují tři typy souborů zásad, nebudete omezeni pouze na tři soubory. Můžete mít více souborů každého typu souboru. Například pokud nechcete provádět změny v souboru rozšíření, můžete vytvořit soubor Extensions2 k dalšímu rozšíření souboru rozšíření.

## <a name="custom-policy-core-concepts"></a>Základní koncepty vlastních zásad

Služba CIAM (Customer identity and Access Management) v Azure zahrnuje:

- Adresář uživatele, který je přístupný pomocí Microsoft Graph a který obsahuje uživatelská data pro místní účty i federované účty.
- Přístup k **architektuře prostředí identity** , která orchestruje vztah důvěryhodnosti mezi uživateli a entitami a předává deklarace mezi nimi k dokončení úlohy správy identity nebo přístupu.
- Služba tokenů zabezpečení (STS), která vydává tokeny ID, aktualizují tokeny a přístupové tokeny (a ekvivalentní kontrolní výrazy SAML) a ověřuje je k ochraně prostředků.

Azure AD B2C vzájemně spolupracuje s poskytovateli identity, uživateli, jinými systémy a s adresářem místního uživatele v sekvenci, aby dosáhli úkolu identity. Přihlaste se například uživateli, zaregistrujte nového uživatele nebo resetujte heslo. Architektura prostředí identit a zásady (označované také jako cesta uživatele nebo zásada architektury pro vztahy důvěryhodnosti) vytváří vztah důvěryhodnosti více stran a explicitně definují objekty actor, akce, protokoly a posloupnost kroků k dokončení.

Architektura prostředí identit je plně konfigurovatelná cloudová platforma Azure založená na zásadách, která orchestruje vztah důvěryhodnosti mezi entitami ve standardních formátech protokolů, jako jsou OpenID Connect, OAuth, SAML a několik nestandardních, například REST APIch výměn deklarací z systému na systém. Rozhraní vytváří uživatelsky přívětivé prostředí s označením, které podporuje HTML a CSS.

Vlastní zásada je vyjádřena jako jeden nebo více souborů ve formátu XML, které odkazují na sebe navzájem v hierarchickém řetězu. Prvky XML definují schéma deklarací identity, transformace deklarací identity, definice obsahu, zprostředkovatele deklarací, technické profily a kroky orchestrace cest uživatelů mimo jiné prvky. Vlastní zásada je přístupná jako jeden nebo několik souborů XML, které jsou spouštěny architekturou prostředí identity při vyvolání předávající stranou. Vývojáři, kteří konfigurují vlastní zásady, musí podrobná nastavení důvěryhodných vztahů za účelem zahrnutí koncových bodů metadat, přesných definic deklarací identity a konfigurací tajných kódů, klíčů a certifikátů podle potřeby u každého poskytovatele identity.

### <a name="inheritance-model"></a>Model dědičnosti

Když aplikace zavolá soubor zásad RP, rozhraní identity Experience v Azure AD B2C přidá všechny prvky ze základního souboru, ze souboru rozšíření a pak ze souboru zásad RP, aby sestavoval aktuální zásady.  Prvky stejného typu a názvu v souboru RP budou přepsány na základě rozšíření a přepsání rozšíření Bases.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme s vlastními zásadami](custom-policy-get-started.md)
