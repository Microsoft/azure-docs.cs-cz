---
title: 'Azure AD Connect: principy deklarativního zřizování | Microsoft Docs'
description: Vysvětluje deklarativní konfigurační model zřizování v Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cfbb870d-be7d-47b3-ba01-9e78121f0067
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59dc94e37dfa1ef8b0b079bf5d78d0504e0cb8c7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91313616"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Azure AD Connect synchronizace: principy deklarativního zřizování
Toto téma vysvětluje konfigurační model v Azure AD Connect. Model se nazývá deklarativní zřizování a umožňuje snadno provádět změny konfigurace. Mnoho věcí popsaných v tomto tématu je pro většinu zákaznických scénářů pokročilé a není nutné.

## <a name="overview"></a>Přehled
Deklarativní zřizování je zpracování objektů přicházejících ze zdrojového připojeného adresáře a určuje, jak by měl být objekt a atributy transformovány ze zdroje do cíle. Objekt se zpracovává v synchronizačním kanálu a kanál je stejný pro příchozí a odchozí pravidla. Příchozí pravidlo pochází z prostoru konektoru do úložiště metaverse a odchozí pravidlo je z úložiště metaverse do prostoru konektoru.

![Diagram, který zobrazuje příklad synchronizačního kanálu.](./media/concept-azure-ad-connect-sync-declarative-provisioning/sync1.png)  

Kanál má několik různých modulů. Každý z nich zodpovídá za jeden koncept v synchronizaci objektů.

![Diagram znázorňující moduly v kanálu.](./media/concept-azure-ad-connect-sync-declarative-provisioning/pipeline.png)  

