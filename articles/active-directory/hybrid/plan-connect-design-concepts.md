---
title: 'Azure AD Connect: Koncepty návrhu | Dokumenty společnosti Microsoft'
description: Toto téma podrobně popisuje určité oblasti návrhu implementace
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4114a6c0-f96a-493c-be74-1153666ce6c9
ms.service: active-directory
ms.custom: azure-ad-connect
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb41e14a7ecf41a2698a063c3067a98d8acf8f07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253881"
---
# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: Koncepty návrhu
Účelem tohoto dokumentu je popsat oblasti, které musí být promyšlené během návrhu implementace Azure AD Connect. Tento dokument je hloubkově popsán v určitých oblastech a tyto pojmy jsou stručně popsány i v jiných dokumentech.

## <a name="sourceanchor"></a>sourceAnchor
Atribut sourceAnchor je definován jako *atribut neměnný během životnosti objektu*. Jednoznačně identifikuje objekt jako stejný objekt v místním prostředí a ve službě Azure AD. Atribut se také nazývá **immutableId** a dva názvy se používají zaměnitelné.

Slovo neměnné, to je "nelze změnit", je důležité pro tento dokument. Vzhledem k tomu, že hodnotu tohoto atributu nelze změnit po jeho nastavení, je důležité vybrat návrh, který podporuje váš scénář.

Atribut se používá pro následující scénáře:

* Když je nový server synchronizačního motoru sestavený nebo znovu sestavený po scénáři zotavení po havárii, tento atribut propojí existující objekty ve službě Azure AD s objekty v místním prostředí.
* Pokud přesunete z identity pouze pro cloud na synchronizované modelu identity, pak tento atribut umožňuje objekty "pevné shody" existující objekty ve službě Azure AD s místními objekty.
* Pokud používáte federaci, pak tento atribut spolu s **userPrincipalName** se používá v deklaraci jednoznačně identifikovat uživatele.

Toto téma hovoří pouze o sourceAnchor, protože se týká uživatelů. Stejná pravidla platí pro všechny typy objektů, ale je pouze pro uživatele tento problém obvykle je problém.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Výběr dobrého atributu sourceAnchor
Hodnota atributu musí dodržovat následující pravidla:

* Méně než 60 znaků na délku
  * Znaky, které nejsou a-z, A-Z nebo 0-9, jsou kódovány a počítány jako 3 znaky
* Neobsahuje speciální znak: &#92; ! # $ % & * + / = ? ↑ &#96; { } | ~ < > ( ) " ; : , [ ] " \@ _
* Musí být globálně jedinečný
* Musí být řetězec, celé číslo nebo binární.
* By neměla být založena na uživatelskéjméno, protože tyto mohou změnit
* Nemělo by se ujednotlivých písmen a vyvarovat se hodnot, které se mohou v jednotlivých případech lišit
* Má být přiřazenpři vytvoření objektu.

Pokud vybraný sourceAnchor není typu řetězce, pak Azure AD Connect Base64Encode hodnotu atributu zajistit žádné speciální znaky se zobrazí. Pokud používáte jiný federační server než ADFS, ujistěte se, že váš server může také Base64Encode atribut.

Atribut sourceAnchor rozlišuje malá a velká písmena. Hodnota "JohnDoe" není totéž jako "johndoe". Ale neměli byste mít dva různé objekty pouze s rozdílem v případě.

Pokud máte jednu doménovou strukturu v místním prostředí, pak atribut, který byste měli použít, je **objectGUID**. Toto je také atribut používaný při použití expresní nastavení v Azure AD Connect a také atribut používaný DirSync.

Pokud máte více doménových struktur a nepřesouváte uživatele mezi doménovými strukturami a doménami, pak **objectGUID** je dobrým atributem, který se má použít i v tomto případě.

