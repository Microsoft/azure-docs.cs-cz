---
title: 'Synchronizace Azure AD Connect: Změna konfigurace v synchronizaci Azure AD Connect'
description: Provede vás, jak provést změnu konfigurace v synchronizaci Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d77882817934d5ad98f16965aeb9dc246931c495
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261161"
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Synchronizace azure ad připojení: změna výchozí konfigurace
Účelem tohoto článku je vás provede, jak provést změny výchozí konfigurace v Azure Active Directory (Azure AD) Connect synchronizace. Poskytuje kroky pro některé běžné scénáře. S těmito znalostmi byste měli být schopni provádět jednoduché změny ve vlastní konfiguraci na základě vlastních obchodních pravidel.

> [!WARNING]
> Pokud provedete změny výchozípravidla synchronizace out-of-box pak tyto změny budou přepsány při příští aktualizaci Azure AD Connect, výsledkem jsou neočekávané a pravděpodobně nežádoucí výsledky synchronizace.
>
> Výchozí pravidla synchronizace out-of-box mají kryptografický otisk. Pokud provedete změnu těchto pravidel, kryptografický otisk již nebude shodovat. Můžete mít problémy v budoucnu při pokusu o použití nové verze Azure AD Connect. Proveďte pouze změny tak, jak je popsáno v tomto článku.

## <a name="synchronization-rules-editor"></a>Editor pravidel synchronizace
Editor pravidel synchronizace se používá k zobrazení a změně výchozí konfigurace. Najdete ji v nabídce **Start** ve skupině **Azure AD Connect.**  
![Nabídka Start s Editorem pravidel synchronizace](./media/how-to-connect-sync-change-the-configuration/startmenu2.png)

Při otevření editoru se zobrazí výchozí pravidla out-of-box.

