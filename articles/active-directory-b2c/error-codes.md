---
title: Odkaz na kód chyby
titleSuffix: Azure AD B2C
description: Seznam kódů chyb, které může služba Azure Active Directory B2C vrátit.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5b09b3c19ab1c5b23e56e25afc1d9631cd1caa68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188660"
---
# <a name="error-codes-azure-active-directory-b2c"></a>Kódy chyb: Azure Active Directory B2C

Služba Azure Active Directory B2C může vrátit následující chyby.

| Kód chyby | Zpráva |
| ---------- | ------- |
| `AADB2C90002` | Prostředek CORS{0}' ' vrátil 404 nebyl nalezen. |
| `AADB2C90006` | Identifikátor URI přesměrování{0}" " uvedený v požadavku není registrován{1}pro ID klienta " . |
| `AADB2C90007` | Aplikace přidružená k id klienta '{0}' nemá žádné registrované identifikátory URI přesměrování. |
| `AADB2C90008` | Požadavek neobsahuje parametr ID klienta. |
| `AADB2C90010` | Požadavek neobsahuje parametr oboru. |
| `AADB2C90011` | ID klienta{0}" " uvedené v požadavku neodpovídá{1}id klienta " registrovanému v zásadách. |
| `AADB2C90012` | Obor "{0}" uvedený v požadavku není podporován. |
| `AADB2C90013` | Požadovaný typ odpovědi{0}" " uvedený v požadavku není podporován. |
| `AADB2C90014` | Požadovaný režim odpovědi{0}" " uvedený v požadavku není podporován. |
| `AADB2C90016` | Požadovaný typ kontrolního{0}výrazu klienta '{1}neodpovídá očekávanému typu ' . |
| `AADB2C90017` | Kontrolní výraz klienta uvedený v požadavku je neplatný:{0} |
| `AADB2C90018` | ID klienta{0}" zadané v požadavku není{1}registrováno v tenantovi " . |
| `AADB2C90019` | Kontejner klíčů s id '{0}{1}v tenantovi ' nemá platný klíč. Důvod: {2}. |
| `AADB2C90021` | Technický profil{0}" " neexistuje v{1}politice "{2}" nájemce " . |
| `AADB2C90022` | Nelze vrátit metadata pro{0}zásadu "{1}' v tenantovi " . |
| `AADB2C90023` | Profil{0}' ' neobsahuje požadovaný klíč{1}metadat ' . |
| `AADB2C90025` | Profil{0}" v{1}politice "{2}v tenantovi " neobsahuje{3}požadovaný kryptografický klíč " . |
| `AADB2C90027` | Základní pověření zadaná pro '{0}' jsou neplatná. Zkontrolujte, zda jsou pověření správná a zda byl prostředek udělen. |
| `AADB2C90028` | Klientský certifikát{0}zadaný pro " je neplatný. Zkontrolujte, zda je certifikát správný, obsahuje soukromý klíč a zda byl prostředek udělen. |
| `AADB2C90031` | Zásada{0}' ' neurčuje výchozí cestu uživatele. Ujistěte se, že zásady nebo její rodiče určují výchozí cestu uživatele jako součást oddílu předávající strany. |
| `AADB2C90035` | Služba je dočasně nedostupná. Opakujte akci po několika minutách. |
| `AADB2C90036` | Požadavek neobsahuje identifikátor URI, který by uživatele přesměroval k odhlášení. Zadejte identifikátor URI v poli post_logout_redirect_uri parametr. |
| `AADB2C90037` | Při zpracování požadavku došlo k chybě. Obraťte se na správce webu, ke jehož přístupu se pokoušíte. |
| `AADB2C90039` | Požadavek obsahuje kontrolní výraz klienta,{0}ale v{1}zadaných zásadách " v tenantovi " chybí client_secret v relyingPartyPolicy. |
| `AADB2C90040` | Cesta uživatele{0}' ' neobsahuje krok deklarací odeslání. |
| `AADB2C90043` | Výzva zahrnutá v požadavku obsahuje neplatné hodnoty. Očekávané "žádné", "přihlášení", "souhlas" nebo "select_account". |
| `AADB2C90044` | Deklarace{0}" " není překládáním{1}deklarací podporována " . |
| `AADB2C90046` | Máme potíže s načtením vašeho aktuálního stavu. Možná budete chtít zkusit zahájení relace znovu od začátku. |
| `AADB2C90047` | Prostředek '{0}' obsahuje chyby skriptu, které brání jeho načtení. |
| `AADB2C90048` | Na serveru došlo k neošetřené výjimce. |
| `AADB2C90051` | Nebyli nalezeni žádní vhodní poskytovatelé deklarací identity. |
| `AADB2C90052` | Neplatné uživatelské jméno nebo heslo. |
| `AADB2C90053` | Uživatel se zadaným pověřením nebyl nalezen. |
| `AADB2C90054` | Neplatné uživatelské jméno nebo heslo. |
| `AADB2C90055` | Obor "{0}" uvedený v požadavku musí určithttps://example.com/calendar.readzdroj, například " ". |
| `AADB2C90057` | Poskytnutá aplikace není nakonfigurována tak, aby umožňovala tok implicitního oauthu. |
| `AADB2C90058` | Poskytnutá aplikace není nakonfigurována tak, aby umožňovala veřejné klienty. |
| `AADB2C90067` | Identifikátor URI přesměrování odhlášení{0}příspěvku má neplatný formát. Zadejte adresu URL založenou na protokolu https, například ' ' nebo pro nativní klienty používají identifikátor URI nativního klienta IETF urn:ietf:wg:oauth:2.0:oob.Specify an https based URL such as 'https://example.com/return' or for native clients use the IETF native client URI urn:ietf:wg:oauth:2.0:oob '. |
| `AADB2C90068` | Poskytnutá aplikace s ID '{0}' není platná pro tuto službu. Použijte aplikaci vytvořenou prostřednictvím portálu B2C a akci opakujte. |
| `AADB2C90075` | Výměna deklarací{0}" "{1}zadaná v kroku{2}" "{3}vrácená odpověď chyby HTTP s kódem ' a Důvodem ' . |
| `AADB2C90077` | Uživatel nemá existující relaci a parametr výzvy požadavku{0}má hodnotu ' '. |
| `AADB2C90079` | Klienti musí zaslat client_secret při uplatnění důvěrného grantu. |
| `AADB2C90080` | Platnost poskytnutého grantu vypršela. Ověřte znovu a akci opakujte. Aktuální čas: {0}, Grant {1}emitovaný čas: {2}, Udělit čas vypršení platnosti posuvného okna: . |
| `AADB2C90081` | Zadaný client_secret neodpovídá očekávané hodnotě pro tohoto klienta. Opravte client_secret a akci opakujte. |
| `AADB2C90083` | Požadavek chybí požadovaný {0}parametr: . |
| `AADB2C90084` | Veřejní klienti by neměli posílat client_secret při splacení veřejně nabytého grantu. |
| `AADB2C90085` | Služba zjistila vnitřní chybu. Ověřte znovu a akci opakujte. |
| `AADB2C90086` | Zadaná{0}grant_type [ ] není podporována. |
| `AADB2C90087` | Poskytnutá dotace nebyla vydána pro tuto verzi koncového bodu protokolu. |
| `AADB2C90088` | Poskytnutý grant nebyl pro tento koncový bod vydán. Skutečná hodnota {0} : a očekávaná hodnota :{1} |
| `AADB2C90092` | Zadaný soubor aplikace s{0}ID " je{1}pro klienta zakázán . Povolte aplikaci a akci opakujte. |
| `AADB2C90107` | Aplikace s ID{0}' ' nemůže získat token ID buď proto, že obor openid nebyl v požadavku poskytnut nebo pro něj aplikace není autorizována. |
| `AADB2C90108` | Krok orchestrace{0}' ' neurčuje CpimIssuerTechnicalProfileReferenceId, když byl očekáván. |
| `AADB2C90110` | Parametr oboru musí obsahovat "openid" při požadování response_type, který obsahuje "id_token". |
| `AADB2C90111` | Váš účet byl uzamčen. Obraťte se na osobu podpory, abyste ji odemkli, a akci opakujte. |
| `AADB2C90114` | Váš účet je dočasně uzamčen, aby se zabránilo neoprávněnému použití. Zkuste to později. |
| `AADB2C90115` | Při požadavku na "kód" response_type, parametr oboru musí obsahovat prostředek nebo ID klienta pro přístupové tokeny a "openid" pro tokeny ID. Navíc patří "offline_access" pro obnovovací tokeny. |
| `AADB2C90117` | Obor "{0}" uvedený v požadavku není podporován. |
| `AADB2C90118` | Uživatel zapomněl své heslo. |
| `AADB2C90120` | Maximální věková{0}parametr ' ' zadaný v požadavku je neplatný. Maximální stáří musí být celé{1}číslo mezi{2}" a " včetně. |
| `AADB2C90122` | Vstup pro{0}' ' přijaté v požadavku se nezdařilo ověření požadavku HTTP. Ujistěte se, že vstup neobsahuje znaky, jako je < nebo &. |
| `AADB2C90128` | Účet přidružený k tomuto grantu již neexistuje. Ověřte znovu a akci opakujte. |
| `AADB2C90129` | Poskytnutý grant byl odvolán. Ověřte znovu a akci opakujte. |
| `AADB2C90145` | Nebyla nalezena žádná neověřená telefonní čísla a zásady neumožňují uživateli zadané číslo. |
| `AADB2C90146` | Obor "{0}" uvedený v požadavku určuje více než jeden prostředek pro přístupový token, který není podporován. |
| `AADB2C90149` | Načtení{0}skriptu se nezdařilo. |
| `AADB2C90151` | Uživatel překročil maximální počet opakovaných pokusů pro vícefaktorové ověřování. |
| `AADB2C90152` | Vícefaktorový dotaz na dotazování se nepodařilo získat odpověď ze služby. |
| `AADB2C90154` | Vícefaktorový požadavek na ověření se nepodařilo získat id relace ze služby. |
| `AADB2C90155` | Vícefaktorová žádost o ověření se{0}nezdařila z důvodu " ". |
| `AADB2C90156` | Vícefaktorová žádost o ověření se{0}nezdařila s odůvodněním " ". |
| `AADB2C90157` | Uživatel překročil maximální počet opakování pro krok uplatněný samostatně. |
| `AADB2C90158` | Žádost o ověření, která se sama{0}přihlásila, selhala s odůvodněním . |
| `AADB2C90159` | Žádost o ověření, která se sama{0}přihlásila, selhala s odůvodněním " ". |
| `AADB2C90161` | Self-tvrdil odeslat odpověď selhala{0}s odůvodněním ' '. |
| `AADB2C90165` | Zpráva o zahájení aplikace SAML{0}s id ' " nebyla nalezena ve stavu. |
| `AADB2C90168` | Požadavek http-přesměrování neobsahuje požadovaný parametr{0}' ' pro podepsaný požadavek. |
| `AADB2C90178` | Podpisový certifikát{0}' ' nemá žádný soukromý klíč. |
| `AADB2C90182` | Zadaná code_verifier neodpovídá přidruženým code_challenge |
| `AADB2C90183` | Zadaná code_verifier je neplatná. |
| `AADB2C90184` | Zadaná code_challenge_method není podporována. Podporované hodnoty jsou prosté nebo S256 |
| `AADB2C90188` | Technický profil SAML{0}' ' určuje adresu{1}URL Entii partnera ' ,,{2}ale načítání metadat se nezdaří s odůvodněním ' . |
| `AADB2C90194` | Deklarace{0}" ' určená pro nosný token není v dostupných deklaracích k dispozici. Dostupné nároky "{1}". |
| `AADB2C90205` | Tato aplikace nemá dostatečná oprávnění vůči tomuto webovému prostředku k provedení operace. |
| `AADB2C90206` | Došlo k inicializaci klienta časovým časem. |
| `AADB2C90208` | Zadaný parametr id_token_hint vypršel. Zadejte jiný token a akci opakujte. |
| `AADB2C90209` | Zadaný parametr id_token_hint neobsahuje přijatou cílovou skupinu. Platné hodnoty publika: '{0}'. Zadejte jiný token a akci opakujte. |
| `AADB2C90210` | Zadaný parametr id_token_hint nelze ověřit. Zadejte jiný token a akci opakujte. |
| `AADB2C90211` | Žádost obsahovala neúplný stav cookie. |
| `AADB2C90212` | Požadavek obsahoval neplatný stav cookie. |
| `AADB2C90220` | Kontejner klíčů v{0}tenantovi '{1}' s identifikátorem úložiště ' existuje, ale neobsahuje platný certifikát. Platnost certifikátu může vypršet nebo může být certifikát aktivní v budoucnu (nbf). |
| `AADB2C90223` | Došlo k chybě dezinfekce prostředku CORS. |
| `AADB2C90224` | Tok vlastníka prostředku nebyl pro aplikaci povolen. |
| `AADB2C90225` | Uživatelské jméno nebo heslo uvedené v požadavku jsou neplatné. |
| `AADB2C90226` | Zadaná výměna tokenů je podporována pouze přes HTTP POST. |
| `AADB2C90232` | Zadaný id_token_hint parametr neobsahuje přijatého vystavittele. Platné emitenty:{0}' '. Zadejte jiný token a akci opakujte. |
| `AADB2C90233` | Zadaný parametr id_token_hint se nezdařil o ověření podpisu. Zadejte jiný token a akci opakujte. |
| `AADB2C90235` | Platnost poskytnuté id_token vypršela. Zadejte jiný token a akci opakujte. |
| `AADB2C90237` | Poskytnuté id_token neobsahuje platnou cílovou skupinu. Platné hodnoty publika: '{0}'. Zadejte jiný token a akci opakujte. |
| `AADB2C90238` | Poskytnuté id_token neobsahuje platného vystavittele. Platné hodnoty emitenta: '{0}'. Zadejte jiný token a akci opakujte. |
| `AADB2C90239` | Poskytnuté id_token nepodařilo ověření podpisu. Zadejte jiný token a akci opakujte. |
| `AADB2C90240` | Zadaný id_token je poškozený a nelze analyzovat. Zadejte jiný token a akci opakujte. |
| `AADB2C90242` | Technický profil SAML{0}' ' určuje PartnerEntity CDATA,{1}které nelze načíst z důvodu ' '. |
| `AADB2C90243` | Klíč/tajný klíč klienta idp není správně nakonfigurován. |
| `AADB2C90244` | V tuto chvíli je příliš mnoho požadavků. Počkejte chvíli a akci opakujte. |
| `AADB2C90248` | Tok vlastníka prostředků lze použít pouze aplikace vytvořené prostřednictvím portálu pro správu B2C. |
| `AADB2C90250` | Obecný koncový bod přihlášení není podporován. |
| `AADB2C90255` | Výměna pojistných událostí uvedená v technickém profilu "{0}nebyla dokončena očekávaným způsobem. Možná budete chtít zkusit zahájení relace znovu od začátku. |
| `AADB2C90261` | Výměna deklarací{0}identity "{1}zadaná v kroku " vrátila chybovou odpověď HTTP, kterou nebylo možné analyzovat. |
| `AADB2C90272` | Parametr id_token_hint nebyl v požadavku zadán. Zadejte token a akci opakujte. |
| `AADB2C90273` | Byla přijata neplatná{0}odpověď : ' |
| `AADB2C90274` | Metadata zprostředkovatele neurčuje jednu službu odhlášení nebo vazba koncového bodu není jednou z 'urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect' nebo 'urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST'. |
| `AADB2C90276` | Požadavek není v souladu s{0}nastavením{1}ovládacího prvku{2}" ': ' v technicalProfile ' ' pro zásadu '{3}" nájemce '{4}. |
| `AADB2C90277` | Krok orchestrace{0}" " cesty{1}uživatele "{2}" zásady " neobsahuje odkaz na definici obsahu. |
| `AADB2C90279` | Zadaný id klienta '{0}' neodpovídá ID klienta, který grant vydal. |
| `AADB2C90284` | Žádost s identifikátorem "{0}" nebyla schválena a nelze ji použít pro místní účty. |
| `AADB2C90285` | Aplikace s identifikátorem '{0}' nebyla nalezena. |
| `AADB2C90288` | UserJourney s id{0}' ' odkazuje{1}v TechnicalProfile '{2}' pro obnovení token{3}u vykoupení pro tenanta ' ' neexistuje v zásadách ' ' nebo některé z jeho základních zásad. |
| `AADB2C90289` | Při připojování k poskytovateli identity došlo k chybě. Zkuste to prosím znovu později. |
| `AADB2C90296` | Aplikace nebyla správně nakonfigurována. Obraťte se na správce webu, ke jehož přístupu se pokoušíte. |
| `AADB2C99005` | Požadavek obsahuje neplatný parametr oboru,{0}který obsahuje neplatný znak " ". |
| `AADB2C99006` | Azure AD B2C nemůže najít rozšíření aplikace{0}s ID aplikace ' '. Prosím, https://go.microsoft.com/fwlink/?linkid=851224 navštivte pro více informací. |
| `AADB2C99011` | Hodnota metadat '{0}' nebyla zadána{1}v technicalprofile{2}' ' v zásadách . |
| `AADB2C99013` | Dodaná{0}kombinace grant_type{1}[ ] a token_type [ ] není podporována. |
| `AADB2C99015` | Profil{0}' '{1}v zásadách{2}' v tenantovi ' chybí všechny InputClaims potřebné pro tok pověření vlastníka prostředku. |
