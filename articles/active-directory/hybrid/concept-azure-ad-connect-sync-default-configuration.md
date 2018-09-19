---
title: 'Synchronizace Azure AD Connect: Principy výchozí konfigurace | Dokumentace Microsoftu'
description: Tento článek popisuje výchozí konfigurace ve službě Azure AD Connect sync.
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
ms.openlocfilehash: bd708d279649138fcb17362491da4eb7539c478b
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46313954"
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Synchronizace služby Azure AD Connect: Principy výchozí konfigurace
Tento článek vysvětluje, out-of-box konfigurační pravidla. Dokumentují pravidla a vliv těchto pravidel v konfiguraci. To vás také provede výchozí konfiguraci synchronizace Azure AD Connect. Cílem je, že čtečky rozumí fungováním konfigurační model s názvem deklarativní zřizování v reálný příklad. Tento článek předpokládá, že jste již nainstalovali a konfigurace synchronizace služby Azure AD Connect pomocí Průvodce instalací.

Informace o tom podrobnosti o konfiguraci modelu, přečtěte si [Principy deklarativní zřizování](concept-azure-ad-connect-sync-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Pravidla Out-of-box v místním do služby Azure AD
Následující výrazy můžete najít v konfiguraci out-of-box.

### <a name="user-out-of-box-rules"></a>Pravidla out-of-box uživatele
Tato pravidla platí také pro typ objektu iNetOrgPerson.

Objekt uživatele musí splňovat následující se dá provést synchronizace:

* Musí mít sourceAnchor.
* Po vytvoření objektu ve službě Azure AD, nelze změnit sourceAnchor. Pokud je hodnota změněné v místním, objekt přestane synchronizace, dokud sourceAnchor se změní zpět na původní hodnotu.
* Musí mít atribut accountEnabled (userAccountControl) naplněna. Tento atribut v místní službě Active Directory, jsou vždy k dispozici a mají údaj vyplněný.

Následující objekty uživatele **není** synchronizovány se službou Azure AD:

* `IsPresent([isCriticalSystemObject])`. Zajištění mnoho out-of-box objektů ve službě Active Directory, jako je předdefinovaný účet správce, nejsou synchronizovány.
* `IsPresent([sAMAccountName]) = False`. Zkontrolujte, jestli nejsou synchronizované uživatelské objekty s žádný atribut sAMAccountName. Tento případ by mohlo dojít pouze prakticky v doméně upgradu ze systému Windows NT 4.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Nesynchronizovat má účet služby používaný službou synchronizace Azure AD Connect a jeho staršími verzemi.
* Nesynchronizovat účty serveru Exchange, které nebude fungovat v systému Exchange Online.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Nesynchronizovat objekty, které nebude fungovat v systému Exchange Online.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  Tato bitová maska (& H21C07000) byste odfiltrovat následující objekty:
  * Poštovní veřejné složce (v náhledu od verze 1.1.524.0)
  * Odebrání dodatečné poštovní schránky systému
  * Poštovní schránky databáze poštovní schránky (poštovní schránky systému)
  * Univerzální skupina zabezpečení (pro uživatele se nedají použít, ale je k dispozici kvůli starším verzím)
  * Non-univerzální skupiny (pro uživatele se nedají použít, ale je k dispozici kvůli starším verzím)
  * Plán poštovní schránky
  * Zjišťování poštovní schránky
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Všechny objekty victim replikace nebude synchronizovat.

Platí následující pravidla atribut:

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. Atribut sourceAnchor není uživatel z propojená poštovní schránka. Předpokládá se, že pokud byla nalezena propojená poštovní schránka, je skutečný účet později připojen.
* Exchange týkající se atributy synchronizují jenom Pokud atribut **mailNickName** má hodnotu.
* Po několika doménovými strukturami se atributy využité v tomto pořadí:
  1. Atributy týkající se přihlášení (např. userPrincipalName) udělují z doménové struktury s povolený účet.
  2. Atributy, které se mohou nacházet serveru Exchange GAL (globální seznam adres) udělují z doménové struktury s poštovní schránku systému Exchange.
  3. Pokud se najdou se žádné poštovní schránky, tyto atributy mohou pocházet ze všech doménových strukturách.
  4. Exchange týkající se udělují atributy (technické atributy nejsou viditelné v globálním seznamu adres) z doménové struktury kde `mailNickname ISNOTNULL`.
  5. Pokud existuje více doménových struktur, které splňují jednu z těchto pravidel, pořadí (datum/čas) vytvoření konektorů (doménové struktury) slouží k určení, která doménová struktura přispívá atributy.

### <a name="contact-out-of-box-rules"></a>Obraťte se na pravidla out-of-box
Objekt kontaktu musí splňovat následující se dá provést synchronizace:

* Kontakt musí být s povoleným e-mailem. Je ověřený pomocí následujících pravidel:
  * `IsPresent([proxyAddresses]) = True)`. Musí být vyplněno atributu proxyAddresses.
  * Primární e-mailovou adresu najdete v atributu proxyAddresses nebo atribut pošty. Přítomnost \@ se používá k ověření, že obsah je e-mailovou adresu. Některé z těchto dvou pravidel musí být vyhodnocena na hodnotu True.
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Je k dispozici položka se "SMTP:" a pokud neexistuje, můžete \@ nalezena v řetězci?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. Atribut mail vyplněný a pokud se jedná, můžete \@ nalezena v řetězci?

Následující objekty kontaktní **není** synchronizovány se službou Azure AD:

* `IsPresent([isCriticalSystemObject])`. Ujistěte se synchronizují žádné kontaktní objekty, které jsou označené jako důležité. Nesmí být žádné s výchozí konfigurací.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Tyto objekty nebude fungovat v systému Exchange Online.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Všechny objekty victim replikace nebude synchronizovat.

### <a name="group-out-of-box-rules"></a>Skupina pravidel out-of-box
Objekt skupiny musí splňovat následující se dá provést synchronizace:

* Musí mít méně než 50 000 členů. Tento počet je počet členů v místní skupině.
  * Pokud má víc členů než poprvé spustí se synchronizace, není synchronizovaný skupině.
  * Pokud počet členů růst od kdy byl původně vytvořen, pak když dosáhne 50 000 členů přestane synchronizace, dokud počet členství je menší než 50 000 znovu.
  * Poznámka: Počet 50 000 členství je také vynucuje Azure AD. Nejste schopni synchronizovat skupiny s více členy, i když můžete upravit nebo odebrat toto pravidlo.
* Pokud je skupina **distribuční skupiny**, pak musí také být pošty. Zobrazit [obraťte se na pravidla out-of-box](#contact-out-of-box-rules) pro toto pravidlo je vynuceno.

Následující objekty skupiny jsou **není** synchronizovány se službou Azure AD:

* `IsPresent([isCriticalSystemObject])`. Zajištění mnoho out-of-box objektů ve službě Active Directory, jako je například předdefinované skupiny administrators, nejsou synchronizovány.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Starší verze skupina, která nástroj DirSync používá.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Skupiny rolí.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Všechny objekty victim replikace nebude synchronizovat.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>Pravidla out-of-box sady cizích objektů zabezpečení
FSP jsou připojeny k "žádný" (\*) objektu v úložišti metaverse. Ve skutečnosti stane toto připojení pouze pro uživatele a skupiny zabezpečení. Tato konfigurace zajistí, že jsou ve skupinách mezi doménovými strukturami přeložit a správně reprezentovaná ve službě Azure AD.

### <a name="computer-out-of-box-rules"></a>Pravidla out-of-box počítače
Objekt počítače musí splňovat následující se dá provést synchronizace:

* `userCertificate ISNOTNULL`. Pouze počítače s Windows 10 vyplnit tento atribut. Se synchronizují všechny objekty počítače s hodnotou v tomto atributu.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Principy scénář pravidla out-of-box
V tomto příkladu používáme nasazení jedné doménové struktury účtu (A), jednu doménovou strukturu prostředků (R) a jednoho adresáře služby Azure AD.

![Obrázek s popis scénáře](./media/concept-azure-ad-connect-sync-default-configuration/scenario.png)

V této konfiguraci se předpokládá, že není povolený účet v doménové struktuře účtu a zakázaný účet v doménové struktuře prostředku s propojená poštovní schránka.

Naším cílem s výchozí konfigurací je:

* Atributy týkající se přihlašování se synchronizují z doménové struktury s povolený účet.
* Atributy, které lze nalézt v GAL (globální seznam adres) se synchronizují z doménové struktury s poštovní schránku. Pokud se najdou se žádné poštovní schránky, se používá jiné doménové struktury.
* Pokud je nalezen propojená poštovní schránka, propojený účet povolená musí najít pro objekt, který chcete exportovat do služby Azure AD.

### <a name="synchronization-rule-editor"></a>Synchronization Rule Editor
Konfigurace můžete zobrazit a změnit pomocí nástroje Editor pravidel synchronizace (SRE) a najdete zástupce v nabídce start.

![Ikona Editor pravidel synchronizace](./media/concept-azure-ad-connect-sync-default-configuration/sre.png)

SRE je nástroj resource kit a je nainstalován pomocí synchronizace Azure AD Connect. Aby bylo možné jej spustit, musíte být členem skupiny ADSyncAdmins. Při spuštění, zobrazí vypadat přibližně takto:

![Příchozí pravidla synchronizace](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

V tomto podokně se zobrazí všechna synchronizační pravidla, které jsou vytvořené pro vaši konfiguraci. Každý řádek v tabulce je jeden synchronizační pravidlo. Na levé straně v části typy pravidel, jsou uvedeny dva různé typy: příchozí a odchozí. Příchozí a odchozí je z pohledu úložiště metaverse. Hlavně budete moct soustředit na příchozí pravidla v tomto přehledu. Skutečný seznam pravidel synchronizace závisí na zjištěné schématu ve službě AD. Na obrázku výše doménové struktury účtu (fabrikamonline.com) nemá žádné služby, jako je například Exchange a Lync, a žádná pravidla synchronizace byly vytvořeny pro tyto služby. Nicméně v doménové struktuře prostředku (res.fabrikamonline.com) zjistíte synchronizační pravidla pro tyto služby. Obsah pravidla se liší v závislosti na verzi zjištěna. Například v nasazení s Exchange 2013 existují další toky atributů, které jsou nakonfigurované než v systému Exchange 2010 nebo 2007.

### <a name="synchronization-rule"></a>Synchronizační pravidlo
Synchronizační pravidlo je objekt konfigurace sadu atributů tok, když je splněna podmínka. Také se používá k popisu, jak je objekt v prostoru konektoru vztahujících se k objektu v úložišti metaverse, označované jako **spojení** nebo **odpovídat**. Synchronizační pravidla mají přednost před hodnotu, která udává, jak se vztahují k sobě navzájem. Synchronizační pravidlo s nižší číselnou hodnotu má vyšší prioritu a v došlo ke konfliktu toku atributů vyšší prioritu služby wins řešení konfliktů.

Jako příklad, podívejte se na synchronizační pravidlo **v ze služby AD – uživatel AccountEnabled**. Označit tento řádek SRE a vyberte **upravit**.

Toto pravidlo je pravidlo out-of-box, zobrazí se při otevření pravidlo upozornění. Neprovádějte žádné [změny pravidel out-of-box](how-to-connect-sync-best-practices-changing-default-configuration.md), takže budete vyzváni, jaké jsou vaše záměry. V takovém případě pouze chcete zobrazit pravidla. Vyberte **ne**.

![Synchronizační pravidla upozornění](./media/concept-azure-ad-connect-sync-default-configuration/warningeditrule.png)

Synchronizační pravidlo obsahuje čtyři oddíly konfigurace: popis, rozsah filtru, spojení pravidla a transformace.

#### <a name="description"></a>Popis
První část obsahuje základní informace, jako je název a popis.

![Popis kartu editor pravidel synchronizace ](./media/concept-azure-ad-connect-sync-default-configuration/syncruledescription.png)

Můžete také najít informace, o které připojený systém se toto pravidlo vztahuje, kterých objekt v připojený systém, které se vztahuje na typ a typ objekt úložiště metaverse. Typ objektu úložiště metaverse je vždy osobě bez ohledu na to, když je typ zdrojového objektu uživatele, iNetOrgPerson nebo kontaktujte. Typ objektu úložiště metaverse by nikdy měnit, takže je vytvořena jako obecného typu. Typ odkazu můžete nastavit na spojení, StickyJoin nebo zřídit. Toto nastavení funguje společně s části pravidla pro připojení a se věnujeme později.

Uvidíte také, že toto synchronizační pravidlo se používá pro synchronizaci hesel. Pokud je uživatel v oboru pro toto synchronizační pravidlo, heslo synchronizovaly z místní do cloudu (za předpokladu, že jste povolili funkce Synchronizace hesla).

#### <a name="scoping-filter"></a>Filtr oborů
Část Filtr vytváření oboru slouží ke konfiguraci uplatnění synchronizační pravidlo. Vzhledem k tomu, že název synchronizačního pravidla máte před sebou označuje měly použít pouze pro povolené uživatele, obor je nakonfigurovaná tak, že atribut AD **userAccountControl** nesmí nastaven bit 2. Při synchronizační modul Vyhledá uživatele ve službě AD, se vztahuje toto synchronizační pravidlo, když **userAccountControl** je nastavena na desítkovou hodnotu 512 (povoleného uživatele normální). Pokud má uživatel nevztahuje pravidlo **userAccountControl** nastavena na 514 (zakázaný uživatel normální).

![Zkoumání karta v editoru pravidel synchronizace ](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilter.png)

Filtr oborů má skupiny a klauzule, které mohou být vnořené. Všechny klauzule uvnitř skupiny musí být splněny pro synchronizační pravidlo použít. Když jsou definovány více skupin, alespoň jednu skupinu musí být splněny pro pravidlo použít. To znamená logický operátor OR vyhodnocen mezi skupinami a logické a vyhodnotit uvnitř skupiny. Příklad této konfigurace najdete v pravidla odchozí synchronizace **na AAD – připojte se k skupiny**. Existuje několik skupin synchronizace filtr, třeba jednoho pro skupiny zabezpečení (`securityEnabled EQUAL True`) a jeden pro distribuční skupiny (`securityEnabled EQUAL False`).

![Zkoumání karta v editoru pravidel synchronizace ](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilterout.png)

Toto pravidlo se používá k definování skupiny, které by mělo proběhnout zřízení do služby Azure AD. Distribuční skupiny musí být povolena synchronizace s Azure AD mail, ale pro skupiny zabezpečení e-mailu se nevyžaduje.

#### <a name="join-rules"></a>Připojte se k pravidla
Třetí části slouží ke konfiguraci, jak objekty v prostoru konektoru vztahují na objekty v úložišti metaverse. Pravidlo se podívat na dříve nemá žádnou konfiguraci pro připojení pravidla, takže místo toho chcete se podívat na **v ze služby AD – uživatel připojit**.

![Karta pravidla zúčastnit editor pravidel synchronizace ](./media/concept-azure-ad-connect-sync-default-configuration/syncrulejoinrules.png)

Obsah spojení pravidlo závisí na odpovídající možnosti vybrané v Průvodci instalací. Pro příchozí pravidlo hodnocení začíná objekt v prostoru konektoru zdroje a každou skupinu v pravidlech spojení se vyhodnocují v pořadí. Pokud zdrojový objekt je vyhodnoceno tak, aby odpovídaly právě jednoho objektu v úložišti metaverse pomocí některé z pravidel spojení, objekty jsou spojeny. Pokud není nalezena žádná shoda se vyhodnotily všechna pravidla, použije se typ odkazu na stránce popis. Pokud tuto konfiguraci je nastavená na **zřízení**, pak je vytvořen nový objekt v cílové úložiště metaverse. Nový objekt úložiště metaverse se také označuje jako k poskytování **projektu** objektu úložiště metaverse.

Pravidla spojení se jenom vyhodnotí jednou. Když se připojí objekt prostoru konektoru a objektu úložiště metaverse, zůstanou připojené, je stále splněny rozsahu synchronizačního pravidla.

Při vyhodnocování pravidel synchronizace, musí být pouze jeden synchronizační pravidlo s spojení pravidel definovaných v oboru. Pokud se najde více synchronizační pravidla, jejichž spojení pravidla pro jeden objekt, je vržena chyba. Z tohoto důvodu osvědčeným postupem je mít pouze jeden synchronizační pravidlo s spojení definován při více pravidel synchronizace jsou v oboru pro objekt. V konfiguraci out-of-box synchronizace Azure AD Connect, můžete tato pravidla se dá vyhledat dotazem podle názvu a najít ty, které mají slovo **připojení** na konci názvu. Synchronizační pravidlo bez definovaná nějaká pravidla spojení platí toky atributů při dalším synchronizačním pravidle propojeny objekty nebo zřídit nový objekt v cíli.

Když se podíváte na obrázku výše vidíte, že je pravidlo pokusu o připojení k **objectSID** s **msExchMasterAccountSid** (Exchange) a **msRTCSIP-OriginatorSid** (Lync) což je, co Očekáváme, že v doménovou strukturu účtu prostředku. Stejné pravidlo na všechny doménové struktury pro vás. Předpokladem je, že každá doménová struktura může být doménové struktury účtu nebo prostředků. Tato konfigurace funguje také pokud máte účty, které za provozu v jedné doménové struktury a nemusí být připojen.

#### <a name="transformations"></a>Transformace
Transformace oddíl definuje všechny toky atributů, které se vztahují k cílovému objektu, když jsou připojené objekty a filtr rozsahu je splněna. Když se vrátíme ke **v ze služby AD – uživatel AccountEnabled** synchronizačního pravidla, vyhledejte následující transformace:

![Transformace kartu editor pravidel synchronizace ](./media/concept-azure-ad-connect-sync-default-configuration/syncruletransformations.png)

Vložit tuto konfiguraci v kontextu, v nasazení doménové struktury prostředků účtu, očekává se najít povolený účet v doménové struktuře účtu a zakázaný účet v doménové struktuře prostředku s nastavením Exchange a Lync. Synchronizační pravidlo, které sledujete obsahuje atributy vyžadované pro přihlášení a tyto atributy jakým způsobem se předávají z doménové struktury ve kterých je povolený účet. Tyto toky atributů jsou umístěny společně v jedné synchronizační pravidlo.

Transformace mohou mít různé typy: Konstanta, přímo a výraz.

* Konstantní tok vždy toky pevně zakódované hodnotu. V případě výše, hodnota vždy nastaví **True** v atribut úložiště metaverze s názvem **accountEnabled**.
* Tok s přímým přístupem vždy toky hodnotu atributu ve zdroji do cílového atributu jako-je.
* To umožňuje pokročilejší konfigurace třetí typ toku je výraz.

Výraz jazyka je VBA (Visual Basic for Applications), takže uživatelé, kteří s prostředím systému Microsoft Office nebo VBScript rozpozná formát. Atributy jsou uzavřeny v hranatých závorkách [attributeName]. Názvy atributů a názvy funkcí jsou malá a velká písmena, ale Editor pravidel synchronizace vyhodnotí výrazy a zobrazit upozornění, pokud výraz není platný. Všechny výrazy jsou vyjádřeny na jednom řádku pomocí vnořených funkcí. Zobrazit sílu jazyka konfigurace, tady je tok pro pwdLastSet, ale s dalšími komentáři vložit:

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

Zobrazit [Principy výrazů deklarativního zřizování](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) Další informace o jazyk výrazů pro toky atributů.

### <a name="precedence"></a>Priorita
Se teď podívat na některé jednotlivé synchronizační pravidla, ale pravidla spolupracují v konfiguraci. V některých případech hodnotu atributu přispět z více pravidel synchronizace na stejný atribut target. V takovém případě přednost atributu slouží k určení, který atribut wins. Jako příklad podívejte se na atribut sourceAnchor. Tento atribut je důležité atribut bude moct přihlásit ke službě Azure AD. Tok atributů, který můžete najít pro tento atribut v dvě různá pravidla synchronizace **v ze služby AD – uživatel AccountEnabled** a **v ze služby AD – běžné uživatele**. Z důvodu prioritu synchronizačního pravidla atribut sourceAnchor je přispět z doménové struktury s povolený účet nejprve po několik objektů spojeno do objektu úložiště metaverse. Pokud nejsou žádné povolené účty, pak synchronizační modul používá synchronizační pravidlo pokrývající vše **v ze služby AD – běžné uživatele**. Tato konfigurace zajistí, že i pro účty, které jsou zakázané, stále existuje sourceAnchor.

![Příchozí pravidla synchronizace](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

Priorita pro synchronizační pravidla nastavena ve skupinách pomocí Průvodce instalací. Všechna pravidla ve skupině mají stejný název, ale jsou připojené k různým adresářům připojené. Průvodce instalací poskytuje pravidlo **v ze služby AD – uživatel připojit** nejvyšší prioritu a Iteruje přes všechny připojené adresářů AD. Pak bude pokračovat s další skupiny pravidla v předdefinované pořadí. Ve skupině přidají pravidla v pořadí, ve kterém konektory byly přidány v průvodci. Pokud přidáte jiný konektor pomocí průvodce, přeuspořádají synchronizační pravidla a pravidla nový konektor jsou vloženy poslední v každé skupině.

### <a name="putting-it-all-together"></a>Vložení všechno dohromady
Teď už máte dostatek informací o pravidel tak, aby měli pochopit, jak služba konfigurace pracuje s různá pravidla synchronizace. Když se podíváte na uživatele a atributy, které se poskytly úložišti metaverse, pravidla se použijí v uvedeném pořadí:

| Název | Poznámka |
|:--- |:--- |
| V ze služby AD – připojení uživatele |Pravidla pro připojení k objekty prostor konektoru s úložiště metaverse. |
| V ze služby AD – povolené uživatelské účty |Atributy vyžadované pro přihlášení ke službě Azure AD a Office 365. Chceme, aby tyto atributy z povolený účet. |
| V ze služby AD – běžné uživatele ze serveru Exchange |Nebyly nalezeny atributy v globálním adresáři. Předpokládáme, že kvalita dat je nejlepší v doménové struktuře, ve kterém jsme našli poštovní schránky uživatele. |
| V ze služby AD – běžné uživatele |Nebyly nalezeny atributy v globálním adresáři. V případě, že jsme nenašli poštovní schránku, jakýkoli jiný objekt připojené k doméně můžete přispívat hodnotu atributu. |
| V ze služby AD – uživatelů Exchange |Existuje pouze pokud byl zjištěn Exchange. Vede všechny atributy Exchange infrastruktury. |
| V ze služby AD – uživatel Lync |Existuje pouze pokud byl zjištěn Lync. Vede všechny atributy Lync infrastruktury. |

## <a name="next-steps"></a>Další postup
* Další informace o konfiguraci modelu v [Principy deklarativní zřizování](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Další informace o jazyk výrazů v [Principy výrazů deklarativního zřizování](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Pokračujte ve čtení, jak funguje konfigurace out-of-box [Principy uživatelů a kontaktů](concept-azure-ad-connect-sync-user-and-contacts.md)
* Informace o tom, aby praktické změnu pomocí deklarativní zřizování v [jak provést změnu výchozí konfigurace](how-to-connect-sync-change-the-configuration.md).

**Témata s přehledem**

* [Synchronizace Azure AD Connect: Principy a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)