Pokud přesouváte uživatele mezi doménovými strukturami a doménami, musíte najít atribut, který se nemění nebo může být přesunut s uživateli během přesunu. Doporučeným přístupem je zavedení syntetického atributu. Atribut, který by mohl obsahovat něco, co vypadá jako identifikátor GUID by bylo vhodné. Během vytváření objektu je vytvořen nový identifikátor GUID a vyražen uživateli. Na serveru synchronizačního modulu lze vytvořit vlastní pravidlo synchronizace, které vytvoří tuto hodnotu na základě **objektu GUID** a aktualizuje vybraný atribut v části PŘIDÁ. Při přesunutí objektu nezapomeňte také zkopírovat obsah této hodnoty.

Dalším řešením je vybrat existující atribut, o kterém víte, že se nezmění. Běžně používané atributy zahrnují **ID zaměstnance**. Pokud vezmete v úvahu atribut, který obsahuje písmena, ujistěte se, že neexistuje žádná šance, že velká písmena (velká písmena vs. malá písmena) se mohou změnit pro hodnotu atributu. Špatné atributy, které by neměly být použity patří tyto atributy s názvem uživatele. V manželství nebo rozvodu se očekává změna názvu, což není povoleno pro tento atribut. To je také jeden z důvodů, proč atributy jako **userPrincipalName**, **mail**a **targetAddress** nejsou ani možné vybrat v průvodci instalací Azure AD Connect. Tyto atributy také\@obsahují znak " " , který není povolen ve sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Změna atributu sourceAnchor
Hodnotu atributu sourceAnchor nelze změnit po vytvoření objektu ve službě Azure AD a synchronizaci identity.

Z tohoto důvodu platí následující omezení pro Azure AD Connect:

* SourceAnchor atribut lze nastavit pouze během počáteční instalace. Pokud znovu spustíte průvodce instalací, bude tato možnost jen pro čtení. Pokud potřebujete toto nastavení změnit, musíte je odinstalovat a znovu nainstalovat.
* Pokud nainstalujete jiný server Azure AD Connect, musíte vybrat stejný atribut sourceAnchor, jak se dříve používalo. Pokud jste dříve používali DirSync a přesunout do Azure AD Connect, pak je nutné použít **objectGUID,** protože to je atribut používaný DirSync.
* Pokud hodnota sourceAnchor se změní po objekt byl exportován do Azure AD, pak Synchronizace Azure AD Connect vyvolá chybu a neumožňuje žádné další změny na tento objekt před problém byl opraven a sourceAnchor se změní zpět ve zdroji Adresář.

