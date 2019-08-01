---
title: Azure Active Directory B2C vlastní zásady | Microsoft Docs
description: Přečtěte si o Azure Active Directory B2C vlastních zásadách.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: be6d54886f23b0fa219b1e4b8948b4a4c51f5864
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716827"
---
# <a name="custom-policies-in-azure-active-directory-b2c"></a>Vlastní zásady v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Vlastní zásady jsou konfigurační soubory, které definují chování vašeho tenanta Azure Active Directory (Azure AD) B2C. Uživatelské toky jsou předdefinované na portálu Azure AD B2C pro nejběžnější úlohy identity. Vlastní zásady mohou být plně upravovány vývojářem identity, aby bylo možné dokončit mnoho různých úloh.

## <a name="comparing-user-flows-and-custom-policies"></a>Porovnání toků uživatelů a vlastních zásad

| | Toky uživatele | Vlastní zásady |
|-|-------------------|-----------------|
| Cíloví uživatelé | Všichni vývojáři aplikací s odbornými znalostmi identity nebo bez nich. | Specialisté na identity, systémy integrátorů, konzultantů a interních identit. Jsou pohodlné díky tokům OpenID Connect a pochopení zprostředkovatelů identit a ověřování založeného na deklaracích identity. |
| Metoda konfigurace | Azure Portal s uživatelsky přívětivým uživatelským rozhraním (UI). | Přímo upravování souborů XML a následné nahrávání do Azure Portal. |
| Přizpůsobení uživatelského rozhraní | Úplné přizpůsobení uživatelského rozhraní, včetně HTML, CSS a JavaScriptu.<br><br>Vícejazyčná podpora s vlastními řetězci. | Jedné |
| Přizpůsobení atributu | Standardní a vlastní atributy. | Jedné |
| Správa tokenů a relací | Vlastní token a možnosti více relací. | Jedné |
| Zprostředkovatelé identit | Předdefinovaný místní nebo sociální zprostředkovatel a většina poskytovatelů OIDC identity, jako je například federace s Azure Active Directory klienty. | OIDC založené na standardech, OAUTH a SAML.  Ověřování je také možné pomocí integrace s rozhraními REST API. |
| Úkoly identity | Zaregistrujte se nebo přihlaste pomocí místních nebo mnoha sociálních účtů.<br><br>Samoobslužné resetování hesla.<br><br>Úprava profilu<br><br>Multi-Factor Authentication.<br><br>Přizpůsobení tokenů a relací.<br><br>Toky přístupového tokenu. | Dokončete stejné úlohy jako toky uživatelů pomocí vlastních zprostředkovatelů identity nebo použijte vlastní obory.<br><br>Zřídit uživatelský účet v jiném systému v době registrace.<br><br>Poslat uvítací e-mail pomocí vlastního poskytovatele e-mailové služby.<br><br>Použijte úložiště uživatele mimo Azure AD B2C.<br><br>Pomocí rozhraní API ověřte informace poskytnuté uživatelem s důvěryhodným systémem. |

## <a name="policy-files"></a>Soubory zásad

Používají se tyto tři typy souborů zásad:

- **Základní soubor** – obsahuje většinu definicí. Doporučujeme, abyste v tomto souboru provedli minimální počet změn, které vám pomůžou s odstraňováním potíží a dlouhodobou údržbou vašich zásad.
- **Soubor rozšíření** – obsahuje jedinečné změny konfigurace vašeho tenanta.
- **Soubor předávající strany (RP)** – jeden soubor zaměřený na úlohy, který je vyvolán přímo pomocí aplikace nebo služby (také označovaný jako předávající strana). Každý jedinečný úkol vyžaduje svůj vlastní RP a v závislosti na požadavcích na branding může být číslo "celkem aplikací x celkový počet případů použití".

Tok uživatelů v Azure AD B2C postupujte podle výše popsaného vzoru se třemi soubory, ale vývojář uvidí pouze soubor RP, zatímco Azure Portal provede změny v pozadí do souboru rozšíření.

## <a name="custom-policy-core-concepts"></a>Základní koncepty vlastních zásad

Služba CIAM (Customer identity and Access Management) v Azure zahrnuje:

- Adresář uživatele, který je přístupný pomocí Microsoft Graph a který obsahuje uživatelská data pro místní účty i federované účty.
- Přístup k **architektuře prostředí identity** , která orchestruje vztah důvěryhodnosti mezi uživateli a entitami a předává deklarace mezi nimi k dokončení úlohy správy identity nebo přístupu.
- Služba tokenů zabezpečení (STS), která vydává tokeny ID, aktualizují tokeny a přístupové tokeny (a ekvivalentní kontrolní výrazy SAML) a ověřuje je k ochraně prostředků.

Azure AD B2C vzájemně spolupracuje s poskytovateli identity, uživateli, jinými systémy a s adresářem místního uživatele v sekvenci, aby dosáhli úkolu identity. Přihlaste se například uživateli, zaregistrujte nového uživatele nebo resetujte heslo. Architektura prostředí identit a zásady (označované také jako cesta uživatele nebo zásada architektury pro vztahy důvěryhodnosti) vytváří vztah důvěryhodnosti více stran a explicitně definují objekty actor, akce, protokoly a posloupnost kroků k dokončení.

Architektura prostředí identit je plně konfigurovatelná cloudová platforma Azure založená na zásadách, která orchestruje vztah důvěryhodnosti mezi entitami ve standardních formátech protokolů, jako jsou OpenID Connect, OAuth, SAML, WSFed a několik nestandardních, například REST. Výměny deklarací identity od systému založeného na rozhraní API. Rozhraní vytváří uživatelsky přívětivé prostředí s označením, které podporuje HTML a CSS.

Vlastní zásada je vyjádřena jako jeden nebo více souborů ve formátu XML, které odkazují na sebe navzájem v hierarchickém řetězu. Prvky XML definují schéma deklarací identity, transformace deklarací identity, definice obsahu, zprostředkovatele deklarací, technické profily a kroky orchestrace cest uživatelů mimo jiné prvky. Vlastní zásada je přístupná jako jeden nebo několik souborů XML, které jsou spouštěny architekturou prostředí identity při vyvolání předávající stranou. Vývojáři, kteří konfigurují vlastní zásady, musí podrobná nastavení důvěryhodných vztahů za účelem zahrnutí koncových bodů metadat, přesných definic deklarací identity a konfigurací tajných kódů, klíčů a certifikátů podle potřeby u každého poskytovatele identity.

### <a name="inheritance-model"></a>Model dědičnosti

Když aplikace zavolá soubor zásad RP, rozhraní identity Experience v Azure AD B2C přidá všechny prvky ze základního souboru, ze souboru rozšíření a pak ze souboru zásad RP, aby sestavoval aktuální zásady.  Prvky stejného typu a názvu v souboru RP budou přepsány na základě rozšíření a přepsání rozšíření Bases.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md)
