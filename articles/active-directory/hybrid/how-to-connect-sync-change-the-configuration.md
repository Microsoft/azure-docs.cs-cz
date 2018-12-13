---
title: 'Synchronizace Azure AD Connect: Ujistěte se, změny ve službě Azure AD Connect sync konfigurace | Dokumentace Microsoftu'
description: Provede vás k provedení změny v konfiguraci ve službě Azure AD Connect sync.
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
ms.date: 08/30/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 6579e2ced3742eb1a70ccca96b9608fc6da628ee
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53190625"
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Synchronizace Azure AD Connect: Proveďte změnu výchozí konfigurace
Cílem tohoto článku je pro vás provedou postupem provést změny ve výchozí konfiguraci ve službě Azure Active Directory (Azure AD) Connect sync. Obsahuje postup pro některé běžné scénáře. Se znalostí by měl být schopen provést jednoduché změny do vaší vlastní konfigurace na základě vlastních obchodních pravidel.

> [!WARNING]
> Pokud provedete změny výchozích pravidel synchronizace pak tyto změny budou přepsány při příštím spuštění služby Azure AD Connect se aktualizuje, výsledkem je neočekávaný a pravděpodobně synchronizace nežádoucí výsledky.
>
> Out-of-box synchronizační pravidla mají kryptografického otisku. Pokud změníte tato pravidla, je už odpovídající kryptografický otisk. V budoucnu, když se pokusíte použít nová verze služby Azure AD Connect, mohou mít problémy. Proveďte změny jen tak, jak je popsáno v tomto článku.

## <a name="synchronization-rules-editor"></a>Editor pravidel synchronizace
Editor pravidel synchronizace umožňuje zobrazit a změnit výchozí konfiguraci. Vyhledejte ji na **Start** nabídky v části **Azure AD Connect** skupiny.  
![Nabídka Start s Editor pravidel synchronizace](./media/how-to-connect-sync-change-the-configuration/startmenu2.png)

Při otevření editoru se zobrazí výchozí pravidla out-of-box.

