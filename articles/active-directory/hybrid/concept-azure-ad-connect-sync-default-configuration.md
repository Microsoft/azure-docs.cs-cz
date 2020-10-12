---
title: 'Azure AD Connect synchronizace: principy výchozí konfigurace | Microsoft Docs'
description: Tento článek popisuje výchozí konfiguraci v Azure AD Connect synchronizaci.
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
ms.openlocfilehash: 5e55526e0a63a0c603e2b62ccb3ac0efed911cff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91295222"
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Synchronizace služby Azure AD Connect: Principy výchozí konfigurace
Tento článek vysvětluje předem připravená konfigurační pravidla. Dokumentuje pravidla a způsob, jakým tato pravidla ovlivňují konfiguraci. Také vás provede výchozí konfigurací Azure AD Connect synchronizace. Cílem je, aby čtenář pochopil, jak model konfigurace s názvem deklarativní zřizování funguje v reálném příkladu. V tomto článku se předpokládá, že jste už nainstalovali a nakonfigurovali Azure AD Connect synchronizaci pomocí Průvodce instalací nástroje.

Chcete-li porozumět podrobnostem o modelu konfigurace, přečtěte si téma [Principy deklarativního zajišťování](concept-azure-ad-connect-sync-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Dodaná pravidla z místního prostředí do Azure AD
Následující výrazy se dají najít v konfiguraci dodaného pole.

### <a name="user-out-of-box-rules"></a>Předpřipravená pravidla uživatele
Tato pravidla platí také pro typ objektu iNetOrgPerson.

Aby bylo možné synchronizovat objekt uživatele, musí splňovat následující požadavky:

* Musí mít sourceAnchor.
* Po vytvoření objektu ve službě Azure AD se sourceAnchor nedá změnit. Pokud je hodnota změněna místně, objekt se zastaví, dokud se sourceAnchor nezmění zpátky na předchozí hodnotu.
* Musí mít naplněný atribut accountEnabled (pod ním). V případě místní služby Active Directory je tento atribut vždy přítomen a vyplněn.

Následující objekty uživatele **nejsou synchronizované s** Azure AD:

* `IsPresent([isCriticalSystemObject])`. Zajistěte, aby se v Active Directory nesynchronizoval žádné objekty, jako je například integrovaný účet správce.
* `IsPresent([sAMAccountName]) = False`. Ujistěte se, že objekty uživatele bez atributu sAMAccountName nejsou synchronizované. V takovém případě se v doméně, která je upgradována z NT4, stane jen prakticky.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Nesynchronizujte účet služby používaný službou Azure AD Connect Sync a jejími předchozími verzemi.
* Nesynchronizujte účty Exchange, které nefungují v Exchange Online.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Nesynchronizujte objekty, které nefungují v Exchangi Online.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  Tato bitová maska (&H21C07000) by vyfiltroval následující objekty:
  * Veřejná složka s povoleným e-mailem (ve verzi Preview jako verze 1.1.524.0)
  * Poštovní schránka System Attendant
  * Poštovní schránka databáze poštovní schránky (systémová poštovní schránka)
  * Univerzální skupina zabezpečení (neplatí pro uživatele, ale je k dispozici z původních důvodů)
  * Neuniverzální skupina (neplatí pro uživatele, ale je k dispozici z původních důvodů)
  * Plán poštovní schránky
  * Poštovní schránka zjišťování
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Nesynchronizujte žádné objekty oběti replikace.

Platí následující pravidla atributu:

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. Atribut sourceAnchor nepřispívá z propojené poštovní schránky. Předpokládá se, že pokud se našla propojená poštovní schránka, je skutečný účet připojený později.
* Atributy související se systémem Exchange jsou synchronizovány pouze v případě, že atribut **mailNickname** má hodnotu.
* Pokud existuje více doménových struktur, pak jsou atributy spotřebovány v následujícím pořadí:
  1. Atributy související s přihlašováním (například userPrincipalName) jsou k dispozici z doménové struktury s povoleným účtem.
  2. Atributy, které lze najít v globálním adresáři Exchange (globální seznam adres), jsou k dispozici z doménové struktury s poštovní schránkou Exchange.
  3. Pokud nelze nalézt poštovní schránku, pak tyto atributy mohou pocházet z libovolné doménové struktury.
  4. Atributy související se systémem Exchange (technické atributy, které nejsou viditelné v globálním seznamu), jsou k dispozici v doménové struktuře, kde `mailNickname ISNOTNULL` .
  5. Pokud existuje více doménových struktur, které by splňovaly jedno z těchto pravidel, pak se pro určení doménové struktury, která tyto atributy přispívá, použije pořadí vytváření (datum/čas) konektorů (doménové struktury). První připojená doménová struktura bude první doménovou strukturou, která se bude synchronizovat. 

### <a name="contact-out-of-box-rules"></a>Kontaktovat dodaná pravidla
Aby bylo možné synchronizovat objekt kontaktu, musí splňovat následující požadavky:

* Kontakt musí být povolen poštou. Ověřuje se pomocí následujících pravidel:
  * `IsPresent([proxyAddresses]) = True)`. Atribut proxyAddresses musí být vyplněn.
  * Primární e-mailovou adresu lze najít buď v atributu proxyAddresses, nebo v atributu mail. \@K ověření, že se jedná o e-mailovou adresu, se používá přítomnost. Jedno z těchto dvou pravidel musí být vyhodnoceno na hodnotu true.
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Existuje položka s řetězcem "SMTP:" a pokud existuje, lze \@ v řetězci najít?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. Je naplněný atribut mail a pokud je, lze \@ v řetězci najít?

Následující objekty kontaktů **nejsou synchronizovány** do služby Azure AD:

* `IsPresent([isCriticalSystemObject])`. Ujistěte se, že nejsou synchronizovány žádné objekty kontaktů označené jako kritické. Neměl by být ve výchozí konfiguraci.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Tyto objekty nefungují v Exchangi Online.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Nesynchronizujte žádné objekty oběti replikace.

### <a name="group-out-of-box-rules"></a>Seskupení předdefinovaných pravidel
Aby bylo možné synchronizovat objekt skupiny, musí splňovat následující požadavky:

* Musí mít méně než 50 000 členů. Tento počet je počet členů v místní skupině.
  * Pokud má více členů před prvním zahájením synchronizace, Skupina nebude synchronizována.
  * Pokud počet členů v době, kdy byl původně vytvořen, vzrostl, pak při dosažení 50 000 členů zastaví synchronizaci, dokud počet členů nebude znovu nižší než 50 000.
  * Poznámka: počet členů 50 000 se vynutil taky v Azure AD. Nemůžete synchronizovat skupiny s více členy, i když toto pravidlo upravíte nebo odeberete.
* Pokud je skupina **distribuční skupinou**, musí být také povolená pošta. Pro toto pravidlo se vynutila [pravidla pro kontaktování, která](#contact-out-of-box-rules) jsou v seznamu.

Následující objekty skupiny **nejsou synchronizované s** Azure AD:

* `IsPresent([isCriticalSystemObject])`. Zajistěte, aby se v Active Directory nesynchronizoval žádné objekty, jako jsou například předdefinovaná skupina Administrators.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Starší skupina, kterou používá DirSync.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Skupina rolí.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Nesynchronizujte žádné objekty oběti replikace.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>ForeignSecurityPrincipal předpřipravená pravidla
FSPs jsou připojené k libovolnému objektu ( \* ) v úložišti Metaverse. Ve skutečnosti tato připojení probíhá pouze pro uživatele a skupiny zabezpečení. Tato konfigurace zajišťuje, že se ve službě Azure AD vyřeší a správně reprezentují členství mezi doménovými strukturami.

### <a name="computer-out-of-box-rules"></a>Předpřipravená pravidla pro počítač
Aby bylo možné synchronizovat objekt počítače, musí splňovat následující požadavky:

* `userCertificate ISNOTNULL`. Tento atribut naplní pouze počítače se systémem Windows 10. Všechny objekty počítače s hodnotou v tomto atributu jsou synchronizovány.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Princip scénáře předdefinovaných pravidel
V tomto příkladu používáme nasazení s jednou doménovou strukturou účtů (A), jednu doménovou strukturou prostředků (R) a jedním adresářem služby Azure AD.

![Obrázek s popisem scénáře](./media/concept-azure-ad-connect-sync-default-configuration/scenario.png)

V této konfiguraci předpokládáme, že je v doménové struktuře účtu povolený účet a v doménové struktuře prostředků je zakázaný účet s propojenou poštovní schránkou.

Naším cílem s výchozí konfigurací je:

* Atributy související s přihlašováním se synchronizují z doménové struktury s povoleným účtem.
* Atributy, které lze najít v globálním seznamu adres (globální seznam adres), jsou synchronizovány z doménové struktury s poštovní schránkou. Pokud nelze nalézt poštovní schránku, bude použita jiná doménová struktura.
* Pokud se najde propojená poštovní schránka, pro objekt, který se má exportovat do Azure AD, se musí najít propojený povolený účet.

### <a name="synchronization-rule-editor"></a>Editor pravidel synchronizace
Konfigurace se dá zobrazit a změnit pomocí editoru pravidel synchronizace nástrojů (SRE) a zástupce je možné najít v nabídce Start.

![Ikona editoru pravidel synchronizace](./media/concept-azure-ad-connect-sync-default-configuration/sre.png)

SRE je nástroj Resource Kit a je nainstalovaný s Azure AD Connect synchronizace. Abyste ho mohli spustit, musíte být členem skupiny ADSyncAdmins. Při spuštění se zobrazí něco podobného:

![Pravidla synchronizace příchozí](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

V tomto podokně uvidíte všechna synchronizační pravidla vytvořená pro vaši konfiguraci. Každý řádek v tabulce je jedno pravidlo synchronizace. Vlevo v části typy pravidel jsou uvedeny dva různé typy: příchozí a odchozí. Příchozí a odchozí je ze zobrazení úložiště metaverse. Nebudete se primárně soustředit na pravidla příchozí v tomto přehledu. Skutečný seznam synchronizačních pravidel závisí na zjištěném schématu ve službě AD. Na obrázku výše nemá doménová struktura účtů (fabrikamonline.com) žádné služby, jako je třeba Exchange a Lync, a pro tyto služby se nevytvořila žádná pravidla synchronizace. V doménové struktuře prostředků (res.fabrikamonline.com) ale najdete pravidla synchronizace pro tyto služby. Obsah pravidel se liší v závislosti na zjištěné verzi. Například v nasazení s Exchange 2013 existují více toků atributů nakonfigurovaných než v Exchange 2010/2007.

### <a name="synchronization-rule"></a>Synchronizační pravidlo
Synchronizační pravidlo je objekt konfigurace se sadou atributů při splnění podmínky. Slouží také k popisu způsobu, jakým objekt v prostoru konektoru souvisí s objektem v úložišti Metaverse, označovaným jako **Join** nebo **Match**. Synchronizační pravidla mají hodnotu priority, která určuje, jak vzájemně souvisí. Synchronizační pravidlo s nižší číselnou hodnotou má vyšší prioritu a v konfliktu toku atributů má vyšší prioritu řešení konfliktů.

Podívejte se na pravidlo synchronizace **ve službě AD – uživatel AccountEnabled**. Označte tento řádek v SRE a vyberte **Upravit**.

Vzhledem k tomu, že toto pravidlo je předem nastavené pravidlo, při otevření pravidla se zobrazí upozornění. Nemusíte dělat žádné [změny pravidel](how-to-connect-sync-best-practices-changing-default-configuration.md)dodaných na sebe, takže se zobrazí dotaz na to, co vaše záměry jsou. V takovém případě chcete pouze zobrazit pravidlo. Vyberte **Ne**.

![Upozornění na pravidla synchronizace](./media/concept-azure-ad-connect-sync-default-configuration/warningeditrule.png)

Synchronizační pravidlo má čtyři konfigurační oddíly: Popis, filtr oboru, pravidla spojování a transformace.

#### <a name="description"></a>Description
První část uvádí základní informace, jako je název a popis.

![Karta Popis v editoru pravidel synchronizace](./media/concept-azure-ad-connect-sync-default-configuration/syncruledescription.png)

Najdete zde také informace o tom, na kterém propojeném systému se toto pravidlo vztahuje, který typ objektu v připojeném systému se vztahuje a typ objektu Metaverse. Typ objektu metaverse je vždy osoba bez ohledu na to, kdy je typ zdrojového objektu uživatel, objekt iNetOrgPerson nebo kontakt. Typ objektu Metaverse by se nikdy neměl měnit, aby se vytvořil jako obecný typ. Typ odkazu je možné nastavit na JOIN, StickyJoin nebo zřídit. Toto nastavení spolupracuje s oddílem pravidla spojení a je zahrnuto později.

Můžete také zjistit, že se toto pravidlo synchronizace používá pro synchronizaci hesel. Pokud je uživatel v oboru pro toto pravidlo synchronizace, heslo je synchronizováno z místního prostředí do cloudu (za předpokladu, že jste povolili funkci synchronizace hesla).

#### <a name="scoping-filter"></a>Filtr oboru
Oddíl filtr oboru se používá ke konfiguraci, kdy se má pravidlo synchronizace vztahovat. Vzhledem k tomu, že název synchronizačního pravidla, které se chystáte, by měl být použit pouze pro povolené uživatele, je rozsah nakonfigurován tak, aby mohl uživatel **s atributem** AD bez tohoto nastavení obsahovat bit 2. Když synchronizační modul najde uživatele ve službě AD, použije toto pravidlo synchronizace, **Pokud je uživatel** nastavený na desítkovou hodnotu 512 (povoleno normálnímu uživateli). Toto pravidlo neplatí, pokud má **uživatel nastaveno na hodnotu 514** (zakázaný normální uživatel).

![Snímek obrazovky, který zobrazuje část "filtr oboru" v okně Upravit pravidlo příchozí synchronizace.](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilter.png)

Filtr oboru má skupiny a klauzule, které mohou být vnořené. Aby bylo možné použít synchronizační pravidlo, musí být splněny všechny klauzule uvnitř skupiny. Pokud je definováno více skupin, musí být pro pravidlo splněna aspoň jedna skupina. To znamená, že logická nebo je vyhodnocena mezi skupinami a logickou a je vyhodnocena uvnitř skupiny. Příklad této konfigurace najdete v odchozím synchronizačním pravidle z protokolu **AAD – připojení ke skupině**. Existuje několik skupin filtru synchronizace, například jeden pro skupiny zabezpečení ( `securityEnabled EQUAL True` ) a jeden pro distribuční skupiny ( `securityEnabled EQUAL False` ).

![Karta obor v editoru pravidel synchronizace](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilterout.png)

Toto pravidlo slouží k definování skupin, které se mají zřídit do Azure AD. Distribuční skupiny musí mít povolený e-mail, aby je bylo možné synchronizovat se službou Azure AD, ale pro skupiny zabezpečení není vyžadován e-mail.

#### <a name="join-rules"></a>Pravidla spojování
Třetí část se používá ke konfiguraci způsobu, jakým objekty v prostoru konektoru souvisí s objekty v úložišti Metaverse. Pravidlo, které jste si prohlédli dříve, nemá žádnou konfiguraci pro pravidla spojení, takže místo toho se budete chtít podívat **ve službě AD – připojení uživatele**.

![Karta pravidla připojení v editoru pravidel synchronizace](./media/concept-azure-ad-connect-sync-default-configuration/syncrulejoinrules.png)

Obsah pravidla spojení závisí na možnosti odpovídajícího výběru v Průvodci instalací nástroje. Pro příchozí pravidlo začíná hodnocení objektem v prostoru zdrojového konektoru a každá skupina v pravidlech spojení se vyhodnocuje v pořadí. Je-li zdrojový objekt vyhodnocen tak, aby odpovídal přesně jednomu objektu v úložišti Metaverse pomocí jednoho z pravidel spojení, jsou objekty spojeny. Pokud byla vyhodnocena všechna pravidla a neexistuje žádná shoda, je použit typ odkazu na stránce popis. Pokud je tato konfigurace nastavená na **zřídit**, vytvoří se nový objekt v cílovém úložišti Metaverse, pokud je k dispozici alespoň jeden atribut v kritériích spojení (má hodnotu). Pro zřízení nového objektu do úložiště metaverse se také říká, že je potřeba **projektovat** objekt do úložiště metaverse.

Pravidla spojení jsou vyhodnocována pouze jednou. Když je připojen objekt prostoru konektoru a objekt úložiště metaverse, zůstanou spojeny, dokud je rozsah synchronizačního pravidla stále spokojen.

Při vyhodnocování pravidel synchronizace musí být v oboru pouze jedno pravidlo synchronizace s definovanými pravidly spojení. Pokud je pro jeden objekt nalezeno více synchronizačních pravidel s pravidly spojení, je vyvolána chyba. Z tohoto důvodu se doporučuje mít pouze jedno pravidlo synchronizace s připojením, pokud je v oboru pro objekt definováno více synchronizačních pravidel. V dopředných konfiguracích pro Azure AD Connect synchronizaci můžete tato pravidla najít tak, že si podíváte na název a vyhledáte je pomocí Word **Join** na konci názvu. Pravidlo synchronizace bez definovaných pravidel spojení aplikuje toky atributů, pokud jiné synchronizační pravidlo spojilo objekty dohromady nebo zřídilo nový objekt v cíli.

Pokud se podíváte na obrázek výše, vidíte, že se pravidlo snaží spojit s **objectSID** pomocí rozhraní **msExchMasterAccountSID** (Exchange) a **msRTCSIP-OriginatorSid** (Lync), což očekáváme v topologii doménové struktury prostředků. Stejné pravidlo najdete ve všech doménových strukturách. Předpokladem je, že každá doménová struktura může být buď účet, nebo doménová struktura prostředků. Tato konfigurace funguje také v případě, že máte účty, které jsou v jedné doménové struktuře živé a není nutné je připojit.

#### <a name="transformations"></a>Transformace
Oddíl transformuje všechny toky atributů, které se vztahují na cílový objekt, když jsou objekty připojeny, a filtr oboru je splněn. Když se vrátíte zpět k části pravidlo synchronizace **v rámci AD – uživatel AccountEnabled** , najdete následující transformace:

![Karta transformace v editoru pravidel synchronizace](./media/concept-azure-ad-connect-sync-default-configuration/syncruletransformations.png)

Pokud chcete tuto konfiguraci vložit do kontextu, v Account-Resource nasazení doménové struktury se očekává, že se v doménové struktuře účtů a zakázaný účet v doménové struktuře prostředků v nastaveních Exchange a Lyncu najde povolený účet. Synchronizační pravidlo, které se chystáte, obsahuje atributy vyžadované pro přihlášení a tyto atributy by měly být předávány z doménové struktury, ve které je povolený účet. Všechny tyto toky atributů jsou spojeny do jednoho synchronizačního pravidla.

Transformace může mít různé typy: konstanta, přímá a výraz.

* Konstantní tok vždy přetéká hodnotu pevně zakódované. V případě výše uvedeného případu vždy nastaví hodnotu **true** v atributu Metaverse s názvem **accountEnabled**.
* Přímý tok vždy natéká hodnotu atributu ve zdroji do cílového atributu tak, jak je.
* Třetí typ toku je výraz a umožňuje pokročilejší konfigurace.

Expression Language je VBA (jazyk Visual Basic for Application), takže lidé, kteří mají zkušenosti s systém Microsoft Office nebo VBScript, budou formát rozpoznávat. Atributy jsou uzavřeny v hranatých závorkách [attributeName]. V názvech atributů a funkcích se rozlišují velká a malá písmena, ale editor synchronizačních pravidel vyhodnocuje výrazy a poskytuje upozornění, pokud výraz není platný. Všechny výrazy jsou vyjádřeny na jednom řádku s vnořenými funkcemi. Pokud chcete zobrazit sílu konfiguračního jazyka, je zde tok pro pwdLastSet, ale s vloženými dalšími komentáři:

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

Další informace o jazyku výrazů pro toky atributů najdete v tématu [Principy deklarativních zřizovacích výrazů](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) .

### <a name="precedence"></a>Priorita
Nyní jste si prohlédli některá jednotlivá pravidla synchronizace, ale pravidla společně v konfiguraci spolupracují. V některých případech je hodnota atributu popsána z více synchronizačních pravidel do stejného cílového atributu. V tomto případě se pro určení atributu WINS používá priorita atributů. Podívejte se na příklad atributu sourceAnchor. Tento atribut je důležitý, aby se mohl přihlásit k Azure AD. Tok atributů pro tento atribut můžete najít ve dvou různých synchronizačních pravidlech, **ve službě AD – User AccountEnabled** a **in from AD – User Common**. Kvůli prioritě synchronizačního pravidla je atribut sourceAnchor z doménové struktury s povoleným účtem, který je nejdřív k dispozici, když se k objektu úložiště metaverse připojí několik objektů. Pokud neexistují žádné povolené účty, synchronizační modul používá synchronizační pravidlo zachytit vše **v rámci služby AD – společný uživatel**. Tato konfigurace zajišťuje, že i v případě zakázaných účtů je stále k dispozici sourceAnchor.

![Pravidla synchronizace příchozí](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

Priorita pro pravidla synchronizace je nastavena v části skupiny Průvodce instalací nástroje. Všechna pravidla ve skupině mají stejný název, ale jsou připojená k různým připojeným adresářům. Průvodce instalací poskytuje pravidlo **ve službě AD – nejvyšší prioritu připojení uživatele** a projde všemi připojenými adresáři služby AD. Pak pokračuje v dalších skupinách pravidel v předdefinovaném pořadí. V rámci skupiny se pravidla přidávají v pořadí, v jakém byly přidány konektory v průvodci. Pokud se pomocí Průvodce přidá jiný konektor, pravidla synchronizace se přeřadí a pravidla nového konektoru se do každé skupiny vloží jako poslední.

### <a name="putting-it-all-together"></a>Spojení všech součástí dohromady
Nyní ví, že pravidla synchronizace jsou schopná pochopit, jak konfigurace funguje s různými synchronizačními pravidly. Pokud se podíváte na uživatele a atributy, které se podílejí do úložiště metaverse, pravidla se aplikují v tomto pořadí:

| Name | Komentář |
|:--- |:--- |
| Ve službě AD – připojení uživatele |Pravidlo pro spojování objektů prostoru konektoru s úložištěm Metaverse |
| V nástroji ze služby AD – UserAccount povoleno |Atributy vyžadované pro přihlášení k Azure AD a Microsoft 365. Chceme tyto atributy z povoleného účtu. |
| V nástroji ze služby AD – uživatel společný ze systému Exchange |V globálním seznamu adres byly nalezeny atributy. Předpokládáme, že kvalita dat je nejlepší v doménové struktuře, ve které jsme našli poštovní schránku uživatele. |
| V nástroji ze služby AD – společná pro uživatele |V globálním seznamu adres byly nalezeny atributy. V případě, že jsme nenašli poštovní schránku, může hodnota atributu přispívat libovolný jiný připojený objekt. |
| V nástroji ze služby AD – uživatel Exchange |Existuje pouze v případě, že byl zjištěn server Exchange. Natéká všechny atributy výměny infrastruktury. |
| V nástroji ze služby AD – uživatel Lync |Existuje pouze v případě, že byla zjištěna Lync. Natéká všechny atributy infrastruktury Lyncu. |

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o modelu konfigurace v tématu [Principy deklarativního zřizování](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Přečtěte si další informace o jazyce výrazů v tématu [Principy deklarativních zřizovacích výrazů](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Pokračovat v čtení, jak dodaná konfigurace funguje při [porozumění uživatelům a kontaktům](concept-azure-ad-connect-sync-user-and-contacts.md)
* Informace o tom, jak provést praktickou změnu pomocí deklarativního zajišťování, najdete v tématu [Postup provedení změny ve výchozí konfiguraci](how-to-connect-sync-change-the-configuration.md).

**Témata s přehledem**

* [Azure AD Connect synchronizace: pochopení a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)

