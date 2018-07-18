---
title: 'Azure AD Connect: Koncepty návrhu | Dokumentace Microsoftu'
description: Toto téma podrobně popisuje určité oblasti návrhu implementace
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 4114a6c0-f96a-493c-be74-1153666ce6c9
ms.service: active-directory
ms.custom: azure-ad-connect
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 05/30/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 6d8d911acf3e3eff2cf3340972b9b77a10be0a5f
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "35643395"
---
# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: Koncepty návrhu
Účelem tohoto dokumentu je popisují oblasti, které musí být si mysleli, že během návrhu implementace služby Azure AD Connect. Tento dokument je podrobný pohled na určité oblasti a tyto koncepty stručně jsou popsány v i další dokumenty.

## <a name="sourceanchor"></a>sourceAnchor
Atribut sourceAnchor je definován jako *atribut neměnný po celou dobu životnosti objektu*. Jednoznačně identifikuje objektů jako stejný objekt místní a ve službě Azure AD. Atribut se také nazývá **immutableId** a používají se dva názvy zaměnitelné.

Neměnné, slovo, které je "nemůže být změněn", je důležité k tomuto dokumentu. Protože tento atribut hodnotu nelze změnit, jakmile byla nastavena, je potřeba vybrat návrh, který podporuje váš scénář.

Atribut se používá v následujících scénářích:

* Pokud nový synchronizační server modul je sestaven či znovu sestavit po na scénář zotavení po havárii, odkazuje tento atribut existujících objektů ve službě Azure AD s objekty v místním.
* Pokud přejdete z čistě cloudové identity do modelu synchronizované identity, tento atribut umožňuje objekty "pevné shoda" existujících objektů ve službě Azure AD s místními objekty.
* Pokud používáte federace, pak tento atribut spolu s **userPrincipalName** se používá v deklarace identity k jednoznačné identifikaci uživatele.

Toto téma se pouze hovoří o sourceAnchor totiž souvisí s uživateli. Stejná pravidla platí pro všechny typy objektů, ale je jenom pro uživatele, že tento problém je obvykle problém.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Výběru atributu sourceAnchor dobré
Hodnota atributu musí postupovat podle následujících pravidel:

* Míň než 60 znaků
  * Kódování a počítá jako 3 znaky. znaky nebude a – z, A-Z nebo 0-9
* Nesmí obsahovat speciální znaky: &#92; ! # $ % & * + / = ? ^ &#96; { } | ~ (< >) '; : , [ ] " \@ _
* Musí být globálně jedinečný
* Musí být řetězec, celé číslo nebo binárního souboru
* By neměl být založen na jméno uživatele, protože ty můžete změnit
* By neměl být malá a velká písmena a vyhnout se hodnoty, které se může lišit podle případ
* By mělo být přiřazeno při vytvoření objektu

Pokud vybraný sourceAnchor není typu String, Azure AD Connect Base64Encode hodnota atributu zajistit žádné speciální znaky zobrazí. Pokud používáte jiného federačního serveru do služby AD FS, ujistěte se, že váš server, můžete také Base64Encode atribut.

Atribut sourceAnchor je velká a malá písmena. Hodnota "Jannovak" není stejný jako "jannovak". Ale by neměly mít dva objekty s pouze jedním rozdílem v případě.

Pokud máte jednu doménovou strukturu v místním prostředí, pak byste měli použít atribut je **objectGUID**. Toto je také atribut používaný při použití expresního nastavení ve službě Azure AD Connect a také atributu, která nástroj DirSync používá.

Pokud máte několik doménových struktur a nepřemisťujte uživatelů mezi doménovými strukturami a doménami, pak **objectGUID** je dobrý atribut používat i v tomto případě.

