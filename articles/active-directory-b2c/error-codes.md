---
title: Odkaz na kód chyby
titleSuffix: Azure AD B2C
description: Seznam kódů chyb, které mohou být vráceny službou Azure Active Directory B2C.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/02/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f6ae806b7666d83652e6b82bac16d89f2f9ce7aa
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92215429"
---
# <a name="error-codes-azure-active-directory-b2c"></a>Kódy chyb: Azure Active Directory B2C

Služba Azure Active Directory B2C může vrátit následující chyby.

| Kód chyby | Zpráva |
| ---------- | ------- |
| `AADB2C90002` | Prostředek CORS {0} vrátil hodnotu 404, která nebyla nalezena. |
| `AADB2C90006` | Identifikátor URI přesměrování {0} zadaný v požadavku není zaregistrován pro ID klienta {1} . |
| `AADB2C90007` | Aplikace přidružená k ID klienta {0} nemá žádné registrované identifikátory URI pro přesměrování. |
| `AADB2C90008` | Požadavek neobsahuje parametr ID klienta. |
| `AADB2C90010` | Požadavek neobsahuje parametr oboru. |
| `AADB2C90011` | ID klienta {0} uvedené v žádosti neodpovídá ID klienta {1} zaregistrovanému v zásadě. |
| `AADB2C90012` | Obor {0} zadaný v požadavku není podporován. |
| `AADB2C90013` | Požadovaný typ odpovědi {0} zadaný v požadavku není podporován. |
| `AADB2C90014` | Požadovaný režim odpovědi {0} zadaný v požadavku není podporován. |
| `AADB2C90016` | Požadovaný typ kontrolního výrazu klienta se {0} neshoduje s očekávaným typem {1} . |
| `AADB2C90017` | Kontrolní výraz klienta zadaný v požadavku je neplatný: {0} |
| `AADB2C90018` | ID klienta {0} zadané v žádosti není zaregistrované v tenantovi {1} . |
| `AADB2C90019` | Kontejner klíčů s ID {0} v tenantovi nemá {1} platný klíč. Důvod: {2} . |
| `AADB2C90021` | Technický profil ' {0} ' neexistuje v zásadách ' ' {1} klienta ' ' {2} . |
| `AADB2C90022` | Nejde vrátit metadata pro zásadu {0} v tenantovi {1} . |
| `AADB2C90023` | Profil neobsahuje {0} požadovaný klíč metadat {1} . |
| `AADB2C90025` | Profil ' {0} ' v zásadě ' ' {1} v tenantovi ' {2} ' neobsahuje požadovaný kryptografický klíč ' {3} '. |
| `AADB2C90027` | Základní přihlašovací údaje zadané pro {0} jsou neplatné. Ověřte, zda jsou pověření správná a zda je udělen přístup k tomuto prostředku. |
| `AADB2C90028` | Klientský certifikát zadaný pro {0} je neplatný. Ověřte, že je certifikát správný, obsahuje privátní klíč a tento přístup je udělený prostředkem. |
| `AADB2C90031` | Zásada {0} neurčuje výchozí cestu uživatele. Ujistěte se, že zásada nebo její rodiče určují výchozí cestu uživatele jako součást oddílu předávající strany. |
| `AADB2C90035` | Služba je dočasně nedostupná. Zkuste to prosím znovu za několik minut. |
| `AADB2C90036` | Požadavek neobsahuje identifikátor URI pro přesměrování uživatele na odeslání odhlašovacího. V poli parametr post_logout_redirect_uri zadejte identifikátor URI. |
| `AADB2C90037` | Při zpracování požadavku došlo k chybě. Kontaktujte prosím správce webu, ke kterému se pokoušíte získat přístup. |
| `AADB2C90039` | Požadavek obsahuje kontrolní výraz klienta, ale zadané zásady {0} v tenantovi {1} nemají Client_secret v u relyingpartypolicy. |
| `AADB2C90040` | Cesta uživatele neobsahuje {0} Krok odeslat deklarace. |
| `AADB2C90043` | Výzva obsažená v žádosti obsahuje neplatné hodnoty. Bylo očekáváno klíčové slovo None, login, souhlasu nebo select_account. |
| `AADB2C90044` | {0}Překladač deklarací identity nepodporuje deklaraci identity {1} . |
| `AADB2C90046` | Nedaří se nám načíst váš aktuální stav. Možná budete chtít zkusit spustit relaci od začátku. |
| `AADB2C90047` | Prostředek {0} obsahuje chyby skriptu, které brání jeho načtení. |
| `AADB2C90048` | Na serveru došlo k neošetřené výjimce. |
| `AADB2C90051` | Nenašly se žádné vhodné zprostředkovatele deklarací identity. |
| `AADB2C90052` | Neplatné uživatelské jméno nebo heslo. |
| `AADB2C90053` | Nepovedlo se najít uživatele se zadaným pověřením. |
| `AADB2C90054` | Neplatné uživatelské jméno nebo heslo. |
| `AADB2C90055` | Obor {0} zadaný v požadavku musí určovat prostředek, třeba https://example.com/calendar.read . |
| `AADB2C90057` | Zadaná aplikace není nakonfigurovaná tak, aby povolovala implicitní tok protokolu OAuth. |
| `AADB2C90058` | Zadaná aplikace není nakonfigurovaná tak, aby umožňovala veřejné klienty. |
| `AADB2C99059` | Zadaný požadavek musí představovat code_challenge. Vyžaduje se pro jednostránkové aplikace pomocí toku autorizačního kódu.| 
| `AADB2C90067` | Identifikátor URI přesměrování po odhlášení {0} má neplatný formát. Zadejte adresu URL založenou na protokolu HTTPS, jako je ' https://example.com/return ' nebo pro nativní klienty použijte identifikátor URI nativního klienta IETF: IETF: WG: OAuth: 2.0: OOB '. |
| `AADB2C90068` | Poskytnutá aplikace s IDENTIFIKÁTORem není {0} platná pro tuto službu. Použijte prosím aplikaci vytvořenou prostřednictvím portálu B2C a zkuste to znovu. |
| `AADB2C90075` | Výměna deklarací identity {0} zadaná v kroku {1} vrátila chybovou odpověď HTTP s kódem {2} a důvodem {3} . |
| `AADB2C90077` | Uživatel nemá existující relaci a parametr výzvy požadavku má hodnotu {0} . |
| `AADB2C90079` | Klienti musí při uplatnění důvěrného grantu odeslat client_secret. |
| `AADB2C90080` | Platnost poskytnutého udělení vypršela. Proveďte prosím znovu ověření a zkuste to znovu. Aktuální čas: {0} , čas vydání grantu: {1} , přidělení posuvných oken čas vypršení platnosti: {2} . |
| `AADB2C90081` | Zadaný client_secret se neshoduje s očekávanou hodnotou tohoto klienta. Opravte prosím client_secret a zkuste to znovu. |
| `AADB2C90083` | V požadavku chybí povinný parametr: {0} . |
| `AADB2C90084` | Veřejní klienti by při uplatňování veřejně získaného grantu neměli posílat client_secret. |
| `AADB2C90085` | V této službě došlo k vnitřní chybě. Proveďte prosím znovu ověření a zkuste to znovu. |
| `AADB2C90086` | Zadaný grant_type [ {0} ] není podporován. |
| `AADB2C90087` | Poskytnutý grant nebyl vydán pro tuto verzi koncového bodu protokolu. |
| `AADB2C90088` | Poskytnutý grant nebyl vystaven pro tento koncový bod. Skutečná hodnota: {0} a očekávaná hodnota: {1} |
| `AADB2C90091` | Zrušení uživatele |
| `AADB2C90092` | Poskytnutá aplikace s ID {0} je pro tenanta zakázaná {1} . Povolte prosím aplikaci a zkuste to znovu. |
| `AADB2C90107` | Aplikace s ID {0} nemůže získat token ID, protože v požadavku nebyl zadán obor OpenID, nebo aplikace není pro ni autorizována. |
| `AADB2C90108` | Krok orchestrace {0} ' ' neurčuje CpimIssuerTechnicalProfileReferenceId, pokud byla očekávána jedna. |
| `AADB2C90110` | Parametr scope musí zahrnovat ' OpenID ' při žádosti o response_type, která zahrnuje ' id_token '. |
| `AADB2C90111` | Váš účet byl uzamčen. Řekněte pracovníkovi podpory, aby ho odemkl, a pak to zkuste znovu. |
| `AADB2C90114` | Váš účet je dočasně zamčený, aby nedocházelo k neoprávněnému použití. Zkuste to později. |
| `AADB2C90115` | Při vyžadování response_type kódu musí parametr scope zahrnovat ID prostředku nebo klienta pro přístupové tokeny a ' OpenID ' pro tokeny ID. Kromě toho zahrňte ' offline_access ' pro aktualizační tokeny. |
| `AADB2C90117` | Obor {0} zadaný v požadavku není podporován. |
| `AADB2C90118` | Uživatel zapomněl svoje heslo. |
| `AADB2C90120` | Parametr maximálního stáří {0} zadaný v požadavku je neplatný. Maximální stáří musí být celé číslo v rozmezí od {1} do {2} (včetně). |
| `AADB2C90122` | Vstup pro objekt {0} přijatý v požadavku nedokázal ověřit požadavek HTTP. Ujistěte se, že vstup neobsahuje znaky jako < nebo &. |
| `AADB2C90128` | Účet přidružený k tomuto udělení už neexistuje. Proveďte prosím znovu ověření a zkuste to znovu. |
| `AADB2C90129` | Poskytnutý Grant byl odvolán. Proveďte prosím znovu ověření a zkuste to znovu. |
| `AADB2C90145` | Nebyla nalezena žádná neověřená telefonní čísla a zásada nepovoluje uživateli zadané číslo. |
| `AADB2C90146` | Obor {0} zadaný v požadavku určuje více než jeden prostředek pro přístupový token, což není podporováno. |
| `AADB2C90149` | Skript ' {0} ' se nepodařilo načíst. |
| `AADB2C90151` | Uživatel překročil maximální počet opakování pro službu Multi-Factor Authentication. |
| `AADB2C90152` | Žádosti o vícefaktorové hlasování se nepodařilo získat odpověď ze služby. |
| `AADB2C90154` | Žádosti o vícefaktorové ověřování se nepodařilo získat ID relace ze služby. |
| `AADB2C90155` | Žádost o vícefaktorové ověřování se nezdařila z důvodu {0} . |
| `AADB2C90156` | Žádost o vícefaktorové ověření se nezdařila z důvodu {0} . |
| `AADB2C90157` | Uživatel překročil maximální počet opakovaných pokusů pro krok s vlastním kontrolním výrazem. |
| `AADB2C90158` | Požadavek na ověření s vlastním uplatněním se nezdařil z důvodu {0} . |
| `AADB2C90159` | Požadavek na ověření s vlastním uplatněním se nezdařil z důvodu {0} . |
| `AADB2C90161` | Odpověď na odeslání s vlastním kontrolním výrazem se nezdařila z důvodu {0} . |
| `AADB2C90165` | Zpráva iniciace SAML s ID {0} nebyla ve stavu nalezena. |
| `AADB2C90168` | Požadavek HTTP-Redirect neobsahuje požadovaný parametr ' {0} ' pro podepsaný požadavek. |
| `AADB2C90178` | Podpisový certifikát ' {0} ' nemá žádný privátní klíč. |
| `AADB2C90182` | Dodaná code_verifier se neshodují s přidruženými code_challenge |
| `AADB2C90183` | Zadaný code_verifier není platný. |
| `AADB2C90184` | Zadaný code_challenge_method není podporován. Podporované hodnoty jsou jednoduché nebo S256 |
| `AADB2C90188` | Technický profil SAML {0} Určuje PartnerEntity adresu URL {1} , ale načtení metadat z důvodu je neúspěšné {2} . |
| `AADB2C90194` | Deklarace identity {0} zadaná pro token nosiče není v dostupných deklaracích k dispozici. Dostupné deklarace identity {1} . |
| `AADB2C90205` | Tato aplikace nemá dostatečná oprávnění pro tento webový prostředek k provedení operace. |
| `AADB2C90206` | Při inicializaci klienta došlo k vypršení časového limitu. |
| `AADB2C90208` | Platnost zadaného parametru id_token_hint vypršela. Zadejte prosím jiný token a zkuste to znovu. |
| `AADB2C90209` | Zadaný id_token_hint parametr neobsahuje přijatou cílovou skupinu. Platné hodnoty cílové skupiny: ' {0} '. Zadejte prosím jiný token a zkuste to znovu. |
| `AADB2C90210` | Zadaný parametr id_token_hint nelze ověřit. Zadejte prosím jiný token a zkuste to znovu. |
| `AADB2C90211` | Požadavek obsahoval nekompletní soubor cookie stavu. |
| `AADB2C90212` | Požadavek obsahoval neplatný soubor cookie stavu. |
| `AADB2C90220` | Kontejner klíčů v tenantovi {0} s identifikátorem úložiště {1} existuje, ale neobsahuje platný certifikát. Je možné, že vypršela platnost certifikátu nebo že se váš certifikát může v budoucnu aktivovat (NBF). |
| `AADB2C90223` | Při sesdílení prostředku CORS došlo k chybě. |
| `AADB2C90224` | Tok vlastníka prostředků nebyl pro aplikaci povolen. |
| `AADB2C90225` | Uživatelské jméno nebo heslo zadané v žádosti jsou neplatné. |
| `AADB2C90226` | Zadaná výměna tokenu je podporovaná jenom přes HTTP POST. |
| `AADB2C90232` | Zadaný id_token_hint parametr neobsahuje přijatý Vystavitel. Platní vydavatelé: ' {0} '. Zadejte prosím jiný token a zkuste to znovu. |
| `AADB2C90233` | U zadaného parametru id_token_hint se nepodařilo ověřit podpis. Zadejte prosím jiný token a zkuste to znovu. |
| `AADB2C90235` | Platnost poskytnutého id_token vypršela. Zadejte prosím jiný token a zkuste to znovu. |
| `AADB2C90237` | Zadaný id_token neobsahuje platnou cílovou skupinu. Platné hodnoty cílové skupiny: ' {0} '. Zadejte prosím jiný token a zkuste to znovu. |
| `AADB2C90238` | Zadaný id_token neobsahuje platného vystavitele. Platné hodnoty vystavitele: {0} ' '. Zadejte prosím jiný token a zkuste to znovu. |
| `AADB2C90239` | U poskytnutého id_token se nepodařilo ověřit podpis. Zadejte prosím jiný token a zkuste to znovu. |
| `AADB2C90240` | Zadaný id_token je poškozen a nelze jej analyzovat. Zadejte prosím jiný token a zkuste to znovu. |
| `AADB2C90242` | Technický profil SAML {0} Určuje PARTNERENTITY CDATA, který nelze načíst z důvodu {1} . |
| `AADB2C90243` | Klíč a tajný kód klienta IDP není správně nakonfigurovaný. |
| `AADB2C90244` | V tuto chvíli je moc velký počet požadavků. Chvíli prosím počkejte a zkuste to znovu. |
| `AADB2C90248` | Tok vlastníka prostředků můžou používat jenom aplikace vytvořené prostřednictvím portálu pro správu B2C. |
| `AADB2C90250` | Obecný koncový bod přihlášení není podporován. |
| `AADB2C90255` | Výměna deklarací identity zadaná v technickém profilu se {0} nedokončila podle očekávání. Možná budete chtít zkusit spustit relaci od začátku. |
| `AADB2C90261` | Výměna deklarací identity {0} zadaná v kroku {1} vrátila chybovou odpověď HTTP, kterou nebylo možné analyzovat. |
| `AADB2C90272` | V požadavku nebyl zadán parametr id_token_hint. Zadejte prosím token a zkuste to znovu. |
| `AADB2C90273` | Byla přijata neplatná odpověď: ' {0} ' |
| `AADB2C90274` | Metadata poskytovatele neurčují službu Single logout nebo vazba koncového bodu není jednou z "urn: Oasis: names: TC: SAML: 2.0: Bindings: HTTP-redirect" nebo "urn: Oasis: names: TC: SAML: 2.0: Bindings: HTTP-POST". |
| `AADB2C90276` | Požadavek není v souladu s nastavením ovládacího prvku ' {0} ': ' {1} ' v technicalProfile ' {2} ' pro zásadu {3} {4} ' ' klienta ' '. |
| `AADB2C90277` | Krok orchestrace {0} uživatelsky {1} navýšení cesty pro zásadu neobsahuje {2} odkaz na definici obsahu. |
| `AADB2C90279` | Zadané ID klienta se {0} neshoduje s ID klienta, které vydalo udělené oprávnění. |
| `AADB2C90284` | Aplikaci s identifikátorem se {0} nepodařilo udělit souhlas a nelze ji použít pro místní účty. |
| `AADB2C90285` | Aplikace s identifikátorem nebyla {0} nalezena. |
| `AADB2C90288` | UserJourney s ID, na které se {0} odkazuje TechnicalProfile {1} pro aktualizaci uplatnění tokenu pro tenanta {2} , neexistuje v zásadě {3} ani v žádné z jeho základních zásad. |
| `AADB2C90289` | Při připojování k poskytovateli identity došlo k chybě. Zkuste to později. |
| `AADB2C90296` | Aplikace nebyla správně nakonfigurována. Kontaktujte prosím správce webu, ke kterému se pokoušíte získat přístup. |
| `AADB2C99005` | Požadavek obsahuje neplatný parametr oboru, který obsahuje neplatný znak {0} . |
| `AADB2C99006` | Aplikace rozšíření s ID aplikace se nedá najít Azure AD B2C {0} . Další informace najdete na stránce https://go.microsoft.com/fwlink/?linkid=851224 . |
| `AADB2C99011` | Hodnota metadat nebyla {0} zadána v TechnicalProfile {1} v zásadě {2} . |
| `AADB2C99013` | Zadaná kombinace grant_type [ {0} ] a token_type [ {1} ] není podporována. |
| `AADB2C99015` | U profilu {0} v zásadě v {1} tenantovi {2} chybí všechny InputClaims vyžadované pro tok přihlašovacích údajů k heslu vlastníka prostředku. |
