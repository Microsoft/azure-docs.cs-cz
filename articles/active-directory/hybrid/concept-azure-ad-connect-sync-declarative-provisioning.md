---
title: 'Azure AD Connect: Principy deklarativní zřizování | Dokumenty společnosti Microsoft'
description: Vysvětluje deklarativní zřizování modelu konfigurace v Azure AD Connect.
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
ms.openlocfilehash: 543c1a6706f794b81c4f93fc6fff3a61ed3fb9e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60246317"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Synchronizace Azure AD Connect: Principy deklarativního zřizování
Toto téma vysvětluje model konfigurace v Azure AD Connect. Model se nazývá deklarativní zřizování a umožňuje snadno provést změnu konfigurace. Mnoho věcí popsaných v tomto tématu jsou pokročilé a není vyžadováno pro většinu scénářů zákazníků.

## <a name="overview"></a>Přehled
Deklarativní zřizování zpracovává objekty přicházející ze zdrojového připojeného adresáře a určuje, jak by měl být objekt a atributy transformovány ze zdroje na cíl. Objekt je zpracován v kanálu synchronizace a kanál je stejný pro příchozí a odchozí pravidla. Příchozí pravidlo je z prostoru konektoru do metaverse a odchozí pravidlo je z metaverse do prostoru konektoru.

![Kanál synchronizace](./media/concept-azure-ad-connect-sync-declarative-provisioning/sync1.png)  

Potrubí má několik různých modulů. Každý z nich je zodpovědný za jeden koncept v synchronizaci objektů.

![Kanál synchronizace](./media/concept-azure-ad-connect-sync-declarative-provisioning/pipeline.png)  

