---
title: 'Synchronizace Azure AD Connect: provedení změn v synchronizaci Azure AD Connect v konfiguraci | Microsoft Docs'
description: Vás provede procesem jak provést změnu konfigurace v synchronizaci Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: bad1cbe0b142e146ada28f2af5d152973100e919
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34595100"
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Synchronizace Azure AD Connect: Změňte výchozí konfigurace
Účelem tohoto článku je vám ukážeme, jak změnit výchozí konfigurace v synchronizaci Azure Active Directory (Azure AD) připojit. Popisuje kroky pro některé běžné scénáře. Replikace byste měli možnost provádět jednoduché změny do vlastní konfigurace založené na vlastní obchodní pravidla.

## <a name="synchronization-rules-editor"></a>Editor pravidla synchronizace
Editor pravidla synchronizace umožňuje zobrazit a změnit výchozí konfiguraci. Můžete najít na **spustit** nabídky v části **Azure AD Connect** skupiny.  
![Nabídka Start s Editor pravidla synchronizace](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

Při otevření editoru, zobrazí se výchozí pravidla out-of-box.

![Editor pravidla synchronizace](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navigace v editoru
Rozevírací seznamy pomocí v horní části editoru, můžete rychle vyhledat konkrétní pravidlo. Například pokud chcete zobrazit pravidla, kde je zahrnuté atribut proxyAddresses, můžete změnit rozevírací seznamy takto:  
![Filtrování SRE](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png)  
Obnovit filtrování a načíst novou konfiguraci, stisknutím klávesy F5 na klávesnici.

V pravém horním rohu **přidat nové pravidlo** tlačítko. Toto tlačítko slouží k vytvoření vlastního pravidla.

V dolní části jsou tlačítka pro funguje na vybranou synchronizační pravidlo. **Upravit** a **odstranit** udělat, co očekáváte. **Export** vytvoří skript prostředí PowerShell pro opětovné vytvoření pravidla synchronizace. Pomocí tohoto postupu můžete přesunout synchronizační pravidlo z jednoho serveru na jiný.

## <a name="create-your-first-custom-rule"></a>Vytvoření vaší první vlastní pravidlo
Výčet nejběžnějších změn, jsou toky atributů. Data ve vašem adresáři zdroj nemusí být stejné jako v Azure AD. V příkladu v této části, ujistěte se, křestní jméno uživatele je vždy v *správné případ*.

### <a name="disable-the-scheduler"></a>Zakázat plánovače
[Plánovač](active-directory-aadconnectsync-feature-scheduler.md) ve výchozím nastavení spouští každých 30 minut. Ujistěte se, že se nespouští při provádění změn a řešení potíží s nové pravidel. Chcete-li dočasně zakázat plánovač, spusťte prostředí PowerShell a spusťte `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Zakázat plánovače](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Vytvoření pravidla
1. Klikněte na tlačítko **přidat nové pravidlo**.
2. Na **popis** stránky, zadejte následující:  
   ![Příchozí pravidlo filtrování](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)  
   * **Název**: Zadejte popisný název pravidla.
   * **Popis**: poskytnout některé vyjasnění, někdo pochopit, co pravidlo je.
   * **Připojený systém**: Toto je systém, ve kterém můžete objekt nalezen. V takovém případě vyberte **konektor služby Active Directory**.
   * **Typ objektu systému/Metaverse připojené**: vyberte **uživatele** a **osoba**, v uvedeném pořadí.
   * **Typ vazby**: Změňte tuto hodnotu s **připojení**.
   * **Priorita**: Zadejte hodnotu, která je v systému jedinečný. Nižší číselná hodnota znamená vyšší prioritu.
   * **Značka**: necháte prázdné. Toto pole zadá hodnotu by měl mít jenom out-of-box pravidla od společnosti Microsoft.
3. Na **Scoping filtru** zadejte **givenName ISNOTNULL**.  
   ![Příchozí pravidlo filtru pro vytváření oboru](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png)  
   V této části se používá k definování pro objektů, které pravidlo platí. Pokud je ponecháno prázdné, pravidlo by platit pro všechny uživatelské objekty. Ale, která bude zahrnovat konferenční místnosti, účty služeb a jiných jiné osoby uživatelských objektů.
4. Na **připojení pravidla** nechte pole prázdné.
5. Na **transformace** změňte **typ toku** k **výraz**. Pro **atribut Target**, vyberte **givenName**. A pro **zdroj**, zadejte **PCase([givenName])**.
   ![Příchozí pravidlo transformace](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png)  
   Synchronizační modul je velká a malá písmena pro název funkce i název atributu. Pokud zadáte něco v pořádku, když přidáte pravidlo zobrazit upozornění. Můžete uložit a pokračovat, ale budete muset znovu otevřít a opravte pravidlo.
6. Klikněte na tlačítko **přidat** uložíte pravidlo.

Nové vlastní pravidlo by měly jít vidět s jinými pravidly synchronizace v systému.

### <a name="verify-the-change"></a>Ověření změny
Pomocí této nové změny budete chtít zajistěte, aby ho pracuje dle očekávání a není aktivována případné chyby. V závislosti na počet objektů, které máte existují dva způsoby, jak provést tento krok:

- Spustíte úplnou synchronizaci pro všechny objekty.
- Spusťte úplné synchronizace a náhled na jednoho objektu.

Otevřete **synchronizační služba** z **spustit** nabídky. Postup v této části jsou v tento nástroj.

**Úplná synchronizace pro všechny objekty**  

   1. Vyberte **konektory** v horní části. Identifikovat konektor, který jste změnili v předchozím oddílu (v tomto případě Active Directory Domain Services) a vyberte jej. 
   2. Pro **akce**, vyberte **spustit**.
   3. Vyberte **úplná synchronizace**a potom vyberte **OK**.
   ![Úplná synchronizace](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png)  
   Objekty jsou nyní aktualizovat v úložišti metaverse. Ověřte změny podle objektu v úložišti metaverse.

**Verze Preview a úplnou synchronizaci na jednoho objektu.**  

   1. Vyberte **konektory** v horní části. Identifikovat konektor, který jste změnili v předchozím oddílu (v tomto případě Active Directory Domain Services) a vyberte jej.
   2. Vyberte **hledání prostoru konektoru**. 
   3. Použít **oboru** najít objekt, který chcete použít k testování změn. Vyberte objekt a klikněte na tlačítko **Preview**. 
   4. Na nové obrazovce vyberte **potvrdit Preview**.  
   ![Potvrzení verze preview](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png)  
   Tato změna se potvrdí teď do úložiště metaverse.

**Zobrazení objektu v úložišti metaverse**  

1. Vyberte několik ukázkových objektů a ujistěte se, že očekává se hodnota a že pravidlo použije. 
2. Vyberte **hledání úložiště Metaverse** shora. Přidejte všechny filtr, který budete muset najít relevantní objekty. 
3. Výsledek hledání otevřete objekt. Podívejte se na hodnoty atributu a taky ověřit v **pravidla synchronizace** sloupec, který pravidlo použije podle očekávání.  
![Vyhledávání Metaverse](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Povolit plánovače
Pokud všechno podle očekávání, můžete znovu povolit plánovače. Z prostředí PowerShell, spusťte `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Další běžné změny toku atributů
V předchozí části je popsané, jak provádět změny tok atributů. V této části najdete některé další příklady. Kroky pro vytvoření pravidla synchronizace je zkratka, ale úplné postup najdete v předchozí části.

### <a name="use-an-attribute-other-than-the-default"></a>Použít jiné než výchozí atribut
V tomto scénáři Fabrikam je doménové struktuře, kde se používá místní abecedy pro křestní jméno, příjmení a zobrazovaný název. Reprezentace znaků latinky těchto atributů najdete v rozšiřující atributy. Pro vytvoření globální adresy seznamu ve službě Azure AD a Office 365, organizace chce místo toho použijte tyto atributy.

S konfigurací výchozí objekt z místní doménové struktuře vypadat třeba takto:  
![Tok atributů 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

K vytvoření pravidla s jinými toky atributů, postupujte takto:

1. Otevřete **editoru pravidel synchronizace** z **spustit** nabídky.
2. S **příchozí** vybraný na levé straně a klikněte **přidat nové pravidlo** tlačítko.
3. Toto pravidlo zadejte název a popis. Vyberte místní instanci služby Active Directory a typy objektů relevantní. V **typu propojení**, vyberte **připojení**. Pro **přednost před**, vyberte číslo, které nepoužívají jiné pravidlo. Pravidla out-of-box začínat 100, takže hodnota 50 lze použít v tomto příkladu.
  ![Tok atributů 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
4. Nechte **Scoping filtru** prázdný. (To znamená, že ho měli použít pro všechny uživatelské objekty v doménové struktuře.)
5. Nechte **připojení pravidla** prázdný. (To znamená, umožní pravidlo out-of-box zpracovat žádné spojení.)
6. V **transformace**, vytvořte následující toky:  
  ![Tok atributů 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
7. Klikněte na tlačítko **přidat** uložíte pravidlo.
8. Přejděte na **Synchronization Service Manager**. Na **konektory**, vyberte konektor, které jste přidali pravidlo. Vyberte **spustit**a potom vyberte **úplná synchronizace**. Úplná synchronizace přepočítá všechny objekty s použitím aktuální pravidla.

Toto je výsledek pro stejný objekt s tohoto vlastního pravidla:  
![Tok atributů 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Délka atributů
Řetězec atributy jsou indexovanou ve výchozím nastavení, a maximální délka je 448 znaků. Při práci s atributy řetězce, které může obsahovat více, ujistěte se, že jste v toku atributů patří:  
`attributeName` <- `Left([attributeName],448)`.

### <a name="changing-the-userprincipalsuffix"></a>Změna userPrincipalSuffix
Atribut userPrincipalName ve službě Active Directory není vždy znám uživatelé a nemusí být vhodný jako přihlášení. Průvodce instalací synchronizace Azure AD Connect, můžete vybrat jiný atribut – například *e-mailu*. Ale v některých případech musí být atribut vypočítat.

Například společnost Contoso má dva adresáře Azure AD, jeden pro produkční prostředí a jeden pro testování. Chtějí uživatele v jejich testovacím klientem použít jinou příponu v přihlašovací ID:  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`.

V tomto výrazu trvat všechno levém prvního @-sign (Word) a zřetězení s pevnou řetězec.

### <a name="convert-a-multi-value-attribute-to-single-value"></a>Vícehodnotový atribut převést na jednu hodnotu
Některé atributy ve službě Active Directory jsou více hodnot ve schématu, i když hledají jednohodnotové v Active Directory Users and Computers. Příkladem je popis atributů:  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`.

V tomto výrazu, pokud atribut má hodnotu, proveďte první položka (*položky*) v atributu, odebrat úvodní i koncové mezery (*Trim*) a pak udržovat nejprve 448 znaků (*vlevo* ) v řetězci.

### <a name="do-not-flow-an-attribute"></a>Není toku atributu
Pozadí na scénáři pro tento oddíl, najdete v části [řídit proces toku atributů](active-directory-aadconnectsync-understanding-declarative-provisioning.md#control-the-attribute-flow-process).

Existují dva způsoby, jak není toku atributu. První je pomocí Průvodce instalací k [odebrat vybrané atributy](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Tato možnost funguje, pokud byly nikdy synchronizované atribut před. Ale pokud jste spustili synchronizaci tento atribut a později ho odebrat pomocí této funkce, přestane modul synchronizace, Správa atribut a existující hodnoty jsou ponechána ve službě Azure AD.

Pokud chcete odebrat hodnotu atributu a ujistěte se, že není v budoucnu toku, je třeba vytvořit vlastní pravidlo.

V tomto scénáři Fabrikam uvědomili jsme mít si, že některé atributy, které jsme synchronizace do cloudu by neměl být existuje. Chceme mít jistotu, že tyto atributy se odeberou z Azure AD.  
![Chybný rozšíření atributy](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

1. Vytvoření nového pravidla synchronizace příchozích dat a naplnit popis.
  ![Popisy](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
2. Vytvořit toky atributů s **výraz** pro **typ toku** a s **AuthoritativeNull** pro **zdroj**. Literálové **AuthoritativeNull** označuje, že hodnota by měla být prázdná v úložišti metaverse, i když nižší prioritu synchronizační pravidlo se pokusí zadání hodnoty.
  ![Transformace pro atributy rozšíření](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
3. Uložte pravidlo synchronizace. Spuštění **synchronizační služba**, najít konektor, vyberte **spustit**a potom vyberte **úplná synchronizace**. Tento krok přepočítá všechny toky atributů.
4. Ověřte, zda určený změny být exportovány podle hledání prostoru konektoru.
  ![Odstranění dvoufázové instalace](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Vytvoření pravidla pomocí prostředí PowerShell
Pomocí editoru pravidlo synchronizace funguje správně, pokud máte pouze několik změn, aby. Pokud potřebujete provést mnoho změn, může být prostředí PowerShell lepší volbou. Některé pokročilé funkce jsou dostupné v prostředí PowerShell jenom.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>Získat skript prostředí PowerShell pro pravidlo out-of-box
Chcete-li zobrazit PowerShell skriptu vytvořené pravidlo out-of-box, v editoru pravidel synchronizace vyberte pravidlo a klikněte na **exportovat**. Tato akce vám dává skript prostředí PowerShell, který vytvořili pravidlo.

### <a name="advanced-precedence"></a>Pokročilé priorit
Pravidla synchronizace out-of-box začínat hodnotou priority 100. Pokud máte mnoho doménových struktur a budete muset udělat mnoho vlastní změn, nemusí být 99 synchronizační pravidla dostatek.

Můžete určit, aby synchronizační modul, který chcete vložit dříve, než pravidla out-of-box dalších pravidlech. Chcete-li toto chování, postupujte takto:

1. Označit první pravidlo synchronizace out-of-box (**v z připojení uživatele AD**) v editoru pravidel synchronizace a vyberte **exportovat**. Zkopírujte hodnotu SR identifikátor.  
![Prostředí PowerShell před změnou](./media/active-directory-aadconnectsync-change-the-configuration/powershell1.png)  
2. Vytvořte nové pravidlo synchronizace. Editor pravidla synchronizace slouží k jeho vytvoření. Pravidlo exportujte do skriptu prostředí PowerShell.
3. Ve vlastnosti **PrecedenceBefore**, vložte identifikátor hodnotu z pravidla out-of-box. Nastavte **přednost** k **0**. Zajistěte, aby atribut identifikátor je jedinečný a že nejsou opakovaného použití identifikátoru GUID z jiné pravidlo. Také zkontrolujte, zda **ImmutableTag** není nastavena vlastnost. Tato vlastnost by měla být nastavit pouze pro pravidlo out-of-box.
4. Uložení skriptu prostředí PowerShell a spusťte ji. Výsledkem je, že vlastní pravidla je přiřazena přednost před hodnotu 100 a všemi ostatními pravidly out-of-box se zvýší.  
![Prostředí PowerShell po změně](./media/active-directory-aadconnectsync-change-the-configuration/powershell2.png)  

Máte mnoho vlastní synchronizační pravidla pomocí stejné **PrecedenceBefore** hodnota v případě potřeby.

## <a name="enable-synchronization-of-usertype"></a>Povolit synchronizaci UserType
Azure AD Connect podporuje synchronizaci **UserType** atribut pro **uživatele** objekty ve verzi 1.1.524.0 a novější. Přesněji řečeno byly zavedeny následující změny:

- Schéma typu objektu **uživatele** v konektor služby Azure AD je rozšířen o atribut UserType, která je typu řetězec a je jedinou hodnotu.
- Schéma typu objektu **osoba** v úložišti metaverse je rozšířen o atribut UserType, která je typu řetězec a je jedinou hodnotu.

Ve výchozím nastavení není atribut UserType nastavený na povolit pro synchronizaci, protože neexistuje žádný odpovídající atribut UserType v místní službě Active Directory. Je nutné ručně povolit synchronizaci. Před provedením tohoto kroku je nutné provést Všimněte si následujícího chování vynucováno Azure AD:

- Azure AD přijímá pouze dvě hodnoty pro atribut UserType: **člen** a **hosta**.
- Pokud atribut UserType není povolen pro synchronizaci ve službě Azure AD Connect, Azure AD Uživatelé vytvořena prostřednictvím synchronizace adresářů by mít atribut UserType nastavený na nastavena na **člen**.
- Azure AD nepovoluje atribut UserType nastavený na na stávající služby Azure AD uživatelům změnit přes Azure AD Connect. Ho lze nastavit pouze při vytváření uživatele Azure AD.

Než povolíte synchronizaci atribut UserType nastavený na, je třeba nejprve rozhodnout, jak je atribut odvozený z místní služby Active Directory. Následují nejčastější přístupy:

- Určit nepoužité místní AD atribut (například extensionAttribute1) má být použit jako zdrojový atribut. Určené místní AD atribut by měl být typu **řetězec**, být jednohodnotové a obsahovat hodnotu **člen** nebo **hosta**. 

    Pokud zvolíte tuto metodu, je třeba zajistit, že je atribut určené naplněný správnou hodnotu pro všechny existující uživatelské objekty ve službě Active Directory v místě, které jsou synchronizovány do Azure AD, než povolíte synchronizaci atribut UserType nastavený na .

- Alternativně můžete hodnotu pro atribut UserType odvozovat od dalších vlastností. Například chcete synchronizovat všechny uživatele jako **hosta** Pokud místních atribut userPrincipalName AD končí část domény *@partners.fabrikam123.org*. 

    Jak je uvedeno nahoře, Azure AD Connect na stávající služby Azure AD uživatelům změnit přes Azure AD Connect atribut UserType nastavený na nepovoluje. Proto musíte zajistit, aby logiku, kterou jste se rozhodli, je v souladu s jak atribut UserType nastavený na už je nakonfigurovaný pro všechny stávající uživatele Azure AD ve vašem klientovi.

Postup povolení synchronizace atribut UserType nastavený na jde vyhodnotit jako:

1.  Zakažte plánovače synchronizace a ověřte, že neexistuje žádná synchronizace v průběhu.
2.  Přidejte zdrojový atribut pro místní AD Connector. schéma.
3.  Přidáte UserType schématu konektoru služby Azure AD.
4.  Vytvoření pravidla synchronizace příchozích dat tok hodnota atributu z místní služby Active Directory.
5.  Vytvořte pravidlo odchozí synchronizace pro tok hodnota atributu do služby Azure AD.
6.  Spusťte úplnou synchronizaci cyklus.
7.  Povolte plánovače synchronizace.

>[!NOTE]
> Zbývající část tohoto oddílu popisuje tyto kroky. Jsou popsány v rámci nasazení služby Azure AD s jednou doménovou strukturou topologií a bez vlastní synchronizační pravidla. Pokud máte topologie s více doménovými strukturami, nakonfigurovat vlastní synchronizační pravidla, nebo mají na testovacím serveru, bude nutné upravit kroky odpovídajícím způsobem.

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Krok 1: Zakázat plánovače synchronizace a ověřte, že neexistuje žádná synchronizace v průběhu
Abyste se vyhnuli, export nezamýšleným změny do Azure AD, ujistěte se, že žádná synchronizace bude probíhat, když jste právě aktualizují se pravidla synchronizace. Postup při zakázání plánovače předdefinované synchronizace:

 1. Spusťte relaci prostředí PowerShell na server Azure AD Connect.
 2. Zakázat plánované synchronizace spuštěním rutiny `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 3. Otevřete Synchronization Service Manager tak, že přejdete na **spustit** > **synchronizační služba**.
 4. Přejděte na **operace** kartě a potvrďte neprobíhá žádná operace se stavem *v průběhu*.

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>Krok 2: Přidejte do místní zdrojový atribut schéma AD Connector.
Všechny atributy Azure AD jsou importovány do místní AD prostoru konektoru. Zdrojový atribut přidat do seznamu importované atributy:

 1. Přejděte na **konektory** kartě Synchronization Service Manager.
 2. Klikněte pravým tlačítkem na místní AD konektoru a vyberte **vlastnosti**.
 3. V dialogovém okně automaticky otevírané okno přejděte do **vybrat atributy** kartě.
 4. Ujistěte se, že zdrojový atribut je zaškrtnutí v seznamu atributů.
 5. Klikněte na tlačítko **OK** uložit.
![Přidejte zdrojový atribut místně schéma AD Connector.](./media/active-directory-aadconnectsync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-to-the-azure-ad-connector-schema"></a>Krok 3: Přidání UserType schématu Azure AD Connector.
Ve výchozím nastavení není atribut UserType nastavený na importovat do Azure AD Connect místa. Chcete-li přidat atribut UserType nastavený na seznam importovaných atributy:

 1. Přejděte na **konektory** kartě Synchronization Service Manager.
 2. Klikněte pravým tlačítkem myši **konektoru služby Azure AD** a vyberte **vlastnosti**.
 3. V dialogovém okně automaticky otevírané okno přejděte do **vybrat atributy** kartě.
 4. Ujistěte se, že je atribut UserType nastavený na zaškrtnutí v seznamu atributů.
 5. Klikněte na tlačítko **OK** uložit.

![Přidejte zdrojový atribut do schématu Azure AD Connector.](./media/active-directory-aadconnectsync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Krok 4: Vytvoření pravidla synchronizace příchozích dat tok hodnota atributu z místní služby Active Directory
Pravidla synchronizace příchozích dat umožňuje hodnota atributu, které jsou předávány z atributu zdroje z místní služby Active Directory do úložiště metaverse:

1. Otevřete Editor pravidla synchronizace přechodem na **spustit** > **editoru pravidel synchronizace**.
2. Nastavit filtr hledání **směr** být **příchozí**.
3. Klikněte **přidat nové pravidlo** tlačítko pro vytvoření nového příchozího pravidla.
4. V části **popis** kartě, zadejte následující konfiguraci:

    | Atribut | Hodnota | Podrobnosti |
    | --- | --- | --- |
    | Název | *Zadejte název* | Například *v ze služby Active Directory – UserType uživatele* |
    | Popis | *Zadejte popis* |  |
    | Připojený systém | *Vyberte místní AD connector.* |  |
    | Typ objektu systému připojené | **Uživatel** |  |
    | Typ objektu úložiště Metaverse | **Osoba** |  |
    | Typ propojení | **Spojení** |  |
    | Priorita | *Vyberte číslo mezi 1 – 99* | 1 – 99 je vyhrazený pro vlastní synchronizačního pravidla. Není vyberte hodnotu, která je používána jinou synchronizační pravidlo. |

5. Přejděte na **Scoping filtru** kartě a přidejte **jednoho oboru filtru skupiny** s následující klauzulí:

    | Atribut | Operátor | Hodnota |
    | --- | --- | --- |
    | adminDescription | NOTSTARTWITH | Uživatel\_ |

    Oboru filtru určuje, které místní AD objekty, že je použito toto pravidlo příchozí synchronizace. V tomto příkladu používáme stejné oboru filtru používány *v ze služby Active Directory – běžné uživatele* out-of-box synchronizační pravidlo, které brání synchronizační pravidlo bylo použito pro uživatelské objekty vytvořené pomocí uživatele Azure AD Funkce zpětného zápisu. Možná budete muset upravit oboru filtru, podle vašeho nasazení Azure AD Connect.

6. Přejděte na **transformace** kartě a implementovat pravidla požadované transformace. Například, pokud jste určili nepoužité místní AD atribut (například extensionAttribute1) jako zdrojový atribut pro UserType, můžete implementovat toku přímé atributů:

    | Typ toku | Cílový atribut | Zdroj | Použít jednou | Merge – typ |
    | --- | --- | --- | --- | --- |
    | Přímé | UserType | extensionAttribute1 | Nezaškrtnuto | Aktualizace |

    Jiný příklad chcete odvodit hodnotu pro atribut UserType od dalších vlastností. Například chcete synchronizovat všechny uživatele jako hosta v případě místních atribut userPrincipalName AD končí část domény *@partners.fabrikam123.org*. Můžete implementovat výraz takto:

    | Typ toku | Cílový atribut | Zdroj | Použít jednou | Merge – typ |
    | --- | --- | --- | --- | --- |
    | Přímé | UserType | IIf(IsPresent([userPrincipalName]),IIf(CBool(InStr(LCase([userPrincipalName]),"@partners.fabrikam123.org")=0), "Člen", "Guest"), chyba ("UserPrincipalName není přítomen k určení UserType")) | Nezaškrtnuto | Aktualizace |

7. Klikněte na tlačítko **přidat** k vytvoření příchozího pravidla.

![Vytvoření pravidla synchronizace příchozích dat](./media/active-directory-aadconnectsync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Krok 5: Vytvoření pravidlo odchozí synchronizace pro tok hodnota atributu do služby Azure AD
Pravidlo odchozí synchronizace umožňuje hodnota atributu, které jsou předávány z úložiště metaverse do atribut UserType nastavený na ve službě Azure AD:

1. Přejděte do editoru pravidla synchronizace.
2. Nastavit filtr hledání **směr** být **odchozí**.
3. Klikněte **přidat nové pravidlo** tlačítko.
4. V části **popis** kartě, zadejte následující konfiguraci:

    | Atribut | Hodnota | Podrobnosti |
    | ----- | ------ | --- |
    | Název | *Zadejte název* | Například *Out aad – UserType uživatele* |
    | Popis | *Zadejte popis* ||
    | Připojený systém | *Vyberte konektor AAD* ||
    | Typ objektu systému připojené | **Uživatel** ||
    | Typ objektu úložiště Metaverse | **Osoba** ||
    | Typ propojení | **Spojení** ||
    | Priorita | *Vyberte číslo mezi 1 – 99* | 1 – 99 je vyhrazený pro vlastní synchronizačního pravidla. Není vyberte hodnotu, která je používána jinou synchronizační pravidlo. |

5. Přejděte na **Scoping filtru** kartě a přidejte **jednoho oboru filtru skupiny** s klauzulemi dva:

    | Atribut | Operátor | Hodnota |
    | --- | --- | --- |
    | sourceObjectType | ROVNÁ | Uživatel |
    | cloudMastered | NOTEQUAL | True |

    Oboru filtru určuje, do které objekty Azure AD, že je použito toto pravidlo odchozí synchronizace. V tomto příkladu používáme stejné oboru filtru z *na AD – identitu uživatele* out-of-box synchronizační pravidlo. Pravidlo synchronizace zabrání bylo použito pro uživatelské objekty, které nejsou synchronizovány z místní služby Active Directory. Možná budete muset upravit oboru filtru, podle vašeho nasazení Azure AD Connect.

6. Přejděte na **transformace** kartě a implementovat následující pravidla transformace:

    | Typ toku | Cílový atribut | Zdroj | Použít jednou | Merge – typ |
    | --- | --- | --- | --- | --- |
    | Přímé | UserType | UserType | Nezaškrtnuto | Aktualizace |

7. Klikněte na tlačítko **přidat** vytvoření odchozí pravidla.

![Vytvořit pravidlo odchozí synchronizace](./media/active-directory-aadconnectsync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>Krok 6: Spusťte úplnou synchronizaci cyklus
Obecně platí je požadované cyklus úplná synchronizace, protože jsme přidali nové atributy služby Active Directory i Azure AD Connector schémata a zavedená vlastní synchronizační pravidla. Chcete-li ověřit změny před exportováním je do Azure AD. 

Chcete-li ověřit změny při spuštění ručně kroky, které tvoří cyklus úplné synchronizace můžete použít následující kroky.

1. Spuštění **úplný import** na **místní AD Connector**:

   1. Přejděte na **Operations** kartě Synchronization Service Manager.
   2. Klikněte pravým tlačítkem myši **místní AD Connector** a vyberte **spustit**.
   3. V dialogovém okně automaticky otevírané okno Vyberte **úplný Import** a pak klikněte na **OK**.
   4. Počkejte na dokončení operace.

    > [!NOTE]
    > Úplný import, můžete přeskočit na místní AD Connector. Pokud zdrojový atribut je již zahrnut v seznamu importu atributy. Jinými slovy, můžete neměl žádné změny během [krok 2: Přidání zdrojového atributu do místní AD Connector. schéma](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Spuštění **úplný import** na **konektoru služby Azure AD**:

   1. Klikněte pravým tlačítkem myši **konektoru služby Azure AD** a vyberte **spustit**.
   2. V dialogovém okně automaticky otevírané okno Vyberte **úplný Import** a pak klikněte na **OK**.
   3. Počkejte na dokončení operace.

3. Ověření změny pravidel synchronizace na existující objekt uživatele:

    Zdrojový atribut z místního služby Active Directory a UserType z Azure AD byly naimportovány do svých příslušných prostor konektoru. Před pokračováním úplnou synchronizaci, proveďte **Preview** na stávajícího uživatele objekt v místní AD prostoru konektoru. Objekt, který jste zvolili měli zdrojový atribut naplněno.
    
    Úspěšně **Preview** UserType vložené do úložiště metaverse je dobré indikátoru, které jste nakonfigurovali synchronizaci pravidla správně. Informace o tom, jak provést **Preview**, naleznete v části [změnu ověřit](#verify-the-change).

4. Spuštění **úplné synchronizace** na **on-premises AD Connector**:

   1. Klikněte pravým tlačítkem myši **místní AD Connector** a vyberte **spustit**.
   2. V dialogovém okně automaticky otevírané okno Vyberte **úplná synchronizace** a pak klikněte na **OK**.
   3. Počkejte na dokončení operace.

5. Ověřte **čekajících exportů** do služby Azure AD:

   1. Klikněte pravým tlačítkem myši **konektoru služby Azure AD** a vyberte **hledání prostoru konektoru**.
   2. V **hledání prostoru konektoru** dialogové okno automaticky otevírané okno:

      - Nastavit **oboru** k **čekající na vyřízení Export**.
      - Zaškrtněte všechny tři políčka: **přidat**, **upravit**, a **odstranit**.
      - Klikněte **vyhledávání** tlačítko získat seznam objektů se změnami pro export. Chcete-li zkontrolovat změny pro daný objekt, dvakrát klikněte na objekt.
      - Ověřte, že se očekává změny.

6. Spustit **exportovat** na **konektoru služby Azure AD**:

   1. Klikněte pravým tlačítkem myši **konektoru služby Azure AD** a vyberte **spustit**.
   2. V **spustit konektor** automaticky otevírané okno dialogové okno, vyberte **exportovat** a pak klikněte na **OK**.
   3. Počkejte, než pro export do Azure AD na dokončení.

> [!NOTE]
> Takto nezadávejte zahrnují úplná synchronizace a exportovat kroky na konektor služby Azure AD. Tyto kroky nejsou požadované, protože jsou hodnoty atributu odesílaných z místní služby Active Directory do Azure AD pouze.

### <a name="step-7-re-enable-the-sync-scheduler"></a>Krok 7: Opětovné povolení plánovače synchronizace
Znovu povolte plánovače předdefinované synchronizace:

1. Spusťte relaci prostředí PowerShell.
2. Znovu povolit spuštěním rutiny plánované synchronizace `Set-ADSyncScheduler -SyncCycleEnabled $true`.


## <a name="next-steps"></a>Další postup
* Další informace o konfiguraci modelu v [Principy deklarativní zřizování](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* Další informace o jazyk výrazů v [Principy deklarativní zřizování výrazy](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

**Témata s přehledem**

* [Synchronizace Azure AD Connect: pochopení a přizpůsobení synchronizace](active-directory-aadconnectsync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md)