Pokud přesunete uživatele mezi doménovými strukturami a doménami, pak během přesunutí musíte najít atribut, který se nemění, nebo je přesunout s uživateli. Doporučený postup je zavést syntetické atribut. Atribut, který by mohl obsahovat něco, která vypadá jako identifikátor GUID by být vhodné. Při vytváření objektu je nový identifikátor GUID vytvořené a uživatel k označení. Je možné vytvořit vlastní synchronizační pravidlo v synchronizační server modul pro vytvoření této hodnoty na základě **objectGUID** a aktualizovat vybraný atribut v AD DS. Při přesunutí objektu, nezapomeňte také kopírovat obsah z této hodnoty.

Druhým řešením je vybrat stávající atribut, které už znáte, nezmění. Běžně používané atributy patří **employeeID**. Pokud uvažujete atribut, který obsahuje písmena, ujistěte se, že neexistuje že žádná možnost případu (velká a malá písmena) můžete změnit hodnotu atributu. Chybné atributy, které by se neměly zahrnovat tyto atributy s názvem uživatele. V manželství nebo rozvodu název by měl změnit, což není povoleno pro tento atribut. To je také jeden důvod, proč atributy, jako například **userPrincipalName**, **e-mailu**, a **targetAddress** nejsou i možné vybrat při instalaci Azure AD Connect Průvodce. Tyto atributy obsahovat také "\@" znak, který není povolen v sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Změna atribut sourceAnchor
Hodnota atributu sourceAnchor nelze změnit poté, co objekt se vytvořil ve službě Azure AD a identita je synchronizována.

Z tohoto důvodu se vztahují následující omezení na Azure AD Connect:

* Atribut sourceAnchor lze nastavit pouze během počáteční instalace. Pokud znovu spustíte Průvodce instalací, tato možnost je jen pro čtení. Pokud je potřeba toto nastavení změnit, musíte odinstalovat a znovu nainstalovat.
* Pokud instalujete další server Azure AD Connect, je nutné vybrat stejný atribut sourceAnchor jak už používá. Pokud jste dříve používali nástroj DirSync a přesuňte do služby Azure AD Connect, pak je nutné použít **objectGUID** vzhledem k tomu, který je v atributu, která nástroj DirSync používá.
* Pokud se po změně hodnoty pro sourceAnchor objekt byly exportovány do služby Azure AD, pak Azure AD Connect sync vyvolá chybu a nepovoluje žádné další změny na, že se změní objekt před Opravili jsme problém a sourceAnchor zpět v ředitel zdroje y.

