---
title: 'Azure AD Connect synchronizace: Konfigurace filtrování | Microsoft Docs'
description: Vysvětluje, jak nakonfigurovat filtrování v Azure AD Connect synchronizaci.
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
ms.topic: how-to
ms.date: 03/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 595cf2c1dbc105634d33b426c67e5123b9751e6e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996538"
---
# <a name="azure-ad-connect-sync-configure-filtering"></a>Synchronizace Azure AD Connect: Konfigurace filtrování
Pomocí filtrování můžete určit, které objekty se zobrazí v Azure Active Directory (Azure AD) z místního adresáře. Výchozí konfigurace přebírá všechny objekty ve všech doménách v konfigurovaných doménových strukturách. Obecně platí, že se jedná o doporučenou konfiguraci. Uživatelé, kteří používají Microsoft 365 úlohy, jako jsou Exchange Online a Skype pro firmy, můžou využít kompletní globální seznam adres, aby mohli posílat e-maily a volat všechny. S výchozí konfigurací by měly stejné prostředí jako při místní implementaci Exchange nebo Lyncu.

V některých případech ale potřebujete udělat změny ve výchozí konfiguraci. Tady je několik příkladů:

* Plánujete používání [topologie adresářů služby Multi-Azure AD](plan-connect-topologies.md#each-object-only-once-in-an-azure-ad-tenant). Pak je nutné použít filtr k řízení, které objekty jsou synchronizovány do konkrétního adresáře služby Azure AD.
* Spustíte pilotní projekt pro Azure nebo Microsoft 365 a budete chtít jenom podmnožinu uživatelů v Azure AD. V případě malého pilotního projektu není důležité mít úplný globální seznam adres k předvedení funkce.
* Máte spoustu účtů služeb a dalších nepracovních účtů, které v Azure AD nechcete.
* Z důvodu dodržování předpisů neodstraňujte žádné uživatelské účty v místním prostředí. Zakážete je jenom vy. Ale v Azure AD chcete mít k dispozici jenom aktivní účty.

Tento článek popisuje, jak nakonfigurovat různé metody filtrování.

> [!IMPORTANT]
> Microsoft nepodporuje úpravy ani provoz synchronizace služby Azure AD Connect mimo formálně zdokumentované akce. Některé z těchto akcí můžou mít za následek nekonzistentní nebo nepodporovaný stav Azure AD Connect synchronizace. V důsledku toho společnost Microsoft nemůže poskytnout technickou podporu pro taková nasazení.

## <a name="basics-and-important-notes"></a>Základy a důležité poznámky
V Azure AD Connect synchronizace můžete povolit filtrování kdykoli. Pokud začnete s výchozí konfigurací synchronizace adresářů a pak nakonfigurujete filtrování, objekty, které jsou odfiltrované, se už nebudou synchronizovat do Azure AD. Z důvodu této změny jsou všechny objekty v Azure AD, které byly dříve synchronizovány, ale byly filtrovány, odstraněny ve službě Azure AD.

Než začnete provádět změny filtrování, ujistěte se, že jste [naplánovali úlohu](#disable-the-scheduled-task) , abyste omylem neexportovali změny, které jste ještě neověřili, aby byly správné.

Vzhledem k tomu, že filtrování může odebrat více objektů najednou, chcete zajistit, aby nové filtry byly správné, než začnete s exportem změn v Azure AD. Po dokončení kroků konfigurace důrazně doporučujeme, abyste před exportem a provedením změn ve službě Azure AD provedli [Postup ověření](#apply-and-verify-changes) .

Chcete-li chránit před odstraněním více objektů havárií, je ve výchozím nastavení zapnuta funkce "[zabránit nechtěnému odstranění](how-to-connect-sync-feature-prevent-accidental-deletes.md)". Pokud odstraníte mnoho objektů z důvodu filtrování (standardně 500), je nutné postupovat podle kroků v tomto článku, které umožní, aby se odstranění procházela do služby Azure AD.

Pokud použijete Build před 2015. listopadu ([1.0.9125](reference-connect-version-history.md)), proveďte změnu konfigurace filtru a použijte synchronizaci hodnot hash hesel, pak budete muset po dokončení konfigurace aktivovat úplnou synchronizaci všech hesel. Postup, jak spustit úplnou synchronizaci s heslem, najdete v tématu [Aktivace úplné synchronizace všech hesel](tshoot-connect-password-hash-synchronization.md#trigger-a-full-sync-of-all-passwords). Pokud jste na Build 1.0.9125 nebo novějším, pak pravidelná akce **úplné synchronizace** také vypočítá, jestli se mají hesla synchronizovat, a pokud už tento další krok nepotřebujete.

Pokud se **uživatelské** objekty ve službě Azure AD omylem odstranily kvůli chybě filtrování, můžete objekty uživatelů v Azure AD znovu vytvořit odebráním konfigurací filtrování. Potom můžete adresáře znovu synchronizovat. Tato akce obnoví uživatele z odpadkového koše ve službě Azure AD. Nemůžete však zrušit odstranění jiných typů objektů. Pokud například omylem odstraníte skupinu zabezpečení a použijete ji k prostředku ACL, skupinu a její seznamy ACL nelze obnovit.

Azure AD Connect odstraní pouze objekty, které mají za následek, že jsou v oboru. Pokud jsou v Azure AD objekty, které byly vytvořené jiným synchronizačním modulem, a tyto objekty nejsou v oboru, přidání filtrování je neodstraní. Například pokud začnete se serverem DirSync, který vytvořil úplnou kopii celého adresáře ve službě Azure AD, a nainstalujete nový Azure AD Connect synchronizační Server paralelně s filtrováním povoleným od začátku, Azure AD Connect neodstraní nadbytečné objekty, které jsou vytvořené pomocí DirSync.

Konfigurace filtrování se zachová při instalaci nebo upgradu na novější verzi Azure AD Connect. Vždy je vhodné ověřit, že se konfigurace po upgradu na novější verzi ještě před spuštěním prvního synchronizačního cyklu neúmyslně změnila.

Pokud máte více než jednu doménovou strukturu, musíte použít konfigurace filtrování popsané v tomto tématu pro každou doménovou strukturu (za předpokladu, že chcete stejnou konfiguraci pro všechny z nich).

### <a name="disable-the-scheduled-task"></a>Zakázat naplánovanou úlohu
Chcete-li zakázat předdefinovaný Plánovač, který spouští cyklus synchronizace každých 30 minut, postupujte podle následujících kroků:

1. Přejít na příkazový řádek PowerShellu.
2. Spusťte příkaz `Set-ADSyncScheduler -SyncCycleEnabled $False` pro zakázání plánovače.
3. Proveďte změny popsané v tomto článku.
4. Spusťte příkaz `Set-ADSyncScheduler -SyncCycleEnabled $True` a znovu povolte Plánovač.

**Použijete-li sestavení Azure AD Connect před 1.1.105.0**  
Pokud chcete zakázat naplánovanou úlohu, která spouští synchronizační cyklus každé tři hodiny, postupujte podle těchto kroků:

1. Spusťte **Plánovač úloh** v nabídce **Start** .
2. Přímo v rámci **knihovny Plánovač úloh** vyhledejte úkol s názvem **Azure AD Sync Scheduler**, klikněte pravým tlačítkem myši a vyberte možnost **Zakázat**.  
   ![Plánovač úloh](./media/how-to-connect-sync-configure-filtering/taskscheduler.png)  
3. Nyní můžete provést změny konfigurace a spustit synchronizační modul ručně z konzoly **Synchronization Service Manager** .

Až dokončíte všechny změny filtrování, nezapomeňte se vrátit zpátky a znovu **Povolit** úlohu.

## <a name="filtering-options"></a>Možnosti filtrování
Pro nástroj pro synchronizaci adresářů můžete použít následující typy konfigurace filtrování:

* [**Založené na skupinách**](#group-based-filtering): filtrování založené na jedné skupině se dá nakonfigurovat jenom při počáteční instalaci pomocí Průvodce instalací.
* [**Založené na doméně**](#domain-based-filtering): pomocí této možnosti můžete vybrat domény, které se synchronizují do Azure AD. Domény můžete také přidat a odebrat z konfigurace synchronizačního prostředí, když provedete změny v místní infrastruktuře po instalaci Azure AD Connect synchronizace.
* [**Organizační jednotka (OU)**](#organizational-unitbased-filtering): pomocí této možnosti můžete vybrat, které organizační jednotky se synchronizují do Azure AD. Tato možnost je určena pro všechny typy objektů ve vybraných organizačních jednotkách.
* [**Založené na atributech**](#attribute-based-filtering): pomocí této možnosti můžete objekty filtrovat na základě hodnot atributů objektů. Můžete mít také různé filtry pro různé typy objektů.

Můžete použít více možností filtrování současně. Filtrování na základě organizační jednotky můžete například použít pouze k zahrnutí objektů do jedné organizační jednotky. Ve stejnou chvíli můžete použít filtrování založené na atributech k dalšímu filtrování objektů. Při použití více metod filtrování používá filtr logické a mezi filtry.

## <a name="domain-based-filtering"></a>Filtrování založené na doméně
V této části najdete postup konfigurace filtru domény. Pokud jste do doménové struktury přidali nebo odebrali domény po instalaci Azure AD Connect, je také nutné aktualizovat konfiguraci filtrování.

Upřednostňovaným způsobem, jak změnit filtrování založené na doméně, je spuštění Průvodce instalací a změna [filtrování domén a organizačních jednotek](how-to-connect-install-custom.md#domain-and-ou-filtering). Průvodce instalací automatizuje všechny úlohy popsané v tomto tématu.

Pokud z nějakého důvodu nemůžete spustit Průvodce instalací, měli byste postupovat podle těchto kroků.

Konfigurace filtrování založené na doméně se skládá z těchto kroků:

1. Vyberte domény, které chcete zahrnout do synchronizace.
2. U každé přidané a odebrané domény upravte profily spuštění.
3. [Použijte a ověřte změny](#apply-and-verify-changes).

### <a name="select-the-domains-to-be-synchronized"></a>Vyberte domény, které chcete synchronizovat.
Existují dva způsoby, jak vybrat domény k synchronizaci:
    - Použití synchronizační služby
    - Pomocí Průvodce Azure AD Connect.


#### <a name="select-the-domains-to-be-synchronized-using-the-synchronization-service"></a>Vyberte domény, které se mají synchronizovat pomocí synchronizační služby.
Chcete-li nastavit filtr domény, proveďte následující kroky:

1. Přihlaste se k serveru, na kterém běží Azure AD Connect synchronizace pomocí účtu, který je členem skupiny zabezpečení **ADSyncAdmins** .
2. Spusťte **synchronizační službu** z nabídky **Start** .
3. Vyberte možnost **konektory** a v seznamu **konektory** vyberte konektor s typem **Active Directory Domain Services**. V **Možnosti akce** vyberte **vlastnosti**.  
   ![Vlastnosti konektoru](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Klikněte na **Konfigurovat oddíly adresáře**.
5. V seznamu **Vybrat oddíly adresářů** vyberte a zrušte výběr domén podle potřeby. Ověřte, zda jsou vybrány pouze oddíly, které chcete synchronizovat.  
   ![Snímek obrazovky, který zobrazuje oddíly adresáře v okně Vlastnosti.](./media/how-to-connect-sync-configure-filtering/connectorpartitions.png)  
   Pokud jste změnili místní infrastrukturu služby Active Directory a přidali nebo odebrali domény z doménové struktury, klikněte na tlačítko **aktualizovat** a získejte aktualizovaný seznam. Po obnovení budete požádáni o zadání přihlašovacích údajů. Poskytněte jakékoli přihlašovací údaje s oprávněním ke čtení pro službu Windows Server Active Directory. Nemusí se jednat o uživatele, který je předem vyplněný v dialogovém okně.  
   ![Nutná aktualizace](./media/how-to-connect-sync-configure-filtering/refreshneeded.png)  
6. Až skončíte, zavřete dialogové okno **vlastnosti** kliknutím na **OK**. Pokud jste z doménové struktury odebrali domény, zobrazí se automaticky otevírané okno se zprávou, že se odebrala doména a tato konfigurace se vyčistí.
7. Pokračujte v úpravách profilů spuštění.

#### <a name="select-the-domains-to-be-synchronized-using-the-azure-ad-connect-wizard"></a>Vyberte domény, které se mají synchronizovat pomocí Průvodce Azure AD Connect.
Chcete-li nastavit filtr domény, proveďte následující kroky:

1.  Spuštění Průvodce Azure AD Connect
2.  Klikněte na **Konfigurovat**.
3.  Vyberte možnost **přizpůsobit možnosti synchronizace** a klikněte na tlačítko **Další**.
4.  Zadejte svoje přihlašovací údaje služby Azure AD.
5.  Na obrazovce **připojené adresáře** klikněte na **Další**.
6.  Na **stránce filtrování domén a organizačních jednotek** klikněte na **aktualizovat**.  Nové domény se nyní zobrazí a odstraněné domény zmizí.
   ![Oddíly](./media/how-to-connect-sync-configure-filtering/update2.png)  

### <a name="update-the-run-profiles"></a>Aktualizace profilů spuštění
Pokud jste aktualizovali svůj doménový filtr, budete také muset aktualizovat profily spuštění.

1. V seznamu **konektory** zkontrolujte, zda je zvolen konektor, který jste změnili v předchozím kroku. V **Možnosti akce** vyberte **Konfigurovat profily spuštění**.  
   ![Profily spuštění konektoru 1](./media/how-to-connect-sync-configure-filtering/connectorrunprofiles1.png)  
2. Vyhledejte a Identifikujte následující profily:
    * Full Import
    * Úplná synchronizace
    * Rozdílový import
    * Rozdílová synchronizace
    * Export
3. Pro každý profil upravte **přidané** a **odebrané** domény.
    1. Pro každý z pěti profilů proveďte následující kroky pro každou **přidanou** doménu:
        1. Vyberte profil spuštění a klikněte na **Nový krok**.
        2. Na stránce **Konfigurovat krok** v rozevírací nabídce **typ** vyberte typ kroku se stejným názvem, jako má profil, který konfigurujete. Potom klikněte na **Další**.  
        ![Profily spuštění konektoru 2](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep1.png)  
        3. Na stránce **Konfigurace konektoru** v rozevírací nabídce **oddíl** vyberte název domény, kterou jste přidali do filtru domény.  
        ![Profily spuštění konektoru 3](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep2.png)  
        4. Zavřete dialogové okno **Konfigurovat profil spuštění** kliknutím na tlačítko **Dokončit**.
    2. Pro každý z pěti profilů proveďte následující kroky pro každou z **odebraných** domén:
        1. Vyberte profil spuštění.
        2. Pokud je **hodnota** atributu **partition** identifikátorem GUID, vyberte krok spuštění a klikněte na **Odstranit krok**.  
        ![Profily spuštění konektoru 4](./media/how-to-connect-sync-configure-filtering/runprofilesdeletestep.png)  
    3. Ověřte změnu. Každá doména, kterou chcete synchronizovat, by měla být v jednotlivých profilech spuštění uvedena jako krok.
4. Chcete-li zavřít dialogové okno **Konfigurovat profily spuštění** , klikněte na tlačítko **OK**.
5.  Chcete-li dokončit konfiguraci, je nutné spustit **úplný import** a **rozdílovou synchronizaci**. Pokračujte v čtení části [použití a ověření změn](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Filtrování na základě organizační jednotky
Upřednostňovaným způsobem, jak změnit filtrování na základě organizační jednotky, je spuštění Průvodce instalací a změna [filtrování domén a organizačních jednotek](how-to-connect-install-custom.md#domain-and-ou-filtering). Průvodce instalací automatizuje všechny úlohy popsané v tomto tématu.

Pokud z nějakého důvodu nemůžete spustit Průvodce instalací, měli byste postupovat podle těchto kroků.

Chcete-li nakonfigurovat filtrování na základě organizační jednotky, proveďte následující kroky:

1. Přihlaste se k serveru, na kterém běží Azure AD Connect synchronizace pomocí účtu, který je členem skupiny zabezpečení **ADSyncAdmins** .
2. Spusťte **synchronizační službu** z nabídky **Start** .
3. Vyberte možnost **konektory** a v seznamu **konektory** vyberte konektor s typem **Active Directory Domain Services**. V **Možnosti akce** vyberte **vlastnosti**.  
   ![Vlastnosti konektoru](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Klikněte na **Konfigurovat oddíly adresáře**, vyberte doménu, kterou chcete nakonfigurovat, a potom klikněte na **kontejnery**.
5. Po zobrazení výzvy zadejte přihlašovací údaje s oprávněním ke čtení vaší místní služby Active Directory. Nemusí se jednat o uživatele, který je předem vyplněný v dialogovém okně.
6. V dialogovém okně **Vybrat kontejnery** vymažte jednotky, které nechcete synchronizovat s adresářem cloudu, a klikněte na tlačítko **OK**.  
   ![Organizační jednotky v dialogovém okně vybrat kontejnery](./media/how-to-connect-sync-configure-filtering/ou.png)  
   * Pro počítače s Windows 10, které se mají úspěšně synchronizovat do Azure AD, by se měl vybrat kontejner **počítače** . Pokud jsou počítače připojené k doméně umístěné v jiných organizačních jednotkách, ujistěte se, že jsou vybrané.
   * Pokud máte několik doménových struktur se vztahem důvěryhodnosti, měl by být vybraný kontejner **ForeignSecurityPrincipals**. Tento kontejner umožňuje řešit členství ve skupinách zabezpečení napříč doménovými strukturami.
   * Pokud jste povolili funkci zpětného zápisu zařízení, měla by být vybraná organizační jednotka **RegisteredDevices** . Pokud používáte jinou funkci zpětného zápisu, třeba zpětný zápis skupiny, ujistěte se, že jsou tato umístění vybraná.
   * Vyberte všechny ostatní organizační jednotky, kde se nacházejí uživatelé, třídy iNetOrgPerson, skupiny, kontakty a počítače. Na obrázku jsou všechny tyto organizační jednotky umístěny v ManagedObjects organizační jednotce.
   * Pokud používáte filtrování na základě skupin, musí být zahrnutá organizační jednotka, ve které se skupina nachází.
   * Všimněte si, že můžete nakonfigurovat, jestli se nové organizační jednotky, které se přidají po dokončení konfigurace filtrování, synchronizují nebo nesynchronizují. Podrobnosti najdete v následující části.
7. Až skončíte, zavřete dialogové okno **vlastnosti** kliknutím na **OK**.
8. Chcete-li dokončit konfiguraci, je nutné spustit **úplný import** a **rozdílovou synchronizaci**. Pokračujte v čtení části [použití a ověření změn](#apply-and-verify-changes).

### <a name="synchronize-new-ous"></a>Synchronizovat nové organizační jednotky
Nové organizační jednotky, které se vytvoří po filtrování, se standardně synchronizují. Tento stav je označen vybraným zaškrtávacím políčkem. Můžete také zrušit výběr některých dílčích organizačních jednotek. Chcete-li získat toto chování, klikněte na pole, dokud se neobjeví bíle s modrou značkou zaškrtnutí (výchozí stav). Pak zrušte výběr všech dílčích organizačních jednotek, které nechcete synchronizovat.

Pokud jsou všechny dílčí organizační jednotky synchronizovány, je pole bílá s modrou značkou zaškrtnutí.  
![Organizační jednotka se všemi vybranými poli](./media/how-to-connect-sync-configure-filtering/ousyncnewall.png)

Pokud se některé dílčí organizační jednotky zruší, je pole šedé s bílou značkou zaškrtnutí.  
![Organizační jednotka s některými dílčími organizačními jednotkami není vybraná.](./media/how-to-connect-sync-configure-filtering/ousyncnew.png)

V této konfiguraci je synchronizovaná nová organizační jednotka vytvořená v rámci ManagedObjects.

Průvodce instalací Azure AD Connect vždy vytvoří tuto konfiguraci.

### <a name="dont-synchronize-new-ous"></a>Nesynchronizovat nové organizační jednotky
Po dokončení konfigurace filtrování můžete nakonfigurovat synchronizační modul tak, aby nesynchronizoval nové organizační jednotky. Tento stav je uveden v uživatelském rozhraní pomocí pole s plnou šedou barvou bez zaškrtnutí. Chcete-li získat toto chování, klikněte na pole, dokud se nevrátí do bílé bez zaškrtnutí. Pak vyberte dílčí organizační jednotky, které chcete synchronizovat.

![Organizační jednotka s nevybraným kořenovým adresářem](./media/how-to-connect-sync-configure-filtering/oudonotsyncnew.png)

V této konfiguraci není synchronizovaná nová organizační jednotka vytvořená v rámci ManagedObjects.

## <a name="attribute-based-filtering"></a>Filtrování na základě atributů
Ujistěte se, že k práci s těmito kroky používáte Build 2015 ([1.0.9125](reference-connect-version-history.md)) nebo novější.

> [!IMPORTANT]
>Společnost Microsoft doporučuje, abyste nezměnili výchozí pravidla vytvořená nástrojem **Azure AD Connect**. Pokud chcete pravidlo upravit, naklonujte ho a zakažte původní pravidlo. Proveďte změny klonovaného pravidla. Počítejte s tím, že pokud to uděláte (zakážete původní pravidlo), nebudete mít k dispozici žádné opravy chyb nebo funkce povolené prostřednictvím tohoto pravidla.

Filtrování na základě atributů je nejpružnější způsob, jak filtrovat objekty. Výkon [deklarativního zřizování](concept-azure-ad-connect-sync-declarative-provisioning.md) můžete využít k řízení téměř všech aspektů při synchronizaci objektu s Azure AD.

Můžete použít [příchozí](#inbound-filtering) filtrování ze služby Active Directory do úložiště metaverse a [odchozí](#outbound-filtering) filtrování z úložiště metaverse do Azure AD. Doporučujeme použít filtrování příchozích zpráv, protože je nejjednodušší je udržovat. Filtrování odchozího připojení byste měli použít pouze v případě, že je vyžadováno pro připojení objektů z více než jedné doménové struktury před provedením vyhodnocení.

### <a name="inbound-filtering"></a>Příchozí filtrování
Příchozí filtrování používá výchozí konfiguraci, ve které objekty, které se budou nacházet do služby Azure AD, musí mít atribut úložiště metaverse cloudFiltered nastaven na hodnotu, která má být synchronizovaná. Je-li hodnota tohoto atributu nastavena na **hodnotu true**, objekt není synchronizován. Neměla by být nastavená na **false**, podle návrhu. Aby bylo zajištěno, že další pravidla mají možnost přispívat k hodnotě, má tento atribut pouze hodnoty **true** nebo **null** (chybí).

V části příchozí filtrování můžete použít sílu **rozsahu** k určení, které objekty se mají synchronizovat nebo ne synchronizovat. V takovém případě provádíte úpravy podle požadavků vaší organizace. Modul Scope má **skupinu** a **klauzuli** pro určení, kdy je pravidlo synchronizace v oboru. Skupina obsahuje jednu nebo více klauzulí. K dispozici je logický operátor AND mezi více klauzulemi a logickým operátorem OR mezi více skupinami.

Můžeme se podívat na příklad:  
![Snímek obrazovky znázorňující příklad Přidání filtrů oborů](./media/how-to-connect-sync-configure-filtering/scope.png)  
Ta by se měla číst jako **(oddělení = IT) nebo (oddělení = Sales a c = US)**.

V následujících ukázkách a krocích použijete objekt uživatele jako příklad, ale můžete ho použít pro všechny typy objektů.

V následujících ukázkách začíná hodnota priority 50. Může to být jakékoli nepoužité číslo, ale mělo by být nižší než 100.

#### <a name="negative-filtering-do-not-sync-these"></a>Negativní filtrování: "nesynchronizovat tyto"
V následujícím příkladu odfiltrujete (nesynchronizovat) všechny uživatele, u kterých má **extensionAttribute15** hodnotu **unsync**.

1. Přihlaste se k serveru, na kterém běží Azure AD Connect synchronizace pomocí účtu, který je členem skupiny zabezpečení **ADSyncAdmins** .
2. Spustí **Editor pravidel synchronizace** z nabídky **Start** .
3. Ujistěte se, že je vybraná možnost **příchozí** , a klikněte na **Přidat nové pravidlo**.
4. Zadejte pro pravidlo popisný název, například "*in from AD – User DoNotSyncFilter*". Vyberte správnou doménovou strukturu, vyberte možnost **uživatel** jako **typ objektu cs** a jako **typ objektu MV** vyberte **osoba** . V **typu odkazu** vyberte **připojit**. V poli **Priorita** zadejte hodnotu, která není aktuálně používána jiným synchronizačním pravidlem (například 50), a poté klikněte na tlačítko **Další**.  
   ![Vstupní 1 Popis](./media/how-to-connect-sync-configure-filtering/inbound1.png)  
5. Ve **filtru Rozsah** klikněte na **Přidat skupinu** a pak klikněte na **Přidat klauzuli**. V **atributu** vyberte **ExtensionAttribute15**. Ujistěte se, že je **operátor** nastavený na hodnotu **EQUAL**, a do pole **hodnota** zadejte hodnotu **nesynchronizováno** . Klikněte na **Next** (Další).  
   ![Obor příchozího 2](./media/how-to-connect-sync-configure-filtering/inbound2.png)  
6. Nechejte pravidla **připojení** prázdná a pak klikněte na **Další**.
7. Klikněte na **Přidat transformaci**, **Vyberte FlowType** jako **konstantu** a jako **cílový atribut** vyberte **cloudFiltered** . Do textového pole **zdroj** zadejte **hodnotu true**. Kliknutím na tlačítko **Přidat** uložte pravidlo.  
   ![Vstupní 3 transformace](./media/how-to-connect-sync-configure-filtering/inbound3.png)
8. Chcete-li dokončit konfiguraci, je nutné spustit **úplnou synchronizaci**. Pokračujte v čtení části [použití a ověření změn](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Kladné filtrování: pouze synchronizovat tyto
Vyjádření kladného filtrování může být náročnější, protože také je nutné vzít v úvahu objekty, které nejsou zjevně synchronizovány, například konferenční místnosti. Také přepíšete výchozí filtr v předem vytvořeném pravidle **v rámci služby AD-User JOIN**. Když vytváříte vlastní filtr, nezapomeňte Nezahrnovat důležité systémové objekty, objekty konfliktů replikace, speciální poštovní schránky a účty služby pro Azure AD Connect.

Možnost kladného filtrování vyžaduje dvě pravidla synchronizace. Potřebujete jedno pravidlo (nebo několik) se správným rozsahem objektů, které se mají synchronizovat. Potřebujete také druhé pravidlo pro zachycení všech synchronizací, které filtruje všechny objekty, které se ještě neidentifikovaly jako objekt, který by se měl synchronizovat.

V následujícím příkladu synchronizujete pouze uživatelské objekty, kde má atribut oddělení hodnotu **prodej**.

1. Přihlaste se k serveru, na kterém běží Azure AD Connect synchronizace pomocí účtu, který je členem skupiny zabezpečení **ADSyncAdmins** .
2. Spustí **Editor pravidel synchronizace** z nabídky **Start** .
3. Ujistěte se, že je vybraná možnost **příchozí** , a klikněte na **Přidat nové pravidlo**.
4. Zadejte pro pravidlo popisný název, například "*in from AD – Sales Sales Sync*". Vyberte správnou doménovou strukturu, vyberte možnost **uživatel** jako **typ objektu cs** a jako **typ objektu MV** vyberte **osoba** . V **typu odkazu** vyberte **připojit**. V poli **Priorita** zadejte hodnotu, která není aktuálně používána jiným synchronizačním pravidlem (například 51), a poté klikněte na tlačítko **Další**.  
   ![Popis pro příchozí 4](./media/how-to-connect-sync-configure-filtering/inbound4.png)  
5. Ve **filtru Rozsah** klikněte na **Přidat skupinu** a pak klikněte na **Přidat klauzuli**. V **atributu** vyberte **oddělení**. Ujistěte se, že je operátor nastaven na hodnotu **EQUAL** a zadejte hodnotu **prodej** v poli **hodnota** . Klikněte na **Next** (Další).  
   ![Vstupní 5 oboru](./media/how-to-connect-sync-configure-filtering/inbound5.png)  
6. Nechejte pravidla **připojení** prázdná a pak klikněte na **Další**.
7. Klikněte na **Přidat transformaci**, jako **FlowType** vyberte **konstanta** a jako **cílový atribut** vyberte **cloudFiltered** . Do pole **zdroj** zadejte **false**. Kliknutím na tlačítko **Přidat** uložte pravidlo.  
   ![Vstupní 6 transformace](./media/how-to-connect-sync-configure-filtering/inbound6.png)  
   Jedná se o speciální případ, kdy explicitně nastavíte cloudFiltered na **hodnotu false**.
8. Nyní musíme vytvořit pravidlo synchronizace catch-ALL. Zadejte pro pravidlo popisný název, například "*in from AD – User catch-All Filter*". Vyberte správnou doménovou strukturu, vyberte možnost **uživatel** jako **typ objektu cs** a jako **typ objektu MV** vyberte **osoba** . V **typu odkazu** vyberte **připojit**. V případě **priority** zadejte hodnotu, která není aktuálně používána jiným synchronizačním pravidlem (například 99). Vybrali jste hodnotu priority, která je vyšší (nižší priorita) než předchozí pravidlo synchronizace. Ale také jste ponechali několik místa, abyste mohli později přidat další filtrování synchronizačních pravidel, když budete chtít začít synchronizovat další oddělení. Klikněte na **Next** (Další).  
   ![Popis pro příchozí 7](./media/how-to-connect-sync-configure-filtering/inbound7.png)  
9. Nechejte **Filtr oboru** nastaven na prázdné a klikněte na **Další**. Prázdný filtr znamená, že pravidlo bude použito pro všechny objekty.
10. Nechejte pravidla **připojení** prázdná a pak klikněte na **Další**.
11. Klikněte na **Přidat transformaci**, jako **FlowType** vyberte **konstanta** a jako **cílový atribut** vyberte **cloudFiltered** . Do pole **zdroj** zadejte **hodnotu true**. Kliknutím na tlačítko **Přidat** uložte pravidlo.  
    ![Vstupní 3 transformace](./media/how-to-connect-sync-configure-filtering/inbound3.png)  
12. Chcete-li dokončit konfiguraci, je nutné spustit **úplnou synchronizaci**. Pokračujte v čtení části [použití a ověření změn](#apply-and-verify-changes).

Pokud potřebujete, můžete vytvořit další pravidla prvního typu, kde v synchronizaci zahrnete více objektů.

### <a name="outbound-filtering"></a>Filtrování odchozích zpráv
V některých případech je nutné filtrování provádět až poté, co se objekty připojí do úložiště metaverse. Může být třeba zkontrolovat atribut mail z doménové struktury prostředků a atribut userPrincipalName z doménové struktury účtu, abyste zjistili, jestli by měl být objekt synchronizovaný. V těchto případech vytvoříte filtrování na odchozím pravidle.

V tomto příkladu změníte filtrování tak, aby byly synchronizovány pouze uživatelé, kteří mají své e-maily i userPrincipalName končící @contoso.com :

1. Přihlaste se k serveru, na kterém běží Azure AD Connect synchronizace pomocí účtu, který je členem skupiny zabezpečení **ADSyncAdmins** .
2. Spustí **Editor pravidel synchronizace** z nabídky **Start** .
3. V části **Typ pravidel** klikněte na **odchozí**.
4. V závislosti na používané verzi připojení Najděte buď pravidlo s názvem **odchozí do Azure AD – připojení nebo připojení uživatele** **k Azure AD – SOAInAD** a klikněte na **Upravit**.
5. V automaticky otevíraném okně odpovězte **Ano** a vytvořte kopii pravidla.
6. Na stránce **Popis** změňte **prioritu** na nepoužitou hodnotu, například 50.
7. V levém navigačním panelu klikněte na **Filtr oboru** a pak klikněte na **Přidat klauzuli**. V **atributu** vyberte **Pošta**. V **operátoru** vyberte **ENDSWITH**. Do **hodnoty** zadejte **\@ contoso.com** a pak klikněte na **Přidat klauzuli**. V **atributu** vyberte **userPrincipalName**. V **operátoru** vyberte **ENDSWITH**. Do **hodnoty** zadejte **\@ contoso.com**.
8. Klikněte na **Uložit**.
9. Chcete-li dokončit konfiguraci, je nutné spustit **úplnou synchronizaci**. Pokračujte v čtení části [použití a ověření změn](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Použít a ověřit změny
Po provedení změn konfigurace je musíte použít pro objekty, které jsou již v systému k dispozici. Může to také být, že by se měly zpracovat objekty, které nejsou aktuálně v synchronizačním modulu (a synchronizační modul potřebuje znovu načíst zdrojový systém a ověřit jeho obsah).

Pokud jste změnili konfiguraci pomocí filtrování **domén** nebo **organizační jednotky** , musíte provést **úplný import** a následně **rozdílovou synchronizaci**.

Pokud jste změnili konfiguraci pomocí filtrování **atributů** , je nutné provést **úplnou synchronizaci**.

Proveďte následující kroky:

1. Spusťte **synchronizační službu** z nabídky **Start** .
2. Vyberte **konektory**. V seznamu **konektory** vyberte konektor, ve kterém jste dříve provedli změnu konfigurace. V **Možnosti akce** vyberte **Spustit**.  
   ![Spuštění konektoru](./media/how-to-connect-sync-configure-filtering/connectorrun.png)  
3. V části **profily spuštění** vyberte operaci, která byla zmíněna v předchozí části. Pokud potřebujete spustit dvě akce, spusťte druhý po dokončení první operace. ( **Stavový** sloupec je **nečinný** pro vybraný konektor.)

Po synchronizaci jsou všechny změny připravené k exportu. Než skutečně provedete změny ve službě Azure AD, budete chtít ověřit, že jsou všechny tyto změny správné.

1. Spusťte příkazový řádek a pokračujte na `%ProgramFiles%\Microsoft Azure AD Sync\bin` .
2. Spusťte `csexport "Name of Connector" %temp%\export.xml /f:x`.  
   Název konektoru je v synchronizační službě. Má název podobný řetězci "contoso.com – Azure AD" pro Azure AD.
3. Spusťte `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`.
4. Nyní máte soubor v% Temp% s názvem export.csv, který lze prozkoumat v aplikaci Microsoft Excel. Tento soubor obsahuje všechny změny, které mají být exportovány.
5. Proveďte potřebné změny dat nebo konfigurace a znovu spusťte tyto kroky (import, synchronizace a ověření), dokud nebudou změny, které se chystáte exportovat, odpovídat na to, co očekáváte.

Až budete spokojeni, exportujte změny do Azure AD.

1. Vyberte **konektory**. V seznamu **konektory** vyberte konektor Azure AD. V **Možnosti akce** vyberte **Spustit**.
2. V **profilu spuštění** vyberte **exportovat**.
3. Pokud se změní konfigurace odstranit mnoho objektů, zobrazí se při exportu chyba, pokud je počet vyšší než nastavená prahová hodnota (ve výchozím nastavení 500). Pokud se zobrazí tato chyba, musíte dočasně zakázat funkci[zabránit nechtěnému odstranění](how-to-connect-sync-feature-prevent-accidental-deletes.md).

Teď je čas znovu povolit Plánovač.

1. Spusťte **Plánovač úloh** v nabídce **Start** .
2. Přímo v rámci **knihovny Plánovač úloh** vyhledejte úkol s názvem **Azure AD Sync Scheduler**, klikněte pravým tlačítkem myši a vyberte **Povolit**.

## <a name="group-based-filtering"></a>Filtrování na základě skupin
Filtrování na základě skupin můžete nakonfigurovat při první instalaci Azure AD Connect pomocí [vlastní instalace](how-to-connect-install-custom.md#sync-filtering-based-on-groups). Je určený pro pilotní nasazení, kde chcete synchronizovat jenom malou sadu objektů. Pokud zakážete filtrování na základě skupin, nebude možné ho znovu povolit. Použití filtrování na základě skupin ve vlastní konfiguraci se *nepodporuje* . Tato funkce se podporuje jenom pomocí Průvodce instalací. Až dokončíte pilotní nasazení, použijte jednu z dalších možností filtrování v tomto tématu. Při použití filtrování na základě organizačních jednotek ve spojení s filtrováním podle skupin musí být k dispozici organizační jednotky, kde se nachází skupina a její členové.

Při synchronizaci více doménových struktur AD můžete nakonfigurovat filtrování založené na skupinách zadáním jiné skupiny pro každý konektor služby AD. Pokud chcete synchronizovat uživatele v jedné doménové struktuře služby AD a stejný uživatel má jeden nebo více odpovídajících objektů v jiných doménových strukturách služby AD, musíte zajistit, aby byl objekt uživatele a všechny jeho odpovídající objekty v oboru filtrování na základě skupiny. Příklady:

* Máte uživatele v jedné doménové struktuře, který má odpovídající objekt FSP (cizí objekt zabezpečení) v jiné doménové struktuře. Oba objekty musí být v oboru filtrování založeném na skupině. V opačném případě se uživatel nesynchronizuje do Azure AD.

* Máte uživatele v jedné doménové struktuře, který má odpovídající účet prostředku (například propojenou poštovní schránku) v jiné doménové struktuře. Dále jste nakonfigurovali Azure AD Connect k propojení uživatele s účtem prostředků. Oba objekty musí být v oboru filtrování založeném na skupině. V opačném případě se uživatel nesynchronizuje do Azure AD.

* Máte uživatele v jedné doménové struktuře, který má odpovídající e-mailový kontakt v jiné doménové struktuře. Dále jste nakonfigurovali Azure AD Connect pro připojení uživatele k e-mailovým kontaktům. Oba objekty musí být v oboru filtrování založeném na skupině. V opačném případě se uživatel nesynchronizuje do Azure AD.


## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [Azure AD Connect konfiguraci synchronizace](how-to-connect-sync-whatis.md) .
- Přečtěte si další informace o [integraci místních identit s Azure AD](whatis-hybrid-identity.md).
