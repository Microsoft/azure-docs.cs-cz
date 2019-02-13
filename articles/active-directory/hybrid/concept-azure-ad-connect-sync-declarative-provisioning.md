---
title: 'Azure AD Connect: Principy deklarativní zřizování | Dokumentace Microsoftu'
description: Vysvětluje deklarativní zřizování konfigurační model ve službě Azure AD Connect.
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
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56171821"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Synchronizace Azure AD Connect: Principy deklarativního zřizování
Toto téma popisuje model konfigurace ve službě Azure AD Connect. Model se nazývá deklarativní zřizování a umožní vám provádět změny s lehkostí a elegancí konfigurace. Mnoho věcí, které jsou popsané v tomto tématu jsou rozšířené a nevyžaduje se pro většinu scénářů zákazníků.

## <a name="overview"></a>Přehled
Deklarativní zřizování zpracovává objekty pocházejí připojeném adresáři zdroje a určuje, jak objektů a atributy by měl být transformuje ze zdroje na cíl. Objekt, jsou zpracovávána v kanálu synchronizace a zřetězení příkazů je stejný pro příchozí a odchozí pravidla. Příchozí pravidlo je v prostoru konektoru do úložiště metaverse a odchozí pravidlo je v úložišti metaverse prostoru konektoru.

![Synchronizace kanálu](./media/concept-azure-ad-connect-sync-declarative-provisioning/sync1.png)  

Tento kanál obsahuje několik různých modulů. Každý z nich je zodpovědná za jeden koncept v synchronizace objektů.

![Synchronizace kanálu](./media/concept-azure-ad-connect-sync-declarative-provisioning/pipeline.png)  