* Zdroj, zdrojový objekt
* [Rozsah](#scope), najde všechna pravidla synchronizace, která jsou v oboru.
* [Join](#join), určuje vztah mezi prostorem konektoru a úložištěm Metaverse.
* Transformuje, vypočítá, jak mají být atributy transformované a flow
* [Priorita](#precedence), řeší konfliktní příspěvky atributů
* Cíl, cílový objekt

## <a name="scope"></a>Obor
Modul Scope vyhodnocuje objekt a určuje pravidla, která jsou v oboru a měla by být součástí zpracování. V závislosti na hodnotách atributů objektu jsou vyhodnocena odlišná pravidla synchronizace, která jsou v oboru. Například zakázaný uživatel, který nemá poštovní schránku Exchange, má různá pravidla, než je povolený uživatel s poštovní schránkou.  
![Diagram, který zobrazuje modul oboru pro objekt.](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope1.png)  

Obor je definován jako skupiny a klauzule. Klauzule jsou uvnitř skupiny. Logický operátor AND se používá mezi všemi klauzulemi ve skupině. Například (oddělení = IT a země = Dánsko). Logická nebo se používá mezi skupinami.

![Obor](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope2.png)  
Obor tohoto obrázku by měl být čten jako (oddělení = IT a země = Dánsko) nebo (země = Švédsko). Pokud je skupina 1 nebo 2 vyhodnocena na hodnotu true, pak je pravidlo v rozsahu.

Modul Scope podporuje následující operace.

| Operace | Description |
| --- | --- |
| ROVNÁ SE, NOTEQUAL |Porovnávání řetězců, které vyhodnocuje, zda je hodnota rovna hodnotě v atributu. Pro vícehodnotové atributy viz ISIN a ISNOTIN. |
| LESSTHAN, LESSTHAN_OR_EQUAL |Porovnávání řetězců, které vyhodnocuje, zda je hodnota menší než hodnota v atributu. |
| OBSAHUJE, NOTCONTAINS |Porovnávání řetězců, které vyhodnocuje, zda je hodnota nalezena někde uvnitř hodnoty v atributu. |
| STARTSWITH, NOTSTARTSWITH |Porovnávání řetězců, které vyhodnocuje, zda je hodnota na začátku hodnoty v atributu. |
| ENDSWITH, NOTENDSWITH |Porovnávání řetězců, které vyhodnocuje, zda je hodnota na konci hodnoty v atributu. |
| GREATERTHAN, GREATERTHAN_OR_EQUAL |Porovnávání řetězců, které vyhodnocuje, zda je hodnota větší než hodnota v atributu. |
| ISNULL, ISNOTNULL |Vyhodnotí, zda atribut chybí z objektu. Pokud atribut není k dispozici a je tedy null, je pravidlo v rozsahu. |
| ISIN, ISNOTIN |Vyhodnotí, zda je hodnota přítomna v definovaném atributu. Tato operace je víceúrovňové variace EQUAL a NOTEQUAL. Atribut by měl být vícehodnotový atribut a pokud hodnota může být nalezena v některé z hodnot atributu, pak je pravidlo v oboru. |
| ISBITSET, ISNOTBITSET |Vyhodnotí, zda je nastaven konkrétní bit. Můžete například použít k vyhodnocení bitů v rámci služby správce \ a zjistit, jestli je uživatel povolený nebo zakázaný. |
| ISNOTMEMBEROF |Hodnota by měla obsahovat DN pro skupinu v prostoru konektoru. Pokud je objekt členem zadané skupiny, pravidlo je v oboru. |

## <a name="join"></a>Připojení
Modul JOIN v kanálu synchronizace zodpovídá za nalezení vztahu mezi objektem ve zdroji a objektem v cíli. U příchozího pravidla by tato relace byla objektem v prostoru konektoru, kde najdete relaci k objektu v úložišti Metaverse.  
![Spojení mezi cs a MV](./media/concept-azure-ad-connect-sync-declarative-provisioning/join1.png)  
Cílem je zjistit, jestli už v úložišti Metaverse není nějaký objekt, který vytvořil jiný konektor, musí být přidružený k. Například v doménové struktuře prostředků účtu by uživatel z doménové struktury účtu měl být připojený k uživateli z doménové struktury prostředků.

Spojení se většinou používají pro příchozí pravidla pro připojení objektů prostoru konektoru ke stejnému objektu úložiště metaverse.

Spojení se definují jako jedna nebo víc skupin. V rámci skupiny máte klauzule. Logický operátor AND se používá mezi všemi klauzulemi ve skupině. Logická nebo se používá mezi skupinami. Skupiny jsou zpracovávány v pořadí shora dolů. Když jedna skupina našla přesně jednu shodu s objektem v cíli, nejsou vyhodnocena žádná další pravidla spojení. Pokud se najde nula nebo více než jeden objekt, zpracování pokračuje do další skupiny pravidel. Z tohoto důvodu by se pravidla měla vytvořit v pořadí nejvíce explicitně a na konci budou větší přibližná.  
![Definice spojení](./media/concept-azure-ad-connect-sync-declarative-provisioning/join2.png)  
Spojení v tomto obrázku jsou zpracovávána shora dolů. Kanál synchronizace se nejprve zobrazí, pokud existuje shoda s časem. V takovém případě se druhé pravidlo uvidí, pokud se název účtu dá použít k spojování objektů. Pokud se nejedná o shodu, třetí a konečné pravidlo je více přibližné shody s použitím jména uživatele.

Pokud byla vyhodnocena všechna pravidla spojení a neexistuje přesně jedna shoda, je použit **typ odkazu** na stránce **Popis** . Pokud je tato možnost nastavená na **zřizování**, vytvoří se nový objekt v cíli.  
![Snímek obrazovky zobrazující, že je otevřená rozevírací nabídka typ propojení](./media/concept-azure-ad-connect-sync-declarative-provisioning/join3.png)  

Objekt by měl mít pouze jedno pravidlo synchronizace s pravidly JOIN v oboru. Pokud existuje více pravidel synchronizace, kde je definováno spojení, dojde k chybě. Priorita se nepoužívá k vyřešení konfliktů spojení. Objekt musí mít pravidlo JOIN v oboru pro atributy, které se mají směrovat stejným směrem příchozího a odchozího směrování. Pokud potřebujete flowovat atributy příchozích i odchozích dat do stejného objektu, musíte mít pravidlo příchozí i odchozí synchronizace s připojením.

Při pokusu o zřízení objektu na cílový prostor konektoru má odchozí spojení zvláštní chování. Atribut DN se používá k prvnímu pokusu o zpětné spojení. Pokud je v cílovém spojovacím prostoru již objekt se stejným rozlišujícím názvem, jsou objekty spojeny.

Modul JOIN se vyhodnocuje jenom jednou, když se nové pravidlo synchronizace dostane do rozsahu. Když se objekt připojí, nepřipojí se ani v případě, že už nejsou splněná kritéria spojení. Pokud chcete objekt odpojíte, pravidlo synchronizace, které se připojilo k objektům, musí přejít mimo rozsah.

### <a name="metaverse-delete"></a>Odstranění Metaverse
Objekt Metaverse zůstane, dokud je v oboru s **typem odkazu** nastaveno na **zřídit** nebo **StickyJoin** jedno pravidlo synchronizace. StickyJoin se používá v případě, že konektor nemá povoleno zřídit nový objekt do úložiště metaverse, ale když se připojí, musí být před odstraněním objektu Metaverse odstraněn ve zdroji.

Při odstranění objektu Metaverse jsou všechny objekty přidružené k pravidlu odchozí synchronizace označené pro **zřizování** označeny pro odstranění.

## <a name="transformations"></a>Transformace
Transformace slouží k definování způsobu, jakým mají atributy tok ze zdroje do cíle. Toky mohou mít jeden z následujících **typů toků**: Direct, constant nebo Expression. Přímý tok natéká hodnotu atributu tak, jak je, bez dalších transformací. Hodnota konstanty nastaví zadanou hodnotu. Výraz používá deklarativní jazyk výrazů zřizování k vyjádření, jak by měla být transformace. Podrobnosti o jazyce výrazů najdete v tématu [Principy deklarativního zajišťování jazyka výrazů](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) .

![Zřídit nebo připojit](./media/concept-azure-ad-connect-sync-declarative-provisioning/transformations1.png)  

Zaškrtávací políčko **použít jednou** definuje, že by měl být atribut nastaven pouze při počátečním vytvoření objektu. Například tato konfigurace může být použita k nastavení počátečního hesla pro nový objekt uživatele.

### <a name="merging-attribute-values"></a>Sloučení hodnot atributů
V tokůch atributů je k dispozici nastavení určující, zda mají být atributy s více hodnotami sloučeny z několika různých konektorů. Výchozí hodnota je **Update**, což znamená, že se má vyhodnotit pravidlo synchronizace s nejvyšší prioritou.

![Snímek obrazovky, který zobrazuje oddíl "Přidat transformace" s otevřenou rozevírací nabídkou "typy sloučení".](./media/concept-azure-ad-connect-sync-declarative-provisioning/mergetype.png)  

K dispozici jsou také **slučované** a **MergeCaseInsensitive**. Tyto možnosti umožňují sloučit hodnoty z různých zdrojů. Můžete ji například použít ke sloučení atributu member nebo proxyAddresses z několika různých doménových struktur. Při použití této možnosti musí všechna pravidla synchronizace v oboru pro objekt používat stejný typ sloučení. Nelze definovat **aktualizaci** z jednoho konektoru a **Sloučit** z jiného. Pokud se pokusíte, zobrazí se chyba.

Rozdíl mezi **sloučením** a **MergeCaseInsensitive** je způsob zpracování duplicitních hodnot atributů. Synchronizační modul zajišťuje, že duplicitní hodnoty nejsou vloženy do atributu target. V případě **MergeCaseInsensitive** nejsou k dispozici duplicitní hodnoty pouze s rozdílem v případě, že nejsou k dispozici. V cílovém atributu byste například neměli vidět obě " SMTP:bob@contoso.com " i " smtp:bob@contoso.com ". **Sloučení** se díváte jenom na přesné hodnoty a několik hodnot, u kterých se může vyskytovat jenom rozdíl.

Možnost **nahradit** je stejná jako **aktualizace**, ale není použita.

### <a name="control-the-attribute-flow-process"></a>Řízení procesu toku atributů
Pokud jsou pro přispívání do stejného atributu úložiště nakonfigurovaná víc pravidel příchozí synchronizace, pak se k určení vítěze použije priorita. Pravidlo synchronizace s nejvyšší prioritou (nejnižší číselná hodnota) bude přispívat k hodnotě. Totéž se stane u odchozích pravidel. Pravidlo synchronizace s nejvyšší prioritou WINS a přispívání hodnotou do připojeného adresáře.

V některých případech by pravidlo synchronizace mělo určit, jak se mají chovat další pravidla. Pro tento případ se používají některé speciální literály.

Pro pravidla příchozí synchronizace lze pomocí literálu **null** označit, že tok nemá žádnou hodnotu pro přispívání. Další pravidlo s nižší prioritou může přispívat k hodnotě. Pokud žádné pravidlo nepřispělo k hodnotě, odebere se atribut Metaverse. V případě odchozího pravidla, pokud je hodnota **null** konečnou hodnotou po zpracování všech pravidel synchronizace, je hodnota v připojeném adresáři odebrána.

Literál **AuthoritativeNull** je podobný **hodnotě null** , ale s rozdílem, že žádná pravidla s nižší prioritou nesmí přispívat k hodnotě.

Tok atributů může také používat **IgnoreThisFlow**. Je podobné hodnotě NULL v tom smyslu, že není k dispozici žádná hodnota k přispívání. Rozdílem je, že neodebere již existující hodnotu v cíli. Vypadá to, že v takovém případě tok atributu nikdy nebyl nalezen.

Tady je příklad:

Ve *službě AD-User Exchange Hybrid* se dá najít tento tok:  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
Tento výraz by měl být čten jako: Pokud se ve službě Azure AD nachází poštovní schránka uživatele, pak bude tok atributu z Azure AD do AD. Pokud ne, neprovádějte žádné toky zpátky do služby Active Directory. V takovém případě by se zachovala stávající hodnota ve službě AD.

### <a name="importedvalue"></a>ImportedValue
Funkce ImportedValue se liší od všech ostatních funkcí, protože název atributu musí být uzavřen v uvozovkách, nikoli v hranatých závorkách:  
`ImportedValue("proxyAddresses")`.

Obvykle během synchronizace atribut používá očekávanou hodnotu, i když ještě nebyl exportován nebo došlo k chybě při exportu ("horní část věže"). Příchozí synchronizace předpokládá, že atribut, který ještě nedosáhl připojeného adresáře, ho nakonec dosáhne. V některých případech je důležité synchronizovat jenom hodnotu potvrzenou připojeným adresářem ("hologramová a rozdílová importová věž").

Příklad této funkce najdete v předběžném synchronizačním pravidle *ve službě AD – uživatel společný ze systému Exchange*. V hybridním Exchangi by hodnota přidaná v Exchangi Online měla být synchronizovaná jenom v případě, že byla potvrzena, že byla hodnota exportována úspěšně:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Priorita
Když se několik pravidel synchronizace pokusí přispívat stejnou hodnotu atributu k cíli, použije se hodnota priority k určení vítěze. Pravidlo s nejvyšší prioritou, nejnižší číselnou hodnotou, přispěje k přispívání atributu v konfliktu.

![Typy sloučení](./media/concept-azure-ad-connect-sync-declarative-provisioning/precedence1.png)  

Toto řazení lze použít k definování přesnější toků atributů pro malou podmnožinu objektů. Například Přednastavená pravidla zajišťují, že atributy z povoleného účtu (**User AccountEnabled**) mají přednost před jinými účty.

Priorita může být definována mezi konektory. To umožňuje konektorům s lepšími daty přispívat hodnoty jako první.

### <a name="multiple-objects-from-the-same-connector-space"></a>Více objektů ze stejného prostoru konektoru
Pokud máte více objektů ve stejném prostoru konektoru připojeném ke stejnému objektu úložiště metaverse, je nutné změnit prioritu. Pokud je v oboru stejného pravidla synchronizace více objektů, pak modul synchronizace nebude moci určit prioritu. Je dvojznačný, který zdrojový objekt by měl přispívat do úložiště metaverse. Tato konfigurace je hlášena jako nejednoznačná, a to i v případě, že atributy ve zdroji mají stejnou hodnotu.  
![Diagram, který zobrazuje více objektů spojených se stejným objektem MV s průhledným překrytím červeného X. ](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple1.png)  

V tomto scénáři je nutné změnit rozsah pravidel synchronizace, aby zdrojové objekty měly v oboru jiná pravidla synchronizace. To umožňuje definovat různé priority.  
![Více objektů spojených se stejným objektem MV](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o jazyce výrazů v tématu [Principy deklarativních zřizovacích výrazů](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Podívejte se, jak se v tématu [Principy výchozí konfigurace](concept-azure-ad-connect-sync-default-configuration.md)používá deklarativní zřizování.
* Informace o tom, jak provést praktickou změnu pomocí deklarativního zajišťování, najdete v tématu [Postup provedení změny ve výchozí konfiguraci](how-to-connect-sync-change-the-configuration.md).
* Další informace o tom, jak uživatelé a kontakty spolupracují, najdete v tématu [Principy uživatelů a kontaktů](concept-azure-ad-connect-sync-user-and-contacts.md).

**Témata s přehledem**

* [Azure AD Connect synchronizace: pochopení a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)

**Referenční témata**

* [Azure AD Connect Sync: Reference k funkcím](reference-connect-sync-functions-reference.md)
