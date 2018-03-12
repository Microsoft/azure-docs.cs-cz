---
title: "Azure AD Connect: Koncepty návrhu | Microsoft Docs"
description: "Toto téma podrobnosti určité oblasti návrhu implementace"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 4114a6c0-f96a-493c-be74-1153666ce6c9
ms.service: active-directory
ms.custom: azure-ad-connect
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 179a669e4c9567950d22ed76a693ec6ab7a2db8d
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: Koncepty návrhu
Účelem tohoto tématu je k popisu oblastí, které musí považovat při návrhu implementace služby Azure AD Connect. Toto téma se podrobné informace o určité oblasti a tyto koncepty stručně jsou popsány v i další témata.

## <a name="sourceanchor"></a>sourceAnchor
Atribut sourceAnchor je definován jako *atribut neměnné průběhu životnosti objektu*. Jednoznačně identifikuje objektů, že je stejný objekt místní a ve službě Azure AD. Atribut taky nazývá **immutableId** a tyto názvy se použijí zaměňovat.

Neměnné, word, který je "nelze změnit", je důležité, abyste v tomto tématu. Vzhledem k tomu, že tento atribut hodnotu nelze změnit, jakmile byla nastavena, je důležité vybrat návrh, který podporuje váš scénář.

Atribut se používá pro následující scénáře:

* Pokud nový synchronizační server modul je vytvořen, nebo znovu sestavit po na scénář zotavení po havárii, tento atribut propojí existující objekty ve službě Azure AD s objekty na místě.
* Pokud přejdete z čistě cloudové identity k modelu synchronizované identity, tento atribut umožňuje objekty, které se "pevné shodu" existující objekty ve službě Azure AD s místními objekty.
* Pokud používáte federace a potom tento atribut společně s **userPrincipalName** slouží k jednoznačné identifikaci uživatele v deklaraci identity.

Toto téma sourceAnchor pouze zmíněn, protože se týká uživatelům. Stejná pravidla platí pro všechny typy objektů, ale je určena pouze pro uživatele, že tento problém obvykle není vážný.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Výběr atribut sourceAnchor funkční
Hodnota atributu musí postupujte podle následujících pravidel:

* Být menší než 60 znaků.
  * Kódování a počítají jako znaky 3 znaky nebude a – z, A-Z nebo 0-9
* Neobsahuje zvláštní znak: &#92; ! # $ % & * + / = ? ^ &#96; { } | ~ < > ( ) ' ; : , [ ] " @ _
* Musí být globálně jedinečný
* Musí být řetězec, celé číslo nebo binárního souboru
* By neměl být založené na jméno uživatele, tyto změny
* By neměl být malá a velká písmena a vyhnout se hodnoty, které se mohou lišit podle případu
* By se měla přiřadit při vytvoření objektu

Pokud vybraný sourceAnchor není typu String, potom Azure AD Connect Base64Encode hodnota atributu zajistit žádné speciální znaky zobrazí. Pokud používáte jiné federační server než služba AD FS, ujistěte se, se váš server můžete také Base64Encode atribut.

Atribut sourceAnchor je malá a velká písmena. Hodnota "JohnDoe" není stejný jako "johndoe". Ale by neměl mít dva různé objekty s pouze rozdíly v případě.

Pokud máte jednu doménovou strukturu je na místě, pak byste měli použít atribut **objectGUID**. Toto je také atribut používaný kdy použít Expresní nastavení ve službě Azure AD Connect a také atribut používaný nástrojem DirSync.

Pokud máte více doménových struktur a nepřesouvejte uživatelů mezi doménové struktury a domény, pak **objectGUID** je dobrý atribut používat i v tomto případě.

