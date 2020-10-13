---
title: 'Azure AD Connect: koncepty návrhu | Microsoft Docs'
description: Toto téma podrobně popisuje určité oblasti návrhu implementace.
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
ms.openlocfilehash: baa03499cc11bda24ead986dd64621572484cbb1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89279648"
---
# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: koncepty návrhu
Účelem tohoto dokumentu je popsat oblasti, které je potřeba promyslet během návrhu implementace Azure AD Connect. Tento dokument je hluboko podrobně v určitých oblastech a tyto pojmy se stručně popisují i v dalších dokumentech.

## <a name="sourceanchor"></a>sourceAnchor
Atribut sourceAnchor je definován jako *neproměnlivý atribut během životnosti objektu*. Jednoznačně identifikuje objekt jako stejný objekt v místním prostředí i ve službě Azure AD. Atribut se také označuje jako **immutableId** a dva názvy jsou používány jako zaměnitelné.

Slovo není proměnlivé, to znamená "nemůže být změněno", je důležité pro tento dokument. Vzhledem k tomu, že hodnota tohoto atributu po nastavení nemůže být změněna, je důležité vybrat návrh, který podporuje váš scénář.

Atribut se používá pro následující scénáře:

* Když je nový server synchronizačního modulu sestavený nebo znovu sestavený po scénáři zotavení po havárii, tento atribut propojuje stávající objekty v Azure AD s objekty v místním prostředí.
* Pokud přejdete z identity jenom cloudu na synchronizovaný model identity, pak tento atribut umožňuje objektům "těžká shoda" stávajících objektů ve službě Azure AD s místními objekty.
* Pokud používáte federaci, pak se tento atribut společně s atributem **userPrincipalName** používá v deklaraci identity k jednoznačné identifikaci uživatele.

Toto téma mluví jenom o sourceAnchor, protože se týká uživatelů. Stejná pravidla platí pro všechny typy objektů, ale jsou pouze pro uživatele, u kterých se tento problém týká obvykle.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Výběr dobrého atributu sourceAnchor
Hodnota atributu musí odpovídat následujícím pravidlům:

* Délka je kratší než 60 znaků.
  * Znaky, které nejsou a-z, A-Z nebo 0-9, jsou zakódovány a počítány jako 3 znaky.
* Neobsahuje speciální znak: &#92;! # $% & * +/=? ^ &#96; {} | ~ < >  () '; : , [ ] " \@ _
* Musí být globálně jedinečný.
* Musí to být řetězec, celé číslo nebo binární hodnota.
* By neměl být založen na názvu uživatele, protože se může změnit
* Neměl by rozlišovat velká a malá písmena a nepoužívejte hodnoty, které se mohou lišit podle velikosti písmen
* By měl být přiřazen při vytvoření objektu

Pokud vybraný sourceAnchor není typu String, pak Azure AD Connect Base64Encode hodnoty atributu, aby nedošlo k žádnému speciálnímu znaku. Pokud používáte jiný federační server než ADFS, ujistěte se, že server může také Base64Encode atribut.

V atributu sourceAnchor se rozlišují velká a malá písmena. Hodnota "JohnDoe" není shodná s hodnotou "JohnDoe". Ale neměli byste mít dva různé objekty, které mají jenom rozdíl v případě případu.

Pokud máte místní doménovou strukturu, pak atribut, který byste měli použít, je **objectGUID**. To je také atribut používaný při použití expresního nastavení v Azure AD Connect a také atribut používaný DirSync.

Pokud máte více doménových struktur a nepřesouváte uživatele mezi doménovými strukturami a doménami, pak je objekt **objectGUID** dobrým atributem, který se použije i v tomto případě.

Pokud přesouváte uživatele mezi doménovými strukturami a doménami, je nutné najít atribut, který se nemění nebo může být během přesunu přesunut s uživateli. Doporučeným přístupem je zavedení syntetického atributu. Atribut, který může obsahovat něco, co vypadá jako identifikátor GUID, by byl vhodný. Při vytváření objektu se vytvoří nový GUID a na uživatele se vyřídí razítko. Na serveru synchronizace se dá vytvořit vlastní pravidlo synchronizace pro vytvoření této hodnoty na základě identifikátoru **GUID** a aktualizace vybraného atributu v nástroji Přidat. Při přesunu objektu Nezapomeňte také zkopírovat obsah této hodnoty.

