---
title: 'Synchronizace služby Azure AD Connect: Konfigurace filtrování | Dokumenty společnosti Microsoft'
description: Vysvětluje, jak nakonfigurovat filtrování v synchronizaci Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 880facf6-1192-40e9-8181-544c0759d506
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 983699dfbfe3e8fa332da4810d1514a11029077f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261096"
---
# <a name="azure-ad-connect-sync-configure-filtering"></a>Synchronizace Azure AD Connect: Konfigurace filtrování
Pomocí filtrování můžete řídit, které objekty se zobrazí ve službě Azure Active Directory (Azure AD) z místního adresáře. Výchozí konfigurace přebírá všechny objekty ve všech doménách v nakonfigurovaných doménových strukturách. Obecně se jedná o doporučenou konfiguraci. Uživatelé, kteří používají úlohy Office 365, jako je Exchange Online a Skype pro firmy, mají prospěch z kompletního globálního seznamu adres, aby mohli posílat e-maily a volat všem. S výchozí konfigurací by měli stejné zkušenosti, které by měli s místní implementací Exchange nebo Lyncu.

V některých případech však musíte provést některé změny výchozí konfigurace. Zde je několik příkladů:

* Plánujete použít [topologii adresáře služby Multi Azure AD](plan-connect-topologies.md#each-object-only-once-in-an-azure-ad-tenant). Potom je třeba použít filtr pro řízení objektů, které jsou synchronizovány s určitým adresářem Azure AD.
* Spustíte pilotní projekt pro Azure nebo Office 365 a chcete jenom podmnožinu uživatelů ve službě Azure AD. V malém pilotním projektu není důležité mít úplný globální seznam adres, který předvede funkce.
* Máte mnoho účtů služeb a dalších neosobních účtů, které nechcete ve službě Azure AD.
* Z důvodů dodržování předpisů neodstraníte žádné uživatelské účty místně. Jen je znemožníš. Ale ve službě Azure AD chcete, aby byly k dispozici pouze aktivní účty.

Tento článek popisuje, jak nakonfigurovat různé metody filtrování.

> [!IMPORTANT]
> Microsoft nepodporuje úpravy ani provoz synchronizace služby Azure AD Connect mimo formálně zdokumentované akce. Každá z těchto akcí může mít za následek nekonzistentní nebo nepodporovaný stav synchronizace Azure AD Connect. V důsledku toho společnost Microsoft nemůže poskytovat technickou podporu pro tato nasazení.

## <a name="basics-and-important-notes"></a>Základy a důležité poznámky
V synchronizaci Azure AD Connect můžete filtrování povolit kdykoli. Pokud začnete s výchozí konfigurací synchronizace adresářů a pak nakonfigurujete filtrování, objekty, které jsou odfiltrovány, se již nebudou synchronizovat do služby Azure AD. Z důvodu této změny všechny objekty ve službě Azure AD, které byly dříve synchronizovány, ale byly pak filtrovány jsou odstraněny ve službě Azure AD.

Než začnete provádět změny filtrování, ujistěte se, že [jste naplánovanou úlohu zakázali,](#disable-the-scheduled-task) abyste neomylně neexportovali změny, které jste ještě neověřili, že jsou správné.

Vzhledem k tomu, že filtrování můžete odebrat mnoho objektů současně, chcete se ujistit, že nové filtry jsou správné před zahájením exportu všechny změny azure ad. Po dokončení kroků konfigurace důrazně doporučujeme, abyste před exportem a prováděním změn ve službě Azure AD postupovali podle [ověřovacích kroků.](#apply-and-verify-changes)

Chcete-li zabránit náhodnému odstranění mnoha objektů, je funkce["prevent accidental deletes"](how-to-connect-sync-feature-prevent-accidental-deletes.md)ve výchozím nastavení zapnuta. Pokud odstraníte mnoho objektů z důvodu filtrování (500 ve výchozím nastavení), je třeba postupovat podle kroků v tomto článku povolit odstranění přejít do Azure AD.

Pokud používáte sestavení před listopadem 2015[(1.0.9125](reference-connect-version-history.md#1091250)), proveďte změnu konfigurace filtru a použijte synchronizaci hash hesla, musíte po dokončení konfigurace spustit úplnou synchronizaci všech hesel. Postup spuštění úplné synchronizace hesla naleznete v [tématu Aktivace úplné synchronizace všech hesel](tshoot-connect-password-hash-synchronization.md#trigger-a-full-sync-of-all-passwords). Pokud používáte sestavení 1.0.9125 nebo novější, pak pravidelná **akce úplné synchronizace** také vypočítá, zda mají být hesla synchronizována a zda tento krok navíc již není vyžadován.

Pokud byly **objekty uživatelů** neúmyslně odstraněny ve službě Azure AD z důvodu chyby filtrování, můžete znovu vytvořit uživatelské objekty ve službě Azure AD odebráním konfigurace filtrování. Pak můžete znovu synchronizovat adresáře. Tato akce obnoví uživatele z koše ve službě Azure AD. Nelze však obnovit jiné typy objektů. Pokud například omylem odstraníte skupinu zabezpečení, která byla použita pro acl prostředku, skupina a její seznamy Řízení pádu nelze obnovit.

Azure AD Connect odstraní pouze objekty, které kdysi považuje za v oboru. Pokud existují objekty ve službě Azure AD, které byly vytvořeny jiným synchronizačním strojem a tyto objekty nejsou v oboru, přidání filtrování je neodebere. Pokud například začnete se serverem DirSync, který vytvořil úplnou kopii celého adresáře ve službě Azure AD, a nainstalujete nový synchronizační server Azure AD Connect paralelně s povoleným filtrováním od začátku, Azure AD Connect neodebere další objekty. které jsou vytvořeny DirSync.

Konfigurace filtrování se zachová při instalaci nebo upgradu na novější verzi služby Azure AD Connect. Je vždy osvědčeným postupem ověřit, že konfigurace nebyla neúmyslně změněna po upgradu na novější verzi před spuštěním prvního cyklu synchronizace.

Pokud máte více než jednu doménovou strukturu, je nutné použít konfigurace filtrování, které jsou popsány v tomto tématu, pro každou doménovou strukturu (za předpokladu, že chcete stejnou konfiguraci pro všechny z nich).

### <a name="disable-the-scheduled-task"></a>Zakázání naplánované úlohy
Chcete-li zakázat předdefinovaný plánovač, který každých 30 minut spouští cyklus synchronizace, postupujte takto:

1. Přejděte na výzvu powershellu.
2. Spuštěním `Set-ADSyncScheduler -SyncCycleEnabled $False` zakážete plánovač.
3. Proveďte změny, které jsou popsány v tomto článku.
4. Chcete-li plánovač znovu povolit, spusťte `Set-ADSyncScheduler -SyncCycleEnabled $True` jej.

**Pokud používáte sestavení Azure AD Connect před 1.1.105.0**  
Chcete-li zakázat naplánovanou úlohu, která každé tři hodiny spustí cyklus synchronizace, postupujte takto:

1. Spusťte **plánovač úloh** z nabídky **Start.**
2. Přímo v části **Knihovna plánovače úloh**vyhledejte úlohu s názvem **Plánovač synchronizace Azure AD**, klepněte pravým tlačítkem myši a vyberte **zakázat**.  
   ![Plánovač úloh](./media/how-to-connect-sync-configure-filtering/taskscheduler.png)  
3. Nyní můžete provádět změny konfigurace a spustit synchronizační modul ručně z konzoly **Synchronizačního správce služeb.**

Po dokončení všech změn filtrování nezapomeňte vrátit a úkol znovu **povolit.**

## <a name="filtering-options"></a>Možnosti filtrování
Na nástroj pro synchronizaci adresářů můžete použít následující typy konfigurace filtrování:

* [**Na základě skupiny**](#group-based-filtering): Filtrování založené na jedné skupině lze konfigurovat pouze při počáteční instalaci pomocí průvodce instalací.
* [**Na základě domény**](#domain-based-filtering): Pomocí této možnosti můžete vybrat, které domény se synchronizují s Azure AD. Můžete také přidat a odebrat domény z konfigurace synchronizačního modulu při provádění změn místní infrastruktury po instalaci synchronizace Azure AD Connect.
* [**Organizační jednotka (OU)-based:**](#organizational-unitbased-filtering)Pomocí této možnosti můžete vybrat, které organizační jednotky synchronizovat s Azure AD. Tato volba je pro všechny typy objektů ve vybraných vou.
* [**Na základě atributů**](#attribute-based-filtering): Pomocí této volby můžete filtrovat objekty na základě hodnot atributů na objektech. Můžete také mít různé filtry pro různé typy objektů.

Můžete použít více možností filtrování současně. Můžete například použít filtrování založené na ou k zahrnutí objektů do jedné ou. Současně můžete použít filtrování založené na atributech k dalšímu filtrování objektů. Při použití více metod filtrování filtry používají logické "A" mezi filtry.

## <a name="domain-based-filtering"></a>Filtrování založené na doméně
Tato část obsahuje postup konfigurace filtru domény. Pokud jste po instalaci služby Azure AD Connect přidali nebo odebrali domény v doménové struktuře, budete muset také aktualizovat konfiguraci filtrování.

Upřednostňovaným způsobem změny filtrování podle domény je spuštění průvodce instalací a změna [filtrování domény a ou.](how-to-connect-install-custom.md#domain-and-ou-filtering) Průvodce instalací automatizuje všechny úkoly, které jsou popsány v tomto tématu.

Tyto kroky byste měli postupovat pouze v případě, že z nějakého důvodu nemůžete spustit průvodce instalací.

Konfigurace filtrování podle domény se skládá z těchto kroků:

1. Vyberte domény, které chcete zahrnout do synchronizace.
2. Pro každou přidanou a odebranou doménu upravte profily spuštění.
3. [Použít a ověřit změny](#apply-and-verify-changes).

### <a name="select-the-domains-to-be-synchronized"></a>Vyberte domény, které mají být synchronizovány.
Existují dva způsoby, jak vybrat domény, které mají být synchronizovány:
    - Použití služby synchronizace
    - Pomocí průvodce Azure AD Connect.


#### <a name="select-the-domains-to-be-synchronized-using-the-synchronization-service"></a>Vyberte domény, které mají být synchronizovány pomocí služby synchronizace.
Chcete-li nastavit filtr domény, postupujte takto:

1. Přihlaste se k serveru, na který běží synchronizace Azure AD Connect, pomocí účtu, který je členem skupiny zabezpečení **ADSyncAdmins.**
2. **Spusťte službu synchronizace** z nabídky **Start.**
3. Vyberte **Konektory**a v seznamu **Konektory** vyberte konektor s typem **Služby Active Directory Domain Services**. V **okně Akce**vyberte možnost **Vlastnosti**.  
   ![Vlastnosti konektoru](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Klepněte na **tlačítko Konfigurovat oddíly adresáře**.
5. V seznamu **Vybrat oddíly adresáře** vyberte a zrušte výběr domén podle potřeby. Ověřte, zda jsou vybrány pouze oddíly, které chcete synchronizovat.  
   ![Oddíly](./media/how-to-connect-sync-configure-filtering/connectorpartitions.png)  
   Pokud jste změnili místní infrastrukturu služby Active Directory a přidali nebo odebrali domény z doménové struktury, klikněte na tlačítko **Aktualizovat** a získejte aktualizovaný seznam. Při aktualizaci budete požádáni o pověření. Zadejte všechna pověření s přístupem pro čtení do služby Active Directory systému Windows Server. Nemusí to být uživatel, který je předem vyplněn v dialogovém okně.  
   ![Je nutná aktualizace.](./media/how-to-connect-sync-configure-filtering/refreshneeded.png)  
6. Až budete hotovi, zavřete dialogové okno **Vlastnosti** klepnutím na **tlačítko OK**. Pokud jste odebrali domény z doménové struktury, vyskakovací okno se zprávou říká, že doména byla odebrána a tato konfigurace bude vyčištěna.
7. Pokračujte v úpravě profilů spuštění.

#### <a name="select-the-domains-to-be-synchronized-using-the-azure-ad-connect-wizard"></a>Vyberte domény, které mají být synchronizovány pomocí průvodce Azure AD Connect
Chcete-li nastavit filtr domény, postupujte takto:

1.  Spuštění průvodce Azure AD Connect
2.  Klikněte na **Konfigurovat**.
3.  Vyberte **Možnosti vlastní synchronizace** a klepněte na tlačítko **Další**.
4.  Zadejte svoje přihlašovací údaje služby Azure AD.
5.  Na obrazovce **Připojené adresáře** klepněte na **tlačítko Další**.
6.  Na **stránce filtrování domény a ou položky** klepněte na **tlačítko Aktualizovat**.  Nové domény se nyní zobrazují a smazané domény zmizí.
   ![Oddíly](./media/how-to-connect-sync-configure-filtering/update2.png)  

### <a name="update-the-run-profiles"></a>Aktualizace profilů spuštění
Pokud jste aktualizovali filtr domény, musíte také aktualizovat profily spuštění.

1. V seznamu **Konektory** zkontrolujte, zda je vybránkonektor, který jste změnili v předchozím kroku. V **aplikaci Actions**vyberte **konfigurovat profily spuštění**.  
   ![Profily spouštění konektorů 1](./media/how-to-connect-sync-configure-filtering/connectorrunprofiles1.png)  
2. Vyhledejte a identifikujte následující profily:
    * Full Import
    * Úplná synchronizace
    * Rozdílový import
    * Rozdílová synchronizace
    * Export
3. Pro každý profil upravte **přidané** a **odebrané** domény.
    1. Pro každý z pěti profilů proveďte následující kroky pro každou **přidanou** doménu:
        1. Vyberte profil spuštění a klepněte na **tlačítko Nový krok**.
        2. Na stránce **Konfigurovat krok** vyberte v rozevírací nabídce **Typ** typ typu kroku se stejným názvem jako profil, který konfigurujete. Pak klikněte na **Další**.  
        ![Profily spouštění konektorů 2](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep1.png)  
        3. Na stránce **Konfigurace konektoru** vyberte v rozevírací nabídce **Oddíl** název domény, kterou jste přidali do filtru domény.  
        ![Profily spouštění konektorů 3](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep2.png)  
        4. Chcete-li zavřít dialogové **okno Konfigurovat profil spuštění,** klepněte na tlačítko **Dokončit**.
    2. Pro každý z pěti profilů proveďte následující kroky pro každou **odebranou** doménu:
        1. Vyberte profil spuštění.
        2. Pokud je **hodnota** atributu **Partition** identifikátorEM GUID, vyberte krok spuštění a klepněte na tlačítko **Odstranit krok**.  
        ![Profily spouštění konektorů 4](./media/how-to-connect-sync-configure-filtering/runprofilesdeletestep.png)  
    3. Ověřte změny. Každá doména, kterou chcete synchronizovat, by měla být uvedena jako krok v každém profilu spuštění.
4. Chcete-li zavřít dialogové **okno Konfigurovat profily spuštění,** klepněte na tlačítko **OK**.
5.  Chcete-li dokončit konfiguraci, je třeba spustit **úplný import** a **synchronizaci delta**. Pokračovat ve čtení části [Použít a ověřit změny](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Filtrování založené na organizačních jednotkách
Upřednostňovaným způsobem, jak změnit filtrování založené na ou, je spuštění průvodce instalací a změna [filtrování domény a ou.](how-to-connect-install-custom.md#domain-and-ou-filtering) Průvodce instalací automatizuje všechny úkoly, které jsou popsány v tomto tématu.

Tyto kroky byste měli postupovat pouze v případě, že z nějakého důvodu nemůžete spustit průvodce instalací.

Chcete-li nakonfigurovat filtrování založené na organizačních jednotkách, postupujte takto:

1. Přihlaste se k serveru, na který běží synchronizace Azure AD Connect, pomocí účtu, který je členem skupiny zabezpečení **ADSyncAdmins.**
2. **Spusťte službu synchronizace** z nabídky **Start.**
3. Vyberte **Konektory**a v seznamu **Konektory** vyberte konektor s typem **Služby Active Directory Domain Services**. V **okně Akce**vyberte možnost **Vlastnosti**.  
   ![Vlastnosti konektoru](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Klepněte na tlačítko **Konfigurovat oddíly adresáře**, vyberte doménu, kterou chcete konfigurovat, a klepněte na **položku Kontejnery**.
5. Po zobrazení výzvy zadejte všechna pověření s přístupem pro čtení do místní služby Active Directory. Nemusí to být uživatel, který je předem vyplněn v dialogovém okně.
6. V dialogovém okně **Vybrat kontejnery** zrušte zaškrtnutí ouslužeb, které nechcete synchronizovat s cloudovým adresářem, a klepněte na tlačítko **OK**.  
   ![Hlavní uživatele v dialogovém okně Vybrat kontejnery](./media/how-to-connect-sync-configure-filtering/ou.png)  
   * **Kontejner Počítače** by měl být vybrán pro počítače s Windows 10, které mají být úspěšně synchronizovány s Azure AD. Pokud jsou počítače spojené s doménou umístěny v jiných operačních úsech, zkontrolujte, zda jsou vybrány.
   * Pokud máte několik doménových struktur se vztahem důvěryhodnosti, měl by být vybraný kontejner **ForeignSecurityPrincipals**. Tento kontejner umožňuje řešit členství ve skupinách zabezpečení napříč doménovými strukturami.
   * Byla by vybrána hlavní výuce **RegisteredDevices,** pokud jste povolili funkci zpětného zápisu zařízení. Pokud používáte jinou funkci zpětného zápisu, například zpětný zápis skupiny, ujistěte se, že jsou vybrána tato umístění.
   * Vyberte libovolnou jinou organizační organizační organizační položku, ve které jsou umístěni uživatelé, uživatelé, iNetOrgPersons, skupiny, kontakty a počítače. Na obrázku jsou všechny tyto hlavní nyové únoky umístěny v ousu ManagedObjects.
   * Pokud používáte filtrování založené na skupině, musí být zahrnuta hlavní výtěr, ve které se skupina nachází.
   * Všimněte si, že můžete nakonfigurovat, zda nové hlavní uživatele, které jsou přidány po dokončení konfigurace filtrování jsou synchronizovány nebo nejsou synchronizovány. Podrobnosti naleznete v další části.
7. Až budete hotovi, zavřete dialogové okno **Vlastnosti** klepnutím na **tlačítko OK**.
8. Chcete-li dokončit konfiguraci, je třeba spustit **úplný import** a **synchronizaci delta**. Pokračovat ve čtení části [Použít a ověřit změny](#apply-and-verify-changes).

### <a name="synchronize-new-ous"></a>Synchronizace nových operačních dispozičů
Nové výlonové uživatele, které jsou vytvořeny po konfiguraci filtrování, jsou ve výchozím nastavení synchronizovány. Tento stav je označen zaškrtnutým zaškrtávacím políčkem. Můžete také zrušit výběr některých dílčích ouunů. Chcete-li toto chování získat, klepněte na toto políčko, dokud se nestane bílým s modrým zaškrtnutím (jeho výchozí stav). Potom zrušte výběr všech dílčích ouun, které nechcete synchronizovat.

Pokud jsou synchronizovány všechny dílčí vou, je pole bílé s modrým zaškrtnutím.  
![Ou se všemi vybranými poli](./media/how-to-connect-sync-configure-filtering/ousyncnewall.png)

Pokud některé dílčí vou nebyly vybrány, je pole šedé s bílým zaškrtnutím.  
![Ou s některými sub-OU nevybranými](./media/how-to-connect-sync-configure-filtering/ousyncnew.png)

S touto konfigurací je synchronizována nová ouon, která byla vytvořena v rámci managedobjects.

Průvodce instalací služby Azure AD Connect vždy vytvoří tuto konfiguraci.

### <a name="dont-synchronize-new-ous"></a>Nesynchronizovat nové operační příkazy
Synchronizační modul můžete nakonfigurovat tak, aby po dokončení konfigurace filtrování nesynchronizoval nové hlavní uživatele. Tento stav je v uj. Chcete-li toto chování získat, klepněte na toto políčko, dokud nebude bílé bez zaškrtnutí. Potom vyberte dílčí hlavní uživatele, které chcete synchronizovat.

![OU s nevybraným kořenem](./media/how-to-connect-sync-configure-filtering/oudonotsyncnew.png)

S touto konfigurací není synchronizována nová ouon, která byla vytvořena pod managedobjekty.

## <a name="attribute-based-filtering"></a>Filtrování založené na atributech
Ujistěte se, že používáte listopad 2015[(1.0.9125)](reference-connect-version-history.md#1091250)nebo novější sestavení pro tyto kroky do práce.

> [!IMPORTANT]
>Společnost Microsoft doporučuje neupravovat výchozí pravidla vytvořená **službou Azure AD Connect**. Pokud chcete pravidlo upravit, naklonujte jej a zakažte původní pravidlo. Proveďte změny klonovaného pravidla. Vezměte prosím na vědomí, že tímto způsobem (zakázáním původního pravidla) vám budou chybět všechny opravy chyb nebo funkce povolené prostřednictvím tohoto pravidla.

Filtrování založené na atributech je nejflexibilnější způsob filtrování objektů. Můžete použít moc [deklarativní zřizování](concept-azure-ad-connect-sync-declarative-provisioning.md) k řízení téměř každý aspekt při synchronizaci objektu do Azure AD.

[Příchozí](#inbound-filtering) filtrování ze služby Active Directory můžete použít na metaverse a [odchozí](#outbound-filtering) filtrování z metaverse do služby Azure AD. Doporučujeme použít příchozí filtrování, protože to je nejjednodušší udržovat. Odchozí filtrování byste měli použít pouze v případě, že je nutné připojit objekty z více než jedné doménové struktury, než může být provedeno vyhodnocení.

### <a name="inbound-filtering"></a>Příchozí filtrování
Příchozí filtrování používá výchozí konfiguraci, kde objekty směřující do Služby Azure AD musí mít atribut metaverse cloudFiltered není nastavena na hodnotu, která má být synchronizována. Pokud je hodnota tohoto atributu nastavena na **hodnotu True**, objekt není synchronizován. Nemělby být nastaven na **False**, záměrné. Chcete-li se ujistit, že ostatní pravidla mají schopnost přispívat hodnotu, tento atribut má mít pouze hodnoty **True** nebo **NULL** (chybí).

Při příchozím filtrování použijete výkon **oboru** k určení objektů, které mají být synchronizovány nebo nejsou synchronizovány. Toto je místo, kde můžete provést úpravy, aby vyhovovaly vašim požadavkům organizace. Modul oboru má **skupinu** a **klauzuli** k určení, kdy je pravidlo synchronizace v oboru. Skupina obsahuje jednu nebo více klauzulí. Existuje logické "AND" mezi více klauzulí a logické "OR" mezi více skupin.

Podívejme se na příklad:  
![Rozsah](./media/how-to-connect-sync-configure-filtering/scope.png)  
To by mělo být čteno jako **(oddělení = IT) NEBO (oddělení = Prodej A c = nás)**.

V následujících ukázkách a krocích použijete jako příklad objekt uživatele, ale můžete jej použít pro všechny typy objektů.

V následujících ukázkách začíná hodnota priority 50. Může se jedná o libovolné číslo, které se nepoužívá, ale mělo by být nižší než 100.

#### <a name="negative-filtering-do-not-sync-these"></a>Negativní filtrování: "nesynchronizovat tyto"
V následujícím příkladu odfiltrujete (nesynchronizujete) všechny uživatele, u kterých má **extensionAttribute15** hodnotu **NoSync**.

1. Přihlaste se k serveru, na který běží synchronizace Azure AD Connect, pomocí účtu, který je členem skupiny zabezpečení **ADSyncAdmins.**
2. **Spusťte Editor pravidel synchronizace** z nabídky **Start.**
3. Zkontrolujte, jestli je **vybraná možnost Příchozí,** a klepněte na **tlačítko Přidat nové pravidlo**.
4. Pojmenujte pravidlo popisným názvem, například *"In from AD – User DoNotSyncFilter*". Vyberte správnou doménovou strukturu, vyberte **Uživatel** jako **typ objektu CS**a jako **typ objektu MV**vyberte **Osoba** . V **okně Typ propojení**vyberte Připojit **.** Do **pole Priorita**zadejte hodnotu, která není aktuálně používána jiným pravidlem synchronizace (například 50), a klepněte na tlačítko **Další**.  
   ![Popis příchozího 1](./media/how-to-connect-sync-configure-filtering/inbound1.png)  
5. Ve **filtru Obory**klepněte na **tlačítko Přidat skupinu**a klepněte na tlačítko **Přidat klauzuli**. V **atributu**vyberte **Položku ExtensionAttribute15**. Ujistěte se, že **operátor** je nastavena na **EQUAL**a zadejte hodnotu **NoSync** do pole **Hodnota.** Klikněte na **Další**.  
   ![Rozsah příchozích 2](./media/how-to-connect-sync-configure-filtering/inbound2.png)  
6. Ponechejte pravidla **spojení** prázdná a klepněte na tlačítko **Další**.
7. Klepněte na **Přidat transformaci**, vyberte **FlowType** jako **konstantní**a vyberte **cloudFiltered** jako **cílový atribut**. Do textového pole **Zdroj** zadejte **hodnotu True**. Kliknutím na **Přidat** pravidlo uložte.  
   ![Příchozí transformace 3](./media/how-to-connect-sync-configure-filtering/inbound3.png)
8. Chcete-li dokončit konfiguraci, je třeba spustit **úplnou synchronizaci**. Pokračovat ve čtení části [Použít a ověřit změny](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Pozitivní filtrování: "pouze synchronizovat tyto"
Vyjádření pozitivního filtrování může být náročnější, protože je také třeba zvážit objekty, které nejsou zřejmé, že mají být synchronizovány, například konferenční místnosti. Budete také přepsat výchozí filtr v pravidle out-of-box **V ze ad - user join**. Při vytváření vlastního filtru nezapomeňte nezahrnout kritické systémové objekty, objekty konfliktů replikace, speciální poštovní schránky a účty služeb pro Azure AD Connect.

Možnost pozitivního filtrování vyžaduje dvě pravidla synchronizace. K synchronizaci potřebujete jedno pravidlo (nebo několik) se správným rozsahem objektů. Potřebujete také druhé pravidlo synchronizace catch-all, které odfiltruje všechny objekty, které ještě nebyly identifikovány jako objekt, který by měl být synchronizován.

V následujícím příkladu synchronizujete pouze objekty uživatele, u kterých má atribut oddělení hodnotu **Prodej**.

1. Přihlaste se k serveru, na který běží synchronizace Azure AD Connect, pomocí účtu, který je členem skupiny zabezpečení **ADSyncAdmins.**
2. **Spusťte Editor pravidel synchronizace** z nabídky **Start.**
3. Zkontrolujte, jestli je **vybraná možnost Příchozí,** a klepněte na **tlačítko Přidat nové pravidlo**.
4. Pojmenujte pravidlo popisným názvem, například *"In from AD – User Sales sync*". Vyberte správnou doménovou strukturu, vyberte **Uživatel** jako **typ objektu CS**a jako **typ objektu MV**vyberte **Osoba** . V **okně Typ propojení**vyberte Připojit **.** Do **pole Priorita**zadejte hodnotu, která není aktuálně používána jiným pravidlem synchronizace (například 51), a klepněte na tlačítko **Další**.  
   ![Popis příchozích 4](./media/how-to-connect-sync-configure-filtering/inbound4.png)  
5. Ve **filtru Obory**klepněte na **tlačítko Přidat skupinu**a klepněte na tlačítko **Přidat klauzuli**. V **atributu**vyberte **oddělení**. Ujistěte se, že operátor je nastavena na **EQUAL**a zadejte hodnotu **Prodej** do pole **Hodnota.** Klikněte na **Další**.  
   ![Rozsah příchozích 5](./media/how-to-connect-sync-configure-filtering/inbound5.png)  
6. Ponechejte pravidla **spojení** prázdná a klepněte na tlačítko **Další**.
7. Klepněte na **Přidat transformaci**, vyberte **konstantní** jako **FlowType**a vyberte **cloudFiltered** jako **cílový atribut**. Do pole **Zdroj** zadejte **false**. Kliknutím na **Přidat** pravidlo uložte.  
   ![Příchozí 6 transformace](./media/how-to-connect-sync-configure-filtering/inbound6.png)  
   Toto je zvláštní případ, kdy explicitně nastavit cloudFiltered na **False**.
8. Nyní musíme vytvořit pravidlo synchronizace catch-all. Pojmenujte pravidlo popisným názvem, například "*In from AD – User Catch-all filter*". Vyberte správnou doménovou strukturu, vyberte **Uživatel** jako **typ objektu CS**a jako **typ objektu MV**vyberte **Osoba** . V **okně Typ propojení**vyberte Připojit **.** Do **pole Priorita**zadejte hodnotu, která není aktuálně používána jiným pravidlem synchronizace (například 99). Vybrali jste hodnotu priority, která je vyšší (nižší priorita) než předchozí pravidlo synchronizace. Ale také jste opustili některé místnosti, takže můžete přidat další filtrování pravidel synchronizace později, když chcete začít synchronizovat další oddělení. Klikněte na **Další**.  
   ![Popis příchozího 7](./media/how-to-connect-sync-configure-filtering/inbound7.png)  
9. Ponechejte **filtr oborů** prázdný a klepněte na tlačítko **Další**. Prázdný filtr označuje, že pravidlo má být použito na všechny objekty.
10. Ponechejte pravidla **spojení** prázdná a klepněte na tlačítko **Další**.
11. Klepněte na **Přidat transformaci**, vyberte **konstantní** jako **FlowType**a vyberte **cloudFiltered** jako **cílový atribut**. Do pole **Zdroj** zadejte **hodnotu True**. Kliknutím na **Přidat** pravidlo uložte.  
    ![Příchozí transformace 3](./media/how-to-connect-sync-configure-filtering/inbound3.png)  
12. Chcete-li dokončit konfiguraci, je třeba spustit **úplnou synchronizaci**. Pokračovat ve čtení části [Použít a ověřit změny](#apply-and-verify-changes).

Pokud potřebujete, můžete vytvořit další pravidla prvního typu, kde do synchronizace zahrnete více objektů.

### <a name="outbound-filtering"></a>Odchozí filtrování
V některých případech je nutné provést filtrování pouze po objekty se připojily v metaverse. Může být například nutné podívat se na atribut pošty z doménové struktury prostředků a atribut userPrincipalName z doménové struktury účtu, abyste zjistili, zda má být objekt synchronizován. V těchto případech vytvoříte filtrování odchozího pravidla.

V tomto příkladu změníte filtrování tak, aby byly synchronizovány pouze @contoso.com uživatelé, kteří mají svou poštu a adresu userPrincipalName končící:

1. Přihlaste se k serveru, na který běží synchronizace Azure AD Connect, pomocí účtu, který je členem skupiny zabezpečení **ADSyncAdmins.**
2. **Spusťte Editor pravidel synchronizace** z nabídky **Start.**
3. V části **Typ pravidla**klepněte na **položku Odchozí**.
4. V závislosti na verzi aplikace Connect, kterou používáte, najděte buď pravidlo s názvem **Out to AAD – User Join** or Out to **AAD – User Join SOAInAD**a klepněte na tlačítko **Upravit**.
5. V automaticky otevírané okno odpovězte **ano** a vytvořte kopii pravidla.
6. Na stránce **Popis** změňte **prioritu** na nepoužitou hodnotu, například 50.
7. V levé navigaci klikněte na **filtr oborů** a potom klikněte na **Add clause**. V **atributu**vyberte **položku Pošta**. V **operatoru**vyberte **možnost ENDSWITH**. Do **pole Hodnota**zadejte ** \@contoso.com**a klepněte na tlačítko Přidat **klauzuli**. V **atributu**vyberte **userPrincipalName**. V **operatoru**vyberte **možnost ENDSWITH**. Do **pole Hodnota**zadejte ** \@contoso.com**.
8. Klikněte na **Uložit**.
9. Chcete-li dokončit konfiguraci, je třeba spustit **úplnou synchronizaci**. Pokračovat ve čtení části [Použít a ověřit změny](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Použití a ověření změn
Po provedené změny konfigurace je nutné je použít na objekty, které jsou již v systému k dispozici. Může se také zobrazit, že objekty, které nejsou aktuálně v synchronizačním modulu, by měly být zpracovány (a synchronizační modul musí znovu číst zdrojový systém, aby ověřil jeho obsah).

Pokud jste změnili konfiguraci pomocí filtrování **domény** nebo **organizační jednotky,** je třeba provést **úplný import**následovaný **synchronizací delta**.

Pokud jste změnili konfiguraci pomocí filtrování **atributů,** je třeba provést **úplnou synchronizaci**.

Postupujte takto:

1. **Spusťte službu synchronizace** z nabídky **Start.**
2. Vyberte **konektory**. V seznamu **Konektory** vyberte konektor, ve kterém jste dříve provedli změnu konfigurace. V **poakcích**vyberte **Spustit**.  
   ![Konektor spustit](./media/how-to-connect-sync-configure-filtering/connectorrun.png)  
3. V **části Spustit profily**vyberte operaci, která byla zmíněna v předchozí části. Pokud potřebujete spustit dvě akce, spusťte druhou po dokončení první akce. (Sloupec **Stav** je **nečinný** pro vybraný konektor.)

Po synchronizaci jsou všechny změny připraveny k exportu. Než skutečně provedete změny ve službě Azure AD, chcete ověřit, že všechny tyto změny jsou správné.

1. Spusťte příkazový řádek `%ProgramFiles%\Microsoft Azure AD Sync\bin`a přejděte na .
2. Spusťte `csexport "Name of Connector" %temp%\export.xml /f:x`.  
   Název konektoru je ve službě synchronizace. Má název podobný "contoso.com – AAD" pro Azure AD.
3. Spusťte `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`.
4. Nyní máte soubor v %temp% s názvem export.csv, který lze zkontrolováno v aplikaci Microsoft Excel. Tento soubor obsahuje všechny změny, které mají být exportovány.
5. Proveďte potřebné změny dat nebo konfigurace a spusťte tyto kroky znovu (Import, Synchronizovat a Ověřit), dokud změny, které se chystají exportovat, nejsou podle očekávání.

Až budete spokojeni, exportujte změny do Azure AD.

1. Vyberte **konektory**. V seznamu **konektory** vyberte konektor Azure AD. V **poakcích**vyberte **Spustit**.
2. V **možnosti Spustit profily**vyberte **exportovat**.
3. Pokud změny konfigurace odstraní mnoho objektů, zobrazí se v exportu chyba, pokud je číslo větší než nakonfigurovaná prahová hodnota (ve výchozím nastavení 500). Pokud se zobrazí tato chyba, musíte dočasně zakázat funkci["zabránit náhodnému odstranění".](how-to-connect-sync-feature-prevent-accidental-deletes.md)

Nyní je čas znovu povolit plánovač.

1. Spusťte **plánovač úloh** z nabídky **Start.**
2. Přímo v části **Knihovna plánovače úloh**vyhledejte úlohu s názvem **Plánovač synchronizace Azure AD**, klepněte pravým tlačítkem myši a vyberte **povolit**.

## <a name="group-based-filtering"></a>Filtrování založené na skupině
Filtrování založené na skupinách můžete nakonfigurovat při první instalaci služby Azure AD Connect pomocí [vlastní instalace](how-to-connect-install-custom.md#sync-filtering-based-on-groups). Je určen pro pilotní nasazení, kde chcete synchronizovat pouze malou sadu objektů. Pokud zakážete filtrování založené na skupinách, nelze jej znovu povolit. *Není podporováno* použití filtrování na základě skupiny ve vlastní konfiguraci. Konfigurace této funkce je podporována pouze pomocí průvodce instalací. Po dokončení pilotního projektu použijte jednu z dalších možností filtrování v tomto tématu. Při použití filtrování založeného na ou ve spojení s filtrováním na základě skupiny musí být zahrnuta ou diody, ve kterých se skupina a její členové nacházejí.

Při synchronizaci více doménových struktur služby AD můžete nakonfigurovat filtrování založené na skupinách zadáním jiné skupiny pro každou spojnici služby AD. Pokud chcete synchronizovat uživatele v jedné doménové struktuře služby AD a stejný uživatel má jeden nebo více odpovídajících objektů v jiných doménových strukturách služby AD, musíte zajistit, aby objekt uživatele a všechny jeho odpovídající objekty byly v rámci oboru filtrování založeného na skupině. Příklady:

* Máte uživatele v jedné doménové struktuře, která má odpovídající objekt FSP (Foreign Security Principal) v jiné doménové struktuře. Oba objekty musí být v rámci oboru filtrování na základě skupiny. V opačném případě nebude uživatel synchronizován do služby Azure AD.

* Máte uživatele v jedné doménové struktuře, která má odpovídající účet prostředků (např. propojenou poštovní schránku) v jiné doménové struktuře. Dále jste nakonfigurovali Azure AD Connect pro propojení uživatele s účtem prostředků. Oba objekty musí být v rámci oboru filtrování na základě skupiny. V opačném případě nebude uživatel synchronizován do služby Azure AD.

* Máte uživatele v jedné doménové struktuře, která má odpovídající kontakt pošty v jiné doménové struktuře. Dále jste nakonfigurovali Azure AD Connect pro propojení uživatele s kontaktem pošty. Oba objekty musí být v rámci oboru filtrování na základě skupiny. V opačném případě nebude uživatel synchronizován do služby Azure AD.


## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o konfiguraci [synchronizace Azure AD Connect.](how-to-connect-sync-whatis.md)
- Přečtěte si další informace o [integraci místních identit s Azure AD](whatis-hybrid-identity.md).
