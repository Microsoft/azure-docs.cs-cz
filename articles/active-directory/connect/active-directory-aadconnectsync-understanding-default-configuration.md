---
title: 'Synchronizace Azure AD Connect: Principy výchozí konfigurace | Microsoft Docs'
description: Tento článek popisuje výchozí konfigurace v synchronizaci Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ed876f22-6892-4b9d-acbe-6a2d112f1cd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: f4278dc3af1074b6de299444d2b205396bc0a9c0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34595304"
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Synchronizace služby Azure AD Connect: Principy výchozí konfigurace
Tento článek vysvětluje out-of-box konfigurační pravidla. Ho dokumentů, pravidla a jak tato pravidla vliv na konfiguraci. Je také vás provede výchozí konfigurace synchronizace služby Azure AD Connect. Cílem je, že čtečka plně chápe, jak funguje konfigurační model s názvem deklarativní zřizování v příkladu reálného. Tento článek předpokládá, že jste již nainstalovali a konfigurace synchronizace služby Azure AD Connect pomocí Průvodce instalací.

Zjistit podrobnosti o konfigurační model číst [Principy deklarativní zřizování](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Pravidla Out-of-box z místní do služby Azure AD
Následující výrazy naleznete v konfiguraci out-of-box.

### <a name="user-out-of-box-rules"></a>Pravidla out-of-box uživatele
Tato pravidla platí také pro typ objektu iNetOrgPerson.

Objekt uživatele, musí splňovat následující synchronizaci:

* Musí mít sourceAnchor.
* Po vytvoření objektu ve službě Azure AD, nelze změnit sourceAnchor. Pokud je hodnota změněné místní, objekt zastaví, synchronizace, dokud se nezmění sourceAnchor zpět na předchozí hodnotu.
* Musí mít atribut accountEnabled (userAccountControl) naplněno. S místní Active Directory tento atribut je stále přítomen a vyplněná.

Následující objekty uživatele jsou **není** synchronizovat se službou Azure AD:

* `IsPresent([isCriticalSystemObject])`. Ujistěte se, mnoho out-of-box objektů ve službě Active Directory, jako je předdefinovaný účet správce, nejsou synchronizovány.
* `IsPresent([sAMAccountName]) = False`. Zkontrolujte, zda nejsou synchronizované objekty uživatele se žádné atributy sAMAccountName. Tento případ by mohlo dojít pouze prakticky v doméně upgradu ze systému Windows NT 4.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Nesynchronizovat účtu služby používaného synchronizace Azure AD Connect a její starší verze.
* Nesynchronizovat účty serveru Exchange, které nebude fungovat v systému Exchange Online.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Nesynchronizovat objekty, které nebude fungovat v systému Exchange Online.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  Tato bitová maska (& H21C07000) bude filtrovat následující objekty:
  * Poštovní veřejné složce (v Preview od verze 1.1.524.0)
  * Odpovídající poštovní schránky systému
  * Poštovní schránky databáze poštovní schránky (poštovní schránka systému)
  * Univerzální skupina zabezpečení (nemohli použít pro uživatele, ale je k dispozici z důvodu starší verze)
  * Non-univerzální skupiny (nemohli použít pro uživatele, ale je k dispozici z důvodu starší verze)
  * Plán poštovní schránky
  * Zjišťování poštovní schránky
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Nesynchronizovat všechny objekty postižené replikace.

Platí následující pravidla atribut:

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. Atribut sourceAnchor není podílí z propojená poštovní schránky. Předpokládá se, že pokud byl nalezen propojená poštovní schránka, je skutečný účet později připojený.
* Exchange související s atributy se synchronizují pouze pokud atribut **mailNickName** má hodnotu.
* Po několika doménovými strukturami se potom atributy používán v následujícím pořadí:
  1. Atributy související s přihlášení (například userPrincipalName) jsou podílí z doménové struktury s aktivovaný účet.
  2. Atributy, které lze nalézt v Exchange GAL (seznamu pro globální adresa) jsou podílí z doménové struktury s poštovní schránky systému Exchange.
  3. Pokud je možné najít žádné poštovní schránky, tyto atributy mohou pocházet z jakékoli doménové struktury.
  4. Exchange související s atributy (technické atributy nejsou viditelné v globálním seznamu adres) jsou podílí z doménové struktury kde `mailNickname ISNOTNULL`.
  5. Pokud existují několik doménových struktur, které splňují jednu z těchto pravidel, pořadí (datum a čas) vytvoření konektorů (doménové struktury) slouží k určení, která doménová struktura přispívá atributy.

### <a name="contact-out-of-box-rules"></a>Obraťte se na out-of-box pravidla
Objekt kontaktu musí splňovat následující synchronizaci:

* Kontakt musí být povoleným e-mailem. Ověření na základě následujících pravidel:
  * `IsPresent([proxyAddresses]) = True)`. Musí být naplněn atribut proxyAddresses.
  * Primární e-mailovou adresu naleznete v atribut proxyAddresses nebo atribut mail. Přítomnost @ se používá k ověření, že je obsah e-mailovou adresu. Mezi tyto dvě pravidla musí vyhodnotit na hodnotu True.
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Je k dispozici položku s "SMTP:" a pokud neexistuje, můžete @ být nalezen v řetězci?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. Je atribut mail vyplňovat a je-li, můžete @ být nalezen v řetězci?

Následující objekty kontaktní **není** synchronizovat se službou Azure AD:

* `IsPresent([isCriticalSystemObject])`. Ujistěte se, jsou synchronizovány žádné kontaktní objekty označení kritický. Nesmí být žádné s výchozí konfigurací.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Tyto objekty nebude fungovat v systému Exchange Online.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Nesynchronizovat všechny objekty postižené replikace.

### <a name="group-out-of-box-rules"></a>Skupina out-of-box pravidla
Objekt skupiny musí splňovat následující synchronizaci:

* Musí mít méně než 50 000 členy. Je to počet počet členů v místní skupině.
  * Pokud má víc členů než poprvé spustí se synchronizace, skupině není synchronizován.
  * Pokud počet členů tomu, když byl původně vytvořen, pak když dorazí do 50 000 členy přestane synchronizace, dokud počet členství je nižší než 50 000 znovu.
  * Poznámka: Počet 50 000 členství je také vynucené Azure AD. Nemůžete se moci synchronizovat skupiny s více členů, i když můžete upravit nebo odebrat toto pravidlo.
* Pokud je skupina **distribuční skupiny**, a také musí být povoleno e-mailu. V tématu [obraťte se na pravidla out-of-box](#contact-out-of-box-rules) pro toto pravidlo se nevynutí.

Následující objekty skupiny jsou **není** synchronizovat se službou Azure AD:

* `IsPresent([isCriticalSystemObject])`. Ujistěte se, mnoho out-of-box objektů ve službě Active Directory, jako jsou předdefinované skupiny administrators, nejsou synchronizovány.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Starší verze skupina, která nástroj DirSync používá.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Skupina role.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Nesynchronizovat všechny objekty postižené replikace.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>ForeignSecurityPrincipal out-of-box pravidla
FSP jsou připojené k "žádný" (\*) objektu v úložišti metaverse. Ve skutečnosti tohoto spojení se dělá jenom pro uživatele a skupiny zabezpečení. Tato konfigurace zajistí, že jsou ve skupinách mezi doménovými strukturami přeložit a správně reprezentováni v Azure AD.

### <a name="computer-out-of-box-rules"></a>Pravidla out-of-box počítače
Objekt počítače se musí splňovat následující synchronizaci:

* `userCertificate ISNOTNULL`. Pouze počítače s Windows 10 naplnit tento atribut. Všechny objekty počítače s hodnotou v tomto atributu jsou synchronizovány.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Vysvětlení pravidel out-of-box scénář
V tomto příkladu používáme nasazení jedné doménové struktuře účtu (A), jednu doménovou strukturu prostředků (R) a jeden adresář Azure AD.

![Obrázek s popis scénáře](./media/active-directory-aadconnectsync-understanding-default-configuration/scenario.png)

V této konfiguraci se předpokládá, že aktivovaný účet v doménové struktuře účtu a deaktivovaného účtu v doménové struktuře prostředku s propojená poštovní schránka.

Naším cílem s výchozí konfigurací je:

* Atributy související s přihlášení se synchronizují z doménové struktury s aktivovaný účet.
* Atributy, které lze nalézt v GAL (seznamu pro globální adresa) jsou synchronizovány z doménové struktury s poštovní schránku. Pokud lze nalézt žádné poštovní schránky, je použít jiné doménové struktury.
* Pokud je nalezen propojená poštovní schránka, je nutné nalézt propojené aktivovaný účet pro objekt, který má být exportovány do služby Azure AD.

### <a name="synchronization-rule-editor"></a>Synchronization Rule Editor
Konfigurace můžete zobrazit a změnit pomocí nástroje synchronizace pravidla Editor (SRE) a zástupce k němu najdete v nabídce start.

![Ikona editoru pravidel synchronizace](./media/active-directory-aadconnectsync-understanding-default-configuration/sre.png)

Instalaci synchronizace Azure AD Connect SRE je nástroj resource kit. Chcete-li jej spustit, musíte být členem skupiny ADSyncAdmins. Po jeho spuštění, se zobrazí přibližně takto:

![Příchozí pravidla synchronizace](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

V tomto podokně zobrazí všechny synchronizační pravidla, které jsou vytvořeny pro vaši konfiguraci. Každý řádek v tabulce představuje jeden synchronizační pravidlo. Na levé straně v části typy pravidel, jsou uvedeny dva různé typy: příchozí a odchozí. Příchozí a odchozí je z pohledu úložiště metaverse. Zaměřuje se chystáte především na příchozích pravidel v tomto přehledu. Skutečný seznam pravidel synchronizace závisí na schéma zjištěného v AD. Na obrázku výše doménové struktury účtu (fabrikamonline.com) nemá žádné služby, například Exchange a Lync, a žádná pravidla synchronizace byly vytvořeny pro tyto služby. Ale v doménové struktuře prostředku (res.fabrikamonline.com) zjistíte synchronizační pravidla pro tyto služby. Obsah pravidla se liší v závislosti na verzi zjištěna. Například v nasazení s Exchange 2013 existují další toky atributů, které jsou nakonfigurované než v systému Exchange 2010 nebo 2007.

### <a name="synchronization-rule"></a>Synchronizační pravidlo
Pravidlo synchronizace je objekt konfigurace sadu atributů toku, pokud je podmínka. Používá se také k popisu, jak je související objekt v prostoru konektoru k objektu v úložišti metaverse, označuje jako **spojení** nebo **odpovídat**. Synchronizační pravidla mají přednost před hodnotu, která určuje, jak se vztahují k sobě navzájem. Pravidlo synchronizace s nižší číselná hodnota má vyšší prioritu a v konfliktu tok atributů, vyšší prioritu služby wins řešení konfliktů.

Jako příklad, podívejte se na synchronizační pravidlo **v ze služby Active Directory – uživatel AccountEnabled**. Označit tento řádek v SRE a vyberte **upravit**.

Vzhledem k tomu, že toto pravidlo je pravidlo out-of-box, zobrazí se při otevření pravidlo upozornění. Neprovádějte žádné [změny pravidel out-of-box](active-directory-aadconnectsync-best-practices-changing-default-configuration.md), takže se zobrazí dotaz, jaké jsou vaše záměry. V takovém případě chcete jenom zobrazit pravidlo. Vyberte **ne**.

![Synchronizační pravidla upozornění](./media/active-directory-aadconnectsync-understanding-default-configuration/warningeditrule.png)

Synchronizační pravidlo obsahuje čtyři části konfigurace: popis, rozsahu filtru, spojení pravidel a transformace.

#### <a name="description"></a>Popis
První část obsahuje základní informace, jako je název a popis.

![Popis kartě editor synchronizované pravidla ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruledescription.png)

Můžete také získat informace, o které připojený systém toto pravidlo se vztahuje k, které objektu v připojených systému, které se vztahuje na typ a typ objektu úložiště metaverse. Typ objektu úložiště metaverse je vždy osoba bez ohledu na to, když je typ zdrojového objektu uživatele, iNetOrgPerson nebo požádejte. Typ objektu úložiště metaverse nikdy změnit tak, že je vytvořen jako obecného typu. Typ propojení můžete nastavit na spojení, StickyJoin nebo zřídit. Toto nastavení spolupracuje s části připojení pravidla a věnuje se později.

Zobrazí se také, že toto synchronizační pravidlo se používá pro synchronizaci hesel. Pokud je uživatel v oboru pro toto synchronizační pravidlo, heslo synchronizovaných z místní do cloudu (za předpokladu, že jste povolili funkce Synchronizace hesla).

#### <a name="scoping-filter"></a>Filtr vytváření oboru
V části filtru pro vytváření oboru slouží ke konfiguraci, pokud by se měly používat synchronizační pravidlo. Vzhledem k tomu, že název pravidla synchronizace se díváte uvádí, že by měl použít pouze pro povolené uživatele, je nakonfigurován oboru proto AD atribut **userAccountControl** nesmí nastaven bit 2. Je-li synchronizační modul Vyhledá uživatele ve službě AD, platí toto synchronizační pravidlo, když **userAccountControl** nastavená na hodnotu decimal 512 (povoleného normální uživatele). Pokud má uživatel nevztahuje pravidlo **userAccountControl** nastavena na 514 (zakázaný normální uživatel).

![Karta oboru v editoru pravidel synchronizace ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilter.png)

Oboru filtru má skupin a věty, které mohou být použity. Všechny klauzule uvnitř skupiny musí být splněny pro synchronizační pravidlo použít. Když jsou definovány více skupin, alespoň jednu skupinu musí být splněny pro pravidlo použít. To znamená logické nebo vyhodnocena mezi skupinami a logickou a vyhodnotí uvnitř skupiny. Příklad této konfigurace můžete nalézt v odchozí pravidlo synchronizace **Out aad – připojení skupiny**. Existuje několik skupin synchronizace filtru, například jeden pro skupiny zabezpečení (`securityEnabled EQUAL True`) a jeden pro distribuční skupiny (`securityEnabled EQUAL False`).

![Karta oboru v editoru pravidel synchronizace ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilterout.png)

Toto pravidlo se používá k definování skupiny, které musí být zřízená Azure AD. Distribuční skupiny musí být povoleno možné synchronizovat se službou Azure AD e-mailu, ale pro skupiny zabezpečení e-mailu se nevyžaduje.

#### <a name="join-rules"></a>Připojení k pravidla
Třetí části slouží ke konfiguraci, jak objekty v prostoru konektoru vztahují k objektům v úložišti metaverse. Pravidlo, které jste dříve prohlédli nemá žádnou konfiguraci pro připojení k pravidla, takže místo toho chcete podívejte se na **v ze služby Active Directory – připojení uživatele k**.

![Karta pravidla připojení v editoru pravidel synchronizace ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulejoinrules.png)

Obsah spojení pravidlo závisí na odpovídající možnosti vybrané v Průvodci instalací. Pro příchozí pravidlo vyhodnocení začíná na objekt v prostoru konektoru zdroje a každou skupinu v pravidlech spojení je vyhodnoceny v pořadí. Pokud se tak, aby odpovídaly právě jednoho objektu v úložišti metaverse pomocí jednoho pravidla spojení vyhodnotí zdrojový objekt, je objekty připojeni. Pokud byly vyhodnoceny všechna pravidla a není nalezena žádná shoda, bude použit typ odkazu na stránce popis. Pokud tuto konfiguraci je nastavený na **zřídit**, pak se vytvoří nový objekt v cílové úložiště metaverse. Zřídit nový objekt úložiště metaverse a je také označován jako na **projektu** objektu úložiště metaverse.

Spojení pravidla se vyhodnocují jenom jednou. Když jsou připojena objekt prostor konektoru a objektu úložiště metaverse, zůstanou připojené k, tak dlouho, dokud je stále splněna oboru synchronizačního pravidla.

Při vyhodnocování pravidel synchronizace, musí být pouze jeden synchronizační pravidlo s spojení pravidla definovaná v oboru. Pokud se více pravidel synchronizace s pravidly spojení pro jeden objekt, je vržena chyba. Z tohoto důvodu osvědčeným postupem je mít pouze jeden synchronizační pravidlo u připojení při více pravidel synchronizace jsou v oboru pro objekt definovaný. V konfiguraci out-of-box synchronizace Azure AD Connect, můžete tato pravidla nalezen podle názvu a najít ty slovem **připojení** na konci názvu. Pravidlo synchronizace bez definováno žádné připojení k pravidel platí toky atributů při dalším synchronizačním pravidle propojeny objekty nebo zřídit nový objekt v cíl.

Pokud se podíváte na obrázku výše, uvidíte, že pravidlo se snaží připojit **objectSID** s **msExchMasterAccountSid** (Exchange) a **msRTCSIP-OriginatorSid** (Lync), který je co Očekáváme, že v účtu prostředků topologie doménové struktury. Můžete najít stejné pravidlo na všechny doménové struktury. Předpokladem je, že každá doménová struktura může být účtu nebo prostředek doménové struktury. Tato konfigurace taky funguje v případě, že mají účty, které za provozu v jedné doménové struktury a nemusí být připojen.

#### <a name="transformations"></a>Transformace
V části transformace definuje všechny toky atributů, které platí pro cílový objekt, pokud jsou připojené objekty a obor filtru je splněné. Po návratu do **v ze služby Active Directory – uživatel AccountEnabled** synchronizační pravidlo, najít následující transformace:

![Transformace kartě editor synchronizované pravidla ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruletransformations.png)

Uvést do kontextu, v nasazení doménové struktury prostředků účtu, tato konfigurace se očekává, k vyhledání aktivovaný účet v doménové struktuře účtu a deaktivovaného účtu v doménové struktuře prostředku se nastavení Exchange a Lync. Pravidlo synchronizace se díváte obsahuje atributy vyžadované pro přihlášení a tyto atributy musí procházet z doménové struktury je aktivovaný účet. Všechny tyto toky atributů jsou připravili v jedné synchronizační pravidlo.

Transformace může mít různé typy: Konstanta, přímo a výraz.

* Konstantní toku vždy toky pevně zakódované hodnotu. V případě výše vždy nastaví hodnotu **True** do atribut úložiště metaverse s názvem **accountEnabled**.
* Přímé toku vždy toků hodnotu atributu ve zdroji do atribut target jako-je.
* Typ třetí tok je výraz a umožňuje pro pokročilejší konfigurace.

Výraz jazyka je VBA (Visual Basic pro aplikace), takže uživatelé s prostředí systému Microsoft Office nebo VBScript rozpozná formátu. Atributy jsou uzavřené v hranatých závorkách [attributeName]. Názvy atributů a funkce jsou malá a velká písmena, ale editoru pravidla synchronizace vyhodnotí výrazy a zobrazit upozornění, pokud výraz není platný. Všechny výrazy jsou vyjádřeny na jeden řádek s vnořené funkce. Pokud chcete zobrazit možnosti konfigurace jazyka, zde je toku pwdLastSet, ale s další komentáři vložit:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

V tématu [Principy deklarativní zřizování výrazy](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) Další informace o jazyce výraz pro toky atributů.

### <a name="precedence"></a>Priorita
Jste nyní prohlédli některé jednotlivé synchronizační pravidla, ale pravidla spolupracují v konfiguraci. V některých případech je hodnota atributu podílí z více pravidel synchronizace na stejný atribut target. V takovém případě přednost atributu slouží k určení, které atribut služby wins. Jako příklad podívejte se na atribut sourceAnchor. Tento atribut je důležité atribut moct přihlásit k Azure AD. Můžete najít tok atributů pro tento atribut v dvě různá pravidla synchronizace **v ze služby Active Directory – uživatel AccountEnabled** a **v ze služby Active Directory – běžné uživatele**. Z důvodu prioritu synchronizačního pravidla atribut sourceAnchor je podílí z doménové struktury s aktivovaný účet nejprve po několik objektů spojeno do objektu úložiště metaverse. Pokud nejsou povolené účty, pak synchronizační modul používá pravidlo synchronizace catch-all **v ze služby Active Directory – běžné uživatele**. Tato konfigurace zajistí, že i pro účty, kteří jsou zakázáni, stále existuje sourceAnchor.

![Příchozí pravidla synchronizace](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

Prioritu pro synchronizační pravidla nastavená ve skupinách, Průvodce instalací. Všechna pravidla ve skupině mají stejný název, ale jsou připojené k jiné připojeného adresáře. Průvodce instalací poskytuje pravidlo **v ze služby Active Directory – připojení uživatele k** nejvyšší prioritu a iterace, přes všechny připojené adresáře AD. Poté pokračuje v další skupiny pravidel v předdefinovaném pořadí. Uvnitř skupiny se přidají pravidla v pořadí, ve kterém byly přidány konektory v průvodci. Pokud jiný konektor přidána prostřednictvím průvodce, přeuspořádají synchronizační pravidla a pravidla nový konektor jsou vloženy poslední v každé skupině.

### <a name="putting-it-all-together"></a>Třeba umisťovat všechny společně
Víme teď dost o synchronizační pravidla, abyste mohli pochopit, jak funguje v konfiguraci s jinými pravidly synchronizace. Pokud se podíváte na uživatele a atributy, které jsou na úložiště metaverse podílí, pravidla se použijí v uvedeném pořadí:

| Název | Poznámka |
|:--- |:--- |
| V ze služby Active Directory – připojení uživatele |Pravidlo pro připojení objekty konektoru místa s úložiště metaverse. |
| V ze služby Active Directory – povolené uživatelské účty |Atributy, které jsou potřebné pro přihlášení ke službě Azure AD a Office 365. Chceme, aby tyto atributy z aktivovaný účet. |
| V ze služby Active Directory – běžné uživatele ze systému Exchange |V seznamu pro globální adresa byly nalezeny atributy. Předpokládáme, že kvality dat je vhodné v doménové struktuře, kde našli jsme poštovní schránky uživatele. |
| V ze služby Active Directory – běžné uživatele |V seznamu pro globální adresa byly nalezeny atributy. V případě, že jsme nenašli poštovní schránku, můžete přispívat libovolného připojený k objektu hodnotu atributu. |
| V ze služby Active Directory – Exchange uživatele |Existuje pouze pokud byl zjištěn Exchange. Ho toky všechny atributy Exchange infrastruktury. |
| V ze služby Active Directory – uživatele aplikace Lync |Existuje pouze pokud byla zjištěna aplikace Lync. Ho toky všechny atributy Lync infrastruktury. |

## <a name="next-steps"></a>Další postup
* Další informace o konfiguraci modelu v [Principy deklarativní zřizování](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* Další informace o jazyk výrazů v [Principy deklarativní zřizování výrazy](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
* Pokračujte ve čtení fungování konfigurace out-of-box [Principy uživatelů a kontaktů](active-directory-aadconnectsync-understanding-users-and-contacts.md)
* Informace o tom, praktické změnit pomocí deklarativní zřizování v [jak provést změnu výchozí konfigurace](active-directory-aadconnectsync-change-the-configuration.md).

**Témata s přehledem**

* [Synchronizace Azure AD Connect: pochopení a přizpůsobení synchronizace](active-directory-aadconnectsync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md)

