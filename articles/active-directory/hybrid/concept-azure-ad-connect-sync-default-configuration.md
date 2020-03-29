---
title: 'Synchronizace služby Azure AD Connect: Principy výchozí konfigurace | Dokumenty společnosti Microsoft'
description: Tento článek popisuje výchozí konfiguraci v synchronizaci Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ed876f22-6892-4b9d-acbe-6a2d112f1cd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2886b842aab81732beec0fdd7957aab8e2b4f5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548862"
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Synchronizace služby Azure AD Connect: Principy výchozí konfigurace
Tento článek vysvětluje pravidla konfigurace out-of-box. Dokumentuje pravidla a jak tato pravidla ovlivňují konfiguraci. Také vás provede výchozí konfiguraci synchronizace Azure AD Connect. Cílem je, že čtenář chápe, jak model konfigurace, s názvem deklarativní zřizování, pracuje v příkladu reálného světa. Tento článek předpokládá, že jste již nainstalovali a nakonfigurovali synchronizaci služby Azure AD Connect pomocí průvodce instalací.

Chcete-li porozumět podrobnostem modelu konfigurace, [přečtěte si principy deklarativní zřizování](concept-azure-ad-connect-sync-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Pravidla out-of-box z místního do Azure AD
Následující výrazy lze nalézt v předem vybalení konfigurace.

### <a name="user-out-of-box-rules"></a>Pravidla uživatele izavací okno
Tato pravidla platí také pro typ objektu iNetOrgPerson.

Objekt uživatele musí splňovat následující, aby bylo možné synchronizovat:

* Musí mít sourceAnchor.
* Po vytvoření objektu ve službě Azure AD pak sourceAnchor nelze změnit. Pokud se hodnota změní místně, objekt přestane synchronizovat, dokud sourceAnchor se změní zpět na jeho předchozí hodnotu.
* Musí mít vyplněný atribut accountEnabled (userAccountControl). V místní službě Active Directory je tento atribut vždy přítomen a naplněn.

Následující uživatelské objekty **nejsou** synchronizovány do služby Azure AD:

* `IsPresent([isCriticalSystemObject])`. Zajistěte, aby mnoho předem zavislých objektů ve službě Active Directory, například předdefinovaný účet správce, nebylo synchronizováno.
* `IsPresent([sAMAccountName]) = False`. Zajistěte, aby uživatelské objekty bez atributu sAMAccountName nebyly synchronizovány. Tento případ by se prakticky stalo pouze v doméně upgradované z NT4.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Nesynchronizujte účet služby používaný synchronizací Azure AD Connect a jeho staršími verzemi.
* Nesynchronizujte účty serveru Exchange, které by v Exchange Online nefungovaly.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Nesynchronizujte objekty, které by v Exchange Online nefungovaly.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  Tato bitová maska (&H21C07000) by odfiltrovala následující objekty:
  * Veřejná složka s podporou pošty (ve verzi 1.1.524.0)
  * Poštovní schránka obsluhy systému
  * Poštovní schránka databáze poštovní schránky (systémová poštovní schránka)
  * Universal Security Group (nevztahuje se na uživatele, ale je k dispozici z důvodu starší verze)
  * Neuniverzální skupina (nevztahuje se na uživatele, ale je k dispozici z důvodu starší verze)
  * Plán poštovní schránky
  * Poštovní schránka zjišťování
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Nesynchronizujte žádné objekty oběti replikace.

Platí následující pravidla atributů:

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. SourceAnchor atribut není přispěl z propojené poštovní schránky. Předpokládá se, že pokud byla nalezena propojená poštovní schránka, skutečný účet je připojen později.
* Atributy související se serverem Exchange jsou synchronizovány pouze v případě, že atribut **mailNickName** má hodnotu.
* Pokud existuje více doménových struktur, pak atributy jsou spotřebovány v následujícím pořadí:
  1. Atributy související s přihlášením (například userPrincipalName) jsou vloženy z doménové struktury s povoleným účtem.
  2. Atributy, které lze nalézt v globálním seznamu adres serveru Exchange (Globální seznam adres), jsou vloženy z doménové struktury pomocí poštovní schránky serveru Exchange.
  3. Pokud nelze nalézt žádnou poštovní schránku, mohou tyto atributy pocházet z libovolné doménové struktury.
  4. Atributy související s výměnou (technické atributy, které nejsou `mailNickname ISNOTNULL`viditelné v globálním seznamu adres) jsou přispívají z doménové struktury, kde .
  5. Pokud existuje více doménových struktur, které by splňovaly jedno z těchto pravidel, pak pořadí vytvoření (datum a čas) spojnice (doménové struktury) se používá k určení, které doménové struktury přispívá atributy. První připojená doménová struktura bude první doménovou strukturu, která bude synchronizována. 

### <a name="contact-out-of-box-rules"></a>Kontaktní pravidla izaviny
Objekt kontaktu musí splňovat následující požadavky, aby mohl být synchronizován:

* Kontakt musí mít povolenou poštu. Ověřuje se následujícími pravidly:
  * `IsPresent([proxyAddresses]) = True)`. Atribut proxyAddresses musí být naplněn.
  * Primární e-mailovou adresu naleznete v atributu proxyAddresses nebo atributu mail. Přítomnost a \@ se používá k ověření, že obsah je e-mailová adresa. Jedno z těchto dvou pravidel musí být vyhodnoceno jako True.
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Existuje položka s "SMTP:" a pokud existuje, \@ lze najít v řetězci?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. Je atribut pošty naplněn, a pokud \@ ano, lze v řetězci nalézt?

Následující objekty kontaktu **nejsou** synchronizovány do služby Azure AD:

* `IsPresent([isCriticalSystemObject])`. Zajistěte, aby nebyly synchronizovány žádné kontaktní objekty označené jako kritické. Nemělby to být žádný s výchozí konfigurací.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Tyto objekty nebudou fungovat v Exchange Online.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Nesynchronizujte žádné objekty oběti replikace.

### <a name="group-out-of-box-rules"></a>Seskupit pravidla po vybalení
Objekt skupiny musí splňovat následující požadavky, aby bylo možné synchronizovat:

* Musí mít méně než 50 000 členů. Tento počet je počet členů v místní skupině.
  * Pokud má více členů před zahájením synchronizace při prvním spuštění, skupina není synchronizována.
  * Pokud počet členů růst od kdy byl původně vytvořen, pak když dosáhne 50 000 členů, přestane se synchronizovat, dokud nebude počet členů opět nižší než 50 000.
  * Poznámka: Počet 50 000 členství je také vynuceno Azure AD. Není možné synchronizovat skupiny s více členy, i když toto pravidlo upravíte nebo odeberete.
* Pokud je skupina **distribuční skupinou**, musí být také povolena pošta. Viz [Vynucovat pravidla kontaktního out-of-box](#contact-out-of-box-rules) pro toto pravidlo.

Následující objekty skupiny **nejsou** synchronizovány do služby Azure AD:

* `IsPresent([isCriticalSystemObject])`. Zajistěte, aby mnoho předem zavislých objektů ve službě Active Directory, například ve vestavěné skupině administrators, nebylo synchronizováno.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Starší skupina používaná dirsync.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Skupina rolí.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Nesynchronizujte žádné objekty oběti replikace.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>Cizí zabezpečeníHlavní pravidla out-of-box
FSP jsou spojeny s\*"any" ( ) objekt v metaverse. Ve skutečnosti se toto spojení děje pouze pro uživatele a skupiny zabezpečení. Tato konfigurace zajišťuje, že členství mezi doménovými strukturami jsou vyřešeny a správně reprezentovány ve službě Azure AD.

### <a name="computer-out-of-box-rules"></a>Počítačová pravidla out-of-box
Objekt počítače musí splňovat následující požadavky, aby mohl být synchronizován:

* `userCertificate ISNOTNULL`. Tento atribut naplní pouze počítače se systémem Windows 10. Všechny objekty počítače s hodnotou v tomto atributu jsou synchronizovány.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Principy scénáře pravidel předboxu
V tomto příkladu používáme nasazení s doménovou strukturami jednoho účtu (A), jeden doménové struktura prostředků (R) a jeden adresář Azure AD.

![Obrázek s popisem scénáře](./media/concept-azure-ad-connect-sync-default-configuration/scenario.png)

V této konfiguraci se předpokládá, že v doménové struktuře účtu účtu existuje povolený účet a v doménové struktuře prostředků s propojenou poštovní schránkou je povolený účet.

Naším cílem s výchozí konfigurací je:

* Atributy související s přihlášením jsou synchronizovány z doménové struktury s povoleným účtem.
* Atributy, které lze nalézt v globálním seznamu adres (globální seznam adres), jsou synchronizovány z doménové struktury s poštovní schránkou. Pokud nelze najít žádnou poštovní schránku, použije se jiná doménová struktura.
* Pokud je nalezena propojená poštovní schránka, musí být nalezen propojený povolený účet pro objekt, který má být exportován do Azure AD.

### <a name="synchronization-rule-editor"></a>Editor pravidel synchronizace
Konfiguraci lze zobrazit a změnit pomocí editoru pravidel synchronizace nástrojů (SRE) a zástupce k ní lze nalézt v nabídce Start.

![Ikona Editoru pravidel synchronizace](./media/concept-azure-ad-connect-sync-default-configuration/sre.png)

SRE je nástroj pro sadu prostředků a je nainstalovaný se synchronizací Azure AD Connect. Abyste ji mohli spustit, musíte být členem skupiny ADSyncAdmins. Když to začne, uvidíte něco takového:

![Příchozí pravidla synchronizace](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

V tomto podokně se zobrazí všechna pravidla synchronizace vytvořená pro vaši konfiguraci. Každý řádek v tabulce je jedno pravidlo synchronizace. Vlevo v části Typy pravidel jsou uvedeny dva různé typy: Příchozí a Odchozí. Příchozí a odchozí je z pohledu metaverse. V tomto přehledu se zaměříte především na příchozí pravidla. Skutečný seznam pravidel synchronizace závisí na zjištěném schématu ve službě AD. Na obrázku výše doménové struktury účtu (fabrikamonline.com) nemá žádné služby, jako je exchange a Lync a žádná pravidla synchronizace byly vytvořeny pro tyto služby. V doménové struktuře prostředků (res.fabrikamonline.com) však najdete pravidla synchronizace pro tyto služby. Obsah pravidel se liší v závislosti na zjištěné verzi. Například v nasazení se Serverem Exchange 2013 je nakonfigurované více toků atributů než v Exchange 2010/2007.

### <a name="synchronization-rule"></a>Pravidlo synchronizace
Pravidlo synchronizace je konfigurační objekt se sadou atributů, které plynou, když je splněna podmínka. Používá se také k popisu, jak objekt v prostoru spojnice souvisí s objektem v metaverse, označovaný jako **spojení** nebo **shoda**. Pravidla synchronizace mají hodnotu priority označující, jak se vzájemně vztahují. Pravidlo synchronizace s nižší číselnou hodnotou má vyšší prioritu a v konfliktu toku atributů vyhrává vyšší priorita řešení konfliktů.

Jako příklad například podívejte se na pravidlo synchronizace **v ze služby AD – User AccountEnabled**. Označte tento řádek v ss a vyberte **upravit**.

Vzhledem k tomu, že toto pravidlo je pravidlo out-of-box, zobrazí se upozornění při otevření pravidla. Neměli byste provádět žádné [změny pravidel out-of-box](how-to-connect-sync-best-practices-changing-default-configuration.md), takže jste dotázáni, jaké jsou vaše záměry. V takovém případě chcete pouze zobrazit pravidlo. Vyberte **Ne**.

![Upozornění pravidel synchronizace](./media/concept-azure-ad-connect-sync-default-configuration/warningeditrule.png)

Pravidlo synchronizace má čtyři části konfigurace: Popis, Filtr oborů, Pravidla spojení a Transformace.

#### <a name="description"></a>Popis
První část obsahuje základní informace, jako je například název a popis.

![Karta Popis v editoru pravidel synchronizace](./media/concept-azure-ad-connect-sync-default-configuration/syncruledescription.png)

Najdete zde také informace o tom, ke kterému připojenému systému se toto pravidlo vztahuje, o typu objektu v připojeném systému, na který se vztahuje, a o typu metaverse. Typ objektu metaverse je vždy osobou bez ohledu na to, zda je typ zdrojového objektu uživatelem, iNetOrgPerson nebo kontaktem. Typ objektu metaverse by se nikdy neměl měnit, takže je vytvořen jako obecný typ. Typ odkazu lze nastavit na připojit, StickyJoin nebo Provision. Toto nastavení funguje společně s oddílem Pravidla spojení a je zahrnuto později.

Můžete také vidět, že toto pravidlo synchronizace se používá pro synchronizaci hesel. Pokud je uživatel v oboru pro toto pravidlo synchronizace, heslo je synchronizováno z místního do cloudu (za předpokladu, že jste povolili funkci synchronizace hesla).

#### <a name="scoping-filter"></a>Filtr oboru
Oddíl Filtr oborů se používá ke konfiguraci, kdy by mělo platit pravidlo synchronizace. Vzhledem k tomu, že název pravidla synchronizace, na které se díváte, označuje, že by měl být použit pouze pro povolené uživatele, je obor nakonfigurován tak, aby atribut AD **userAccountControl** neměl mít sadu bitu 2. Když synchronizační modul najde uživatele ve službě AD, použije toto pravidlo synchronizace, když je **uživatelAccountControl** nastaven na desetinnou hodnotu 512 (povoleno u normálního uživatele). Nepoužije pravidlo, pokud má uživatel **userAccountControl** nastavenna na 514 (zakázáno normální uživatel).

![Karta Obor v editoru pravidel synchronizace](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilter.png)

Oborový filtr má skupiny a klauzule, které mohou být vnořeny. Aby bylo možné použít pravidlo synchronizace, musí být splněny všechny klauzule uvnitř skupiny. Pokud je definováno více skupin, musí být splněna alespoň jedna skupina, aby bylo pravidlo použít. To znamená, že logické NEBO je vyhodnocován mezi skupinami a logické AND je vyhodnocován uvnitř skupiny. Příklad této konfigurace lze nalézt v odchozí synchronizaci vyloučit **na AAD – group join**. Existuje několik skupin filtrů synchronizace, například`securityEnabled EQUAL True`jedna pro skupiny`securityEnabled EQUAL False`zabezpečení ( ) a jedna pro distribuční skupiny ( ).

![Karta Obor v editoru pravidel synchronizace](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilterout.png)

Toto pravidlo se používá k definování, které skupiny by měly být zřízeny do Azure AD. Distribuční skupiny musí být povolená pošta pro synchronizaci s Azure AD, ale pro skupiny zabezpečení e-mail není vyžadován.

#### <a name="join-rules"></a>Pravidla připojení
Třetí část slouží ke konfiguraci, jak objekty v prostoru spojnice se vztahují k objektům v metaverse. Pravidlo, na které jste se dívali dříve, nemá žádnou konfiguraci pro pravidla připojení, takže místo toho se budete dívat na **In z AD – User Join**.

![Karta Připojit pravidla v editoru pravidel synchronizace](./media/concept-azure-ad-connect-sync-default-configuration/syncrulejoinrules.png)

Obsah pravidla spojení závisí na možnosti párování vybrané v průvodci instalací. U příchozího pravidla začíná vyhodnocení objektem ve zdrojovém prostoru konektoru a každá skupina v pravidlech spojení je vyhodnocována postupně. Pokud je zdrojový objekt vyhodnocen tak, aby přesně odpovídal jednomu objektu v metaverse pomocí jednoho z pravidel spojení, jsou objekty spojeny. Pokud byla vyhodnocena všechna pravidla a neexistuje žádná shoda, použije se typ odkazu na stránce popisu. Pokud je tato konfigurace nastavena na **provision**, pak je v cíli vytvořen nový objekt, metaverse, pokud je přítomen alespoň jeden atribut v kritériích spojení (má hodnotu). Chcete-li zřídit nový objekt metaverse je také známo, že **projekt** objekt metaverse.

Pravidla spojení jsou vyhodnocena pouze jednou. Když jsou spojeny objekt prostoru spojnice a metaverse objektu, zůstávají spojené tak dlouho, dokud je splněn rozsah pravidla synchronizace.

Při vyhodnocování pravidel synchronizace musí být v oboru pouze jedno pravidlo synchronizace s definovanými pravidly spojení. Pokud je pro jeden objekt nalezeno více pravidel synchronizace s pravidly spojení, je vyvolána chyba. Z tohoto důvodu je osvědčeným postupem mít pouze jedno pravidlo synchronizace s spojením definované při více pravidel synchronizace jsou v oboru pro objekt. V předem připravená konfigurace pro synchronizaci Azure AD Connect, tato pravidla lze nalézt při pohledu na název a najít ty se slovem **Připojit** na konci názvu. Pravidlo synchronizace bez definovaných pravidel spojení použije toky atributů, když jiné pravidlo synchronizace spojí objekty dohromady nebo zřídí nový objekt v cíli.

Pokud se podíváte na výše uvedený obrázek, uvidíte, že pravidlo se pokouší spojit **objektSID** s **msExchMasterAccountSid** (Exchange) a **msRTCSIP-OriginatorSid** (Lync), což je to, co očekáváme v topologii doménové struktury prostředků účtu. Stejné pravidlo najdete ve všech lesích. Předpokládá se, že každá doménová struktura může být buď doménovou strukturu účtu nebo zdroje. Tato konfigurace funguje také v případě, že máte účty, které žijí v jedné doménové struktuře a není třeba připojit.

#### <a name="transformations"></a>Transformace
Oddíl transformace definuje všechny toky atributů, které se vztahují k cílovému objektu, když jsou objekty spojeny a je splněn filtr oboru. Přechod zpět na **In ze služby AD – uživatelského účtuPovoleno** synchronizace pravidlo, najdete následující transformace:

![Karta Transformace v editoru pravidel synchronizace](./media/concept-azure-ad-connect-sync-default-configuration/syncruletransformations.png)

Chcete-li tuto konfiguraci uvést do kontextu, očekává se, že v nasazení doménové struktury prostředků account-resource nalezne povolený účet v doménové struktuře účtu a zakázaný účet v doménové struktuře prostředků s nastavením Serveru Exchange a Lyncu. Pravidlo synchronizace, na které se díváte, obsahuje atributy požadované pro přihlášení a tyto atributy by měly proudit z doménové struktury, kde je povolen účet. Všechny tyto toky atributů jsou sestaveny v jednom pravidle synchronizace.

Transformace může mít různé typy: Konstanta, Přímá a Výraz.

* Konstantní tok vždy natéká pevně zakódovanou hodnotu. Ve výše uvedeném případě vždy nastaví hodnotu **True** v atributu metaverse s názvem **accountEnabled**.
* Přímý tok vždy toky hodnotu atributu ve zdroji cílového atributu as-is.
* Třetí typ toku je výraz a umožňuje pokročilejší konfigurace.

Jazyk výrazu je VBA (Visual Basic for Applications), takže uživatelé se zkušenostmi s Microsoft Office nebo VBScript rozpozná formát. Atributy jsou uzavřeny v hranatých závorkách [attributeName]. Názvy atributů a názvy funkcí rozlišují malá a velká písmena, ale Editor pravidel synchronizace vyhodnocuje výrazy a poskytuje upozornění, pokud výraz není platný. Všechny výrazy jsou vyjádřeny na jednom řádku s vnořenými funkcemi. Chcete-li zobrazit sílu konfiguračního jazyka, zde je tok pro pwdLastSet, ale s dalšími vloženými komentáři:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .NET datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

Další informace o jazyku výrazu pro toky atributů naleznete v [tématu Principy deklarativních zřizovacích výrazů.](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)

### <a name="precedence"></a>Priorita
Nyní jste se podívali na některá jednotlivá pravidla synchronizace, ale pravidla spolupracují v konfiguraci. V některých případech je hodnota atributu přispívá z více pravidel synchronizace do stejného cílového atributu. V tomto případě se priorita atributu používá k určení, který atribut vyhrává. Jako příklad se podívejte na atribut sourceAnchor. Tento atribut je důležitý atribut, který je možné přihlásit do Azure AD. Tok atributů pro tento atribut můžete najít ve dvou různých pravidlech **synchronizace, V z ad – User AccountEnabled** a **In from AD – User Common**. Z důvodu priority pravidla synchronizace je atribut sourceAnchor nejprve vložen z doménové struktury s povoleným účtem, pokud je k objektu metaverse připojeno několik objektů. Pokud neexistují žádné povolené účty, pak synchronizační modul používá pravidlo synchronizace catch-all **v modulu AD – uživatel – běžný**. Tato konfigurace zajišťuje, že i pro účty, které jsou zakázány, je stále sourceAnchor.

![Příchozí pravidla synchronizace](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

Priorita pravidel synchronizace je nastavena ve skupinách průvodcem instalací. Všechna pravidla ve skupině mají stejný název, ale jsou připojena k různým připojeným adresářům. Průvodce instalací dává pravidlo **In z ad – uživatel připojit** nejvyšší prioritu a iterováno přes všechny připojené adresáře služby AD. Potom pokračuje s další skupiny pravidel v předdefinovaném pořadí. Uvnitř skupiny jsou pravidla přidána v pořadí, v jakém byly přidány spojnice v průvodci. Pokud je prostřednictvím průvodce přidán jiný konektor, pravidla synchronizace jsou uspořádána a pravidla nového konektoru jsou vložena jako poslední v každé skupině.

### <a name="putting-it-all-together"></a>Spojení všech součástí dohromady
Nyní víme dost o pravidlech synchronizace, abychom mohli pochopit, jak konfigurace funguje s různými pravidly synchronizace. Pokud se podíváte na uživatele a atributy, které jsou přiřazeny k metaverse, pravidla se použijí v následujícím pořadí:

| Name (Název) | Poznámka |
|:--- |:--- |
| V ad – připojení k uživateli |Pravidlo pro spojování objektů prostoru spojnice metaverse. |
| In od AD – uživatelský účet povolen |Atributy požadované pro přihlášení k Azure AD a Office 365. Chceme tyto atributy z povoleného účtu. |
| In z AD – uživatel běžné z Exchange |Atributy nalezené v globálním seznamu adres. Předpokládáme, že kvalita dat je nejlepší v doménové struktuře, kde jsme našli poštovní schránku uživatele. |
| V od AD – uživatel skáty |Atributy nalezené v globálním seznamu adres. V případě, že jsme nenašli poštovní schránku, může hodnota atributu přispět jakýmkoli jiným spojeným objektem. |
| V ad – výměna uživatelů |Existuje pouze v případě, že byla zjištěna server Exchange. To toky všechny atributy exchange infrastruktury. |
| V aplikaci AD – uživatel lyncu |Existuje pouze v případě, že byl zjištěn Lync. To toky všechny infrastruktury Lync atributy. |

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o modelu konfigurace v [principu deklarativní zřizování](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Přečtěte si další informace o jazyku výrazu v [principu deklarativní zřizování výrazy](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Pokračovat ve čtení, jak funguje předem vyrozená konfigurace v [principu uživatelů a kontaktů](concept-azure-ad-connect-sync-user-and-contacts.md)
* Podívejte se, jak provést praktickou změnu pomocí deklarativnízři v [jak provést změnu výchozí konfigurace](how-to-connect-sync-change-the-configuration.md).

**Přehledná témata**

* [Synchronizace služby Azure AD Connect: Principy a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)

