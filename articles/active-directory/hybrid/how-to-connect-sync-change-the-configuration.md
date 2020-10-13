---
title: 'Azure AD Connect synchronizace: provést změnu konfigurace v Azure AD Connect synchronizace'
description: Provede vás postupem provedení změny konfigurace v Azure AD Connect synchronizaci.
services: active-directory
author: billmath
manager: daveba
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07c1405482f107e370327ffbc049c77f483c29bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89662569"
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Azure AD Connect synchronizace: proveďte změnu ve výchozí konfiguraci.
Tento článek vás seznámí s postupem, jak provést změny ve výchozí konfiguraci v Azure Active Directory (Azure AD) Connect Sync. Poskytuje kroky pro některé běžné scénáře. S tímto vědomím byste měli být schopni provádět jednoduché změny vlastní konfigurace na základě vašich vlastních obchodních pravidel.

> [!WARNING]
> Pokud provedete změny výchozích předdefinovaných pravidel synchronizace, budou tyto změny při příští aktualizaci Azure AD Connect přepsány, což vede k neočekávaným a pravděpodobným nežádoucím výsledkům synchronizace.
>
> Výchozí pravidla pro automatické synchronizace, která jsou k dispozici, mají kryptografický otisk. Pokud provedete změnu těchto pravidel, kryptografický otisk již nebude odpovídat. Pokud se pokusíte použít novou verzi Azure AD Connect, může dojít k problémům v budoucnu. Pouze změnit způsob, jak je popsáno v tomto článku.

## <a name="synchronization-rules-editor"></a>Editor pravidel synchronizace
Editor pravidel synchronizace slouží k zobrazení a změně výchozí konfigurace. Můžete ji najít v nabídce **Start** pod skupinou **Azure AD Connect** .  
![Nabídka Start s editorem pravidel synchronizace](./media/how-to-connect-sync-change-the-configuration/startmenu2.png)

Když otevřete Editor, zobrazí se výchozí Přednastavená pravidla.