Pokud přesunete uživatele mezi doménové struktury a domény, pak musíte vyhledat atribut, který se nemění, nebo lze přesunout s uživateli během přesunu. Doporučený postup je zavedení syntetické atribut. Atribut, který může obsahovat něco který vypadá jako identifikátor GUID bude vhodné. Při vytváření objektu je nový identifikátor GUID vytvořit a razítkem na uživatele. Vlastní synchronizační pravidlo lze vytvořit na serveru synchronizačního modulu pro vytvoření této hodnoty na základě **objectGUID** a aktualizovat v přidá vybraný atribut. Při přesunutí objektu, ujistěte se, že jste také kopírovat obsah této hodnoty.

Jiné řešení je vybrat stávající atribut, které znáte se nemění. Běžně používané atributy patří **employeeID**. Pokud uvažujete o atribut, který obsahuje písmena, ujistěte se, že žádné prvního případ (velká a malá písmena) můžete změnit hodnotu atributu se. Chybný atributy, které by se neměla používat zahrnují tyto atributy se jménem uživatele. V manželství nebo rozvodu název by měl změnit, což není povoleno pro tento atribut. Toto je také jeden důvod proč atributy, jako **userPrincipalName**, **e-mailu**, a **targetAddress** nejsou i možné vybrat v Průvodci instalací Azure AD Connect. Tyto atributy obsahovat také "@" znak, který není povolen v sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Změna atribut sourceAnchor
Hodnota atributu sourceAnchor nelze změnit po objekt byl vytvořen ve službě Azure AD a identity je synchronizován.

Z tohoto důvodu se vztahují následující omezení na Azure AD Connect:

* Atribut sourceAnchor lze nastavit pouze během počáteční instalace. Pokud Průvodce instalací, tato možnost je jen pro čtení. Pokud potřebujete změnit toto nastavení, je nutné odinstalovat a znovu nainstalovat.
* Pokud nainstalujete jiný server Azure AD Connect, musíte vybrat stejný jako v předcházejícím atribut sourceAnchor. Pokud jste dříve pomocí nástroje DirSync a přesunout do Azure AD Connect, pak musíte použít **objectGUID** vzhledem k tomu, který je atribut používaný nástrojem DirSync.
* Pokud je po změně hodnoty pro sourceAnchor objekt byly exportovány do služby Azure AD, pak Azure AD Connect sync vyvolá chybu a nepovoluje další změny na zpět v zdrojový adresář nastavení se mění objekt předtím, než byl opraven problém a sourceAnchor.