## <a name="using-ms-ds-consistencyguid-as-sourceanchor"></a>Použití ms-DS-ConsistencyGuid jako sourceAnchor
Ve výchozím nastavení Azure AD Connect (verze 1.1.486.0 a starší) používá objekt GUID jako atribut sourceAnchor. ObjectGUID je generován systémem. Při vytváření místních objektů služby AD nelze určit jeho hodnotu. Jak je vysvětleno v části [sourceAnchor](#sourceanchor), existují scénáře, kde je třeba zadat sourceAnchor hodnotu. Pokud scénáře jsou použitelné pro vás, musíte použít konfigurovatelný atribut Služby AD (například ms-DS-ConsistencyGuid) jako sourceAnchor atribut.

Azure AD Connect (verze 1.1.524.0 a po) nyní usnadňuje použití atributu ms-DS-ConsistencyGuid jako sourceAnchor. Při použití této funkce Azure AD Connect automaticky nakonfiguruje pravidla synchronizace takto:

1. Použijte ms-DS-ConsistencyGuid jako sourceAnchor atribut pro objekty User. ObjectGUID se používá pro jiné typy objektů.

2. Pro všechny dané místní objekt uživatele služby AD, jehož atribut ms-DS-ConsistencyGuid není naplněn, Azure AD Connect zapíše jeho hodnotu objectGUID zpět do atributu ms-DS-ConsistencyGuid v místním adresáři Active Directory. Po naplnění atributu ms-DS-ConsistencyGuid pak Azure AD Connect exportuje objekt do služby Azure AD.

>[!NOTE]
> Jakmile je místní objekt AD importován do služby Azure AD Connect (to znamená, že se importuje do prostoru konektoru a promítne se do Metaverse), už nelze změnit jeho hodnotu sourceAnchor. Chcete-li určit hodnotu sourceAnchor pro daný místní objekt Služby AD, nakonfigurujte jeho atribut ms-DS-ConsistencyGuid před importem do služby Azure AD Connect.

### <a name="permission-required"></a>Je vyžadováno oprávnění.
Aby tato funkce fungovala, musí být účtu služby AD DS používanému k synchronizaci s místní službou Active Directory uděleno oprávnění k zápisu atributu ms-DS-ConsistencyGuid v místní službě Active Directory.

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>Jak povolit funkci ConsistencyGuid – Nová instalace
Můžete povolit použití ConsistencyGuid jako sourceAnchor během nové instalace. Tato část podrobně popisuje expresní i vlastní instalaci.

  >[!NOTE]
  > Pouze novější verze Azure AD Connect (1.1.524.0 a po) podporují použití ConsistencyGuid jako sourceAnchor během nové instalace.

### <a name="how-to-enable-the-consistencyguid-feature"></a>Povolení funkce ConsistencyGuid

#### <a name="express-installation"></a>Expresní instalace
Při instalaci režimu Azure AD Connect with Express průvodce Azure AD Connect automaticky určí nejvhodnější atribut Služby AD, který se má použít jako atribut sourceAnchor pomocí následující logiky:

* Za prvé, průvodce Azure AD Connect dotazuje vašeho klienta Azure AD načíst atribut AD, který se používá jako atribut sourceAnchor v předchozí instalaci Azure AD Connect (pokud existuje). Pokud jsou tyto informace k dispozici, Azure AD Connect používá stejný atribut Služby AD.

  >[!NOTE]
  > Pouze novější verze Azure AD Connect (1.1.524.0 a po) ukládají informace ve vašem tenantovi Azure AD o atributu sourceAnchor použitém během instalace. Starší verze Azure AD Connect ne.

* Pokud nejsou k dispozici informace o použitém atributu sourceAnchor, průvodce zkontroluje stav atributu ms-DS-ConsistencyGuid v místní službě Active Directory. Pokud atribut není nakonfigurován na žádný objekt v adresáři, průvodce používá ms-DS-ConsistencyGuid jako sourceAnchor atribut. Pokud je atribut nakonfigurován na jeden nebo více objektů v adresáři, průvodce dospěje k závěru, že atribut je používán jinými aplikacemi a není vhodný jako atribut sourceAnchor...

* V takovém případě průvodce přejde zpět k použití objectGUID jako sourceAnchor atribut.

* Po rozhodnutí atributu sourceAnchor průvodce uloží informace do tenanta Azure AD. Informace budou použity při budoucí instalaci Služby Azure AD Connect.

Po dokončení expresní instalace vás průvodce informuje, který atribut byl vybrán jako atribut Zdrojová kotva.

![Průvodce informuje atribut AD vyskladněný pro sourceAnchor](./media/plan-connect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>Vlastní instalace
Při instalaci Azure AD Connect s vlastním režimem průvodce Azure AD Connect poskytuje dvě možnosti při konfiguraci atributu sourceAnchor:

![Vlastní instalace - konfigurace sourceAnchor](./media/plan-connect-design-concepts/consistencyGuid-02.png)

| Nastavení | Popis |
| --- | --- |
| Nechat správu zdrojového ukotvení na Azure | Tuto možnost vyberte, pokud chcete, aby Azure AD vybral atribut za vás. Pokud vyberete tuto možnost, Průvodce Azure AD Connect použije stejnou [logiku výběru atributu sourceAnchor použitou během expresní instalace](#express-installation). Podobně jako expresní instalace vás průvodce informuje o tom, který atribut byl po dokončení vlastní instalace vybrán jako atribut Zdrojová kotva. |
| Konkrétní atribut | Tuto možnost vyberte, pokud chcete jako atribut sourceAnchor zadat existující atribut AD. |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>Jak povolit funkci ConsistencyGuid – existující nasazení
Pokud máte existující nasazení Azure AD Connect, které používá objectGUID jako atribut Source Anchor, můžete přepnout na použití ConsistencyGuid místo.

>[!NOTE]
> Pouze novější verze Azure AD Connect (1.1.552.0 a po) podporují přepínání z ObjectGuid na ConsistencyGuid jako atribut Source Anchor.

Chcete-li přepnout z objectGUID na ConsistencyGuid jako atribut Source Anchor:

1. Spusťte průvodce připojením Azure AD a kliknutím na **Konfigurovat** přejděte na obrazovku Úkoly.

2. Vyberte možnost **Konfigurovat úlohu zdrojové ukotvení** a klepněte na tlačítko **Další**.

   ![Povolit konzistenciGuid pro stávající nasazení – krok 2](./media/plan-connect-design-concepts/consistencyguidexistingdeployment01.png)

3. Zadejte přihlašovací údaje správce služby Azure AD a klepněte na tlačítko **Další**.

4. Průvodce připojením Azure AD analyzuje stav atributu ms-DS-ConsistencyGuid v místním adresáři Active Directory. Pokud atribut není nakonfigurován na libovolný objekt v adresáři, Azure AD Connect dojde k závěru, že žádná jiná aplikace je aktuálně používá atribut a je bezpečné jej použít jako atribut Source Anchor. Pokračujte výběrem tlačítka **Další**.

   ![Povolit konzistenciGuid pro stávající nasazení – krok 4](./media/plan-connect-design-concepts/consistencyguidexistingdeployment02.png)

5. Na obrazovce **Připraveno ke konfiguraci** klikněte na **Konfigurovat** a proveďte změnu konfigurace.

   ![Povolit konzistenciGuid pro stávající nasazení – krok 5](./media/plan-connect-design-concepts/consistencyguidexistingdeployment03.png)

6. Po dokončení konfigurace průvodce označuje, že ms-DS-ConsistencyGuid je nyní používán jako atribut Source Anchor.

   ![Povolit konzistenciGuid pro stávající nasazení – krok 6](./media/plan-connect-design-concepts/consistencyguidexistingdeployment04.png)

Pokud je atribut během analýzy (krok 4) nakonfigurován na jeden nebo více objektů v adresáři, průvodce dospěje k závěru, že atribut používá jiná aplikace, a vrátí chybu, jak je znázorněno v diagramu níže. K této chybě může dojít také v případě, že jste dříve povolili funkci ConsistencyGuid na primárním serveru Azure AD Connect a pokoušíte se o totéž na pracovním serveru.

![Povolit consistencyaguid pro existující nasazení – chyba](./media/plan-connect-design-concepts/consistencyguidexistingdeploymenterror.png)

 Pokud jste si jisti, že atribut není používán jinými existujícími aplikacemi, můžete potlačit chybu restartováním průvodce Azure AD Connect s **/SkipLdapSearch** přepínač zadaným. Chcete-li tak učinit, spusťte v příkazovém řádku následující příkaz:

```
"c:\Program Files\Microsoft Azure Active Directory Connect\AzureADConnect.exe" /SkipLdapSearch
```

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>Dopad na konfiguraci služby AD FS nebo federace třetích stran
Pokud používáte Azure AD Connect ke správě místního nasazení služby AD FS, Azure AD Connect automaticky aktualizuje pravidla deklarace deklarace, aby používala stejný atribut Služby AD jako sourceAnchor. Tím je zajištěno, že deklarace ImmutableID generované ADFS je konzistentní s sourceAnchor hodnoty exportované do Azure AD.

Pokud spravujete službu AD FS mimo Azure AD Connect nebo používáte federační servery třetích stran pro ověřování, musíte ručně aktualizovat pravidla deklarace pro deklaraci ImmutableID tak, aby byla konzistentní se zdrojovými hodnotami Anchor exportovanými do Azure AD, jak je popsáno v článku [Upravit pravidla deklarace služby AD FS](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#modclaims). Průvodce vrátí po dokončení instalace následující upozornění:

![Konfigurace federace třetí strany](./media/plan-connect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>Přidání nových adresářů do stávajícího nasazení
Předpokládejme, že jste nasadili Azure AD Connect s povolenou funkcí ConsistencyGuid a teď chcete do nasazení přidat další adresář. Při pokusu o přidání adresáře průvodce Azure AD Connect zkontroluje stav atributu ms-DS-ConsistencyGuid v adresáři. Pokud je atribut nakonfigurován na jeden nebo více objektů v adresáři, průvodce dospěje k závěru, že atribut je používán jinými aplikacemi a vrátí chybu, jak je znázorněno v diagramu níže. Pokud jste si jisti, že atribut není používán existujícími aplikacemi, můžete potlačit chybu restartováním průvodce Azure AD Connect s přepínačem **/SkipLdapSearch** určeným výše popsaným nebo potřebujete kontaktovat podporu pro další informace.

![Přidání nových adresářů do stávajícího nasazení](./media/plan-connect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Přihlášení azure ad
Při integraci místního adresáře s Azure AD je důležité pochopit, jak nastavení synchronizace může ovlivnit způsob, jakým se uživatel ověřuje. Azure AD používá userPrincipalName (UPN) k ověření uživatele. Však při synchronizaci uživatelů, musíte zvolit atribut, který má být použit pro hodnotu userPrincipalName pečlivě.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Výběr atributu pro userPrincipalName
Při výběru atributpro poskytování hodnoty UPN, které mají být použity v Azure jeden by měl zajistit

* Hodnoty atributů odpovídají syntaxi hlavního názvu uživatele (RFC 822),\@to znamená, že by měly být ve formátu domény uživatelského jména
* Přípona v hodnotách odpovídá jedné z ověřených vlastních domén ve službě Azure AD

V expresní nastavení předpokládá volba pro atribut userPrincipalName. Pokud atribut userPrincipalName neobsahuje hodnotu, kterou mají uživatelé přihlásit do Azure, musíte zvolit **vlastní instalaci**.

### <a name="custom-domain-state-and-upn"></a>Stav vlastní domény a hlavní název domény
Je důležité zajistit, aby existovala ověřená doména přípony hlavního názvu uživatele.

John je uživatel v contoso.com. Chcete, aby Jan používal místní\@contoso.com upn k přihlášení k Azure po synchronizaci uživatelů s adresářem Azure AD contoso.onmicrosoft.com. Chcete-li tak učinit, musíte přidat a ověřit contoso.com jako vlastní domény ve službě Azure AD, než začnete synchronizovat uživatele. Pokud přípona hlavního názvu uživatele John, například contoso.com, neodpovídá ověřené doméně ve službě Azure AD, pak Azure AD nahradí příponu HLAVNÍHO názvu uživatele s contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Nesměrovatelné místní domény a hlavní název domény pro Azure AD
Některé organizace mají nesměrovatelné domény, například contoso.local, nebo jednoduché domény s jedním popiskem, jako je contoso. Nejste schopni ověřit nesměrovatelné domény ve službě Azure AD. Azure AD Connect můžete synchronizovat jenom ověřené domény ve službě Azure AD. Když vytvoříte adresář Azure AD, vytvoří směrovatelnou doménu, která se stane výchozí doménou pro vaše Azure AD, například contoso.onmicrosoft.com. Proto je nutné ověřit všechny ostatní směrovatelné domény v takovém scénáři v případě, že nechcete synchronizovat s výchozí onmicrosoft.com domény.

Další informace o přidávání a ověřování domén najdete v části [Přidání vlastního názvu domény do služby Azure Active Directory.](../active-directory-domains-add-azure-portal.md)

Azure AD Connect zjistí, pokud běžíte v prostředí domény nesměrovatelné a vhodně vás varovat před pokračovat s expresní nastavení. Pokud pracujete v nesměrovatelné doméně, pak je pravděpodobné, že hlavní název uživatele má také nesměrovatelné přípony. Například pokud běžíte pod contoso.local, Azure AD Connect doporučuje použít vlastní nastavení, nikoli použít expresní nastavení. Pomocí vlastního nastavení, můžete zadat atribut, který by měl být použit jako UPN pro přihlášení k Azure po synchronizaci uživatelů do Azure AD.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