![Editor pravidel synchronizace](./media/how-to-connect-sync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navigace v editoru
Pomocí rozevíracích seznamu v horní části editoru můžete rychle najít konkrétní pravidlo. Například pokud chcete zobrazit pravidla, kde je obsažen atribut proxyAddresses, můžete změnit rozevírací seznam na následující:  
![SRE filtrování](./media/how-to-connect-sync-change-the-configuration/filtering.png)  
Pokud chcete resetovat filtrování a načíst novou konfiguraci, stiskněte klávesu F5 na klávesnici.

V pravém horním rohu je tlačítko **Přidat nové pravidlo** . Toto tlačítko slouží k vytvoření vlastního pravidla.

V dolní části jsou tlačítka pro práci na vybraném pravidle synchronizace. **Upravit** a **Odstranit** podle očekávání. **Export** vytvoří skript PowerShellu pro opětovné vytvoření pravidla synchronizace. Pomocí tohoto postupu můžete přesunout pravidlo synchronizace z jednoho serveru na jiný.

## <a name="create-your-first-custom-rule"></a>Vytvoření prvního vlastního pravidla
Nejběžnějšími změnami jsou toky atributů. Data ve zdrojovém adresáři nemusejí být stejná jako v Azure AD. V příkladu v této části se ujistěte, že zadaný název uživatele je vždy ve *správném případě*.

### <a name="disable-the-scheduler"></a>Zakázat Plánovač
[Plánovač](how-to-connect-sync-feature-scheduler.md) se ve výchozím nastavení spouští každých 30 minut. Ujistěte se, že se nespouští při provádění změn a odstraňování potíží s novými pravidly. K dočasnému zakázání plánovače spusťte PowerShell a spusťte příkaz `Set-ADSyncScheduler -SyncCycleEnabled $false` .

![Zakázat Plánovač](./media/how-to-connect-sync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Vytvoření pravidla
1. Klikněte na tlačítko **Přidat nové pravidlo**.
2. Na stránce **Popis** zadejte následující:  
   ![Filtrování příchozích pravidel](./media/how-to-connect-sync-change-the-configuration/description2.png)  
   * **Název**: zadejte popisný název pravidla.
   * **Popis**: Dejte nějaké vysvětlení, aby někdo jiný mohl pochopit, pro které pravidlo platí.
   * **Připojený systém**: Jedná se o systém, ve kterém se objekt nachází. V takovém případě vyberte možnost **konektor služby Active Directory**.
   * **Připojený systém/typ objektu úložiště metaverse**: vyberte **uživatele** a **osobu**, v uvedeném pořadí.
   * **Typ odkazu**: Změňte tuto hodnotu na **připojit**.
   * **Priorita**: zadejte hodnotu, která je v systému jedinečná. Dolní číselná hodnota označuje vyšší prioritu.
   * **Tag**: nechejte toto prázdné. Pouze dodaná pravidla od společnosti Microsoft by měla mít tato pole naplněna hodnotou.
3. Na stránce **Filtr oboru** zadejte jméno **daného ISNOTNULL**.  
   ![Filtr oboru příchozího pravidla](./media/how-to-connect-sync-change-the-configuration/scopingfilter.png)  
   Tato část slouží k definování objektů, které by měly pravidlo použít. Pokud je ponecháno prázdné, pravidlo bude platit pro všechny uživatelské objekty. To však zahrnuje konferenční místnosti, účty služeb a jiné uživatelské objekty mimo uživatele.
4. Na stránce **pravidla spojování** ponechte pole prázdné.
5. Na stránce **transformace** změňte **FlowType** na **výraz**. Pro **atribut target**vyberte **křestní**jméno. A pro **zdroj**zadejte **PCase ([jméno])**.
   ![Transformace příchozích pravidel](./media/how-to-connect-sync-change-the-configuration/transformations.png)  
   Synchronizační modul rozlišuje velká a malá písmena pro název funkce i název atributu. Pokud zadáte něco špatného, zobrazí se upozornění, když přidáte pravidlo. Můžete uložit a pokračovat, ale je nutné pravidlo znovu otevřít a opravit.
6. Kliknutím na tlačítko **Přidat** uložte pravidlo.

Nové vlastní pravidlo by mělo být viditelné s dalšími pravidly synchronizace v systému.

### <a name="verify-the-change"></a>Ověřit změnu
S touto novou změnou se chcete ujistit, že funguje podle očekávání a že nevyvolává žádné chyby. V závislosti na počtu objektů existují dva způsoby, jak tento krok provést:

- Spustí úplnou synchronizaci všech objektů.
- Spustí náhled a úplnou synchronizaci s jedním objektem.

Z nabídky **Start** otevřete **synchronizační službu** . Kroky v této části jsou všechny v tomto nástroji.

**Úplná synchronizace všech objektů**  

   1. V horní části vyberte **konektory** . Identifikujte konektor, který jste změnili v předchozí části (v tomto případě Active Directory Domain Services), a vyberte ho. 
   2. V případě **akcí**vyberte **Spustit**.
   3. Vyberte **Úplná synchronizace**a pak vyberte **OK**.
   ![Úplná synchronizace](./media/how-to-connect-sync-change-the-configuration/fullsync.png)  
   Objekty jsou teď aktualizované v úložišti Metaverse. Ověřte změny tím, že prohlížíte objekt v úložišti Metaverse.

**Náhled a Úplná synchronizace pro jeden objekt**  

   1. V horní části vyberte **konektory** . Identifikujte konektor, který jste změnili v předchozí části (v tomto případě Active Directory Domain Services), a vyberte ho.
   2. Vyberte možnost **Hledat místo v konektoru**. 
   3. Pomocí **oboru** můžete najít objekt, který chcete použít k otestování změny. Vyberte objekt a klikněte na **Náhled**. 
   4. Na nové obrazovce vyberte **Potvrdit náhled**.  
   ![Potvrdit náhled](./media/how-to-connect-sync-change-the-configuration/commitpreview.png)  
   Tato změna se teď potvrdí do úložiště metaverse.

**Zobrazení objektu v úložišti Metaverse**  

1. Vyberte několik ukázkových objektů, abyste se ujistili, že je očekávaná hodnota a že se použije pravidlo. 
2. V horní části vyberte **hledání v úložišti Metaverse** . Přidejte libovolný filtr, který potřebujete k nalezení relevantních objektů. 
3. Z výsledku hledání otevřete objekt. Podívejte se na hodnoty atributů a ověřte je ve sloupci **pravidla synchronizace** , který pravidlo používalo podle očekávání.  
![Vyhledávání Metaverse](./media/how-to-connect-sync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Povolit Plánovač
Pokud je vše podle očekávání, můžete znovu povolit Plánovač. Z PowerShellu spusťte `Set-ADSyncScheduler -SyncCycleEnabled $true` .

## <a name="other-common-attribute-flow-changes"></a>Další běžné změny toku atributů
Předchozí část popisuje, jak provést změny toku atributů. V této části jsou k dispozici některé další příklady. Postup vytvoření pravidla synchronizace je zkrácený, ale můžete najít kompletní kroky v předchozí části.

### <a name="use-an-attribute-other-than-the-default"></a>Použijte jiný atribut než výchozí.
V tomto scénáři Fabrikam je k dispozici doménová struktura, kde se používá místní abeceda pro křestní jméno, příjmení a zobrazované jméno. Znaková reprezentace těchto atributů v latince se dá najít v atributech rozšíření. Pro vytvoření globálního seznamu adres ve službě Azure AD a Microsoft 365 chce organizace místo toho použít tyto atributy.

S výchozí konfigurací vypadá objekt z místní doménové struktury takto:  
![Tok atributů 1](./media/how-to-connect-sync-change-the-configuration/attributeflowjp1.png)

Pokud chcete vytvořit pravidlo s dalšími toky atributů, udělejte toto:

1. V nabídce **Start** otevřete **Editor pravidel synchronizace** .
2. Kliknutím na tlačítko pro **Přidání nového pravidla** u pole **příchozí** zůstane vybraná vlevo.
3. Zadejte název a popis pravidla. Vyberte místní instanci služby Active Directory a příslušné typy objektů. V **typu odkazu**vyberte **připojit**. V případě **priority**vyberte číslo, které nepoužívá jiné pravidlo. Pravidla předdefinovaných součástí začínají na 100, takže v tomto příkladu lze použít hodnotu 50.
  ![Tok atributů 2](./media/how-to-connect-sync-change-the-configuration/attributeflowjp2.png)
4. Nechejte **Filtr oboru** prázdný. (To znamená, že by měl platit pro všechny uživatelské objekty v doménové struktuře.)
5. Nechejte **pravidla JOIN** prázdné. (To znamená, že pravidlo pro vynechání na pole zpracuje všechna spojení.)
6. V části **transformace**vytvořte následující toky:  
  ![Tok atributů 3](./media/how-to-connect-sync-change-the-configuration/attributeflowjp3.png)
7. Kliknutím na tlačítko **Přidat** uložte pravidlo.
8. Přejít na **Synchronization Service Manager**. V okně **konektory**vyberte konektor, do kterého jste přidali pravidlo. Vyberte **Spustit**a pak vyberte **Úplná synchronizace**. Úplná synchronizace přepočítá všechny objekty pomocí aktuálních pravidel.

Jedná se o výsledek stejného objektu s tímto vlastním pravidlem:  
![Tok atributů 4](./media/how-to-connect-sync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Délka atributů
Řetězcové atributy jsou ve výchozím nastavení indexované a maximální délka je 448 znaků. Pokud pracujete s řetězcovými atributy, které mohou obsahovat více, nezapomeňte zahrnout následující do toku atributů:  
`attributeName` <- `Left([attributeName],448)`.

### <a name="changing-the-userprincipalsuffix"></a>Změna userPrincipalSuffix
Atribut userPrincipalName ve službě Active Directory není vždy znám uživateli a nemusí být vhodný jako přihlašovací ID. Pomocí Průvodce Azure AD Connect synchronizace instalace můžete zvolit jiný atribut – například *Pošta*. V některých případech však musí být atribut vypočítán.

Například společnost Contoso má dva adresáře služby Azure AD, jednu pro produkční prostředí a jednu pro testování. Chtějí uživatelům v tenantovi, aby v přihlašovacím ID používali jinou příponu:  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`.

V tomto výrazu proveďte vše vlevo od první @-sign (Word) a zřetězení s pevným řetězcem.

### <a name="convert-a-multi-value-attribute-to-single-value"></a>Převést vícehodnotový atribut na jednu hodnotu
Některé atributy ve službě Active Directory jsou ve schématu ve více hodnotách, i když v modulu Uživatelé a počítače služby Active Directory vypadají jako nejednotně. Příkladem je atribut Description:  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`.

V tomto výrazu, pokud má atribut hodnotu, vezměte první položku (*položku*) v atributu, odeberte úvodní a koncové mezery (*Trim*) a potom v řetězci zachovejte prvních 448 znaků (*vlevo*).

### <a name="do-not-flow-an-attribute"></a>Neflowovat atribut
Základní informace o scénáři v této části najdete v tématu [řízení procesu toku atributů](concept-azure-ad-connect-sync-declarative-provisioning.md#control-the-attribute-flow-process).

Existují dva způsoby, jak neflowovat atribut. První je pomocí Průvodce instalací [Odebrat vybrané atributy](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering). Tato možnost funguje, pokud jste atribut nikdy nesynchronizoval. Pokud jste však začali synchronizovat tento atribut a později ho odebrat pomocí této funkce, modul synchronizace zastaví správu atributu a existující hodnoty zůstanou v Azure AD.

Pokud chcete odebrat hodnotu atributu a zajistit, aby v budoucnu nepokračoval, je nutné vytvořit vlastní pravidlo.

V tomto scénáři Fabrikam jsme provedli, že některé atributy, které synchronizujete do cloudu, by neměly být tam. Chceme mít jistotu, že se tyto atributy z Azure AD odeberou.  
![Chybné atributy rozšíření](./media/how-to-connect-sync-change-the-configuration/badextensionattribute.png)

1. Vytvořte nové pravidlo příchozí synchronizace a naplňte jeho popis.
  ![Označení](./media/how-to-connect-sync-change-the-configuration/syncruledescription.png)
2. Vytvoří toky atributů s **výrazem** pro **FlowType** a **AuthoritativeNull** pro **zdroj**. Literální **AuthoritativeNull** označuje, že hodnota by měla být v úložišti Metaverse prázdná, a to i v případě, že se pravidlo synchronizace s nižší prioritou pokusí naplnit hodnotu.
  ![Transformace pro atributy rozšíření](./media/how-to-connect-sync-change-the-configuration/syncruletransformations.png)
3. Uložte pravidlo synchronizace. Spusťte **synchronizační službu**, najděte konektor, vyberte **Spustit**a pak vyberte **Úplná synchronizace**. Tento krok přepočítá všechny toky atributů.
4. Ověřte, že se chystá export zamýšlených změn, a to tak, že se bude hledat v prostoru konektoru.
  ![Dvoufázové odstranění](./media/how-to-connect-sync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Vytváření pravidel pomocí PowerShellu
Použití editoru pravidel synchronizace funguje v případě, že máte jen několik změn, které je třeba provést. Pokud potřebujete provést mnoho změn, může být prostředí PowerShell lepší volbou. Některé pokročilé funkce jsou dostupné jenom v prostředí PowerShell.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>Získání skriptu PowerShellu pro pravidlo předdefinovaných verzí
Pokud chcete zobrazit PowerShellový skript, který vytvořil pravidlo pro předdefinované, vyberte pravidlo v editoru pravidel synchronizace a klikněte na **exportovat**. Tato akce vám poskytne skript prostředí PowerShell, který pravidlo vytvořilo.

### <a name="advanced-precedence"></a>Pokročilá priorita
Přednastavená pravidla synchronizace začnou mít hodnotu priority 100. Pokud máte mnoho doménových struktur a potřebujete provést mnoho vlastních změn, nemusí být pravidla synchronizace 99 dostatečná.

Synchronizačnímu modulu, který chcete vložit další pravidla, můžete dát pokyn před přednastavenými pravidly. K získání tohoto chování použijte následující postup:

1. Označte první předem připravené pravidlo synchronizace (**v programu AD-User JOIN**) v editoru pravidel synchronizace a vyberte **exportovat**. Zkopírujte hodnotu identifikátoru SR.  
![PowerShell před změnou](./media/how-to-connect-sync-change-the-configuration/powershell1.png)  
2. Vytvořte nové pravidlo synchronizace. K jeho vytvoření můžete použít Editor pravidel synchronizace. Exportujte pravidlo do skriptu PowerShellu.
3. Do vlastnosti **PrecedenceBefore**vložte hodnotu identifikátoru z pravidla předdefinovaných polí. Nastavte **prioritu** na **0**. Ujistěte se, že je atribut identifikátoru jedinečný a že nepoužíváte znovu identifikátor GUID z jiného pravidla. Také se ujistěte, že není nastavena vlastnost **ImmutableTag** . Tato vlastnost by měla být nastavena pouze pro pravidlo předdefinovaných polí.
4. Uložte PowerShellový skript a spusťte ho. Výsledkem je, že vaše vlastní pravidlo má přiřazenou hodnotu priority 100 a všechna ostatní pravidla pro dodávání okna se zvýší.  
![PowerShell po změně](./media/how-to-connect-sync-change-the-configuration/powershell2.png)  

V případě potřeby můžete mít mnoho vlastních pravidel synchronizace pomocí stejné hodnoty **PrecedenceBefore** .

## <a name="enable-synchronization-of-usertype"></a>Povolit synchronizaci UserType
Azure AD Connect podporuje synchronizaci atributu **usertype** pro objekty **uživatele** ve verzi 1.1.524.0 a novější. Konkrétně byly zavedeny následující změny:

- Schéma typu objektu **uživatele** v konektoru služby Azure AD je rozšířeno tak, aby zahrnovalo atribut usertype, který je typu String a je jedinou hodnotou.
- Schéma **osoby** typu objektu v úložišti metaverse je rozšířeno tak, aby zahrnovalo atribut usertype, který je typu String a je jedinou hodnotou.

Ve výchozím nastavení není atribut UserType povolen pro synchronizaci, protože v místní službě Active Directory neexistuje žádný odpovídající atribut UserType. Musíte ručně povolit synchronizaci. Než to uděláte, musíte si uvědomit následující chování vynutilé službou Azure AD:

- Azure AD přijímá jenom dvě hodnoty atributu UserType: **Member** a **Host**.
- Pokud atribut UserType není povolený pro synchronizaci v Azure AD Connect, budou uživatelé Azure AD, kteří vytvořili prostřednictvím synchronizace adresářů, mít atribut UserType nastavený na **Member**.
- Před verzí 1.5.30.0 služba Azure AD nepovolila změnu atributu UserType u stávajících uživatelů Azure AD pomocí Azure AD Connect. Ve starších verzích je možné ji nastavit jenom během vytváření uživatelů Azure AD a [měnit přes PowerShell](/powershell/module/azuread/set-azureaduser?view=azureadps-2.0).

Než povolíte synchronizaci atributu UserType, musíte se nejdřív rozhodnout, jak je atribut odvozený z místní služby Active Directory. Níže jsou uvedené nejběžnější přístupy:

- Určete nepoužitý místní atribut AD (například extensionAttribute1), který se použije jako zdrojový atribut. Určený místní atribut AD by měl být typu **řetězec**, musí být tvořen jednou hodnotou a obsahovat **člena** hodnoty nebo **hosta**. 

    Pokud zvolíte tento přístup, musíte zajistit, aby byl označený atribut vyplněný správnou hodnotou pro všechny existující uživatelské objekty v místní službě Active Directory, které jsou synchronizované se službou Azure AD před povolením synchronizace atributu UserType.

- Alternativně můžete odvodit hodnotu atributu UserType z jiných vlastností. Například chcete synchronizovat všechny uživatele jako **hosta** , pokud jejich místní atribut AD userPrincipalName končí částí domény <em>@partners.fabrikam123.org</em> . 

    Jak již bylo zmíněno dříve, starší verze Azure AD Connect nepovolují, aby byl atribut UserType u stávajících uživatelů služby Azure AD změněn pomocí Azure AD Connect. Proto je nutné zajistit, aby byla logika, kterou jste určili, konzistentní s tím, jak je atribut UserType již nakonfigurován pro všechny existující uživatele Azure AD ve vašem tenantovi.

Postup, jak povolit synchronizaci atributu UserType, může být shrnut jako:

1.  Zakažte Plánovač synchronizace a ověřte, že neprobíhá žádná synchronizace.
2.  Přidejte zdrojový atribut do schématu místního AD Connector.
3.  Přidejte UserType do schématu konektoru služby Azure AD.
4.  Vytvořte pravidlo příchozí synchronizace pro tok hodnoty atributu z místní služby Active Directory.
5.  Vytvořte pravidlo odchozí synchronizace pro Flow hodnoty atributu do Azure AD.
6.  Spusťte úplný cyklus synchronizace.
7.  Povolte Plánovač synchronizace.

>[!NOTE]
> Zbytek této části se zabývá těmito kroky. Jsou popsané v kontextu nasazení Azure AD s topologií s jednou doménovou strukturou a bez vlastních pravidel synchronizace. Pokud máte topologii s více doménovými strukturami, nakonfigurovali jste vlastní pravidla synchronizace nebo máte pracovní server, musíte upravit postup odpovídajícím způsobem.

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Krok 1: zákaz plánovače synchronizace a ověření, že probíhá žádná synchronizace
Abyste se vyhnuli exportu nezamýšlených změn do služby Azure AD, zajistěte, aby během aktualizace synchronizačních pravidel probíhala žádná synchronizace. Zakázání integrovaného plánovače synchronizace:

 1. Spusťte relaci PowerShellu na Azure AD Connectovém serveru.
 2. Zakažte naplánovanou synchronizaci spuštěním rutiny `Set-ADSyncScheduler -SyncCycleEnabled $false` .
 3. Otevřete Synchronization Service Manager kliknutím na **Spustit**  >  **synchronizační službu**.
 4. Přejít na kartu **operace** a potvrďte, že neexistuje žádná operace se stavem *probíhá.*

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>Krok 2: Přidání zdrojového atributu do schématu místního AD Connector
Ne všechny atributy služby Azure AD se importují do místního prostoru služby AD Connector. Chcete-li přidat zdrojový atribut do seznamu importovaných atributů:

 1. V Synchronization Service Manager otevřete kartu **konektory** .
 2. Klikněte pravým tlačítkem na místní konektor AD a vyberte **vlastnosti**.
 3. V místním dialogovém okně přejdete na kartu **vybrat atributy** .
 4. Zajistěte, aby byl zdrojový atribut vrácen se změnami v seznamu atributů.
 5. Kliknutím na tlačítko **OK** uložte.
![Přidat zdrojový atribut do schématu místního AD Connectoru](./media/how-to-connect-sync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-attribute-to-the-azure-ad-connector-schema"></a>Krok 3: Přidání atributu UserType do schématu konektoru služby Azure AD
Ve výchozím nastavení atribut UserType není importován do Azure AD Connectho prostoru. Chcete-li přidat atribut UserType do seznamu importovaných atributů:

 1. V Synchronization Service Manager otevřete kartu **konektory** .
 2. Klikněte pravým tlačítkem na **konektor Azure AD** a vyberte **vlastnosti**.
 3. V místním dialogovém okně přejdete na kartu **vybrat atributy** .
 4. Ujistěte se, že je v seznamu atributů zaškrtnut atribut UserType.
 5. Kliknutím na tlačítko **OK** uložte.

![Přidat zdrojový atribut do schématu konektoru služby Azure AD](./media/how-to-connect-sync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Krok 4: vytvoření pravidla příchozí synchronizace pro tok hodnoty atributu z místní služby Active Directory
Pravidlo příchozí synchronizace povoluje, aby hodnota atributu mohla přecházet ze zdrojového atributu z místní služby Active Directory do úložiště metaverse:

1. Kliknutím na **Spustit**  >  **Editor pravidel synchronizace**otevřete Editor pravidel synchronizace.
2. Nastavte **směr** vyhledávacího filtru na **příchozí**.
3. Kliknutím na tlačítko **Přidat nové pravidlo** vytvoříte nové příchozí pravidlo.
4. Na kartě **Popis** zadejte následující konfiguraci:

    | Atribut | Hodnota | Podrobnosti |
    | --- | --- | --- |
    | Name | *Zadat název* | Například *ve službě AD – uživatelskou usertype* |
    | Description | *Zadejte popis.* |  |
    | Připojený systém | *Výběr místního konektoru služby AD* |  |
    | Typ připojeného systémového objektu | **Uživatel** |  |
    | Typ objektu úložiště metaverse | **Osoba** |  |
    | Typ odkazu | **Join** (Spojení) |  |
    | Priorita | *Vyberte číslo v rozmezí 1 až 99.* | 1 – 99 je vyhrazeno pro vlastní pravidla synchronizace. Nevybírejte hodnotu, kterou používá jiné synchronizační pravidlo. |

5. Přejít na kartu **obor filtru** a přidejte **jednu skupinu filtru oborů** s následující klauzulí:

    | Atribut | Operátor | Hodnota |
    | --- | --- | --- |
    | adminDescription | NOTSTARTWITH | Uživatel\_ |

    Filtr oboru určuje, které místní objekty služby AD toto pravidlo příchozí synchronizace používá. V tomto příkladu používáme stejný filtr pro vytváření oborů, který se používá v nástroji *ve službě AD – společné* pravidlo pro synchronizaci předem, které brání použití synchronizačního pravidla pro uživatelské objekty vytvořené prostřednictvím funkce zpětného zápisu uživatelů Azure AD. Je možné, že budete muset upravit filtr oboru podle nasazení Azure AD Connect.

6. Přejít na kartu **transformace** a implementovat požadované pravidlo transformace. Pokud jste například určili nepoužitý místní atribut AD (například extensionAttribute1) jako zdrojový atribut pro UserType, můžete implementovat přímý tok atributů:

    | Typ toku | Cílový atribut | Zdroj | Použít jednou | Typ sloučení |
    | --- | --- | --- | --- | --- |
    | Direct | UserType | extensionAttribute1 | Není zaškrtnuto | Aktualizace |

    V jiném příkladu chcete odvodit hodnotu atributu UserType z jiných vlastností. Například chcete synchronizovat všechny uživatele jako hosta, pokud jejich místní atribut AD userPrincipalName končí částí domény <em>@partners.fabrikam123.org</em> . Můžete implementovat výraz podobný tomuto:

    | Typ toku | Cílový atribut | Zdroj | Použít jednou | Typ sloučení |
    | --- | --- | --- | --- | --- |
    | Výraz | UserType | IIF (nepřítomné ([userPrincipalName]), IIF (CBool (InStr ([userPrincipalName]), " @partners.fabrikam123.org ") = 0), "Member", "Guest"), chyba ("userPrincipalName není k dispozici pro určení usertype")) | Není zaškrtnuto | Aktualizace |

7. Kliknutím na tlačítko **Přidat** vytvořte pravidlo pro příchozí spojení.

![Vytvořit pravidlo příchozí synchronizace](./media/how-to-connect-sync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Krok 5: vytvoření pravidla odchozí synchronizace pro Flow hodnoty atributu do Azure AD
Pravidlo odchozí synchronizace povoluje, aby hodnota atributu byla z úložiště metaverse předávána do atributu UserType v Azure AD:

1. Umožňuje přejít do editoru pravidel synchronizace.
2. Nastavte **směr** vyhledávacího filtru na **odchozí**.
3. Klikněte na tlačítko **Přidat nové pravidlo** .
4. Na kartě **Popis** zadejte následující konfiguraci:

    | Atribut | Hodnota | Podrobnosti |
    | ----- | ------ | --- |
    | Name | *Zadat název* | Například pro *AAD – uživatel – usertype* |
    | Description | *Zadejte popis.* ||
    | Připojený systém | *Vyberte konektor AAD.* ||
    | Typ připojeného systémového objektu | **Uživatel** ||
    | Typ objektu úložiště metaverse | **Osoba** ||
    | Typ odkazu | **Join** (Spojení) ||
    | Priorita | *Vyberte číslo v rozmezí 1 až 99.* | 1 – 99 je vyhrazeno pro vlastní pravidla synchronizace. Nevybírejte hodnotu, kterou používá jiné synchronizační pravidlo. |

5. Přejít na kartu **obor filtru** a přidejte **jednu skupinu filtru oborů** se dvěma klauzulemi:

    | Atribut | Operátor | Hodnota |
    | --- | --- | --- |
    | sourceObjectType | VÝŠI | Uživatel |
    | cloudMastered | NOTEQUAL | Ano |

    Filtr oboru určuje, které objekty služby Azure AD toto pravidlo odchozí synchronizace používá. V tomto příkladu použijeme stejný filtr pro vytváření oborů z pravidla *pro synchronizaci od pro AD – aktuální identita uživatele* . Zabraňuje použití synchronizačního pravidla pro uživatelské objekty, které nejsou synchronizované z místní služby Active Directory. Je možné, že budete muset upravit filtr oboru podle nasazení Azure AD Connect.

6. Přejít na kartu **transformace** a implementovat následující pravidlo transformace:

    | Typ toku | Cílový atribut | Zdroj | Použít jednou | Typ sloučení |
    | --- | --- | --- | --- | --- |
    | Direct | UserType | UserType | Není zaškrtnuto | Aktualizace |

7. Kliknutím na **Přidat** vytvořte odchozí pravidlo.

![Vytvořit pravidlo odchozí synchronizace](./media/how-to-connect-sync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>Krok 6: spuštění úplného cyklu synchronizace
Obecně je potřeba úplný cyklus synchronizace, protože jsme přidali nové atributy do schémat služby Active Directory a konektoru služby Azure AD a zavedli jste vlastní pravidla synchronizace. Před exportem do služby Azure AD chcete ověřit jejich změny. 

Pomocí následujících kroků můžete ověřit změny, a to ručním spuštěním kroků, které tvoří cyklus úplné synchronizace.

1. Spusťte **úplný import** na **místní službě AD Connector**:

   1. V Synchronization Service Manager otevřete kartu **konektory** .
   2. Klikněte pravým tlačítkem na **konektor on-premises AD** a vyberte **Spustit**.
   3. V místním dialogovém okně vyberte **úplný import** a pak klikněte na **OK**.
   4. Počkejte na dokončení operace.

      > [!NOTE]
      > Pokud je zdrojový atribut již obsažen v seznamu importovaných atributů, můžete přeskočit úplný import na místní konektor AD. Jinými slovy, během kroku 2 nemusíte dělat žádné změny [: přidejte zdrojový atribut do schématu místního AD Connector](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Spusťte **úplný import** na **konektoru služby Azure AD**:

   1. Klikněte pravým tlačítkem na **konektor Azure AD** a vyberte **Spustit**.
   2. V místním dialogovém okně vyberte **úplný import** a pak klikněte na **OK**.
   3. Počkejte na dokončení operace.

3. Ověřte změny synchronizačního pravidla u stávajícího objektu uživatele:

    Zdrojový atribut z místní služby Active Directory a UserType z Azure AD se importoval do příslušných prostorů konektoru. Než budete pokračovat v úplné synchronizaci, udělejte ve **verzi Preview** existující objekt uživatele v prostoru konektoru v místní službě AD. Objekt, který jste zvolili, by měl mít naplněný zdrojový atribut.
    
    Úspěšná **verze Preview** s použitím usertype naplněné v úložišti metaverse je dobrým indikátorem, že jste správně nakonfigurovali pravidla synchronizace. Informace o tom, jak **Zobrazit náhled**, najdete v části [ověření změny](#verify-the-change).

4. Spusťte **úplnou synchronizaci** na **místní službě AD Connector**:

   1. Klikněte pravým tlačítkem na **konektor on-premises AD** a vyberte **Spustit**.
   2. V místním dialogovém okně vyberte **úplnou synchronizaci** a pak klikněte na **OK**.
   3. Počkejte na dokončení operace.

5. Ověřit **nedokončené exporty** do Azure AD:

   1. Klikněte pravým tlačítkem na **konektor Azure AD** a vyberte **Hledat místo v konektoru**.
   2. V automaticky otevíraném okně **prostor pro hledání konektoru** :

      - Nastavte **Rozsah** na **Export čeká na vyřízení**.
      - Zaškrtněte všechna tři zaškrtávací políčka: **Přidat**, **Upravit**a **Odstranit**.
      - Kliknutím na tlačítko **Hledat** získáte seznam objektů se změnami, které mají být exportovány. Chcete-li prostudovat změny pro daný objekt, dvakrát klikněte na objekt.
      - Ověřte, zda jsou tyto změny očekávány.

6. Spusťte **Export** na **konektoru služby Azure AD**:

   1. Klikněte pravým tlačítkem na **konektor Azure AD** a vyberte **Spustit**.
   2. V automaticky otevíraném okně **konektoru spustit** vyberte **exportovat** a pak klikněte na **OK**.
   3. Počkejte na dokončení exportu do Azure AD.

> [!NOTE]
> Tyto kroky nezahrnují kroky úplné synchronizace a exportu v konektoru služby Azure AD. Tyto kroky nejsou povinné, protože hodnoty atributu jsou z místní služby Active Directory předávány pouze do služby Azure AD.

### <a name="step-7-re-enable-the-sync-scheduler"></a>Krok 7: opětovné povolení plánovače synchronizace
Opětovné povolení integrovaného plánovače synchronizace:

1. Spusťte relaci PowerShellu.
2. Spusťte rutinu opětovným povolením naplánované synchronizace `Set-ADSyncScheduler -SyncCycleEnabled $true` .


## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o modelu konfigurace v tématu [Principy deklarativního zřizování](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Přečtěte si další informace o jazyce výrazů v tématu [Principy deklarativních zřizovacích výrazů](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

**Témata s přehledem**

* [Azure AD Connect synchronizace: pochopení a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
