---
title: 'Azure AD Connect: Principy deklarativní zřizování | Microsoft Docs'
description: Vysvětluje deklarativní zřizování konfigurační model v Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: cfbb870d-be7d-47b3-ba01-9e78121f0067
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: bb6a0c16322884afba3d306c491c3cd592fc8595
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593187"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Synchronizace Azure AD Connect: Principy deklarativní zřizování
Toto téma vysvětluje konfigurační model v Azure AD Connect. Model se nazývá deklarativní zřizování a umožňuje snadno změníte konfiguraci. Celou řadu věcí, které jsou popsané v tomto tématu jsou rozšířené a není potřeba pro většinu scénářů zákazníka.

## <a name="overview"></a>Přehled
Deklarativní zřizování je zpracování objektů brzo ze zdrojového adresáře připojené a určuje, jak tento objekt a atributy by měly transformaci ze zdroje na cíl. Objekt je zpracovat v kanálu synchronizace a kanál je stejný pro příchozí a odchozí pravidla. Příchozí pravidlo je od prostoru konektoru do úložiště metaverse a odchozí pravidlo je z úložiště metaverse na prostoru konektoru.

![Synchronizace kanálu](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/sync1.png)  

Kanál má několik různých modulů. Každé z nich je zodpovědná za jeden koncept objekt synchronizace.

![Synchronizace kanálu](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/pipeline.png)  