![Editor pravidel synchronizace](./media/how-to-connect-sync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navigace v editoru
Pomocí rozevírací nabídky v horní části stránky editoru, můžete rychle najít konkrétní pravidlo. Například pokud chcete zobrazit pravidla, kde je součástí atributu proxyAddresses, můžete změnit rozevíracích takto:  
![Filtrování SRE](./media/how-to-connect-sync-change-the-configuration/filtering.png)  
Resetovat, filtrování a načíst novou konfiguraci, stiskněte klávesu F5 na klávesnici.

V pravém horním rohu **přidat nové pravidlo** tlačítko. Toto tlačítko slouží k vytvoření vlastního pravidla.

V dolní části jsou tlačítka, které působí na vybranou synchronizační pravidlo. **Upravit** a **odstranit** udělat, co očekáváte. **Export** vytvoří skript Powershellu pro opětovné vytvoření synchronizační pravidlo. Pomocí tohoto postupu můžete přesunout synchronizační pravidlo z jednoho serveru na jiný.

## <a name="create-your-first-custom-rule"></a>Vytvoření vaší první vlastní pravidlo
Výčet nejběžnějších změn se toky atributů. Data ve zdrojovém adresáři nemusí být stejný jako v Azure AD. V příkladu v této části, ujistěte se, že křestní jméno uživatele je vždy v *formát, kdy*.

### <a name="disable-the-scheduler"></a>Zakázat plánovače
[Plánovač](how-to-connect-sync-feature-scheduler.md) ve výchozím nastavení spouští každých 30 minut. Ujistěte se, že se spouští při provádění změn a řešení potíží s nová pravidla. Pokud chcete dočasně zakázat plánovač, spusťte prostředí PowerShell a spusťte `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Zakázat plánovače](./media/how-to-connect-sync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Vytvoření pravidla
1. Klikněte na tlačítko **přidat nové pravidlo**.
2. Na **popis** stránky, zadejte následující:  
   ![Příchozí pravidlo filtrování](./media/how-to-connect-sync-change-the-configuration/description2.png)  
   * **Název**: Zadejte popisný název pravidla.
   * **Popis**: Dejte některé další informace, aby někdo jiný pochopit, co je pravidlo pro.
   * **Připojený systém**: Toto je systém, ve kterém můžete najít objekt. V tomto případě vyberte **konektor služby Active Directory**.
   * **Typ objektu systému/Metaverse připojené**: Vyberte **uživatele** a **osoba**v uvedeném pořadí.
   * **Typ odkazu**: Změňte tuto hodnotu na **připojit**.
   * **Priorita**: Zadejte hodnotu, která je v systému jedinečný. Nižší číselná hodnota znamená vyšší prioritu.
   * **Značka**: Ponechte toto prázdné. Toto pole vyplní hodnotou by měl mít pouze out-of-box pravidla společnosti Microsoft.
3. Na **Scoping filtr** zadejte **givenName ISNOTNULL**.  
   ![Příchozí pravidlo filtr oborů](./media/how-to-connect-sync-change-the-configuration/scopingfilter.png)  
   V této části se používá k definování, pro které objekty má pravidlo platit. Pokud je ponecháno prázdné, pravidlo platit pro všechny objekty uživatelů. Nicméně, který bude zahrnovat konferenčních místnostech dojde, účty služeb a dalších objektů jiných osob uživatele.
4. Na **připojení pravidla** stránce, nechejte pole prázdné.
5. Na **transformace** stránce, změňte **typ toku** k **výraz**. Pro **cílový atribut**vyberte **givenName**. A pro **zdroj**, zadejte **PCase([givenName])**.
   ![Příchozí pravidla transformace](./media/how-to-connect-sync-change-the-configuration/transformations.png)  
   Synchronizační modul je velká a malá písmena pro název funkce a název atributu. Pokud zadáte něco v pořádku, zobrazí upozornění, když přidáte pravidlo. Můžete uložit a pokračovat, ale budete muset znovu otevřít a opravte pravidlo.
6. Klikněte na tlačítko **přidat** uložíte pravidlo.

Nové vlastní pravidlo bude viditelné s jinými pravidly synchronizace v systému.

### <a name="verify-the-change"></a>Ověření změny
Tato nová změna budete chtít Ujistěte se, že funguje podle očekávání a není vyvolání chyby. V závislosti na počtu objektů, které máte existují dva způsoby, jak tento krok:

- Spusťte úplnou synchronizaci pro všechny objekty.
- Spusťte ve verzi preview a úplnou synchronizaci na jeden objekt.

Otevřít **synchronizační služba** z **Start** nabídky. Kroky v této části jsou všechny v tomto nástroji.

**Úplnou synchronizaci pro všechny objekty**  

   1. Vyberte **konektory** v horní části. Identifikujte konektor, který jste změnili v předchozí části (v tomto případě Active Directory Domain Services) a vyberte ji. 
   2. Pro **akce**vyberte **spustit**.
   3. Vyberte **úplnou synchronizaci**a pak vyberte **OK**.
   ![Úplná synchronizace](./media/how-to-connect-sync-change-the-configuration/fullsync.png)  
   Objekty jsou teď aktualizovaný v úložišti metaverse. Zkontrolujte si změny zobrazením objektu v úložišti metaverse.

**Ve verzi Preview a úplnou synchronizaci na jeden objekt**  

   1. Vyberte **konektory** v horní části. Identifikujte konektor, který jste změnili v předchozí části (v tomto případě Active Directory Domain Services) a vyberte ji.
   2. Vyberte **hledání prostoru konektoru**. 
   3. Použít **oboru** vyhledání objektu, který chcete použít k testování změny. Vyberte objekt a klikněte na tlačítko **ve verzi Preview**. 
   4. Na nové obrazovce vyberte **potvrzení změn ve verzi Preview**.  
   ![Potvrzení změn ve verzi preview](./media/how-to-connect-sync-change-the-configuration/commitpreview.png)  
   Změna je nyní potvrzeny do úložiště metaverse.

**Zobrazení objektů v úložišti metaverse**  

1. Můžete si vyberte několik ukázkové objekty, abyste měli jistotu, že se očekává, že hodnota a že pravidlo použito. 
2. Vyberte **vyhledávání Metaverse** shora. Přidejte libovolný filtr, který je potřeba najít relevantní objekty. 
3. Z výsledku hledání otevření objektu. Podívejte se na hodnoty atributů a taky ověřit, v **synchronizační pravidla** sloupec, který pravidlo použije podle očekávání.  
![Vyhledávání Metaverse](./media/how-to-connect-sync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Povolte Plánovač
Pokud je všechno, co podle očekávání, můžete znovu povolit plánovač. Z prostředí PowerShell, spusťte `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Další běžné změny toku atributů
Předchozí část popisuje, jak provádět změny pro tok atributů. V této části jsou k dispozici některé další příklady. Postup pro vytvoření pravidla synchronizace je zkratka, ale kompletní postup najdete v předchozí části.

### <a name="use-an-attribute-other-than-the-default"></a>Použijte atribut jiné než výchozí
V tomto scénáři Fabrikam je použití místní abecedy křestní jméno, příjmení a zobrazovaného názvu doménové struktury. Reprezentace znaků latinky těchto atributů najdete v atributech rozšíření. Pro vytváření globálního adresáře seznamu ve službě Azure AD a Office 365, organizace chce, aby místo toho použít tyto atributy.

S výchozí konfigurací objekt z místní doménové struktuře vypadá například takto:  
![Tok atributů 1](./media/how-to-connect-sync-change-the-configuration/attributeflowjp1.png)

Pokud chcete vytvořit pravidlo s další toky atributů, postupujte takto:

1. Otevřít **Editor pravidel synchronizace** z **Start** nabídky.
2. S **příchozí** vybrané na levé straně klikněte na tlačítko **přidat nové pravidlo** tlačítko.
3. Zadejte pravidla, název a popis. Vyberte místní instancí Active Directory a typy příslušný objekt. V **typu odkazu**vyberte **připojit**. Pro **prioritu**, vyberte číslo, které nepoužívají jiné pravidlo. Pravidla out-of-box začínat 100, takže v tomto příkladu můžete použít hodnotu 50.
  ![Tok atributů 2](./media/how-to-connect-sync-change-the-configuration/attributeflowjp2.png)
4. Ponechte **Scoping filtr** prázdný. (To znamená, ho měli použít pro všechny objekty uživatelů v doménové struktuře.)
5. Ponechte **připojení pravidla** prázdný. (To znamená, dejte pravidlo out-of-box zpracovávat všechna spojení.)
6. V **transformace**, vytvoření následujících tocích:  
  ![Tok atributů 3](./media/how-to-connect-sync-change-the-configuration/attributeflowjp3.png)
7. Klikněte na tlačítko **přidat** uložíte pravidlo.
8. Přejděte na **Synchronization Service Manager**. Na **konektory**, vyberte konektor, které jste přidali pravidla. Vyberte **spustit**a pak vyberte **úplnou synchronizaci**. Úplnou synchronizaci přepočítá všechny objekty pomocí aktuálního pravidla.

Toto je výsledek stejný objekt s tohoto vlastního pravidla:  
![Tok atributů 4](./media/how-to-connect-sync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Délka atributů
Atributy řetězce jsou indexovanou ve výchozím nastavení, a maximální délka je 448 znaků. Pokud pracujete s atributy řetězce, které může obsahovat více, ujistěte se, že mají být zahrnuty následující toku atributů:  
`attributeName` <- `Left([attributeName],448)`.

### <a name="changing-the-userprincipalsuffix"></a>Změna userPrincipalSuffix
Atribut userPrincipalName ve službě Active Directory není vždy platná pro uživatele a nemusí být vhodný jako ID přihlášení. Pomocí Průvodce instalací synchronizace Azure AD Connect, můžete použít jiný atribut – například *e-mailu*. Ale v některých případech je nutné vypočítat atribut.

Například společnost Contoso má dva adresáře Azure AD, jeden pro produkční prostředí a jeden pro testování. Mají uživatelé v rámci jejich tenanta testu, chcete-li použít jinou příponu ID přihlášení:  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`.

V tomto výrazu využít všechno, co levá strana první @-sign (slova) a concatenate s řetězec pevné délky.

### <a name="convert-a-multi-value-attribute-to-single-value"></a>Vícehodnotový atribut převést na jedinou hodnotu
Některé atributy ve službě Active Directory jsou více Vážíme si toho ve schématu, i když vypadají jednou hodnotou v Active Directory Users and Computers. Příkladem je popis atributů:  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`.

V tomto výrazu, pokud atribut má hodnotu, vzít první položky (*položky*) v atributu, odebrat úvodní i koncové mezery (*Trim*) a pak nejprve 448 znaků (*doleva* ) v řetězci.

### <a name="do-not-flow-an-attribute"></a>Není toku atributu
Další informace o scénáře pro tento oddíl, naleznete v tématu [řízení procesu toku atributů](concept-azure-ad-connect-sync-declarative-provisioning.md#control-the-attribute-flow-process).

Existují dva způsoby není tok atributu. První je pomocí Průvodce instalací [odebrat vybrané atributy](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering). Tato možnost funguje, pokud jste nikdy synchronizovali atribut před. Ale pokud jste spustili synchronizaci tento atribut a později ho odebrat pomocí této funkce, přestane modul Synchronizace Správa atribut a stávající hodnoty, které jsou ponechány ve službě Azure AD.

Pokud chcete odebrat hodnotu atributu a ujistěte se, že není v budoucnu tok, třeba vytvořit vlastní pravidlo.

V tomto scénáři Fabrikam uvědomili jsme jste si, že některé atributy, které jsme synchronizace do cloudu by neměl být k dispozici. Chceme mít jistotu, že tyto atributy jsou odebrány ze služby Azure AD.  
![Atributy chybné rozšíření](./media/how-to-connect-sync-change-the-configuration/badextensionattribute.png)

1. Vytvoření nového pravidla synchronizace příchozích dat a vyplňte popis.
  ![Popisy](./media/how-to-connect-sync-change-the-configuration/syncruledescription.png)
2. Vytvořit toky atributů s **výraz** pro **typ toku** a s **AuthoritativeNull** pro **zdroj**. Literál **AuthoritativeNull** znamená, že hodnota by měla být prázdná v úložišti metaverse, i v případě, že nižší prioritu synchronizační pravidlo se pokusí vyplnit hodnoty.
  ![Transformace pro atributy rozšíření](./media/how-to-connect-sync-change-the-configuration/syncruletransformations.png)
3. Uložte pravidlo synchronizace. Spustit **synchronizační služba**, vyhledejte konektor, vyberte **spustit**a pak vyberte **úplnou synchronizaci**. Tento krok přepočítá všechny toky atributů.
4. Ověřte, že zamýšlený změny se chystáte byla exportovaná knihovnou hledání prostoru konektoru.
  ![Fázované delete](./media/how-to-connect-sync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Vytvoření pravidla pomocí prostředí PowerShell
Pomocí editoru pravidlo synchronizace funguje správně, pokud máte pouze několik změn, aby. Pokud potřebujete provést mnoho změn, prostředí PowerShell může být vhodnější. Některé pokročilé funkce, jsou dostupná jenom u prostředí PowerShell.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>Získat skript prostředí PowerShell pro pravidlo out-of-box
Zobrazit skript, který vytvořili pravidlo out-of-box, vyberte pravidlo v editoru pravidel synchronizace a klikněte na PowerShell **exportovat**. Tato akce vám skript prostředí PowerShell, který vytvořili pravidlo.

### <a name="advanced-precedence"></a>Pokročilé prioritu
Pravidla synchronizace out-of-box začínají hodnotou priority 100. Pokud máte mnoho doménových struktur a je třeba provést mnoho vlastních změn, nemusí být 99 synchronizační pravidla dostačující.

Můžete dát pokyn synchronizační modul, který chcete vložit před pravidel out-of-box další pravidla. Pokud chcete získat toto chování, postupujte takto:

1. Označí první pravidlo synchronizace out-of-box (**v od uživatele AD Join**) v editoru pravidel synchronizace a vyberte **exportovat**. Zkopírujte hodnotu identifikátoru SR.  
![Před změnou v Powershellu](./media/how-to-connect-sync-change-the-configuration/powershell1.png)  
2. Vytvořte nové pravidlo synchronizace. Editor pravidel synchronizace můžete použít k jeho vytvoření. Pravidlo exportujte do skriptu prostředí PowerShell.
3. Ve vlastnosti **PrecedenceBefore**, vložte hodnotu identifikátoru z pravidla out-of-box. Nastavte **prioritu** k **0**. Ujistěte se, že je jedinečný identifikátor atributu a že nejsou opakovaně identifikátoru GUID z jiné pravidlo. Také zajistěte, aby **ImmutableTag** není nastavena vlastnost. Tuto vlastnost měli nastavit pouze pro pravidlo out-of-box.
4. Uložte skript prostředí PowerShell a spusťte jej. Výsledkem je, že vaše vlastní pravidla je přiřazena hodnota priority 100 a všemi ostatními pravidly out-of-box jsou zvětšeny.  
![Prostředí PowerShell po změně](./media/how-to-connect-sync-change-the-configuration/powershell2.png)  

Může mít mnoho vlastních synchronizační pravidla pomocí stejné **PrecedenceBefore** hodnota v případě potřeby.

## <a name="enable-synchronization-of-usertype"></a>Povolení synchronizace hodnot UserType
Azure AD Connect podporuje synchronizaci **UserType** atribut pro **uživatele** objektů verze 1.1.524.0 nebo novější. Přesněji řečeno byly zavedeny následující změny:

- Schéma typu objektu **uživatele** na konektor služby Azure AD je rozšířeno na obsahují atribut UserType, která je typu řetězec a je jednou hodnotou.
- Schéma typu objektu **osoba** v úložišti metaverse je rozšířen o atribut UserType, která je typu řetězec a je jednou hodnotou.

Ve výchozím nastavení není atribut UserType nastavený na povolena synchronizace, protože neexistuje žádný odpovídající atribut UserType v místní službě Active Directory. Musíte ručně povolit synchronizaci. Před provedením tohoto kroku je nutné provést Všimněte si následujícího chování vynucuje Azure AD:

- Azure AD přijímá jenom dvě hodnoty pro atribut UserType nastavený na: **Člen** a **hosta**.
- Pokud atribut UserType není povolen pro synchronizaci ve službě Azure AD Connect, uživatelů Azure AD prostřednictvím synchronizace adresářů vytvořili by mají atribut UserType nastavený na **člen**.
- Azure AD nepovoluje atribut UserType na stávající služby Azure AD uživatelům změnit pomocí služby Azure AD Connect. Můžete nastavit pouze během vytváření uživatelů Azure AD.

Než povolíte synchronizaci atribut UserType, musí nejprve rozhodněte, jak atribut pochází z místní služby Active Directory. Následují nejčastější přístupů:

- Určit nepoužité místní atribut AD (například extensionAttribute1) má být použit jako zdrojový atribut. Určené místní AD atribut by měl být typu **řetězec**být jednohodnotové a obsahovat hodnotu **člen** nebo **hosta**. 

    Pokud zvolíte tuto metodu, musíte zajistit, že atribut určené se vyplní správné hodnoty pro všechny existující uživatelské objekty v místní službě Active Directory, které jsou synchronizovány do Azure AD, než povolíte synchronizaci atribut UserType nastavený na .

- Alternativně lze odvodit hodnotu pro atribut UserType z dalších vlastností. Například chcete synchronizovat všechny uživatele jako **hosta** pokud jejich místní atribut userPrincipalName AD končí část domény <em>@partners.fabrikam123.org</em>. 

    Jak už bylo zmíněno dříve, Azure AD Connect není povoleno atribut UserType na stávající služby Azure AD uživatelům změnit pomocí služby Azure AD Connect. Proto musíte zajistit, aby logika, kterou jste se rozhodli je konzistentní s jak atribut UserType nastavený na už je nakonfigurovaný pro všichni stávající uživatelé ve vašem tenantovi Azure AD.

Postup povolení synchronizace atribut UserType nastavený na lze shrnout jako:

1.  Zakázat Plánovač synchronizace a ověřte, že neexistuje žádná synchronizace probíhá.
2.  Přidejte zdrojový atribut pro místní AD Connector schématu.
3.  Přidáte UserType schématu konektor služby Azure AD.
4.  Vytvoření pravidla synchronizace příchozích dat tok hodnota atributu z místní služby Active Directory.
5.  Vytvořte pravidlo odchozí synchronizace tok hodnota atributu do služby Azure AD.
6.  Spusťte úplnou synchronizaci cyklus.
7.  Povolte Plánovač synchronizace.

>[!NOTE]
> Zbývající část se týká těchto kroků. Jsou popsány v rámci nasazení služby Azure AD s jednou doménovou strukturou topologie a bez vlastní synchronizační pravidla. Pokud máte více doménovou strukturu, vlastní synchronizační pravidla nakonfigurován, nebo mají pracovní server, budete muset odpovídajícím způsobem upravit kroky.

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Krok 1: Zakázat Plánovač synchronizace a ověřte, že není žádná probíhající synchronizace
Chcete-li zabránit nežádoucí změny export do služby Azure AD, ujistěte se, že žádná synchronizace bude probíhat, když jste uprostřed aktualizují se pravidla synchronizace. Chcete-li zakázat plánovač integrované synchronizace:

 1. Spusťte relaci Powershellu na serveru služby Azure AD Connect.
 2. Zakažte plánované synchronizaci spuštěním rutiny `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 3. Otevřete Synchronization Service Manager tak, že přejdete do **Start** > **synchronizační služba**.
 4. Přejděte **operace** kartu a potvrdit, neexistuje žádná operace se stavem *probíhá*.

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>Krok 2: Přidejte zdrojový atribut pro místní schéma AD Connector.
Ne všechny atributy služby Azure AD jsou importovány do místní AD prostoru konektoru. Přidání zdrojového atributu na seznam importovaných atributy:

 1. Přejděte **konektory** kartu v Synchronization Service Manager.
 2. Klikněte pravým tlačítkem na místní AD Connector a výběr **vlastnosti**.
 3. V místním dialogovém, přejděte **vybrat atributy** kartu.
 4. Ujistěte se, že zdrojový atribut je kontrolována v seznamu atributů.
 5. Klikněte na tlačítko **OK** uložte.
![Přidejte zdrojový atribut k místnímu schéma AD Connector.](./media/how-to-connect-sync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-to-the-azure-ad-connector-schema"></a>Krok 3: Přidat UserType schématu konektor služby Azure AD
Ve výchozím nastavení atribut UserType není importován do Azure AD Connect prostoru. Chcete-li přidat atribut UserType nastavený na seznam importovaných atributy:

 1. Přejděte **konektory** kartu v Synchronization Service Manager.
 2. Klikněte pravým tlačítkem myši **konektor služby Azure AD** a vyberte **vlastnosti**.
 3. V místním dialogovém, přejděte **vybrat atributy** kartu.
 4. Ujistěte se, že je atribut UserType nastavený na kontrolovány v seznamu atributů.
 5. Klikněte na tlačítko **OK** uložte.

![Přidejte zdrojový atribut schématu konektor služby Azure AD](./media/how-to-connect-sync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Krok 4: Vytvoření pravidla synchronizace příchozích dat tok hodnota atributu z místní služby Active Directory
Pravidlo příchozí synchronizace povoluje hodnotu atributu tok z zdrojový atribut z místní služby Active Directory do úložiště metaverse:

1. Otevřete Editor pravidel synchronizace tak, že přejdete do **Start** > **Editor pravidel synchronizace**.
2. Nastavení filtru hledání **směr** bude **příchozí**.
3. Klikněte na tlačítko **přidat nové pravidlo** tlačítko vytvořením nového příchozího pravidla.
4. V části **popis** kartu, zadejte následující konfiguraci:

    | Atribut | Hodnota | Podrobnosti |
    | --- | --- | --- |
    | Název | *Zadejte název* | Například *v ze služby AD – UserType uživatele* |
    | Popis | *Zadejte popis* |  |
    | Připojený systém | *Vyberte místní AD connector.* |  |
    | Typ objektu systému připojené | **Uživatel** |  |
    | Typ objektu úložiště Metaverse | **Osoba** |  |
    | Typ odkazu | **Spojení** |  |
    | Priorita | *Zvolte číslo v rozmezí 1 – 99* | 1 – 99 je vyhrazený pro vlastní synchronizační pravidla. Nelze vybrat hodnotu, která používá dalším synchronizačním pravidle. |

5. Přejděte na **Scoping filtr** karta a přidat **jednoho oboru filtru skupiny** s následující klauzuli:

    | Atribut | Operátor | Hodnota |
    | --- | --- | --- |
    | adminDescription | NOTSTARTWITH | Uživatel\_ |

    Filtr oborů Určuje, které místní AD objektů, že je použito toto pravidlo příchozí synchronizace. V tomto příkladu použijeme stejný filtr oborů používané *v ze služby AD – běžné uživatele* out-of-box synchronizační pravidlo, které brání použití uživatelských objektech vytvořených prostřednictvím uživatele Azure AD synchronizačního pravidla Funkce zpětného zápisu. Jste možná bude nutné upravit filtr oborů podle vašeho nasazení služby Azure AD Connect.

6. Přejděte **transformace** kartu a implementovat pravidlo požadovanou transformaci. Například, pokud jste určili nepoužité místní atribut AD (například extensionAttribute1) jako zdrojový atribut pro UserType, můžete implementovat tok s přímým přístupem atributů:

    | Typ toku | Cílový atribut | Zdroj | Použít jednou | Sloučit typu |
    | --- | --- | --- | --- | --- |
    | Přímé | UserType | extensionAttribute1 | Není zaškrtnuto | Aktualizace |

    Například budete chtít odvodit hodnotu pro atribut UserType z dalších vlastností. Například chcete synchronizovat všechny uživatele jako hosta, pokud jejich místní atribut userPrincipalName AD končí část domény <em>@partners.fabrikam123.org</em>. Můžete implementovat výrazu takto:

    | Typ toku | Cílový atribut | Zdroj | Použít jednou | Sloučit typu |
    | --- | --- | --- | --- | --- |
    | Přímé | UserType | IIf(IsPresent([userPrincipalName]),IIf(CBool(InStr(LCase([userPrincipalName]),"@partners.fabrikam123.org")=0) "Členů", "Guest"), chyba ("hodnota UserPrincipalName není k dispozici k určení UserType")) | Není zaškrtnuto | Aktualizace |

7. Klikněte na tlačítko **přidat** vytvořte příchozí pravidlo.

![Vytvoření pravidla synchronizace příchozích dat](./media/how-to-connect-sync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Krok 5: Vytvořit pravidlo odchozí synchronizace, které jsou předávány hodnotou atributu do služby Azure AD
Pravidlo odchozí synchronizace povoluje hodnotu atributu, které jsou předávány z úložiště metaverse na atribut UserType ve službě Azure AD:

1. Přejdete na Editor pravidel synchronizace.
2. Nastavení filtru hledání **směr** bude **odchozí**.
3. Klikněte na tlačítko **přidat nové pravidlo** tlačítko.
4. V části **popis** kartu, zadejte následující konfiguraci:

    | Atribut | Hodnota | Podrobnosti |
    | ----- | ------ | --- |
    | Název | *Zadejte název* | Například *na AAD – UserType uživatele* |
    | Popis | *Zadejte popis* ||
    | Připojený systém | *Vyberte konektor AAD* ||
    | Typ objektu systému připojené | **Uživatel** ||
    | Typ objektu úložiště Metaverse | **Osoba** ||
    | Typ odkazu | **Spojení** ||
    | Priorita | *Zvolte číslo v rozmezí 1 – 99* | 1 – 99 je vyhrazený pro vlastní synchronizační pravidla. Nelze vybrat hodnotu, která používá dalším synchronizačním pravidle. |

5. Přejděte na **Scoping filtr** karta a přidat **jednoho oboru filtru skupiny** pomocí dvou klauzulí:

    | Atribut | Operátor | Hodnota |
    | --- | --- | --- |
    | sourceObjectType | ROVNO | Uživatel |
    | cloudMastered | NOTEQUAL | True |

    Filtr oborů Určuje, do kterého objekty Azure AD, že je použito toto pravidlo odchozí synchronizace. V tomto příkladu použijeme stejný filtr oborů z *na AD – identita uživatele* out-of-box synchronizačního pravidla. Ta brání synchronizačního pravidla nepoužíval pro uživatelské objekty, které nejsou synchronizované z místní služby Active Directory. Jste možná bude nutné upravit filtr oborů podle vašeho nasazení služby Azure AD Connect.

6. Přejděte **transformace** kartu a implementovat následující pravidla transformace:

    | Typ toku | Cílový atribut | Zdroj | Použít jednou | Sloučit typu |
    | --- | --- | --- | --- | --- |
    | Přímé | UserType | UserType | Není zaškrtnuto | Aktualizace |

7. Klikněte na tlačítko **přidat** vytvoření odchozí pravidla.

![Vytvořit pravidlo odchozí synchronizace](./media/how-to-connect-sync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>Krok 6: Spustit úplnou synchronizaci cyklus
Obecně platí úplné synchronizační cyklus se totiž jsme přidali nové atributy služby Active Directory i Azure AD Connector schémata a zavedl vlastní synchronizační pravidla. Chcete-li ověřit změny před exportem do služby Azure AD. 

Následující kroky můžete ověřit změny při spuštění ruční kroky, které tvoří úplnou synchronizační cyklus.

1. Spuštění **úplný import** na **místní AD Connector**:

   1. Přejděte **operace** kartu v Synchronization Service Manager.
   2. Klikněte pravým tlačítkem myši **místní AD Connector** a vyberte **spustit**.
   3. V místním dialogovém okně vyberte **úplný Import** a potom klikněte na tlačítko **OK**.
   4. Počkejte na dokončení operace.

    > [!NOTE]
    > Úplný import, můžete přeskočit na místní AD Connector. Pokud zdrojový atribut je již zahrnut v seznamu importu atributy. Jinými slovy nemáte žádné změny během [krok 2: Přidejte zdrojový atribut pro místní AD Connector. schéma](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Spuštění **úplný import** na **konektor služby Azure AD**:

   1. Klikněte pravým tlačítkem myši **konektor služby Azure AD** a vyberte **spustit**.
   2. V místním dialogovém okně vyberte **úplný Import** a potom klikněte na tlačítko **OK**.
   3. Počkejte na dokončení operace.

3. Ověření změny pravidel synchronizace na existující objekt uživatele:

    Zdrojový atribut z místní služby Active Directory a typ uživatele ze služby Azure AD se naimportovaly do jejich odpovídajících prostor konektoru. Než budete pokračovat s úplnou synchronizaci, proveďte **ve verzi Preview** na stávajícího uživatele objekt v místní AD prostoru konektoru. Objekt, který jste zvolili měli zdrojový atribut naplnit.
    
    Úspěšného **ve verzi Preview** s UserType v úložišti metaverse je vhodný indikátor, že nakonfigurujete synchronizaci pravidla správně. Informace o tom, jak provést **ve verzi Preview**, přečtěte si část [zkontrolujte změnu](#verify-the-change).

4. Spuštění **úplné synchronizace** na **on-premises AD Connector**:

   1. Klikněte pravým tlačítkem myši **místní AD Connector** a vyberte **spustit**.
   2. V místním dialogovém okně vyberte **úplnou synchronizaci** a potom klikněte na tlačítko **OK**.
   3. Počkejte na dokončení operace.

5. Ověřte **čekajících exportů** do služby Azure AD:

   1. Klikněte pravým tlačítkem myši **konektor služby Azure AD** a vyberte **Search Connector Space**.
   2. V **Search Connector Space** vyskakovací dialogové okno:

      - Nastavte **oboru** k **čekající na Export**.
      - Zaškrtněte všechna tři políčka: **Přidat**, **upravit**, a **odstranit**.
      - Klikněte na tlačítko **hledání** tlačítko Zobrazit seznam objektů se změnami nelze exportovat. Přezkoumat změny pro daný objekt, dvakrát klikněte na objekt.
      - Ověřte, že jsou očekávané změny.

6. Spustit **exportovat** na **konektoru služby Azure AD**:

   1. Klikněte pravým tlačítkem myši **konektor služby Azure AD** a vyberte **spustit**.
   2. V **spuštění konektoru** zobrazení dialogu zaškrtněte volbu **exportovat** a potom klikněte na tlačítko **OK**.
   3. Počkejte, export do služby Azure AD na dokončení.

> [!NOTE]
> Tyto kroky nejsou zahrnout úplnou synchronizaci a exportovat kroky v konektoru služby Azure AD. Tyto kroky nejsou nutné, protože hodnoty atributů jsou vyplývající z místní služby Active Directory do služby Azure AD pouze.

### <a name="step-7-re-enable-the-sync-scheduler"></a>Krok 7: Povolte Plánovač synchronizace
Povolte Plánovač integrované synchronizace:

1. Spusťte relaci Powershellu.
2. Spuštěním rutiny povolte plánované synchronizaci `Set-ADSyncScheduler -SyncCycleEnabled $true`.


## <a name="next-steps"></a>Další postup
* Další informace o konfiguraci modelu v [Principy deklarativní zřizování](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Další informace o jazyk výrazů v [Principy výrazů deklarativního zřizování](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

**Témata s přehledem**

* [Synchronizace Azure AD Connect: Pochopení a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