Dalším řešením je vybrat existující atribut, který víte, že se nemění. Běžně používané atributy zahrnují **ČísloZaměstnance**. Pokud se podíváte na atribut, který obsahuje písmena, ujistěte se, že se nejedná o případ (velká a malá písmena), která by se mohla změnit pro hodnotu atributu. Chybné atributy, které by neměly být použity, obsahují tyto atributy s názvem uživatele. V sňatku nebo rozvodu se očekává, že se změní název, který není pro tento atribut povolen. To je také jeden z důvodů, proč atributy, jako například **userPrincipalName**, **mail**a **targetAddress** , nejsou dokonce možné vybrat v Průvodci instalací Azure AD Connect. Tyto atributy také obsahují znak " \@ ", který není v sourceAnchor povolen.

### <a name="changing-the-sourceanchor-attribute"></a>Změna atributu sourceAnchor
Hodnotu atributu sourceAnchor nelze změnit poté, co byl objekt vytvořen v rámci služby Azure AD a identita je synchronizována.

Z tohoto důvodu platí následující omezení pro Azure AD Connect:

* Atribut sourceAnchor lze nastavit pouze při počáteční instalaci. Pokud znovu spustíte Průvodce instalací nástroje, tato možnost je jen pro čtení. Pokud potřebujete toto nastavení změnit, musíte odinstalovat a znovu nainstalovat.
* Pokud instalujete jiný server Azure AD Connect, je nutné vybrat stejný atribut sourceAnchor, který byl dříve použit. Pokud jste dříve používali DirSync a přesunuli se na Azure AD Connect, je nutné použít **objectGUID** , protože to je atribut používaný DirSync.
* Pokud se hodnota pro sourceAnchor změní poté, co byl objekt exportován do služby Azure AD, Azure AD Connect synchronizace vyvolá chybu a nepovolí žádné další změny tohoto objektu před tím, než byl problém vyřešen, a sourceAnchor se změní zpátky ve zdrojovém adresáři.