* Zdroj zdrojového objektu
* [Obor](#scope), vyhledá všechna pravidla synchronizace, které jsou v oboru
* [Připojení k](#join), určuje vztah mezi prostoru konektoru a úložiště metaverse
* [Transformace](#transform), jak by měl být atributy transformovat vypočte a toku
* [Priorita](#precedence), vyřeší konfliktní atribut příspěvky
* Cíl, cílový objekt

## <a name="scope"></a>Rozsah
Modul obor je vyhodnocení objekt a určí, pravidla, která jsou v oboru a musí být obsažena ve zpracování. V závislosti na hodnoty atributů pro objekt jsou různé synchronizační pravidla vyhodnocena jako v oboru. Zakázaný uživatel se žádné poštovní schránky Exchange například mít různá pravidla než v případě povoleného uživatele s poštovní schránku.  
![Rozsah](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope1.png)  

Rozsah je definován jako skupiny a klauzule. Klauzulích jsou uvnitř skupiny. Logické a se používá mezi všechny klauzule ve skupině. Například (oddělení IT a země = = Dánsko). Logické nebo se používá mezi skupinami.

![Rozsah](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope2.png)  
Obor na tomto obrázku byste si měli přečíst jako (oddělení IT a země = = Dánsko) nebo (země = Švédsko). Pokud skupina 1 nebo skupinu 2 je vyhodnocena jako true, bude toto pravidlo je v oboru.

Modul oboru podporuje následující operace.

| Operace | Popis |
| --- | --- |
| ROVNÁ NOTEQUAL |Porovnat řetězec, která vyhodnotí, pokud je hodnota rovna hodnotě atributu. Více hodnot atributů najdete v části ISIN a ISNOTIN. |
| LESSTHAN LESSTHAN_OR_EQUAL |Porovnat řetězec, který vyhodnotí, jestli je hodnota menší než hodnota hodnoty v atributu. |
| OBSAHUJE, NOTCONTAINS |Porovnat řetězec, která vyhodnotí Pokud hodnotu lze nalézt někde uvnitř hodnotu v atributu. |
| STARTSWITH, NOTSTARTSWITH |Porovnat řetězec, který se vyhodnotí jako, pokud hodnota je na začátku hodnotu v atributu. |
| ENDSWITH, NOTENDSWITH |Porovnat řetězec, který se vyhodnotí jako, pokud hodnota je na konci hodnotu v atributu. |
| GREATERTHAN GREATERTHAN_OR_EQUAL |Porovnat řetězec, která vyhodnotí, pokud je hodnota vyšší než hodnota atributu. |
| ISNULL ISNOTNULL |Vyhodnotí, jestli je chybí atribut z objektu. Pokud atribut není přítomen a proto hodnotu null, pravidlo je v oboru. |
| ISIN ISNOTIN |Vyhodnotí, pokud je hodnota v definovaný atribut. Tato operace je více hodnot variantu ROVNO a NOTEQUAL. Atribut by měl být více hodnot atributů a pokud hodnotu lze najít na žádném ze hodnoty atributu, pak toto pravidlo je v oboru. |
| ISBITSET, ISNOTBITSET |Vyhodnotí, pokud je konkrétní bit nastavený. Například můžete použít k vyhodnocení bity v userAccountControl chcete zobrazit, pokud uživatel povolený nebo zakázaný. |
| ISMEMBEROF ISNOTMEMBEROF |Hodnota by měla obsahovat rozlišující název pro skupinu v prostoru konektoru. Pokud se objekt členem skupiny zadán, je pravidlo v oboru. |

## <a name="join"></a>Spojit
Připojení k modulu v kanálu synchronizace je zodpovědná za hledání vztah mezi objektem ve zdroji a objekt na cíli. Tento vztah na příchozí pravidlo, bude objekt v prostoru konektoru, hledání vztah k objektu v úložišti metaverse.  
![Připojení mezi cs a mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join1.png)  
Cílem je zobrazit, že pokud je objekt již v úložišti metaverse, vytvořené jiný konektor by měl být spojené s. Například v doménové struktuře prostředku účet by měl být uživatele z doménové struktury účtu připojené s uživatelem z doménové struktury prostředku.

Spojení se používají převážně na příchozí pravidla ke spojení objekty konektoru místo ke stejnému objektu úložiště metaverse.

Že spojení jsou definovány jako jednu nebo více skupin. Uvnitř skupiny máte klauzule. Logické a se používá mezi všechny klauzule ve skupině. Logické nebo se používá mezi skupinami. Skupiny jsou zpracovány v pořadí od shora dolů. Když jedna skupina nalezl přesně jednu shodu s objektem na cíli, se vyhodnocují žádná další pravidla spojení. Pokud nula nebo více než jeden objekt nenajde, bude pokračovat na další skupinu pravidel zpracování. Z tohoto důvodu pravidla musí být vytvořený v pořadí nejvíce explicitní první a více přibližné na konci.  
![Připojení k definici](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join2.png)  
Spojení na tomto obrázku se zpracovávají shora dolů. První synchronizace kanál uvidí, pokud je shoda s employeeID. Pokud ne, druhé pravidlo se zobrazí, pokud název účtu můžete použít ke spojení objekty. Pokud není nalezena shoda buď, je pravidlo třetí a finální více přibližné shody pomocí jméno uživatele.

Pokud byly vyhodnoceny všechna pravidla spojení a je přesně jednu shodu, **typu propojení** na **popis** stránky se používá. Pokud je tato možnost nastavena na **zřídit**, pak se vytvoří nový objekt v cíl.  
![Zřízení nebo připojení k](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join3.png)  

Objekt by měl mít pouze jedno pravidlo jedním synchronizačním s pravidly spojení v oboru. Pokud existují více pravidel synchronizace kterých byla definována spojení, dojde k chybě. Priorita se nepoužije se vyřešit konflikty spojení. Objekt musí mít připojení k pravidlo v oboru pro atributy s stejné příchozí nebo odchozí směr toku. Pokud potřebujete toku atributů příchozí i odchozí ke stejnému objektu, musí mít příchozí i pravidlo odchozí synchronizace s spojení.

Odchozí spojení má zvláštní chování při pokusu o zřízení objektu do prostoru konektoru na cílový. Atribut rozlišující název se používá první vyzkoušení zpětného spojení. Pokud v prostoru konektoru cíl s stejné rozlišující název již existuje objekt, jsou připojené objekty.

Připojení k modulu je Vyhodnocená jenom po při přechodu do oboru dojde nové pravidlo synchronizace. Pokud objekt má připojený, není to odpojování i v případě, že již nesplňuje kritéria spojení. Pokud chcete odpojte objekt, musí se synchronizační pravidlo, které připojený objekty dostala mimo rozsah.

### <a name="metaverse-delete"></a>Odstranění úložiště Metaverse
Zůstane, pokud je v oboru s jedno pravidlo synchronizace objektu úložiště metaverse **typu propojení** nastavena na **zřídit** nebo **StickyJoin**. StickyJoin se používá, pokud není povoleno zřídit nový objekt, který úložiště metaverse konektoru, ale v případě, že se má propojit, je třeba jej odstranit ve zdroji, než je odstraněn objekt úložiště metaverse.

Při odstranění objektu úložiště metaverse všechny objekty přidružené pravidlo odchozí synchronizace označen pro **zřídit** jsou označena pro odstranění.

## <a name="transformations"></a>Transformace
Transformace slouží k určení, jak by měl toku atributů ze zdroje do cíle. Tyto toky může mít jednu z následujících **toku typy**: přímo, konstanta nebo výraz. Hodnota atributu jako tok přímé tok,-je bez další transformací. Konstantní hodnotu Nastaví zadanou hodnotu. Výraz používá deklarativní zřizování jazyk výrazů k express, jak by měla být transformace. Podrobnosti o výraz jazyka najdete v [pochopení deklarativní zřizování jazyk výrazů](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) tématu.

![Zřízení nebo připojení k](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/transformations1.png)  

**Použít jednou** políčko definuje, že atribut by měl být nastavena pouze při vytvoření objektu. Například tato konfigurace slouží k nastavení počátečního hesla pro nový objekt uživatele.

### <a name="merging-attribute-values"></a>Slučování hodnoty atributu
V toky atributů je nastavení určující, pokud by měly být sloučeny více hodnot atributů z několika různých konektorů. Výchozí hodnota je **aktualizace**, což naznačuje, že by měl win synchronizační pravidlo s nejvyšší prioritou.

![Merge – typy](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/mergetype.png)  

K dispozici je také **sloučení** a **MergeCaseInsensitive**. Tyto možnosti umožňují sloučení hodnoty z různých zdrojů. Například může sloužit ke sloučení atributu proxyAddresses nebo člena z několika různých doménových struktur. Pokud použijete tuto možnost, všechna pravidla synchronizace v oboru pro objekt musí používat stejný typ sloučení. Nelze definovat **aktualizace** z jeden konektor a **sloučení** z jiné. Pokud se pokusíte, obdržíte chybu.

Rozdíl mezi **sloučení** a **MergeCaseInsensitive** je postupy zpracování duplicitními hodnotami atributů. Synchronizační modul je zajištěno, že duplicitní hodnoty nejsou vloženy do atribut target. S **MergeCaseInsensitive**, duplicitní hodnoty s pouze rozdíly v případě, že nebudete nacházet. Například byste neměli vidět obě "SMTP:bob@contoso.com"a"smtp:bob@contoso.com" v atribut target. **Sloučení** je jenom prohlížení přesné hodnoty a více hodnot tam, kde existuje jenom rozdíl v případě může být k dispozici.

Možnost **nahradit** je stejný jako **aktualizace**, ale se nepoužívá.

### <a name="control-the-attribute-flow-process"></a>Řízení procesu toku atributů
Pokud více pravidel příchozí synchronizace jsou nakonfigurovaná pro podílet se na stejný atribut úložiště metaverse, přednost se používá přednost. Pravidlo synchronizace s nejvyšší prioritou (nejnižší číselnou hodnotu) se bude podílet se hodnota. Stejné se stane pro odchozí pravidla. Synchronizace pravidla pomocí služby wins nejvyšší prioritu a přispívat hodnota, která má připojený adresář.

V některých případech místo přispívat hodnotu, pravidlo synchronizace měli určit chování ostatní pravidla. Existují některé speciální literály použít pro tento případ.

Pro příchozí pravidla synchronizace, skutečné **NULL** slouží k označení, že tok nemá žádnou hodnotu přispívat. Jiné pravidlo s nižší prioritou můžete přispívat hodnotu. Pokud žádné pravidlo podílí hodnotu, pak se odebere atribut úložiště metaverse. Odchozí pravidla Pokud **NULL** konečná hodnota je po zpracování všech pravidel synchronizace, pak se hodnota odebere v adresáři připojené.

Literálové **AuthoritativeNull** je podobná **NULL** , ale s tím rozdílem, že žádná nižší prioritu pravidla můžete přispívat hodnotu.

Tok atributů použít také **IgnoreThisFlow**. Je to podobné na hodnotu NULL v tom smyslu, že znamená, že není nic přispívat. Rozdíl je v ji neodebere již existující hodnoty na cíli. Je jako toku atributů nebylo nikdy existuje.

Zde naleznete příklad:

V *Out do AD - Exchange uživatele hybridní* naleznete následující postup:  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
Tento výraz byste si měli přečíst jako: Pokud je poštovní schránka uživatele ve službě Azure AD, pak toku atributů z Azure AD k AD. Pokud ne, není nic zpět do služby Active Directory toku. V takovém případě by se zachovat existující hodnotu v AD.

### <a name="importedvalue"></a>ImportedValue
Vzhledem k tomu, že název atributu musí být uzavřena v uvozovkách, nikoli hranaté závorky, se liší od dalších funkcí funkce ImportedValue:  
`ImportedValue("proxyAddresses")`.

Obvykle během synchronizace používá atribut očekávané hodnotě, i když nebyla dosud exportovat nebo došlo k chybě při exportu ("top věž"). Příchozí synchronizace se předpokládá, že atribut, který ještě nedosáhla připojený adresář nakonec nedosáhne. V některých případech je potřeba pouze synchronizovat hodnotu, která byla potvrzena připojený adresář ("hologram a rozdílový import věž").

Příklad této funkce lze nalézt v synchronizační pravidlo out-of-box *v ze služby Active Directory – běžné uživatele ze serveru Exchange*. V hybridní Exchange přidané systémem Exchange online pouze synchronizovat při bylo potvrzeno, že byl úspěšně exportován hodnotu:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Priorita
Při pokusu o několik pravidel synchronizace přispívat stejnou hodnotu atributu na cíl, hodnotu priority, která se používá přednost. Pravidlo s nejvyšší prioritou, nejnižší číselnou hodnotu, bude přispívat atribut v konfliktu.

![Merge – typy](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/precedence1.png)  

Toto uspořádání lze použít k definování přesnější toky atributů pro malou podmnožinu objektů. Například out z – pole pravidla ujistěte se, které atributy z aktivovaný účet (**uživatele AccountEnabled**) mají přednost před z jiné účty.

Mezi konektory lze definovat prioritu. Konektory, který umožňuje lepší daty přispívání hodnoty.

### <a name="multiple-objects-from-the-same-connector-space"></a>Více objektů ze stejné prostoru konektoru
Pokud máte několik objektů ve stejném připojený ke stejnému objektu úložiště metaverse prostoru konektoru, je třeba upravit prioritu. Pokud několik objektů v oboru stejného pravidla synchronizace, není synchronizační modul schopní určit prioritu. Ho je nejednoznačný, které zdrojový objekt musí přispívat hodnotu do úložiště metaverse. Tato konfigurace se hlásí jako nejednoznačný i v případě, že atributy ve zdroji mají stejnou hodnotu.  
![Více objektů spojeno do stejného objektu mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple1.png)  

Pro tento scénář potřebujete změnit obor pravidel synchronizace, takže zdrojové objekty mají různé synchronizační pravidla v oboru. Který umožňuje definovat jinou prioritu.  
![Více objektů spojeno do stejného objektu mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Další postup
* Další informace o jazyk výrazů v [Principy deklarativní zřizování výrazy](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
* Najdete v tématu Jak deklarativní zřizování je použité out-of-box v [Principy výchozí konfigurace](active-directory-aadconnectsync-understanding-default-configuration.md).
* Informace o tom, praktické změnit pomocí deklarativní zřizování v [jak provést změnu výchozí konfigurace](active-directory-aadconnectsync-change-the-configuration.md).
* Dál číst, jak uživatelů a kontaktů spolupracují [Principy uživatelů a kontaktů](active-directory-aadconnectsync-understanding-users-and-contacts.md).

**Témata s přehledem**

* [Synchronizace Azure AD Connect: pochopení a přizpůsobení synchronizace](active-directory-aadconnectsync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md)

**Témata odkazů**

* [Synchronizace Azure AD Connect: odkaz na funkce](active-directory-aadconnectsync-functions-reference.md)