* Zdroj, Zdrojový objekt
* [Obor](#scope), Vyhledá všechna pravidla synchronizace, která jsou v oboru.
* [Spojit](#join), Určuje vztah mezi mezerou mezi spojnicí a metaverse
* Transformace, vypočítá, jak by měly být atributy transformovány a tok
* [Priorita](#precedence), Řeší konfliktní příspěvky atributů
* Cíl, cílový objekt

## <a name="scope"></a>Rozsah
Modul oboru vyhodnocuje objekt a určuje pravidla, která jsou v oboru a měla by být zahrnuta do zpracování. V závislosti na hodnotách atributů na objektu jsou vyhodnocována různá pravidla synchronizace, která mají být v oboru. Například zakázaný uživatel bez poštovní schránky serveru Exchange má jiná pravidla než povolený uživatel s poštovní schránkou.  
![Rozsah](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope1.png)  

Obor je definován jako skupiny a klauzule. Klauzule jsou uvnitř skupiny. Logický operátor AND se používá mezi všemi klauzulemi ve skupině. Například (oddělení =IT a země = Dánsko). Mezi skupinami se používá logický operátor OR.

![Rozsah](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope2.png)  
Rozsah na tomto obrázku by měl být chápán jako (oddělení = IT a země = Dánsko) NEBO (země = Švédsko). Pokud je buď skupina 1 nebo skupina 2 vyhodnocena jako true, je pravidlo v oboru.

Modul oboru podporuje následující operace.

| Operace | Popis |
| --- | --- |
| EQUAL, NOTEQUAL |Řetězec porovnat, který vyhodnotí, pokud hodnota se rovná hodnotě v atributu. Atributy s více hodnotami naleznete v tématech ISIN a ISNOTIN. |
| MÉNĚ NEŽ LESSTHAN_OR_EQUAL |Řetězec porovnat, který vyhodnocuje, pokud hodnota je menší než hodnota v atributu. |
| OBSAHUJE, NEOBSAHUJE |Řetězec porovnat, který vyhodnocuje, pokud hodnota lze nalézt někde uvnitř hodnotu v atributu. |
| ZAČÍNÁ, NOTSTARTSWITH |Řetězec porovnat, který vyhodnocuje, pokud hodnota je na začátku hodnoty v atributu. |
| ENDSWITH, NOTENDSWITH |Řetězec porovnat, který vyhodnotí, pokud hodnota je na konci hodnoty v atributu. |
| VĚTŠÍ, GREATERTHAN_OR_EQUAL |Řetězec porovnat, který vyhodnocuje, pokud hodnota je větší než hodnota v atributu. |
| ISNULL, ISNOTNULL |Vyhodnotí, pokud atribut chybí z objektu. Pokud atribut není k dispozici a proto null, pak pravidlo je v oboru. |
| ISIN, ISNOTIN |Vyhodnotí, zda je hodnota přítomna v definovaném atributu. Tato operace je vícecenná varianta EQUAL a NOTEQUAL. Atribut má být atribut s více hodnotami a pokud lze hodnotu nalézt v některé z hodnot atributu, pak je pravidlo v oboru. |
| ISBITSET, ISNOTBITSET |Vyhodnotí, zda je nastaven určitý bit. Například lze vyhodnotit bity v userAccountControl chcete-li zjistit, zda je uživatel povolen nebo zakázán. |
| ISMEMBEROF, ISNOTMEMBEROF |Hodnota by měla obsahovat DN do skupiny v prostoru konektoru. Pokud je objekt členem zadané skupiny, pravidlo je v oboru. |

## <a name="join"></a>Spojit
Modul spojení v kanálu synchronizace je zodpovědný za nalezení vztahu mezi objektem ve zdroji a objektem v cíli. U příchozího pravidla by tento vztah byl objektem v prostoru spojnice, který by našel vztah k objektu v metaverse.  
![Spojení mezi cs a mv](./media/concept-azure-ad-connect-sync-declarative-provisioning/join1.png)  
Cílem je zjistit, zda je objekt již v metaverse, vytvořené jiným konektorem, by měl být přidružen. Například v doménové struktuře prostředků účtu by měl být uživatel z doménové struktury účtu spojen s uživatelem z doménové struktury prostředků.

Spojení se používají většinou na příchozí pravidla spojit objekty prostoru spojnice dohromady ke stejnému metaverse objektu.

Spojení jsou definována jako jedna nebo více skupin. Uvnitř skupiny máte klauzule. Logický operátor AND se používá mezi všemi klauzulemi ve skupině. Mezi skupinami se používá logický operátor OR. Skupiny jsou zpracovávány v pořadí shora dolů. Pokud jedna skupina nalezla přesně jednu shodu s objektem v cíli, nebudou vyhodnocena žádná další pravidla spojení. Pokud je nalezena nula nebo více než jeden objekt, zpracování pokračuje na další skupinu pravidel. Z tohoto důvodu by měla být pravidla vytvořena v pořadí nejvíce explicitní první a více fuzzy na konci.  
![Definice spojení](./media/concept-azure-ad-connect-sync-declarative-provisioning/join2.png)  
Spojení na tomto obrázku jsou zpracována shora dolů. Nejprve kanál synchronizace zobrazí, pokud je shoda na EmployeeID. Pokud ne, druhé pravidlo vidí, pokud název účtu lze použít ke spojení objektů dohromady. Pokud to není shoda buď, třetí a poslední pravidlo je více přibližné shody pomocí jména uživatele.

Pokud byla vyhodnocena všechna pravidla spojení a neexistuje přesně jedna shoda, použije se **typ odkazu** na stránce **Popis.** Pokud je tato možnost nastavena na **provision**, pak je vytvořen nový objekt v cíli.  
![Zřízení nebo připojení](./media/concept-azure-ad-connect-sync-declarative-provisioning/join3.png)  

Objekt by měl mít pouze jedno pravidlo synchronizace s pravidly spojení v oboru. Pokud existuje více pravidel synchronizace, kde je definováno spojení, dojde k chybě. Priorita se nepoužívá k řešení konfliktů spojení. Objekt musí mít pravidlo spojení v oboru pro atributy toku se stejným příchozím a odchozím směrem. Pokud potřebujete tok atributy příchozí i odchozí na stejný objekt, musíte mít příchozí i odchozí synchronizace pravidlo s join.

Odchozí spojení má zvláštní chování při pokusu o zřízení objektu do prostoru cílové spojnice. Atribut DN se používá k prvnímu pokusu o zpětné spojení. Pokud již existuje objekt v prostoru cílové spojnice se stejným dn, objekty jsou spojeny.

Modul spojení je vyhodnocen pouze jednou, když nové pravidlo synchronizace vstoupí do oboru. Pokud je objekt připojen, není odpojen, i když kritéria spojení již není splněna. Pokud chcete odpojit objekt, pravidlo synchronizace, které se připojilo k objektům, musí přejít mimo rozsah.

### <a name="metaverse-delete"></a>Metaverse odstranit
Metaverse objekt zůstane tak dlouho, dokud je jedno pravidlo synchronizace v oboru s **typ odkazu** nastavena na **provision** nebo **StickyJoin**. StickyJoin se používá, když Connector není povoleno zřídit nový objekt metaverse, ale když se připojil, musí být odstraněny ve zdroji před odstraněním metaverse objektu.

Při odstranění metaverse objektu jsou všechny objekty přidružené k pravidlu odchozí synchronizace označené pro **provision** označeny pro odstranění.

## <a name="transformations"></a>Transformace
Transformace se používají k definování, jak by měly atributy tok ze zdroje do cíle. Toky mohou mít jeden z následujících **typů toku**: Přímé, Konstantní nebo Výraz. Přímý tok, toky hodnota atributu as-is bez další transformace. Konstantní hodnota nastaví zadanou hodnotu. Výraz používá deklarativní zřizování výraz jazyka vyjádřit, jak by měla být transformace. Podrobnosti pro jazyk výrazu lze nalézt v [pochopení deklarativní zřizování jazyka jazyka](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) tématu.

![Zřízení nebo připojení](./media/concept-azure-ad-connect-sync-declarative-provisioning/transformations1.png)  

**Zaškrtávací** políčko Použít jednou definuje, že atribut by měl být nastaven pouze při počátečním vytvoření objektu. Tuto konfiguraci lze například použít k nastavení počátečního hesla pro nový objekt uživatele.

### <a name="merging-attribute-values"></a>Sloučení hodnot atributů
V toku atributu je nastavení k určení, pokud více hodnotové atributy by měly být sloučeny z několika různých konektorů. Výchozí hodnota je **Aktualizovat**, což znamená, že pravidlo synchronizace s nejvyšší prioritou by mělo vyhrát.

![Typy sloučení](./media/concept-azure-ad-connect-sync-declarative-provisioning/mergetype.png)  

K dispozici je také **Merge** a **MergeCaseInsensitive**. Tyto možnosti umožňují sloučit hodnoty z různých zdrojů. Například jej lze použít ke sloučení atributu member nebo proxyAddresses z několika různých doménových struktur. Při použití této možnosti musí všechna pravidla synchronizace v oboru pro objekt používat stejný typ sloučení. **Aktualizovat** nelze definovat z jednoho konektoru a **sloučit** z jiného. Pokud se pokusíte, zobrazí se chyba.

Rozdíl mezi **Merge** a **MergeCaseInsensitive** je, jak zpracovat duplicitní hodnoty atributů. Synchronizační modul zajišťuje, že duplicitní hodnoty nejsou vloženy do cílového atributu. S **MergeCaseInsensitive**, duplicitní hodnoty s pouze rozdíl v případě, že nebudou k dispozici. V cílovém atributu bysteSMTP:bob@contoso.comnapříkladsmtp:bob@contoso.comneměli vidět jak " , tak " ". **Sloučení** je pouze při pohledu na přesné hodnoty a více hodnot, kde je pouze rozdíl v případě, může být přítomen.

Možnost **Nahradit** je stejná jako **aktualizace**, ale nepoužívá se.

### <a name="control-the-attribute-flow-process"></a>Řízení procesu toku atributů
Pokud je více pravidel příchozí synchronizace nakonfigurováno tak, aby přispívaly ke stejnému atributu metaverse, použije se k určení vítěze priorita. Pravidlo synchronizace s nejvyšší prioritou (nejnižší číselná hodnota) bude přispívat hodnotou. Totéž platí pro odchozí pravidla. Pravidlo synchronizace s nejvyšší prioritou vyhrává a přispívá hodnotou do připojeného adresáře.

V některých případech, spíše než přispět hodnotu, pravidlo synchronizace by měla určit, jak by se měla chovat jiná pravidla. Existují některé speciální literály používané pro tento případ.

Pro příchozí pravidla synchronizace literál **NULL** lze použít k označení, že tok nemá žádnou hodnotu přispět. Hodnota může přispět jiným pravidlem s nižší prioritou. Pokud žádné pravidlo nepřispělo hodnotou, je atribut metaverse odebrán. Pro odchozí pravidlo, pokud **null** je konečná hodnota po zpracování všech pravidel synchronizace, pak je hodnota odebrána v připojeném adresáři.

Literál **AutoritativníNull** je podobný **NULL,** ale s rozdílem, že žádná pravidla nižší priority může přispět hodnotu.

Tok atributů můžete také použít **IgnoreThisFlow**. Je podobný NULL v tom smyslu, že označuje, že není nic přispět. Rozdíl je, že neodebere již existující hodnotu v cíli. Je to jako atribut tok nikdy nebyl tam.

Zde naleznete příklad:

V *části Out to AD – hybrid User Exchange* najdete následující tok:  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
Tento výraz by měl být přečten jako: pokud je poštovní schránka uživatele umístěna ve službě Azure AD, pak tok atributz Azure AD do AD. Pokud ne, netokujte nic zpět do služby Active Directory. V takovém případě by zachovat existující hodnotu ve službě AD.

### <a name="importedvalue"></a>Importovaná hodnota
Funkce ImportedValue se liší od všech ostatních funkcí, protože název atributu musí být uzavřen v uvozovkách spíše než v hranatých závorkách:  
`ImportedValue("proxyAddresses")`.

Obvykle během synchronizace atribut používá očekávanou hodnotu, i když ještě nebyl exportován nebo byla přijata chyba během exportu ("horní část věže"). Příchozí synchronizace předpokládá, že atribut, který ještě nedosáhl připojeného adresáře, k němu nakonec dosáhne. V některých případech je důležité synchronizovat pouze hodnotu, která byla potvrzena připojeným adresářem ("hologram a delta import věž").

Příklad této funkce lze nalézt v pravidle synchronizace out-of-box *v aplikaci AD – uživatel běžný ze serveru Exchange*. V hybridní exchange hodnota přidané exchange online by měla být synchronizována pouze tehdy, pokud bylo potvrzeno, že hodnota byla úspěšně exportována:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Priorita
Pokud se několik pravidel synchronizace pokusí přispět stejnou hodnotou atributu k cíli, hodnota priority se použije k určení vítěze. Pravidlo s nejvyšší prioritou, nejnižší číselnou hodnotou, přispěje atributem v konfliktu.

![Typy sloučení](./media/concept-azure-ad-connect-sync-declarative-provisioning/precedence1.png)  

Toto pořadí lze definovat přesnější toky atributů pro malou podmnožinu objektů. Například pravidla out-of-box ujistěte se, že atributy z povoleného účtu **(User AccountEnabled**) mají přednost před jinými účty.

Prioritu lze definovat mezi konektory. To umožňuje konektory s lepší data přispívat hodnoty jako první.

### <a name="multiple-objects-from-the-same-connector-space"></a>Více objektů ze stejného prostoru spojnice
Pokud máte několik objektů ve stejném prostoru spojnice spojené se stejným metaverse objektu, priorita musí být upravena. Pokud několik objektů jsou v oboru stejné pravidlo synchronizace, pak modul synchronizace není schopen určit prioritu. Je nejednoznačné, který zdrojový objekt by měl přispět hodnotou metaverse. Tato konfigurace je hlášena jako nejednoznačné i v případě, že atributy ve zdroji mají stejnou hodnotu.  
![Více objektů spojených se stejným objektem mv](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple1.png)  

V tomto scénáři je třeba změnit rozsah pravidel synchronizace tak, aby zdrojové objekty mají různá pravidla synchronizace v oboru. To vám umožní definovat různé priority.  
![Více objektů spojených se stejným objektem mv](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o jazyku výrazu v [principu deklarativní zřizování výrazy](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Podívejte se, jak se používá deklarativní zřizování out-of-box v [principu výchozí konfigurace](concept-azure-ad-connect-sync-default-configuration.md).
* Podívejte se, jak provést praktickou změnu pomocí deklarativnízři v [jak provést změnu výchozí konfigurace](how-to-connect-sync-change-the-configuration.md).
* Pokračujte ve čtení toho, jak uživatelé a kontakty spolupracují v [aplikaci Principy uživatelů a kontaktů](concept-azure-ad-connect-sync-user-and-contacts.md).

**Přehledná témata**

* [Synchronizace služby Azure AD Connect: Principy a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)

**Referenční témata**

* [Synchronizace azure apřipojení: odkaz na funkce](reference-connect-sync-functions-reference.md)