![Editor pravidel synchronizace](./media/how-to-connect-sync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navigace v editoru
Pomocí rozevíracích sad v horní části editoru můžete rychle najít konkrétní pravidlo. Chcete-li například zobrazit pravidla, kde je zahrnut atribut proxyAddresses, můžete změnit rozevírací řád na následující:  
![Filtrování SRE](./media/how-to-connect-sync-change-the-configuration/filtering.png)  
Chcete-li obnovit filtrování a načíst novou konfiguraci, stiskněte klávesu F5 na klávesnici.

Vpravém horním horním je tlačítko **Přidat nové pravidlo.** Toto tlačítko slouží k vytvoření vlastního pravidla.

V dolní části jsou tlačítka pro jednání s vybraným pravidlem synchronizace. **Upravte** a **odstraňte** to, co očekáváte. **Export** vytvoří skript prostředí PowerShell pro opětovné vytvoření pravidla synchronizace. Pomocí tohoto postupu můžete přesunout pravidlo synchronizace z jednoho serveru na jiný.

## <a name="create-your-first-custom-rule"></a>Vytvoření prvního vlastního pravidla
Nejběžnější změny jsou toky atributů. Data ve zdrojovém adresáři nemusí být stejná jako ve službě Azure AD. V příkladu v této části se ujistěte, že křestní jméno uživatele je vždy ve *správném případě*.

### <a name="disable-the-scheduler"></a>Zakázání plánovače
[Plánovač](how-to-connect-sync-feature-scheduler.md) běží ve výchozím nastavení každých 30 minut. Ujistěte se, že se nespouští, když provádíte změny a odstraňujete nové předpisy. Chcete-li plánovač dočasně zakázat, `Set-ADSyncScheduler -SyncCycleEnabled $false`spusťte prostředí PowerShell a spusťte program .

![Zakázání plánovače](./media/how-to-connect-sync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Vytvoření pravidla
1. Klepněte na **tlačítko Přidat nové pravidlo**.
2. Na stránce **Popis** zadejte následující:  
   ![Filtrování příchozích pravidel](./media/how-to-connect-sync-change-the-configuration/description2.png)  
   * **Název**: Pojmenujte pravidlo popisným názvem.
   * **Popis**: Dejte nějaké vysvětlení, takže někdo jiný může pochopit, co pravidlo je pro.
   * **Připojený systém**: Jedná se o systém, ve kterém lze objekt nalézt. V takovém případě vyberte **položku Active Directory Connector**.
   * **Typ připojeného systému/metaverse:** Vyberte **uživatele** a **osobu**, resp.
   * **Typ odkazu**: Změňte tuto hodnotu na **Připojit se**.
   * **Priorita**: Zadejte hodnotu, která je v systému jedinečná. Nižší číselná hodnota označuje vyšší prioritu.
   * **Značka**: Nechte to prázdné. Pouze out-of-box pravidla od společnosti Microsoft by měl mít toto pole naplněný hodnotou.
3. Na stránce **filtru oborů** zadejte **givenName ISNOTNULL**.  
   ![Filtr oboru příchozího pravidla](./media/how-to-connect-sync-change-the-configuration/scopingfilter.png)  
   Tato část se používá k definování objektů, které by se mělo pravidlo použít. Pokud je ponechána prázdná, pravidlo by se vztahovalo na všechny objekty uživatele. To by však zahrnovalo konferenční místnosti, účty služeb a další uživatelské objekty, které nejsou uživateli.
4. Na stránce **Pravidla spojení** ponechte pole prázdné.
5. Na stránce **Transformace** změňte **FlowType** na **Expression**. V **pole Cílový atribut**vyberte **položku givenName**. A pro **zdroj**zadejte **PCase([givenName])**.
   ![Transformace příchozích pravidel](./media/how-to-connect-sync-change-the-configuration/transformations.png)  
   Synchronizační modul rozlišuje malá a velká písmena pro název funkce i název atributu. Pokud zadáte něco špatně, zobrazí se upozornění při přidání pravidla. Můžete uložit a pokračovat, ale je třeba znovu otevřít a opravit pravidlo.
6. Kliknutím na **Přidat** pravidlo uložte.

Nové vlastní pravidlo by mělo být viditelné s ostatními pravidly synchronizace v systému.

### <a name="verify-the-change"></a>Ověření změny
S touto novou změnou se chcete ujistit, že funguje podle očekávání a nevyvolává žádné chyby. V závislosti na počtu objektů, které máte, existují dva způsoby, jak provést tento krok:

- Spusťte úplnou synchronizaci u všech objektů.
- Spusťte náhled a úplnou synchronizaci u jednoho objektu.

Otevřete **službu synchronizace** z nabídky **Start.** Kroky v této části jsou všechny v tomto nástroji.

**Úplná synchronizace u všech objektů**  

   1. Nahoře vyberte **Konektory.** Identifikujte konektor, který jste změnili v předchozí části (v tomto případě službu Active Directory Domain Services), a vyberte ji. 
   2. V **popřípadě Akce**vyberte **spustit**.
   3. Vyberte **Možnost Úplná synchronizace**a pak vyberte **OK**.
   ![Úplná synchronizace](./media/how-to-connect-sync-change-the-configuration/fullsync.png)  
   Objekty jsou nyní aktualizovány v metaverse. Ověřte změny pohledem na objekt v metaverse.

**Náhled a úplná synchronizace u jednoho objektu**  

   1. Nahoře vyberte **Konektory.** Identifikujte konektor, který jste změnili v předchozí části (v tomto případě službu Active Directory Domain Services), a vyberte ji.
   2. Vyberte **možnost Hledat mezeru v spojnici**. 
   3. Pomocí **scope** vyhledejte objekt, který chcete použít k testování změny. Vyberte objekt a klepněte na **náhled**. 
   4. Na nové obrazovce vyberte **Náhled potvrzení**.  
   ![Náhled potvrzení](./media/how-to-connect-sync-change-the-configuration/commitpreview.png)  
   Změna je nyní potvrzena metaverse.

**Zobrazení objektu v metaverse**  

1. Vyberte několik ukázkových objektů, abyste se ujistili, že je očekávaná hodnota a že pravidlo bylo použito. 
2. V horní části vyberte **Metaverse Search.** Přidejte filtr, který potřebujete k vyhledání příslušných objektů. 
3. Z výsledku hledání otevřete objekt. Podívejte se na hodnoty atributů a také ověřte ve **sloupci Pravidla synchronizace,** že pravidlo bylo použito podle očekávání.  
![Vyhledávání Metaverse](./media/how-to-connect-sync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Povolení plánovače
Pokud je vše podle očekávání, můžete znovu povolit plánovač. Z prostředí PowerShell spusťte `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Další běžné změny toku atributů
Předchozí část popisuje, jak provést změny toku atributů. V této části jsou uvedeny některé další příklady. Postup vytvoření pravidla synchronizace je zkrácen, ale úplné kroky najdete v předchozí části.

### <a name="use-an-attribute-other-than-the-default"></a>Použití jiného než výchozího atributu
V tomto scénáři Fabrikam je doménová struktura, kde místní abeceda se používá pro křestní jméno, příjmení a zobrazované jméno. Latinský znak reprezentace těchto atributů lze nalézt v atributy rozšíření. Pro vytváření globálního seznamu adres ve službě Azure AD a Office 365 chce organizace místo toho použít tyto atributy.

Při výchozí konfiguraci vypadá objekt z místní doménové struktury takto:  
![Tok atributu 1](./media/how-to-connect-sync-change-the-configuration/attributeflowjp1.png)

Chcete-li vytvořit pravidlo s jinými toky atributů, postupujte takto:

1. Otevřete **Editor pravidel synchronizace** z nabídky **Start.**
2. Pokud **je příchozí** stále vybraná vlevo, klikněte na tlačítko Přidat nové **pravidlo.**
3. Pojmenujte a popis pravidla. Vyberte místní instanci služby Active Directory a příslušné typy objektů. V **okně Typ propojení**vyberte Připojit **.** V **případě prioritvyberte**číslo, které není použito jiným pravidlem. Pravidla out-of-box začínají 100, takže hodnota 50 lze použít v tomto příkladu.
  ![Tok atributu 2](./media/how-to-connect-sync-change-the-configuration/attributeflowjp2.png)
4. Ponechte **filtr oboru** prázdný. (To znamená, že by se měl vztahovat na všechny objekty uživatele v doménové struktuře.)
5. Ponechat **pravidla spojení** prázdná. (To znamená, že pravidlo out-of-box zpracuje všechna spojení.)
6. V **transformace**, vytvořte následující toky:  
  ![Tok atributu 3](./media/how-to-connect-sync-change-the-configuration/attributeflowjp3.png)
7. Kliknutím na **Přidat** pravidlo uložte.
8. Přejděte na **správce služeb synchronizace**. V **části Konektory**vyberte spojnici, do které jste pravidlo přidali. Vyberte **Spustit**a pak vyberte **Úplná synchronizace**. Úplná synchronizace přepočítá všechny objekty pomocí aktuálních pravidel.

Toto je výsledek pro stejný objekt s tímto vlastním pravidlem:  
![Tok atributu 4](./media/how-to-connect-sync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Délka atributů
Atributy řetězce jsou ve výchozím nastavení indexovatelné a maximální délka je 448 znaků. Pokud pracujete s atributy řetězce, které mohou obsahovat více, nezapomeňte do toku atributů zahrnout následující:  
`attributeName` <- `Left([attributeName],448)`.

### <a name="changing-the-userprincipalsuffix"></a>Změna uživatelské hospo-uchvátko
Atribut userPrincipalName ve službě Active Directory není vždy znám uživateli a nemusí být vhodný jako přihlašovací ID. Pomocí průvodce instalací synchronizace služby Azure AD Connect můžete zvolit jiný atribut – například *poštu*. Ale v některých případech musí být atribut vypočten.

Například společnost Contoso má dva adresáře Azure AD, jeden pro produkční prostředí a jeden pro testování. Chtějí, aby uživatelé ve svém testovacím tenantovi používali jinou příponu v přihlašovacím ID:  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`.

V tomto výrazu vezměte vše, co zbylo z prvního @-sign (Word) a zřetězit s pevným řetězcem.

### <a name="convert-a-multi-value-attribute-to-single-value"></a>Převod atributu s více hodnotami na jednu hodnotu
Některé atributy ve službě Active Directory mají ve schématu více hodnot, přestože v uživatelích a počítačích služby Active Directory vypadají s jednou hodnotou. Příkladem je atribut description:  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`.

V tomto výrazu, pokud má atribut hodnotu, vezměte první položku (*Položku*) v atributu, odeberte úvodní a koncové mezery (*Trim*) a ponechte prvních 448 znaků (*vlevo*) v řetězci.

### <a name="do-not-flow-an-attribute"></a>Netokat atribut
Pozadí scénáře pro tuto část naleznete v [tématu Řízení procesu toku atributů](concept-azure-ad-connect-sync-declarative-provisioning.md#control-the-attribute-flow-process).

Existují dva způsoby, jak není toku atribut. Prvním z prvních je odebrání [vybraných atributů](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering)pomocí Průvodce instalací . Tato možnost funguje, pokud jste atribut nikdy předtím nesynchronizovali. Pokud jste však začali tento atribut synchronizovat a později jej odebrat pomocí této funkce, synchronizační modul přestane spravovat atribut a existující hodnoty zůstanou ve službě Azure AD.

Pokud chcete odebrat hodnotu atributu a ujistěte se, že tone v budoucnu, je třeba vytvořit vlastní pravidlo.

V tomto scénáři Fabrikam jsme si uvědomili, že některé atributy, které synchronizujeme do cloudu, by tam neměly být. Chceme se ujistit, že tyto atributy jsou odebrány z Azure AD.  
![Chybné atributy rozšíření](./media/how-to-connect-sync-change-the-configuration/badextensionattribute.png)

1. Vytvořte nové pravidlo příchozí synchronizace a naplňte popis.
  ![Popisy](./media/how-to-connect-sync-change-the-configuration/syncruledescription.png)
2. Vytvořte toky atributů s **expressionpro** **FlowType** a s **autoritativnínull** pro **zdroj**. Literál **AutoritativníNull** označuje, že hodnota by měla být prázdná v metaverse, i v případě, že pravidlo synchronizace s nižší prioritou se pokusí naplnit hodnotu.
  ![Transformace pro atributy rozšíření](./media/how-to-connect-sync-change-the-configuration/syncruletransformations.png)
3. Uložte pravidlo synchronizace. Spusťte **synchronizační službu**, vyhledejte konektor, vyberte **Spustit**a pak vyberte **Možnost Úplná synchronizace**. Tento krok přepočítá všechny toky atributů.
4. Ověřte, zda se zamýšlené změny chystají exportovat, a to tak, že prohledáte prostor konektoru.
  ![Postupné odstranění](./media/how-to-connect-sync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Vytváření pravidel pomocí PowerShellu
Použití editoru pravidel synchronizace funguje dobře, když máte k tomu jen několik změn. Pokud potřebujete provést mnoho změn, powershell může být lepší volbou. Některé pokročilé funkce jsou k dispozici pouze v prostředí PowerShell.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>Získání skriptu Prostředí PowerShell pro pravidlo out-of-box
Pokud chcete zobrazit skript prostředí PowerShell, který vytvořil pravidlo out-of-box, vyberte pravidlo v editoru pravidel synchronizace a klepněte na tlačítko **Exportovat**. Tato akce poskytuje skript prostředí PowerShell, který vytvořil pravidlo.

### <a name="advanced-precedence"></a>Rozšířená priorita
Pravidla synchronizace předboxu začínají hodnotou priority 100. Pokud máte mnoho doménových struktur a potřebujete provést mnoho vlastních změn, nemusí stačit pravidla synchronizace 99.

Můžete instruovat synchronizační modul, že chcete před pravidla out-of-box vložit další pravidla. Chcete-li získat toto chování, postupujte takto:

1. Označte první pravidlo synchronizace předem (**In z připojení uživatele služby AD**) v editoru pravidel synchronizace a vyberte **Exportovat**. Zkopírujte hodnotu identifikátoru SR.  
![Prostředí PowerShell před změnou](./media/how-to-connect-sync-change-the-configuration/powershell1.png)  
2. Vytvořte nové pravidlo synchronizace. K jeho vytvoření můžete použít editor pravidel synchronizace. Exportujte pravidlo do skriptu prostředí PowerShell.
3. Ve vlastnosti **Priorita Před**vložte hodnotu Identifikátor z pravidla out-of-box. Nastavte **prioritu** na **0**. Ujistěte se, že atribut Identifier je jedinečný a že nepoužíváte identifikátor GUID z jiného pravidla. Také se ujistěte, že vlastnost **ImmutableTag** není nastavena. Tato vlastnost by měla být nastavena pouze pro pravidlo out-of-box.
4. Uložte skript Prostředí PowerShell a spusťte ho. Výsledkem je, že vlastní pravidlo je přiřazena hodnota priority 100 a všechna ostatní pravidla out-of-box jsou přírůstky.  
![Prostředí PowerShell po změně](./media/how-to-connect-sync-change-the-configuration/powershell2.png)  

Můžete mít mnoho vlastní pravidla synchronizace pomocí stejné **PriorityBefore** hodnotu v případě potřeby.

## <a name="enable-synchronization-of-usertype"></a>Povolit synchronizaci typu UserType
Azure AD Connect podporuje synchronizaci atributu **UserType** pro **uživatelské** objekty ve verzi 1.1.524.0 a novější. Konkrétněbyly zavedeny tyto změny:

- Schéma typu objektu **Uživatel** v konektoru Azure AD je rozšířeno tak, aby zahrnovalo atribut UserType, který je typu řetězce a je jednohodnotový.
- Schéma typu objektu **Osoba** v metaverse je rozšířena tak, aby zahrnovala atribut UserType, který je typu řetězce a je jednohodnotový.

Ve výchozím nastavení není atribut UserType povolen pro synchronizaci, protože v místním adresáři Active Directory neexistuje odpovídající atribut UserType. Synchronizaci je nutné povolit ručně. Než to uděláte, musíte vzít na vědomí následující chování vynucené službou Azure AD:

- Azure AD přijímá pouze dvě hodnoty pro Atribut UserType: **Člen** a **Host**.
- Pokud atribut UserType není povolen pro synchronizaci v Azure AD Connect, uživatelé Azure AD vytvořené prostřednictvím synchronizace adresářů by měl UserType atribut nastaven na **Member**.
- Azure AD neumožňuje UserType atribut na stávající uživatele Azure AD změnit Azure AD Connect. Lze nastavit jenom při vytváření uživatelů Azure AD a [změnit prostřednictvím Powershellu](/powershell/module/azuread/set-azureaduser?view=azureadps-2.0).

Před povolením synchronizace atributu UserType je třeba nejprve rozhodnout, jak bude atribut odvozen z místní služby Active Directory. Nejběžnější přístupy jsou následující:

- Určete nepoužívaný místní atribut AD (například extensionAttribute1), který se má použít jako zdrojový atribut. Určený atribut Služby AD by měl být **typu řetězce**, měl by mít jednu hodnotu a obsahovat hodnotu **Member** nebo **Guest**. 

    Pokud zvolíte tento přístup, musíte zajistit, aby byl určený atribut naplněn správnou hodnotou pro všechny existující uživatelské objekty v místním adresáři Active Directory, které jsou synchronizovány se službou Azure AD před povolením synchronizace atributu UserType. .

- Případně můžete odvodit hodnotu pro atribut UserType z jiných vlastností. Chcete například synchronizovat všechny uživatele jako **host,** pokud jejich místní atribut UserPrincipalName služby AD končí částí <em>@partners.fabrikam123.org</em>domény . 

    Jak již bylo zmíněno dříve, Azure AD Connect nepovoluje usertype atribut na stávající uživatele Azure AD změnit Azure AD Connect. Proto je nutné zajistit, že logika, kterou jste rozhodli, je konzistentní s tím, jak je atribut UserType již nakonfigurován pro všechny stávající uživatele Služby Azure AD ve vašem tenantovi.

Kroky umožňující synchronizaci atributu UserType lze shrnout takto:

1.  Zakažte plánovač synchronizace a ověřte, že neprobíhá žádná synchronizace.
2.  Přidejte zdrojový atribut do místního schématu konektoru AD.
3.  Přidejte UserType do schématu konektoru Azure AD.
4.  Vytvořte pravidlo příchozí synchronizace pro tok hodnoty atributu z místní služby Active Directory.
5.  Vytvořte pravidlo odchozí synchronizace pro tok hodnoty atributu do Azure AD.
6.  Spusťte úplný cyklus synchronizace.
7.  Povolte plánovač synchronizace.

>[!NOTE]
> Zbytek této části se týká těchto kroků. Jsou popsány v kontextu nasazení Azure AD s topologii jedné doménové struktury a bez vlastních pravidel synchronizace. Pokud máte topologii s více doménovými strukturami, nakonfigurovaná vlastní pravidla synchronizace nebo máte pracovní server, je třeba odpovídajícím způsobem upravit kroky.

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Krok 1: Zakázání plánovače synchronizace a ověření, že neprobíhá žádná synchronizace
Chcete-li se vyhnout exportu nechtěných změn do služby Azure AD, ujistěte se, že při aktualizaci pravidel synchronizace neprobíhá žádná synchronizace. Zakázání integrovaného plánovače synchronizace:

 1. Spusťte relaci Prostředí PowerShell na serveru Azure AD Connect.
 2. Zakázat naplánovanou synchronizaci spuštěním rutiny `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 3. Otevřete Správce synchronizačních služeb tak, že přejdete na **službu Spuštění** > **synchronizace**.
 4. Přejděte na kartu **Operace** a potvrďte, že neproběhla žádná operace se stavem *probíhající*.

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>Krok 2: Přidání atributu source do místního schématu konektoru AD Connector
Ne všechny atributy Azure AD se importují do místního prostoru konektoru služby AD. Přidání atributu zdroj do seznamu importovaných atributů:

 1. Přejděte na kartu **Konektory** ve Správci synchronizačních služeb.
 2. Klepněte pravým tlačítkem myši na místní konektor služby AD a vyberte příkaz **Vlastnosti**.
 3. V rozbalovacím dialogovém okně přejděte na kartu **Vybrat atributy.**
 4. Ujistěte se, že je zdrojový atribut v seznamu atributů zaškrtnut.
 5. Chcete-li soubor uložit, klepněte na tlačítko **OK.**
![Přidání atributu zdroje do místního schématu konektoru AD](./media/how-to-connect-sync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-to-the-azure-ad-connector-schema"></a>Krok 3: Přidání typu UserType do schématu konektoru Azure AD Connector
Ve výchozím nastavení se atribut UserType neimportuje do prostoru připojení Azure AD. Přidání atributu UserType do seznamu importovaných atributů:

 1. Přejděte na kartu **Konektory** ve Správci synchronizačních služeb.
 2. Klikněte pravým tlačítkem myši na **konektor Azure AD a** vyberte **vlastnosti**.
 3. V rozbalovacím dialogovém okně přejděte na kartu **Vybrat atributy.**
 4. Zkontrolujte, zda je atribut UserType v seznamu atributů zaškrtnut.
 5. Chcete-li soubor uložit, klepněte na tlačítko **OK.**

![Přidání zdrojového atributu do schématu programu Azure AD Connector](./media/how-to-connect-sync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Krok 4: Vytvoření pravidla příchozí synchronizace pro tok hodnoty atributu z místní služby Active Directory
Pravidlo příchozí synchronizace umožňuje, aby hodnota atributu přetekovala z atributu source z místní služby Active Directory do metaverse:

1. Otevřete Editor pravidel synchronizace tak, že **přejdete** > do**Editoru pravidel synchronizace**.
2. Nastavte vyhledávací filtr **Směr** na **Příchozí**.
3. Kliknutím na tlačítko **Přidat nové pravidlo** vytvořte nové příchozí pravidlo.
4. Na kartě **Popis** zadejte následující konfiguraci:

    | Atribut | Hodnota | Podrobnosti |
    | --- | --- | --- |
    | Name (Název) | *Zadejte název* | Například *In from AD – User UserType* |
    | Popis | *Uveďte popis* |  |
    | Připojený systém | *Vyberte místní konektor AD* |  |
    | Typ připojeného systémového objektu | **Uživatel** |  |
    | Typ objektu Metaverse | **Person (Osoba)** |  |
    | Typ propojení | **Připojit** |  |
    | Priorita | *Zvolte číslo mezi 1–99* | 1–99 je vyhrazena pro vlastní pravidla synchronizace. Nevybírejte hodnotu, která je používána jiným pravidlem synchronizace. |

5. Přejděte na kartu **filtru oborů** a přidejte **jednu skupinu filtrů oboru** s následující klauzulí:

    | Atribut | Operátor | Hodnota |
    | --- | --- | --- |
    | Admindescription | NOTSTART WITH | Uživatele\_ |

    Filtr oborů určuje, na které místní objekty AD se toto pravidlo příchozí synchronizace použije. V tomto příkladu používáme stejný filtr oborů použitý v pravidle synchronizace *In from AD – User Common* out-of-box, které zabraňuje použití pravidla synchronizace na objekty uživatele vytvořené pomocí funkce zpětného zápisu uživatele Azure AD. Možná budete muset vyladit filtr oborů podle vašeho nasazení Azure AD Connect.

6. Přejděte na kartu **Transformace** a implementujte požadované pravidlo transformace. Například pokud jste určili nepoužívaný místní atribut Služby AD (například extensionAttribute1) jako zdrojový atribut pro UserType, můžete implementovat přímý tok atributů:

    | Typ toku | Cílový atribut | Zdroj | Použít jednou | Typ sloučení |
    | --- | --- | --- | --- | --- |
    | Direct | UserType | extensionAttribute1 | Nekontrolovaná | Aktualizace |

    V jiném příkladu chcete odvodit hodnotu atributu UserType z jiných vlastností. Chcete například synchronizovat všechny uživatele jako host, pokud jejich místní atribut UserPrincipalName služby AD končí částí <em>@partners.fabrikam123.org</em>domény . Můžete implementovat výraz, jako je tento:

    | Typ toku | Cílový atribut | Zdroj | Použít jednou | Typ sloučení |
    | --- | --- | --- | --- | --- |
    | Expression | UserType | IIF(IsPresent([userPrincipalName]),IIF(CBool(InStr([userPrincipalName]),"@partners.fabrikam123.org")=0),"Člen",Host"),Error("UserPrincipalName není k dispozici k určení UserType")) | Nekontrolovaná | Aktualizace |

7. Kliknutím na **Přidat** vytvořte příchozí pravidlo.

![Vytvořit pravidlo příchozí synchronizace](./media/how-to-connect-sync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Krok 5: Vytvoření pravidla odchozí synchronizace pro tok hodnoty atributu do Služby Azure AD
Pravidlo odchozí synchronizace umožňuje, aby hodnota atributu proudila z metaverse do atributu UserType ve službě Azure AD:

1. Přejděte do Editoru pravidel synchronizace.
2. Nastavte vyhledávací filtr **Směr** jako **odchozí**.
3. Klikněte na tlačítko **Přidat nové pravidlo.**
4. Na kartě **Popis** zadejte následující konfiguraci:

    | Atribut | Hodnota | Podrobnosti |
    | ----- | ------ | --- |
    | Name (Název) | *Zadejte název* | Například *Out to AAD – User UserType* |
    | Popis | *Uveďte popis* ||
    | Připojený systém | *Výběr konektoru AAD* ||
    | Typ připojeného systémového objektu | **Uživatel** ||
    | Typ objektu Metaverse | **Person (Osoba)** ||
    | Typ propojení | **Připojit** ||
    | Priorita | *Zvolte číslo mezi 1–99* | 1–99 je vyhrazena pro vlastní pravidla synchronizace. Nevybírejte hodnotu, která je používána jiným pravidlem synchronizace. |

5. Přejděte na kartu **filtru oborů** a přidejte **jednu skupinu filtrů oboru** se dvěma klauzulemi:

    | Atribut | Operátor | Hodnota |
    | --- | --- | --- |
    | sourceObjectType | Stejné | Uživatel |
    | cloudMastered | Notequal | True |

    Filtr oborů určuje, na které objekty Azure AD se použije toto pravidlo odchozí synchronizace. V tomto příkladu používáme stejný filtr oborů z pravidla synchronizace *Out to AD – Identita uživatele.* Zabraňuje použití pravidla synchronizace na objekty uživatele, které nejsou synchronizovány z místní služby Active Directory. Možná budete muset vyladit filtr oborů podle vašeho nasazení Azure AD Connect.

6. Přejděte na kartu **Transformace** a implementujte následující pravidlo transformace:

    | Typ toku | Cílový atribut | Zdroj | Použít jednou | Typ sloučení |
    | --- | --- | --- | --- | --- |
    | Direct | UserType | UserType | Nekontrolovaná | Aktualizace |

7. Chcete-li vytvořit odchozí pravidlo, klepněte na **tlačítko Přidat.**

![Vytvořit pravidlo odchozí synchronizace](./media/how-to-connect-sync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>Krok 6: Spuštění úplného cyklu synchronizace
Obecně platí, že je vyžadován úplný cyklus synchronizace, protože jsme přidali nové atributy do schémat active directory a konektoru Azure AD Connector a zavedli vlastní pravidla synchronizace. Chcete ověřit změny před jejich exportem do Azure AD. 

Pomocí následujících kroků můžete ověřit změny při ručním spuštění kroků, které tvoří úplný cyklus synchronizace.

1. Spuštění **úplného importu** **v místním konektoru AD :**

   1. Přejděte na kartu **Operace** ve Správci synchronizačních služeb.
   2. Klikněte pravým tlačítkem myši **na místní konektor a** vyberte **spustit**.
   3. V rozbalovacím dialogovém okně vyberte **Úplné importace** a pak klepněte na **OK**.
   4. Počkejte na dokončení operace.

      > [!NOTE]
      > Úplný import na místní spojnici AD Connector můžete přeskočit, pokud je zdrojový atribut již zahrnut v seznamu importovaných atributů. Jinými slovy, nebylo třeba provádět žádné změny během [kroku 2: Přidání atributu zdroj do místního schématu konektoru služby AD](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Spusťte **úplný import** na **konektoru Azure AD:**

   1. Klikněte pravým tlačítkem myši na **konektor Azure AD a** vyberte **spustit**.
   2. V rozbalovacím dialogovém okně vyberte **Úplné importace** a pak klepněte na **OK**.
   3. Počkejte na dokončení operace.

3. Ověřte změny pravidel synchronizace u existujícího objektu User:

    Zdrojový atribut z místní služby Active Directory a usertype z Azure AD byly importovány do příslušných prostorů konektoru. Než budete pokračovat v úplné synchronizaci, proveďte **náhled** existujícího objektu User v místním prostoru konektoru ad. Zvolený objekt by měl mít vyplněný zdrojový atribut.
    
    Úspěšný **náhled** s UserType naplněný v metaverse je dobrým ukazatelem, že jste správně nakonfigurovali pravidla synchronizace. Informace o tom, jak provést **náhled**, naleznete v části [Ověření změny](#verify-the-change).

4. Spuštění **úplné synchronizace** v **místním konektoru ad konektoru**:

   1. Klikněte pravým tlačítkem myši **na místní konektor a** vyberte **spustit**.
   2. V rozbalovacím dialogovém okně vyberte **Úplná synchronizace** a klepněte na tlačítko **OK**.
   3. Počkejte na dokončení operace.

5. **Ověřte čekající exporty** do Azure AD:

   1. Klikněte pravým tlačítkem myši na **konektor Azure AD a** vyberte možnost Hledat místo **konektoru**.
   2. V rozbalovacím okně **Prostor pro hledání:**

      - Nastavte **obor** na **čekající export**.
      - Zaškrtněte všechna tři zaškrtávací políčka: **Přidat**, **Upravit**a **Odstranit**.
      - Klepnutím na tlačítko **Hledat** zobrazíte seznam objektů se změnami, které mají být exportovány. Chcete-li zkontrolovat změny pro daný objekt, poklepejte na objekt.
      - Ověřte, zda jsou očekávané změny.

6. Spustit **export** na **konektoru Azure AD:**

   1. Klikněte pravým tlačítkem myši na **konektor Azure AD a** vyberte **spustit**.
   2. V rozbalovacím dialogovém okně **Spustit spojnici** vyberte **Exportovat** a klepněte na tlačítko **OK**.
   3. Počkejte na dokončení exportu do služby Azure AD.

> [!NOTE]
> Tyto kroky nezahrnují úplné synchronizace a kroky exportu na konektoru Azure AD. Tyto kroky nejsou povinné, protože hodnoty atributů jsou tok z místní služby Active Directory pouze do služby Azure AD.

### <a name="step-7-re-enable-the-sync-scheduler"></a>Krok 7: Opětovné povolení plánovače synchronizace
Znovu povolte integrovaný plánovač synchronizace:

1. Spusťte relaci Prostředí PowerShell.
2. Znovu povolte naplánovanou synchronizaci spuštěním rutiny `Set-ADSyncScheduler -SyncCycleEnabled $true`.


## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o modelu konfigurace v [principu deklarativní zřizování](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Přečtěte si další informace o jazyku výrazu v [principu deklarativní zřizování výrazy](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

**Přehledná témata**

* [Synchronizace služby Azure AD Connect: Principy a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