## <a name="using-msds-consistencyguid-as-sourceanchor"></a>Použití msDS-ConsistencyGuid jako sourceAnchor
Ve výchozím nastavení, Azure AD Connect (verze 1.1.486.0 a starší) používá objectGUID jako atribut sourceAnchor. Identifikátoru GUID se generuje systémem. Nelze zadat jeho hodnotu, při vytváření místní objekty služby AD. Jak je popsáno v části [sourceAnchor](#sourceanchor), existují scénáře, kde je třeba zadat hodnotu sourceAnchor. Pokud pro vás platí scénáře, musíte použít atribut konfigurovat AD (například msDS-ConsistencyGuid) jako atribut sourceAnchor.

Azure AD Connect (verze 1.1.524.0 a po) teď usnadňuje použití msDS-ConsistencyGuid jako atribut sourceAnchor. Při použití této funkce, Azure AD Connect automaticky nakonfiguruje pravidla synchronizace pro:

1. Použijte msDS-ConsistencyGuid jako atribut sourceAnchor pro uživatelské objekty. ObjectGUID se používá pro jiné typy objektů.

2. Pro všechny zadané místní uživatele AD objekt, jehož atribut msDS-ConsistencyGuid není vyplněná, Azure AD Connect zápisy, jeho hodnota objectGUID zpět do atribut msDS-ConsistencyGuid v místní službě Active Directory. Po naplnění atribut msDS-ConsistencyGuid Azure AD Connect pak exportuje objektu do služby Azure AD.

>[!NOTE]
> Jednou místní AD objektu je importovat do Azure AD Connect (která je, importována do prostoru konektoru AD a promítnout do úložiště Metaverse), jeho sourceAnchor hodnotu nelze změnit, již. Zadejte hodnotu sourceAnchor pro danou místní AD objektu, konfigurovat jeho atribut msDS-ConsistencyGuid před jeho importu do Azure AD Connect.

### <a name="permission-required"></a>Potřebná oprávnění
Pro tuto funkci používat musí účet služby AD DS používá k synchronizaci s místní služby Active Directory udělit oprávnění k zápisu do atribut msDS-ConsistencyGuid v místní službě Active Directory.

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>Postup povolení funkce ConsistencyGuid - novou instalaci
Při nové instalaci můžete povolit použití ConsistencyGuid jako sourceAnchor. Tato část obsahuje Express a vlastní instalace v podrobnostech.

  >[!NOTE]
  > Pouze novější verze služby Azure AD Connect (1.1.524.0 a po) podporuje použití ConsistencyGuid jako sourceAnchor při nové instalaci.

### <a name="how-to-enable-the-consistencyguid-feature"></a>Postup povolení funkce ConsistencyGuid
V současné době můžete tuto funkci povolit pouze během pouze nové instalace Azure AD Connect.

#### <a name="express-installation"></a>Expresní instalace
Při instalaci Azure AD Connect s režimem Express, Průvodce službou Azure AD Connect automaticky určuje atribut AD nejvhodnější používat jako atribut sourceAnchor pomocí následujícího postupu:

* Průvodce Azure AD Connect nejprve dotazuje klientovi Azure AD načíst atribut AD použít jako atribut sourceAnchor v předchozí instalaci Azure AD Connect (pokud existuje). Pokud tyto informace jsou k dispozici, Azure AD Connect používá stejný atribut AD.

  >[!NOTE]
  > Pouze novější verze služby Azure AD Connect (1.1.524.0 a po) jsou uloženy informace o atribut sourceAnchor v klientovi služby Azure AD použít během instalace. Starší verze služby Azure AD Connect nepodporují.

* Pokud není k dispozici informace o atribut sourceAnchor používaný, Průvodce zkontroluje stav atribut msDS-ConsistencyGuid ve vaší místní službě Active Directory. Pokud atribut není nakonfigurovaný na všech objektech v adresáři, použije průvodce msDS-ConsistencyGuid jako atribut sourceAnchor. Pokud atribut je nakonfigurovaný na jeden nebo více objektů v adresáři, průvodce se ukončí atribut se používá jiné aplikace a není vhodný jako atribut sourceAnchor...

* V takovém případě průvodce spadne zpět na používání objectGUID jako atribut sourceAnchor.

* Jakmile atribut sourceAnchor je určeno, Průvodce ukládá informace v klientovi služby Azure AD. Informace se použije budoucí instalace služby Azure AD Connect.

Po dokončení expresní instalace Průvodce vás upozorní, které atribut vydané jako atribut zdrojové ukotvení.

![Průvodce informuje AD atribut vydat pro sourceAnchor](./media/active-directory-aadconnect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>Vlastní instalace
Při instalaci Azure AD Connect s vlastním režimu, poskytuje průvodce službou Azure AD Connect při konfiguraci atribut sourceAnchor dvě možnosti:

![Vlastní instalace – sourceAnchor konfigurace](./media/active-directory-aadconnect-design-concepts/consistencyGuid-02.png)

| Nastavení | Popis |
| --- | --- |
| Nechat správu zdrojového ukotvení na Azure | Tuto možnost vyberte, pokud chcete, aby Azure AD vybral atribut za vás. Pokud vyberete tuto možnost, Průvodce Azure AD Connect se vztahují stejné [logiku výběr atribut sourceAnchor použít během instalace Express](#express-installation). Podobně jako u Expresní instalace, Průvodce vás upozorní, které atribut bylo vydáno jako atribut zdrojové ukotvení po dokončení instalace vlastní. |
| Konkrétní atribut | Tuto možnost vyberte, pokud chcete jako atribut sourceAnchor zadat existující atribut AD. |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>Postup povolení funkce ConsistencyGuid - stávajícího nasazení
Pokud máte existující nasazení Azure AD Connect, který používá jako zdrojové ukotvení atribut objectGUID, můžete je přepnout pomocí ConsistencyGuid místo.

>[!NOTE]
> Pouze novější verze služby Azure AD Connect (1.1.552.0 a po) podporuje změna na ConsistencyGuid jako zdrojové ukotvení atribut ObjectGuid.

Přepnutí z objectGUID do ConsistencyGuid jako atribut zdrojové ukotvení:

1. Spusťte Průvodce službou Azure AD Connect a klikněte na **konfigurace** přejít na obrazovce úlohy.

2. Vyberte **konfigurace zdrojové ukotvení** úloh možnost a klikněte na tlačítko **Další**.

   ![Povolit ConsistencyGuid pro existující nasazení – krok 2](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment01.png)

3. Zadejte své přihlašovací údaje správce Azure AD a klikněte na tlačítko **Další**.

4. Průvodce Azure AD Connect analyzuje stav atribut msDS-ConsistencyGuid ve vaší místní službě Active Directory. Pokud atribut není nakonfigurovaný na všech objektech v adresáři, Azure AD Connect dojde k závěru, že žádná jiná aplikace právě používá atribut a je možné použít jako atribut zdrojové ukotvení. Klikněte na tlačítko **Další** pokračujte.

   ![Povolit ConsistencyGuid pro existující nasazení – krok 4](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment02.png)

5. V **připraveni konfigurovat** obrazovky, klikněte na tlačítko **konfigurace** aby změnu konfigurace.

   ![Povolit ConsistencyGuid pro existující nasazení – krok 5](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment03.png)

6. Po dokončení konfigurace Průvodce označuje, že tento msDS-ConsistencyGuid je nyní používána jako atribut zdrojové ukotvení.

   ![Povolit ConsistencyGuid pro existující nasazení – krok 6](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment04.png)

Během analýzy (krok 4) Pokud je atribut nastaven na jeden nebo více objektů v adresáři, průvodce se ukončí atribut se používá jiná aplikace a vrátí chybu, jak je znázorněno v následujícím diagramu. Této chybě může dojít také, pokud jste dříve povolili funkci ConsistencyGuid na vaší primární Azure AD Connect server a vy se pokoušíte provést stejný na testovacím serveru.

![Povolit ConsistencyGuid pro existující nasazení – chyba](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeploymenterror.png)

 Pokud jste si jisti, že atribut není použít jiné existující aplikace, můžete potlačit chyby restartováním Průvodce Azure AD Connect **/SkipLdapSearchcontact** zadaný. Uděláte to tak, spusťte v příkazovém řádku následující příkaz:

```
"c:\Program Files\Microsoft Azure Active Directory Connect\AzureADConnect.exe" /SkipLdapSearch
```

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>Dopad na služby AD FS nebo konfigurace federování třetích stran
Pokud používáte Azure AD Connect ke správě místního nasazení služby AD FS, Azure AD Connect automaticky aktualizuje pravidel deklarací identity, které můžete použít stejný atribut AD jako sourceAnchor. Tím se zajistí, že deklarace identity ImmutableID generované služby AD FS je konzistentní s hodnotami sourceAnchor exportovány do služby Azure AD.

Pokud spravujete služby AD FS mimo Azure AD Connect nebo třetích stran federační servery používají pro ověřování, je nutné ručně aktualizovat pravidla deklarací pro ImmutableID deklarace identity, aby byla konzistentní s hodnotami sourceAnchor exportované do Azure AD, jak je popsáno v části článku [pravidla deklarací identity upravit AD FS](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#modclaims). Po dokončení instalace, Průvodce vrátí následující upozornění:

![Konfigurace federace třetích stran](./media/active-directory-aadconnect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>Přidávání nových adresářů do stávajícího nasazení
Předpokládejme, že jste nasadili Azure AD Connect s povolenou funkcí ConsistencyGuid a teď chcete přidat další adresář k nasazení. Když se pokusíte přidat k adresáři, Průvodce Azure AD Connect kontroluje stav atribut mSDS-ConsistencyGuid v adresáři. Pokud atribut je nakonfigurovaný na jeden nebo více objektů v adresáři, průvodce se ukončí atribut se používá jiné aplikace a vrátí chybu, jak je znázorněno v následujícím diagramu. Pokud jste si jisti, že atribut není používán existující aplikace, budete muset obraťte se na podporu pro informace o tom, jak potlačit chyby.

![Přidávání nových adresářů do stávajícího nasazení](./media/active-directory-aadconnect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Přihlášení k Azure AD
Při integraci místního adresáře do Azure AD, je důležité pochopit, jak nastavení synchronizace může ovlivnit způsob uživatel se ověřuje. Azure AD userPrincipalName (UPN) používá k ověření uživatele. Při synchronizaci vaši uživatelé však musíte zvolit atribut, který se použije pro hodnotu userPrincipalName pečlivě.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Výběr atribut userPrincipalName
Při výběru atribut pro zajištění hodnotu UPN, které mají být použity v Azure jeden zajistil

* Hodnoty atributu odpovídat UPN syntaxi (RFC 822), který je, že by mělo být ve formátu username@domain
* Přípona v hodnoty odpovídá jednomu z ověřené vlastní domény ve službě Azure AD

V nastavení express je předpokládané volba pro atribut userPrincipalName. Pokud atribut userPrincipalName neobsahuje hodnotu mají vaši uživatelé přihlásit k platformě Azure, pak je nutné vybrat **vlastní instalace**.

### <a name="custom-domain-state-and-upn"></a>Stav vlastní domény a UPN
Je důležité zajistit, že je ověřené domény pro tuto příponu UPN.

Jan je uživatel v doméně contoso.com. Chcete, aby Jan používat místní UPN john@contoso.com pro přihlášení k Azure po uživatelé mají synchronizované s vaší contoso.onmicrosoft.com adresář Azure AD. To pokud chcete udělat, musíte přidat a ověřit contoso.com jako vlastní domény ve službě Azure AD, než začnete synchronizovat uživatele. Pokud příponu UPN Jan, například contoso.com, neodpovídá ověřenou doménu ve službě Azure AD, Azure AD nahrazuje příponu UPN s contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Směrovatelný bez místní domény a UPN pro Azure AD
Některé organizace mají směrovat domény, jako je contoso.local nebo jednoduchý přípony domény jako contoso. Nemůžete se nepodařilo ověřit směrovat domény ve službě Azure AD. Azure AD Connect pro synchronizaci můžete pouze ověřené domény ve službě Azure AD. Když vytvoříte adresář služby Azure AD, vytvoří se směrovatelné domény, který se stane výchozí doménu pro vaši službu Azure AD například contoso.onmicrosoft.com. Proto bude nutné v případě, že nechcete, aby pro synchronizaci výchozí domény onmicrosoft.com ověření všech ostatních směrovatelné domén v takové situaci.

Čtení [přidání vlastního názvu domény do Azure Active Directory](../active-directory-domains-add-azure-portal.md) Další informace o přidání a ověření domény.

Azure AD Connect zjistí, pokud běží v prostředí domény směrovat a bude odpovídajícím způsobem upozornit z pokračovat Expresní nastavení. Pokud pracujete v směrovat doméně, je pravděpodobné, že jména UPN uživatelů mít příliš směrovat přípony. Například pokud používáte v contoso.local, Azure AD Connect navrhuje vám umožní používat vlastní nastavení místo použitím expresního nastavení. Pomocí vlastních nastavení, budete moci zadat atribut, který má být použit jako hlavní název uživatele pro přihlášení k Azure po uživatelů se synchronizují do Azure AD.

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md).