## <a name="using-msds-consistencyguid-as-sourceanchor"></a>Použití msDS-ConsistencyGuid jako parametru sourceAnchor
Ve výchozím nastavení Azure AD Connect (verze 1.1.486.0 a starší) používá jako atribut sourceAnchor objectGUID. ObjectGUID se generuje systémem. Nelze zadat jeho hodnotu při vytváření místních objektů služby AD. Jak je popsáno v části [sourceAnchor](#sourceanchor), existují scénáře, které je potřeba zadat hodnotu sourceAnchor. Pokud scénáře se vztahují na vás, je nutné použít jako atribut sourceAnchor konfigurovatelné atribut AD (například msDS-ConsistencyGuid).

Azure AD Connect (verze 1.1.524.0 a po provedení) nyní usnadňuje použití msDS-ConsistencyGuid jako atribut sourceAnchor. Při použití této funkce, služby Azure AD Connect automaticky nakonfiguruje pravidla synchronizace:

1. Použití msDS-ConsistencyGuid jako atribut sourceAnchor pro uživatelské objekty. ObjectGUID se používá pro jiné typy objektů.

2. Každá místního uživatele AD objekt, jehož atribut msDS-ConsistencyGuid se mají údaj vyplněný, Azure AD Connect zápisy, jeho hodnota objectGUID zpět na atribut msDS-ConsistencyGuid v místní službě Active Directory. Když je atribut msDS-ConsistencyGuid, Azure AD Connect exportuje objektu do služby Azure AD.

>[!NOTE]
> Jednou místní objekt AD, se importují do služby Azure AD Connect (která se importují do prostoru konektoru AD a promítnou do úložiště Metaverse), nelze změnit jeho hodnotu sourceAnchor zobrazovat. Zadat hodnotu sourceAnchor pro zadaný místní AD objektu, konfigurovat jeho atribut msDS-ConsistencyGuid předtím, než je importovat do služby Azure AD Connect.

### <a name="permission-required"></a>Požadováno oprávnění
Tato funkce fungovala musí účet služby AD DS, které jsou použity k synchronizaci s místním Active Directory udělit oprávnění k zápisu do atributu msDS-ConsistencyGuid v místní službě Active Directory.

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>Jak povolit funkci ConsistencyGuid – nové instalace
Použití ConsistencyGuid jako parametru sourceAnchor můžete povolit při nové instalaci. Tato část zahrnuje Express a vlastní instalace najdete v podrobnostech.

  >[!NOTE]
  > Pouze novější verze služby Azure AD Connect (1.1.524.0 a po provedení) podporuje použití ConsistencyGuid jako parametru sourceAnchor při nové instalaci.

### <a name="how-to-enable-the-consistencyguid-feature"></a>Jak povolit funkci ConsistencyGuid
V současné době můžete tuto funkci povolit pouze během pouze nové instalace Azure AD Connect.

#### <a name="express-installation"></a>Expresní instalace
Při instalaci Azure AD Connect s režimem Express, Průvodce Azure AD Connect automaticky určí nejvhodnější atribut AD chcete použít jako atribut sourceAnchor pomocí následujícího postupu:

* Průvodce Azure AD Connect poprvé, dotazuje svého tenanta Azure AD pro načtení atributu AD použít jako atribut sourceAnchor v předchozí instalaci Azure AD Connect (pokud existuje). Pokud tyto informace jsou k dispozici, Azure AD Connect používá stejný atribut AD.

  >[!NOTE]
  > Pouze novější verze služby Azure AD Connect (1.1.524.0 a po provedení) jsou uloženy informace o atribut sourceAnchor ve vašem tenantovi Azure AD použít během instalace. Starší verze služby Azure AD Connect to nejde.

* Pokud není k dispozici informace o atributu sourceAnchor používá, Průvodce zkontroluje stav atribut msDS-ConsistencyGuid ve vašem místním Active Directory. Pokud atribut není nakonfigurovaná na libovolný objekt v adresáři, použije průvodce msDS-ConsistencyGuid jako atribut sourceAnchor. Jestliže je atribut nastaven na jeden nebo více objektů v adresáři, dojde k závěru průvodce, atribut se používá jinými aplikacemi a není vhodný jako atribut sourceAnchor...

* V takovém případě průvodce spadne zpět na používání jako atribut sourceAnchor objectGUID.

* Jakmile atribut sourceAnchor je se rozhodli, Průvodce ukládá informace ve vašem tenantovi Azure AD. Informace se použije budoucí instalace služby Azure AD Connect.

Po dokončení instalace Express, Průvodce vás informuje, který atribut byl vybrán jako atribut zdrojového ukotvení.

![Průvodce informuje atribut AD vybrat pro sourceAnchor](./media/active-directory-aadconnect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>Vlastní instalace
Při instalaci Azure AD Connect s režimem vlastní, Průvodce Azure AD Connect poskytuje dvě možnosti při konfiguraci atribut sourceAnchor:

![Vlastní instalace – konfigurace sourceAnchor](./media/active-directory-aadconnect-design-concepts/consistencyGuid-02.png)

| Nastavení | Popis |
| --- | --- |
| Nechat správu zdrojového ukotvení na Azure | Tuto možnost vyberte, pokud chcete, aby Azure AD vybral atribut za vás. Pokud vyberete tuto možnost, Průvodce Azure AD Connect použije stejný [logiku výběru atributu sourceAnchor využitých Expresní instalace](#express-installation). Podobně jako když Expresní instalace, Průvodce vás informuje, který atribut byl vybrán jako atribut zdrojového ukotvení po dokončení instalace vlastní. |
| Konkrétní atribut | Tuto možnost vyberte, pokud chcete jako atribut sourceAnchor zadat existující atribut AD. |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>Jak povolit funkci ConsistencyGuid - existující nasazení
Pokud máte existující nasazení služby Azure AD Connect, který používá objectGUID jako atribut zdrojového ukotvení, můžete ho přejít na ConsistencyGuid místo.

>[!NOTE]
> Pouze novější verze služby Azure AD Connect (1.1.552.0 a po provedení) podporuje přechod z ObjectGuid ConsistencyGuid jako atribut zdrojového ukotvení.

Přepnutí z objectGUID do ConsistencyGuid jako atribut zdrojového ukotvení:

1. Spusťte Průvodce Azure AD Connect a klikněte na tlačítko **konfigurovat** přejdete do okna úkoly.

2. Vyberte **konfigurovat zdrojové ukotvení** úloh možnost a klikněte na tlačítko **Další**.

   ![Povolit ConsistencyGuid pro existující nasazení – krok 2](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment01.png)

3. Zadejte svoje přihlašovací údaje správce Azure AD a klikněte na tlačítko **Další**.

4. Průvodce Azure AD Connect analyzuje stav atribut msDS-ConsistencyGuid ve vašem místním Active Directory. Pokud atribut není nakonfigurovaná na libovolný objekt v adresáři, Azure AD Connect k závěru, že žádná jiná aplikace právě používá atribut a je bezpečné používat jako atribut zdrojového ukotvení. Klikněte na tlačítko **Další** pokračujte.

   ![Povolit ConsistencyGuid pro existující nasazení – krok 4](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment02.png)

5. V **připravení konfigurovat** obrazovce, klikněte na tlačítko **konfigurovat** provést změnu konfigurace.

   ![Povolit ConsistencyGuid pro existující nasazení – krok 5](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment03.png)

6. Po dokončení konfigurace Průvodce označuje, že msDS-ConsistencyGuid se teď používá jako atribut zdrojového ukotvení.

   ![Povolit ConsistencyGuid pro existující nasazení – krok 6](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment04.png)

Během analýzy (krok 4) Jestliže je atribut nastaven na jeden nebo více objektů v adresáři, Průvodce dojde k závěru atribut se používá jiná aplikace a vrátí chybu, jak je znázorněno v následujícím diagramu. K této chybě může dojít, pokud jste už dříve povolili funkci ConsistencyGuid na primární Azure AD Connect se pokoušíte server a dělat to samé na testovacím serveru.

![Povolit ConsistencyGuid pro existující nasazení – chyba](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeploymenterror.png)

 Pokud jste si jisti, že atribut není použít jiné existující aplikace, můžete potlačit chyby restartováním Průvodce, Azure AD Connect **/SkipLdapSearchcontact** zadané. Chcete-li tak učinit, spusťte následující příkaz v příkazovém řádku:

```
"c:\Program Files\Microsoft Azure Active Directory Connect\AzureADConnect.exe" /SkipLdapSearch
```

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>Dopad na službu AD FS nebo konfigurace federace třetích stran
Pokud používáte Azure AD Connect ke správě místních nasazení služby AD FS, Azure AD Connect aktualizuje pravidla deklarací identity použít stejný atribut AD jako parametru sourceAnchor. Tím se zajistí, že deklarace identity ImmutableID generovaných služby AD FS je konzistentní s hodnotami sourceAnchor exportovány do služby Azure AD.

Pokud spravujete služby AD FS mimo Azure AD Connect nebo třetích stran federační servery používají pro ověřování, je nutné ručně aktualizovat pravidla deklarací pro deklarace identity ImmutableID tak, aby byla konzistentní s hodnotami sourceAnchor exportovány do služby Azure AD, jak je popsáno v článek části [pravidla deklarací identity změnit AD FS](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#modclaims). Po dokončení instalace, vrátí průvodce následující upozornění:

![Konfigurace federace třetích stran](./media/active-directory-aadconnect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>Přidání nového adresáře do stávajícího nasazení
Předpokládejme, že jste nasadili Azure AD Connect s povolenou funkcí ConsistencyGuid, a teď chcete přidat jiný adresář pro nasazení. Při pokusu o přidání adresáře průvodce Azure AD Connect zkontroluje stav atribut mSDS-ConsistencyGuid v adresáři. Jestliže je atribut nastaven na jeden nebo více objektů v adresáři, dojde k závěru průvodce, atribut se používá jinými aplikacemi a vrátí chybu, jak je znázorněno v následujícím diagramu. Pokud jste si jisti, že atribut nepoužívá existující aplikace, budete muset kontaktovat podporu pro informace o tom, jak potlačit chyby.

![Přidání nového adresáře do stávajícího nasazení](./media/active-directory-aadconnect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Přihlášení k Azure AD
Při integraci místního adresáře s Azure AD, je důležité pochopit, jak nastavení synchronizace mohou ovlivnit způsob, jak uživatel se ověřuje. Azure AD userPrincipalName (UPN) používá k ověření uživatele. Ale pokud synchronizujete uživatele, musíte zvolit atribut, který chcete použít pro hodnota userPrincipalName pečlivě.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Výběr atribut userPrincipalName
Při výběru atributu pro poskytování zajistil hodnotu hlavního názvu uživatele, který se má použít v aplikacích Azure

* Atribut hodnoty v souladu s syntaxe hlavní název uživatele (RFC 822), který je by měl mít formát uživatelského jména\@domény
* Přípona hodnoty odpovídají jedné z ověřených vlastních domén ve službě Azure AD

Expresní nastavení je předpokládané volbou pro atribut userPrincipalName. Pokud atribut userPrincipalName neobsahuje hodnotu mají vaši uživatelé k přihlášení do Azure, pak musíte zvolit **vlastní instalace**.

### <a name="custom-domain-state-and-upn"></a>Stav vlastní domény a hlavní název uživatele
Je důležité, aby se zajistilo, že ověřenou doménu pro tuto příponu UPN.

Jan je uživatel v doméně contoso.com. Chcete, aby Jan použít hlavní název uživatele john místní\@contoso.com pro přihlášení k Azure po uživatelů se synchronizovalo s vaší služby Azure AD directory contoso.onmicrosoft.com. Uděláte to tak, budete muset přidat a ověřit contoso.com jako vlastní domény ve službě Azure AD, než začnete synchronizovat uživatele. Pokud přípona UPN John, třeba contoso.com, neodpovídá ověřené domény ve službě Azure AD, Azure AD nahradí přípony UPN s contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Non směrovatelné místních domén a hlavní název uživatele pro službu Azure AD
Některé organizace mají nesměrovatelných doménách, jako je contoso.local, nebo jednoduchý přípony domény, například contoso. Nemůžete ověřit nesměrovatelných domény ve službě Azure AD. Azure AD Connect pro synchronizaci můžete jenom ověřené domény ve službě Azure AD. Při vytváření adresáře služby Azure AD vytvoří směrovatelné domény, který se stane výchozí doménu pro služby Azure AD, například contoso.onmicrosoft.com. Proto bude nutné k ověření dalších směrovatelná doména v takové situaci v případě, že nechcete synchronizovat do výchozí domény onmicrosoft.com.

Čtení [přidání vlastního názvu domény do Azure Active Directory](../active-directory-domains-add-azure-portal.md) pro další informace o přidávání a ověřování domén.

Azure AD Connect rozpozná, pokud jsou spuštěny v prostředí domény nesměrovatelných a bude odpovídajícím způsobem upozornění z budete pokračovat s expresního nastavení. Pokud pracujete v doméně nesměrovatelných, je pravděpodobné, že hlavní název uživatele, uživatelů, mají příliš nesměrovatelných přípony. Například pokud jsou spuštěny v contoso.local, Azure AD Connect navrhuje použití vlastní nastavení místo použitím expresního nastavení. Pomocí vlastních nastavení, budete moct zadat atribut, který se má použít jako hlavní název uživatele pro přihlášení k Azure po uživatelů se synchronizují do služby Azure AD.

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md).