## <a name="using-ms-ds-consistencyguid-as-sourceanchor"></a>Použití MS-DS-ConsistencyGuid jako sourceAnchor
Ve výchozím nastavení používá Azure AD Connect (verze 1.1.486.0 a starší) identifikátor objectGUID jako atribut sourceAnchor. Identifikátor ObjectGUID je generovaný systémem. Při vytváření místních objektů služby AD nejde zadat jeho hodnotu. Jak je vysvětleno v části [sourceAnchor](#sourceanchor), existují scénáře, ve kterých je třeba zadat hodnotu sourceAnchor. Pokud se pro vás použijí tyto scénáře, musíte jako atribut sourceAnchor použít konfigurovatelný atribut AD (například MS-DS-ConsistencyGuid).

Azure AD Connect (verze 1.1.524.0 a After) nyní usnadňuje použití atributu ms-DS-ConsistencyGuid jako atributu sourceAnchor. Při použití této funkce Azure AD Connect automaticky konfiguruje pravidla synchronizace na:

1. Pro uživatelské objekty použijte ms-DS-ConsistencyGuid jako atribut sourceAnchor. Objekt ObjectGUID se používá pro jiné typy objektů.

2. Pro všechny místní objekty uživatele služby AD, jejichž atribut ms-DS-ConsistencyGuid není naplněný, Azure AD Connect zapíše hodnotu objectGUID zpátky do atributu ms-DS-ConsistencyGuid v místní službě Active Directory. Po naplnění atributu ms-DS-ConsistencyGuid Azure AD Connect pak tento objekt exportuje do služby Azure AD.

>[!NOTE]
> Jakmile se místní objekt AD importuje do Azure AD Connect (naimportuje do prostoru konektoru služby AD a prochází do úložiště metaverse), nemůžete už změnit jeho hodnotu sourceAnchor. Pokud chcete zadat hodnotu sourceAnchor pro daný místní objekt služby AD, nakonfigurujte jeho atribut ms-DS-ConsistencyGuid předtím, než se naimportuje do Azure AD Connect.

### <a name="permission-required"></a>Požadováno oprávnění
Aby tato funkce fungovala, musí být účet služba AD DS, který se používá k synchronizaci s místní službou Active Directory, udělena oprávnění k zápisu do atributu ms-DS-ConsistencyGuid v místní službě Active Directory.

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>Jak povolit funkci ConsistencyGuid – nová instalace
Během nové instalace můžete povolit použití ConsistencyGuid jako sourceAnchor. Tato část obsahuje podrobné informace o expresní i vlastní instalaci.

  >[!NOTE]
  > Při nové instalaci podporují ConsistencyGuid jako sourceAnchor jenom novější verze Azure AD Connect (1.1.524.0 a After).

### <a name="how-to-enable-the-consistencyguid-feature"></a>Jak povolit funkci ConsistencyGuid

#### <a name="express-installation"></a>Expresní instalace
Při instalaci Azure AD Connect se expresním režimem Průvodce Azure AD Connect automaticky určí nejvhodnější atribut AD, který se použije jako atribut sourceAnchor, a to pomocí následující logiky:

* Nejdřív Průvodce Azure AD Connect dotazuje tenanta Azure AD, aby načetl atribut AD použitý jako atribut sourceAnchor v předchozí instalaci Azure AD Connect (pokud existuje). Pokud je tato informace k dispozici, Azure AD Connect používá stejný atribut AD.

  >[!NOTE]
  > Jenom novější verze Azure AD Connect (1.1.524.0 a After) ukládají informace v tenantovi Azure AD o atributu sourceAnchor používaného během instalace. Starší verze Azure AD Connect ne.

* Pokud nejsou k dispozici informace o použitém atributu sourceAnchor, Průvodce zkontroluje stav atributu ms-DS-ConsistencyGuid v místní službě Active Directory. Pokud atribut není nakonfigurován pro žádný objekt v adresáři, průvodce použije jako atribut sourceAnchor ms-DS-ConsistencyGuid. Pokud je atribut nakonfigurován na jednom nebo více objektech v adresáři, průvodce uzavře atribut je používán jinými aplikacemi a není vhodný jako atribut sourceAnchor...

* V takovém případě se Průvodce vrátí k použití identifikátoru objectGUID jako atributu sourceAnchor.

* Po rozhodnutí atributu sourceAnchor uloží průvodce informace v tenantovi Azure AD. Informace budou používány v budoucí instalaci Azure AD Connect.

Po dokončení Expresní instalace vás Průvodce informuje o tom, který atribut byl vybrán jako atribut zdrojového ukotvení.

![Průvodce informuje atribut AD vyzvednutý pro sourceAnchor.](./media/plan-connect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>Vlastní instalace
Při instalaci Azure AD Connect s vlastním režimem poskytuje průvodce Azure AD Connect dvě možnosti při konfiguraci atributu sourceAnchor:

![Vlastní instalace – konfigurace sourceAnchor](./media/plan-connect-design-concepts/consistencyGuid-02.png)

| Nastavení | Popis |
| --- | --- |
| Nechat správu zdrojového ukotvení na Azure | Tuto možnost vyberte, pokud chcete, aby Azure AD vybral atribut za vás. Pokud vyberete tuto možnost, průvodce Azure AD Connect použije stejnou [logiku výběru atributu sourceAnchor, která se používá při expresní instalaci](#express-installation). Podobně jako Expresní instalace vám průvodce informuje o tom, který atribut byl po dokončení vlastní instalace vybrán jako atribut zdrojového ukotvení. |
| Konkrétní atribut | Tuto možnost vyberte, pokud chcete jako atribut sourceAnchor zadat existující atribut AD. |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>Jak povolit funkci ConsistencyGuid – existující nasazení
Pokud máte existující nasazení Azure AD Connect, které používá identifikátor objectGUID jako atribut zdrojového ukotvení, můžete ho místo toho přepnout na použití ConsistencyGuid.

>[!NOTE]
> Pouze novější verze Azure AD Connect (1.1.552.0 a After) podporují přepínání z identifikátoru ObjectGuid na ConsistencyGuid jako atribut zdrojového ukotvení.

Chcete-li přepnout z objectGUID na ConsistencyGuid jako atribut zdrojového ukotvení:

1. Spusťte Průvodce Azure AD Connect a kliknutím na **Konfigurovat** přejděte na obrazovku úlohy.

2. Vyberte možnost **Konfigurovat zdroj kotvy** úlohy a klikněte na **Další**.

   ![Povolit ConsistencyGuid pro existující nasazení – krok 2](./media/plan-connect-design-concepts/consistencyguidexistingdeployment01.png)

3. Zadejte svoje přihlašovací údaje správce Azure AD a klikněte na **Další**.

4. Průvodce Azure AD Connect analyzuje stav atributu ms-DS-ConsistencyGuid v místní službě Active Directory. Pokud atribut není nakonfigurován pro žádný objekt v adresáři, Azure AD Connect uzavírá, že žádná jiná aplikace aktuálně nepoužívá atribut a je bezpečné jej použít jako atribut zdrojového ukotvení. Pokračujte kliknutím na **Next** (Další).

   ![Povolit ConsistencyGuid pro existující nasazení – krok 4](./media/plan-connect-design-concepts/consistencyguidexistingdeployment02.png)

5. Na obrazovce **připraveno ke konfiguraci** klikněte na **Konfigurovat** a proveďte změnu konfigurace.

   ![Povolit ConsistencyGuid pro existující nasazení – krok 5](./media/plan-connect-design-concepts/consistencyguidexistingdeployment03.png)

6. Po dokončení konfigurace se v průvodci zobrazí zpráva, že služba MS-DS-ConsistencyGuid se teď používá jako atribut zdrojového ukotvení.

   ![Povolit ConsistencyGuid pro existující nasazení – krok 6](./media/plan-connect-design-concepts/consistencyguidexistingdeployment04.png)

Při analýze (krok 4) platí, že pokud je atribut nakonfigurován na jednom nebo více objektech v adresáři, průvodce uzavře atribut je používán jinou aplikací a vrátí chybu, jak je znázorněno v následujícím diagramu. K této chybě může dojít také v případě, že jste na primárním serveru Azure AD Connect dříve povolili funkci ConsistencyGuid a na svém pracovním serveru se pokoušíte provést stejnou akci.

![Povolit ConsistencyGuid pro existující nasazení – chyba](./media/plan-connect-design-concepts/consistencyguidexistingdeploymenterror.png)

 Pokud jste si jisti, že atribut nepoužívá jiné existující aplikace, můžete chybu potlačit tak, že restartujete Průvodce Azure AD Connect se zadaným přepínačem **/SkipLdapSearch** . Uděláte to tak, že na příkazovém řádku spustíte tento příkaz:

```
"c:\Program Files\Microsoft Azure Active Directory Connect\AzureADConnect.exe" /SkipLdapSearch
```

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>Dopad na AD FS nebo na konfiguraci federace třetích stran
Pokud používáte Azure AD Connect ke správě místního nasazení AD FS, Azure AD Connect automaticky aktualizuje pravidla deklarace identity tak, aby používala stejný atribut AD jako sourceAnchor. Tím se zajistí, že deklarace identity ImmutableID generované službou AD FS je konzistentní s hodnotami sourceAnchor exportovanými do Azure AD.

Pokud spravujete AD FS mimo Azure AD Connect nebo používáte pro ověřování federační servery třetích stran, musíte ručně aktualizovat pravidla deklarací identity pro ImmutableID deklarací identity, aby byla konzistentní s hodnotami sourceAnchor exportovanými do Azure AD, jak je popsáno v části [úprava AD FS pravidla deklarace identity](./how-to-connect-fed-management.md#modclaims). Průvodce po dokončení instalace vrátí následující upozornění:

![Konfigurace federace třetích stran](./media/plan-connect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>Přidávání nových adresářů do stávajícího nasazení
Předpokládejme, že jste nasadili Azure AD Connect s povolenou funkcí ConsistencyGuid a teď chcete do nasazení přidat další adresář. Při pokusu o přidání adresáře Azure AD Connect Průvodce kontroluje stav atributu ms-DS-ConsistencyGuid v adresáři. Pokud je atribut nakonfigurován na jednom nebo více objektech v adresáři, průvodce uzavře atribut je používán jinými aplikacemi a vrátí chybu, jak je znázorněno v následujícím diagramu. Pokud jste si jisti, že atribut nepoužívá existující aplikace, můžete chybu potlačit tak, že restartujete Průvodce Azure AD Connect s přepínačem **/SkipLdapSearch** zadaným výše, nebo pokud potřebujete další informace, obraťte se na podporu.

![Přidávání nových adresářů do stávajícího nasazení](./media/plan-connect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Přihlášení k Azure AD
Při integraci místního adresáře se službou Azure AD je důležité pochopit, jak může nastavení synchronizace ovlivnit způsob, jakým uživatel ověřuje. Azure AD používá k ověření uživatele userPrincipalName (UPN). Při synchronizaci uživatelů je však nutné zvolit atribut, který má být použit pro hodnotu userPrincipalName pečlivě.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Výběr atributu pro userPrincipalName
Když vybíráte atribut pro poskytování hodnoty hlavního názvu uživatele (UPN), která se má použít v Azure One, měla by to zajistit.

* Hodnoty atributů odpovídají syntaxi hlavního názvu uživatele (RFC 822), to znamená, že by měla být ve formátu Doména uživatelského jména. \@
* Přípona v hodnotách odpovídá jedné z ověřených vlastních domén ve službě Azure AD.

V expresním nastavení má předpokládaná volba pro atribut hodnotu userPrincipalName. Pokud atribut userPrincipalName neobsahuje hodnotu, kterou chcete uživatelům přihlašovat do Azure, musíte zvolit **vlastní instalaci**.

### <a name="custom-domain-state-and-upn"></a>Vlastní stav domény a hlavní název uživatele
Je důležité zajistit, aby byla k dispozici ověřená doména pro příponu hlavního názvu uživatele (UPN).

Jan je uživatel v contoso.com. Požadujete, aby Jan používal místní hlavní název uživatele (UPN) John \@ contoso.com pro přihlášení k Azure po synchronizovaných uživatelích s adresářem Azure AD contoso.onmicrosoft.com. Aby to bylo možné, musíte před zahájením synchronizace uživatelů přidat a ověřit contoso.com jako vlastní doménu ve službě Azure AD. Pokud přípona hlavního názvu uživatele (UPN) Jan, například contoso.com, neodpovídá ověřené doméně ve službě Azure AD, pak Azure AD nahradí příponu UPN pomocí contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Místní domény a hlavní název uživatele (UPN) pro Azure AD, které Nesměrovatelné
Některé organizace mají Nesměrovatelné domény, jako je contoso. Local nebo jednoduché domény s jedním popiskem, jako je contoso. Nemůžete ověřit doménu bez směrování v Azure AD. Azure AD Connect se může synchronizovat jenom s ověřenou doménou v Azure AD. Když vytvoříte adresář služby Azure AD, vytvoří se směrovatelný doména, která se jako výchozí doména pro Azure AD bude například contoso.onmicrosoft.com. Proto je nutné ověřit jakoukoli jinou směrovatelný doménu v takovém scénáři pro případ, že se nechcete synchronizovat s výchozí doménou onmicrosoft.com.

Další informace o přidávání a ověřování domén najdete v tématu [Přidání vlastního názvu domény do Azure Active Directory](../fundamentals/add-custom-domain.md) .

Azure AD Connect zjistí, jestli pracujete v Nesměrovatelné doméně a že by vás s výslovným nastavením upozornil. Pokud pracujete v Nesměrovatelné doméně, je pravděpodobným, že hlavní název uživatele (UPN) bude mít i Nesměrovatelné přípony. Pokud například používáte v rámci contoso. Local, Azure AD Connect navrhne použití vlastního nastavení místo použití expresního nastavení. Pomocí vlastního nastavení můžete zadat atribut, který se má použít jako hlavní název uživatele (UPN) pro přihlášení k Azure po synchronizaci uživatelů s Azure AD.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).