* Zdroj, zdrojový objekt
* [Obor](#scope), najde všechna synchronizační pravidla, které jsou v oboru
* [Připojte se k](#join), určuje vztah mezi connector space a metaverse
* Transformace, jak by měl být atributy transformuje vypočte a flow
* [Priorita](#precedence), řeší konfliktní atribut příspěvky
* Cíl, cílový objekt

## <a name="scope"></a>Rozsah
Modul oboru se zabývá tím, objektu a určuje pravidla, která jsou v oboru a měly by být součástí zpracování. V závislosti na atributy hodnoty na objekt jsou vyhodnoceny v oboru různých synchronizační pravidla. Zakázaný uživatel se žádné poštovní schránky serveru Exchange, například mít jiná pravidla než v případě povoleného uživatele s poštovní schránku.  
![Rozsah](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope1.png)  

Rozsah je definován jako skupiny a klauzule. Klauzule jsou uvnitř skupiny. Logický operátor AND se používá mezi všechny klauzule ve skupině. Například (oddělení IT a země = = Dánsko). Logický operátor OR se používá mezi skupinami.

![Rozsah](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope2.png)  
Obor na tomto obrázku byste si měli přečíst jako (oddělení IT a země = = Dánsko) nebo (země = Švédsko). Pokud skupina 1 nebo 2. skupina je vyhodnocen na hodnotu true, pravidlo je v oboru.

Modul oboru podporuje následující operace.

| Operace | Popis |
| --- | --- |
| STEJNÉ NOTEQUAL |Porovnat řetězec, který se vyhodnotí, pokud je hodnota rovna hodnotě atributu. Více jednohodnotových atributů naleznete v tématu ISIN a ISNOTIN. |
| LESSTHAN LESSTHAN_OR_EQUAL |Porovnat řetězec, který vyhodnotí, jestli je hodnota významu méně než hodnoty v atributu. |
| OBSAHUJE, NOTCONTAINS |Porovnat řetězec, který se vyhodnotí, pokud hodnotu lze najít někde v hodnotě v atributu. |
| STARTSWITH NOTSTARTSWITH |Porovnat řetězec, který se vyhodnotí, pokud je hodnota v začátek hodnoty v atributu. |
| ENDSWITH NOTENDSWITH |Porovnat řetězec, který se vyhodnotí, pokud je hodnota na konci hodnotu atributu. |
| GREATERTHAN GREATERTHAN_OR_EQUAL |Porovnat řetězec, který se vyhodnotí, pokud je hodnota větší než hodnota v atributu. |
| ISNULL ISNOTNULL |Vyhodnotí, pokud chybí atribut z objektu. Pokud atribut není k dispozici a proto null, pravidlo je v oboru. |
| ISIN ISNOTIN |Vyhodnotí, jestli je k dispozici v definovaný atribut hodnotu. Tato operace je více Vážíme si toho variantou rovnosti a NOTEQUAL. Atribut by měl být více Vážíme si toho atribut a pokud hodnotu lze najít v některém z hodnoty atributů, klikněte pravidlo je v oboru. |
| ISBITSET, ISNOTBITSET |Vyhodnotí, jestli konkrétní bit nastaven. Například lze použít k vyhodnocení bity v atributu userAccountControl najdete v článku, pokud je uživatel povolen nebo zakázán. |
| ISMEMBEROF ISNOTMEMBEROF |Hodnota by měla obsahovat rozlišující název pro skupinu v prostoru konektoru. Pokud objekt je členem zadané skupiny, je pravidlo v oboru. |

## <a name="join"></a>Spojit
Modul spojení v kanálu synchronizace je odpovědný za vyhledání procesoru vztah mezi objektem ve zdroji a objekt do cíle. Tento vztah na příchozí pravidlo, bude objekt v prostoru konektoru hledání vztahu k objektu v úložišti metaverse.  
![Připojte se k mezi cs a mv](./media/concept-azure-ad-connect-sync-declarative-provisioning/join1.png)  
Cílem je zobrazit, že pokud objekt již v úložišti metaverse, které vytvořil jiný konektor, by měl být spojené s. Například v doménové struktuře účtu prostředku má být uživatel z doménové struktury účtu propojen s uživateli v doménové struktuře prostředku.

Spojení se používají převážně na pravidla příchozího připojení objekty konektoru místo společně do stejného objektu úložiště metaverse.

Spojení jsou definovány jako jednu nebo více skupin. Ve skupině je nutné klauzule. Logický operátor AND se používá mezi všechny klauzule ve skupině. Logický operátor OR se používá mezi skupinami. Skupiny se zpracovávají v pořadí od shora dolů. Nalezený v jedné skupině nemá přesně jednu shodu s objektem v cílovém, jsou vyhodnocovány žádná další pravidla spojení. Pokud se nula nebo více než jeden objekt nenajde, zpracování ani potom další skupiny pravidel. Z tohoto důvodu by měl být pravidla vytvořený v pořadí podle nejvíce explicitní první a na konci více přibližných shod.  
![Připojte se k definici](./media/concept-azure-ad-connect-sync-declarative-provisioning/join2.png)  
Spojení na tomto obrázku jsou zpracovávány shora dolů. Nejprve kanál synchronizace vidí, pokud je shoda s employeeID. Pokud ne, druhé pravidlo se zobrazí, pokud se název účtu je možné ke spojení objekty. Pokud to není shoda buď, je pravidlo třetí a poslední více přibližné shody s použitím jméno uživatele.

Pokud se vyhodnotily všechna pravidla spojení a je přesně jednu shodu **typu odkazu** na **popis** stránky se používá. Pokud je tato možnost nastavená na **zřízení**, pak je vytvořen nový objekt v cíli.  
![Zřízení nebo spojení](./media/concept-azure-ad-connect-sync-declarative-provisioning/join3.png)  

Objekt by měl mít jenom jedno pravidlo jedním synchronizačním pravidlům spojení v oboru. Pokud existuje více pravidel synchronizace kde je definován spojení, dojde k chybě. Priorita se nepoužívá k řešení konfliktů spojení. Objekt musí mít připojení k pravidla v oboru pro atributy, které mají stejné příchozí/odchozí směr toku. Pokud potřebujete toku atributů příchozí i odchozí do stejného objektu, musíte mít příchozí i pravidlo odchozí synchronizace s spojení.

Odchozí spojení má zvláštní chování při pokusu o zřízení objekt prostoru konektoru cíl. Atribut rozlišující název se používá první vyzkoušení zpětného spojení. Pokud je objekt v prostoru konektoru cíl se stejným rozlišující název, objekty jsou spojeny.

Připojení k modulu je vyhodnocen pouze jednou při vstupu nové pravidlo synchronizace do oboru. Když se objekt připojí, není to odpojování i v případě, že je již nejsou splněna kritéria připojení. Pokud chcete odpojování od objektu, musíte synchronizační pravidlo, které připojené objekty dostanou mimo rozsah.

### <a name="metaverse-delete"></a>Odstranit úložiště Metaverse
Objekt úložiště metaverse zůstane, dokud je jedno pravidlo synchronizace v oboru s **typu odkazu** nastavena na **zřízení** nebo **StickyJoin**. StickyJoin se používá při nemá povolení zřizovat nový objekt úložiště metaverse konektoru, ale v případě, že je připojený, musí být odstraněny ve zdroji před odstraněním objektu úložiště metaverse.

Při odstranění objektu úložiště metaverse všechny objekty přidružené pravidlo odchozí synchronizace označen pro **zřízení** jsou označená k odstranění.

## <a name="transformations"></a>Transformace
Transformace se používají k definování, jak atributy jakým způsobem se předávají ze zdroje do cíle. Toky můžete mít jednu z následujících **tok typy**: Přímo, nebo výraz konstanta. Tok s přímým přístupem, toky hodnotu atributu jako-je bez dalších transformací. Konstantní hodnota Nastaví zadanou hodnotu. Výraz používá deklarativní zřizování jazyk výrazů vyjádřit, jak by měla být transformace. Podrobnosti o výraz jazyka najdete v [Principy deklarativní zřizování jazyk výrazů](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) tématu.

![Zřízení nebo spojení](./media/concept-azure-ad-connect-sync-declarative-provisioning/transformations1.png)  

**Použít jednou** zaškrtávací políčko definuje atribut by měl být nastavena pouze při počátečním vytvoření objektu. Například tato konfigurace je možné nastavit počáteční heslo pro nový objekt uživatele.

### <a name="merging-attribute-values"></a>Slučování hodnoty atributů
V toky atributů je k dispozici nastavení k určení, pokud by měly být sloučeny více jednohodnotových atributů z několika různých konektorů. Výchozí hodnota je **aktualizace**, což znamená, že by měl win pravidlo synchronizace s nejvyšší prioritou.

![Sloučit typy](./media/concept-azure-ad-connect-sync-declarative-provisioning/mergetype.png)  

K dispozici je také **sloučit** a **MergeCaseInsensitive**. Tyto možnosti umožňují sloučení hodnot z různých zdrojů. Například jej lze použít ke sloučení atributu člena nebo proxyAddresses z několika různých doménových strukturách. Když použijete tuto možnost, musíte použít všechna synchronizační pravidla v oboru pro objekt stejného typu sloučení. Nelze definovat **aktualizace** z jednoho konektoru a **sloučit** z jiného. Pokud se pokusíte, zobrazí se chybová zpráva.

Rozdíl mezi **sloučit** a **MergeCaseInsensitive** je zpracování duplicitními hodnotami atributů. Synchronizační modul je zajištěno, že duplicitní hodnoty nejsou vložen do cílového atributu. S **MergeCaseInsensitive**, duplicitní hodnoty s pouze jedním rozdílem v případě, že nebudete mít k dispozici. Například, neměli byste se setkat i "SMTP:bob@contoso.com"a"smtp:bob@contoso.com" do cílového atributu. **Sloučit** pouze hlídá tak přesné hodnoty a více hodnot níž se nachází jenom rozdíl v případě může být k dispozici.

Možnost **nahradit** je stejný jako **aktualizace**, ale není použit.

### <a name="control-the-attribute-flow-process"></a>Řízení procesu toku atributů
Po nakonfigurování víc pravidel příchozí synchronizace přispívat na stejný atribut úložiště metaverze Priorita slouží k určení vítěze. Pravidlo synchronizace s nejvyšší prioritou (nejnižší číselnou hodnotu) se to přispívat hodnotu. Stejné dojde pro odchozí pravidla. Synchronizace pravidla s nejvyšší prioritou wins a přispívat hodnotu pro připojený adresář.

V některých případech se místo přispívat hodnotu, pravidlo synchronizace ujasnit některé věci jak ostatní pravidla by měl chovat. Existují některé speciální literály použít pro tento případ.

Pro příchozí pravidla synchronizace, literál **NULL** slouží k označení, že tok nemá žádnou hodnotu, abyste mohli přispívat. Další pravidla s nižší prioritou mohou přispět hodnotu. Pokud žádné pravidlo přispět hodnotu, atribut úložiště metaverze Odebereme. Odchozí pravidla Pokud **NULL** se konečná hodnota po všechna synchronizační pravidla, pak se hodnota odebere v připojeném adresáři.

Literál **AuthoritativeNull** je podobný **NULL** , ale s tím rozdílem, že žádná pravidla nižší priority může přispívat hodnotu.

Tok atributů, který můžete použít také **IgnoreThisFlow**. Je to podobné na hodnotu NULL v tom smyslu, která označuje, že není nutné nic přispívat. Rozdíl je, že v cílovém nezpůsobí odebrání již existující hodnoty. Je třeba tok atributů nebylo nikdy existuje.

Zde naleznete příklad:

V *na AD – hybridní Exchange uživatele* najdete následující postup:  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
Tento výraz byste si měli přečíst jako: Pokud je poštovní schránce uživatele ve službě Azure AD, tok atributů z Azure AD do AD. Pokud ne, není nic zpět do služby Active Directory tok. V takovém případě ponechejte stávající hodnotu ve službě AD.

### <a name="importedvalue"></a>ImportedValue
Funkce ImportedValue je jiný než všechny ostatní funkce, protože název atributu musí být uzavřen v uvozovkách spíše než hranaté závorky:  
`ImportedValue("proxyAddresses")`.

Obvykle během synchronizace používá atribut očekávaná hodnota i v případě, že nebyla ještě exportovat nebo došlo k chybě při exportu ("top tower"). Příchozí synchronizace se předpokládá, že atribut, který zatím nedosáhly připojený adresář nakonec nedosáhne. V některých případech je potřeba pouze synchronizovat hodnotu, která byla potvrzena připojený adresář ("hologram a rozdílový import tower").

Příklad této funkce najdete v synchronizační pravidlo out-of-box *v ze služby AD – běžné uživatele ze serveru Exchange*. V hybridní Exchange přidané systémem Exchange online pouze synchronizovat při bylo potvrzeno, že se úspěšně exportovalo hodnota:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Priorita
Při pokusu o několik pravidel synchronizace přispívat stejnou hodnotu atributu na cíl, hodnotu priority, která slouží k určení vítěze. Pravidla s nejvyšší prioritou, nejmenší číselnou hodnotu, bude přispívat atribut v konfliktu.

![Sloučit typy](./media/concept-azure-ad-connect-sync-declarative-provisioning/precedence1.png)  

Toto uspořádání slouží k definování přesnější toky atributů pro malou podmnožinu objektů. Například out sady pole – pravidla ujistěte se, které atributy z povolený účet (**uživatele AccountEnabled**) mají přednost před z jiných účtů.

Mezi konektory lze definovat prioritu. Konektory, který umožňuje s daty lepších přispívat hodnoty.

### <a name="multiple-objects-from-the-same-connector-space"></a>Více objektů ze stejné prostoru konektoru
Pokud máte několik objektů ve stejném připojen ke stejnému objektu úložiště metaverse prostoru konektoru, musí být upraveny prioritu. Pokud několik objektů v oboru stejného pravidla synchronizace, není synchronizační modul schopen určit prioritu. To je nejednoznačný, které zdrojový objekt by měl přispívat hodnotu do úložiště metaverse. Tato konfigurace je označení nejednoznačná i v případě, že atributy ve zdroji mají stejnou hodnotu.  
![Více objektů spojeno do stejného objektu mv](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple1.png)  

V tomto scénáři budete muset změnit rozsah synchronizační pravidla, takže zdrojové objekty mají různé synchronizační pravidla v oboru. Který umožňuje definovat různé priority.  
![Více objektů spojeno do stejného objektu mv](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Další postup
* Další informace o jazyk výrazů v [Principy výrazů deklarativního zřizování](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Viz jak deklarativní zřizování je použité out-of-box v [Principy výchozí konfigurace](concept-azure-ad-connect-sync-default-configuration.md).
* Informace o tom, aby praktické změnu pomocí deklarativní zřizování v [jak provést změnu výchozí konfigurace](how-to-connect-sync-change-the-configuration.md).
* Přečtěte si, jak uživatelé a kontakty spolupracují i nadále [Principy uživatelů a kontaktů](concept-azure-ad-connect-sync-user-and-contacts.md).

**Témata s přehledem**

* [Synchronizace Azure AD Connect: Pochopení a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)

**Referenční témata**

* [Synchronizace Azure AD Connect: Reference k funkcím](reference-connect-sync-functions-reference.md)
