---
title: 'Synchronizace Azure AD Connect: Konfigurace filtrování | Dokumentace Microsoftu'
description: Vysvětluje, jak nakonfigurovat filtrování ve službě Azure AD Connect sync.
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
ms.topic: article
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 886ac908d2e294f4627f95b35d93ea49a9e1607a
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54472326"
---
# <a name="azure-ad-connect-sync-configure-filtering"></a>Synchronizace Azure AD Connect: Konfigurace filtrování
Pomocí filtrování, můžete řídit objektů, které se zobrazí ve službě Azure Active Directory (Azure AD) z místního adresáře. Výchozí konfigurace přijímá všechny objekty ve všech doménách nakonfigurovaných doménových struktur. Obecně platí toto je doporučená konfigurace. Uživatele, kteří používají úlohy Office 365, jako je Exchange Online a Skype pro firmy, těžit z globální seznam adresu odeslání e-mailu a volat všem uživatelům. Ve výchozí konfiguraci bylo by nutné stejným způsobem, který by měla s implementací místní Exchange nebo Lync.

V některých případech však budete požadované provést nějaké změny ve výchozí konfiguraci. Zde je několik příkladů:

* Plánujete používat [s Azure AD directory topologie](plan-connect-topologies.md#each-object-only-once-in-an-azure-ad-tenant). Je nutné použít filtr k řízení, které objekty se synchronizují do konkrétní adresář Azure AD.
* Spustit pilotní nasazení pro Azure nebo Office 365 a chcete pouze podmnožinu uživatelů ve službě Azure AD. V tomto pilotním programu malé není důležité, aby byly dokončení globálního seznamu adres k předvedení funkcí.
* Budete mít mnoho účtů služby a neosobní účty, u kterých nechcete, aby ve službě Azure AD.
* Z důvodu dodržování předpisů neodstraňovat všechny uživatelské účty místní. Můžete pouze je zakázat. Ale ve službě Azure AD chcete jenom aktivní účty, aby bylo k dispozici.

Tento článek popisuje, jak nakonfigurovat filtrování způsoby.

> [!IMPORTANT]
> Microsoft nepodporuje úpravy ani provoz synchronizace služby Azure AD Connect mimo formálně zdokumentované akce. Některé z těchto akcí můžou způsobit nekonzistentní nebo nepodporovaný stav synchronizace služby Azure AD Connect. Microsoft proto nemůže pro taková nasazení poskytovat technickou podporu.

## <a name="basics-and-important-notes"></a>Základní informace a důležité poznámky
Synchronizace Azure AD Connect můžete povolit filtrování kdykoli. Je-li začít s výchozí konfigurací nástroje synchronizace adresářů a potom nakonfigurujte filtrování, objekty, které filtrují synchronizují do služby Azure AD. Z důvodu této změny odstraní se všechny objekty ve službě Azure AD, které byly dříve synchronizovaných položek, ale pak filtrovaných ve službě Azure AD.

Než začnete provádět změny filtrování, ujistěte se, že jste [zakázat naplánované úlohy](#disable-the-scheduled-task) tak, že nemáte omylem exportovat změny, které ještě nebyly ověřit správné.

Protože filtrování můžete odebrat velký počet objektů ve stejnou dobu, budete chtít ověřit, že nové filtry jsou správné, než začnete export všechny změny do služby Azure AD. Až dokončíte kroky konfigurace, důrazně doporučujeme, abyste postupovali podle [postup ověření](#apply-and-verify-changes) před exportovat a proveďte změny do služby Azure AD.

Pro ochranu před náhodou funkci odstranění velký počet objektů "[prevence náhodného odstranění](how-to-connect-sync-feature-prevent-accidental-deletes.md)" ve výchozím nastavení zapnutá. Pokud odstraníte mnoho objekty z důvodu filtrování (ve výchozím nastavení 500), budete muset postupovat podle kroků v tomto článku umožňující odstraní absolvovat do služby Azure AD.

Pokud používáte sestavení před listopad 2015 ([1.0.9125](reference-connect-version-history.md#1091250)), změňte konfiguraci filtru a používat synchronizaci hodnot hash hesel, je nutné spustit úplnou synchronizaci všechna hesla, po dokončení konfigurace. Pokyny k aktivaci úplné synchronizace hesel najdete v tématu [aktivovat úplná synchronizace všech hesel](tshoot-connect-password-hash-synchronization.md#trigger-a-full-sync-of-all-passwords). Pokud jste při sestavování 1.0.9125 nebo novější, pak standardní **úplné synchronizace** akce vypočítá také určuje, zda se mají synchronizovat hesla, pokud tento krok navíc už nejsou potřeba.

Pokud **uživatele** objekty se neúmyslně odstranily ve službě Azure AD z důvodu chyby filtrování, můžete znovu vytvořit uživatelské objekty ve službě Azure AD tak, že odeberete konfiguraci filtrování. Pak můžete znovu synchronizace vašich adresářů. Tato akce obnoví uživatele z odpadkového koše v Azure AD. Však nemůže obnovit další typy objektů. Například Pokud omylem odstraníte skupinu zabezpečení a používá se k seznamu ACL prostředku, skupiny a její seznamy ACL nelze obnovit.

Azure AD Connect odstraní pouze objekty, které se má jednou za to být v rozsahu. Pokud jsou objekty ve službě Azure AD, které byly vytvořeny pomocí jiného modulu synchronizace a tyto objekty nejsou v oboru, přidání filtrování neodebere je. Například pokud začnete se serverem nástroje DirSync, kterou vytvořili úplnou kopii celý adresář ve službě Azure AD a nainstalovat nový server synchronizace Azure AD Connect paralelně s povoleným od začátku filtrováním, Azure AD Connect nedojde k odebrání dalších objektů které jsou vytvořené pomocí nástroje DirSync.

Konfigurace filtrování se uchovávají při instalaci nebo upgrade na novější verzi služby Azure AD Connect. Vždy je osvědčeným postupem ověřte, že se změnila konfigurace neúmyslně po upgradu na novější verzi před spuštěním první synchronizační cyklus.

Pokud máte více než jednu doménovou strukturu, musíte použít filtrování konfigurace, které jsou popsané v tomto tématu pro každou doménovou strukturu (za předpokladu, že má stejnou konfiguraci pro všechny z nich).

### <a name="disable-the-scheduled-task"></a>Zakázat naplánované úlohy
Pokud chcete zakázat integrované plánovače, který spustí synchronizační cyklus každých 30 minut, postupujte takto:

1. Přejdete na Powershellu příkazový řádek.
2. Spustit `Set-ADSyncScheduler -SyncCycleEnabled $False` zakázat plánovače.
3. Proveďte požadované změny, které jsou popsané v tomto článku.
4. Spustit `Set-ADSyncScheduler -SyncCycleEnabled $True` Plánovač znovu zapnout.

**Pokud používáte Azure AD Connect sestavení před 1.1.105.0**  
Pokud chcete zakázat naplánovanou úlohu, která spustí synchronizační cyklus každé tři hodiny, postupujte takto:

1. Spustit **Plánovač úloh** z **Start** nabídky.
2. Přímo pod **Knihovna plánovače úloh**, najít úloha s názvem **Plánovač synchronizace Azure AD**, klikněte pravým tlačítkem a vyberte **zakázat**.  
   ![Plánovač úloh](./media/how-to-connect-sync-configure-filtering/taskscheduler.png)  
3. Teď můžete provádět změny konfigurace a spustit synchronizační modul ručně z **Synchronization Service Manager** konzoly.

Až dokončíte všechny provedené změny filtrování, nezapomeňte se vraťte a **povolit** úlohy znovu.

## <a name="filtering-options"></a>Možnosti filtrování
Můžete provést následující typy konfigurace filtrování u nástroje pro synchronizaci adresáře:

* [**Na základě skupin**](#group-based-filtering): Filtrování na základě jedné skupiny se nakonfigurovat jenom na počáteční instalaci pomocí Průvodce instalací.
* [**Založený na doméně**](#domain-based-filtering): Když použijete tuto možnost, můžete vybrat, které domény synchronizovat se službou Azure AD. Můžete také přidat a odebrat domény z konfigurace modulu synchronizace, když provedete změny v místní infrastruktuře po instalaci synchronizace Azure AD Connect.
* [**Organizační jednotka (OU) – na základě**](#organizational-unitbased-filtering): Když použijete tuto možnost, můžete vybrat, které organizační jednotky synchronizovat se službou Azure AD. Tato možnost se týká všech typů objektů ve vybrané organizační jednotky.
* [**Založených na atributech**](#attribute-based-filtering): Když použijete tuto možnost, můžete filtrovat objekty podle hodnot atributů na objektech. Také může mít různé filtry pro různé typy objektů.

Můžete použít několik možností filtrování ve stejnou dobu. Například můžete filtrování podle organizační jednotky zahrnout pouze objekty v jedné organizační jednotce. Ve stejnou dobu můžete filtrování podle atributů k filtrování další objekty. Při použití více metod filtrování použijte filtry logický operátor "a" mezi filtry.

## <a name="domain-based-filtering"></a>Filtrování podle domén
Tato část obsahuje postup pro konfiguraci domény filtr. Pokud Přidání nebo odebrání domény v doménové struktuře, po instalaci služby Azure AD Connect, budete muset také aktualizovat konfiguraci filtrování.

Preferovaný způsob, jak změnit filtrování podle domén je tak, že spustíte Průvodce instalací a změna [domény a filtrování podle organizačních jednotek](how-to-connect-install-custom.md#domain-and-ou-filtering). Průvodce instalací automatizuje všechny úkoly, které jsou popsané v tomto tématu.

Pokud si nejste schopni spustit Průvodce instalací z nějakého důvodu, by měla pouze následujícím postupem.

Založený na doméně filtrování konfigurace se skládá z následujících kroků:

1. [Vybrat domény](#select-domains-to-be-synchronized) , který chcete zahrnout do synchronizace.
2. Pro každý přidat nebo odebrat domény, upravte [profily spuštění](#update-run-profiles).
3. [Použít a ověřit změny](#apply-and-verify-changes).

### <a name="select-the-domains-to-be-synchronized"></a>Vybrat domény, které se mají synchronizovat
Pokud chcete nastavit filtr domény, proveďte následující kroky:

1. Přihlaste se k serveru, na kterém je spuštěna synchronizace Azure AD Connect s použitím účtu, který je členem skupiny **ADSyncAdmins** skupiny zabezpečení.
2. Spustit **synchronizační služba** z **Start** nabídky.
3. Vyberte **konektory**a **konektory** seznamu, vyberte konektor s typem **Active Directory Domain Services**. V **akce**vyberte **vlastnosti**.  
   ![Vlastnosti konektoru](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Klikněte na tlačítko **Konfigurovat oddíly adresáře**.
5. V **Vybrat oddíly adresářů** vyberte a zrušte výběr domén podle potřeby. Ověřte, zda jsou vybrány pouze oddíly, které se mají synchronizovat.  
   ![Oddíly](./media/how-to-connect-sync-configure-filtering/connectorpartitions.png)  
   Pokud jste změnili vaši místní infrastrukturu služby Active Directory a přidání nebo odebrání domén z doménové struktury, klikněte **aktualizovat** tlačítko zobrazit aktualizovaný seznam. Při aktualizaci se vám výzva k zadání přihlašovacích údajů. Zadejte všechny přihlašovací údaje s oprávněním ke čtení na Windows Server Active Directory. Nemusí být uživatel, který je předem v dialogovém okně.  
   ![Je potřeba aktualizace](./media/how-to-connect-sync-configure-filtering/refreshneeded.png)  
6. Až budete hotovi, zavřete **vlastnosti** dialogové okno kliknutím **OK**. Pokud odebrání domén z doménové struktury se místní zpráva, že byla odebrána domény a tuto konfiguraci se vymažou.
7. Pokračujte v nastavení [profily spuštění](#update-run-profiles).

### <a name="update-the-run-profiles"></a>Aktualizovat profilů spuštění
Pokud jste aktualizovali filtr domény, musíte také aktualizovat profily spuštění.

1. V **konektory** seznamu, zkontrolujte, že je vybrána konektor, který jste změnili v předchozím kroku. V **akce**vyberte **konfigurovat profily spuštění**.  
   ![Profily 1 spuštění konektoru](./media/how-to-connect-sync-configure-filtering/connectorrunprofiles1.png)  
2. Najít a identifikovat následující profily:
    * Úplný Import
    * Úplná synchronizace
    * Rozdílový Import
    * Rozdílová synchronizace
    * Export
3. Pro každý profil upravit **přidali** a **odebrat** domén.
    1. Pro každý z pěti profilů, proveďte následující kroky pro každý **přidali** domény:
        1. Vyberte profil spuštění a klikněte na tlačítko **nový krok**.
        2. Na **krok konfigurace** stránku, **typ** rozevírací nabídky, vyberte typ kroku se stejným názvem jako profil, který konfigurujete. Pak klikněte na tlačítko **Další**.  
        ![Profily 2 spuštění konektoru](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep1.png)  
        3. Na **konfigurace konektoru** stránku, **oddílu** rozevírací nabídky vyberte název domény, který jste přidali do vaší domény filtru.  
        ![Profily 3 spuštění konektoru](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep2.png)  
        4. Zavřete **konfigurací profilu spuštění** dialogového okna, klikněte na tlačítko **Dokončit**.
    2. Pro každý z pěti profilů, proveďte následující kroky pro každý **odebrat** domény:
        1. Vyberte profil spuštění.
        2. Pokud **hodnotu** z **oddílu** atribut je identifikátor GUID, vyberte kroku spuštění a klikněte na tlačítko **krok odstranit**.  
        ![Profily 4 spuštění konektoru](./media/how-to-connect-sync-configure-filtering/runprofilesdeletestep.png)  
    3. Ověřte změny. Každá doména, která se mají synchronizovat by měly být uvedeny jako krok v každý profil spuštění.
4. Zavřete **konfigurovat profily spuštění** dialogového okna, klikněte na tlačítko **OK**.
5.  K dokončení konfigurace budete muset spustit **úplný import** a **rozdílová synchronizace**. Pokračujte ve čtení části [použít a ověřit změny](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Filtrování organizačních jednotek – podle
Preferovaný způsob, jak změnit filtrování podle organizačních jednotek je tak, že spustíte Průvodce instalací a změna [domény a filtrování podle organizačních jednotek](how-to-connect-install-custom.md#domain-and-ou-filtering). Průvodce instalací automatizuje všechny úkoly, které jsou popsané v tomto tématu.

Pokud si nejste schopni spustit Průvodce instalací z nějakého důvodu, by měla pouze následujícím postupem.

Pokud chcete nakonfigurovat organizační jednotku – na základě filtrování, proveďte následující kroky:

1. Přihlaste se k serveru, na kterém je spuštěna synchronizace Azure AD Connect s použitím účtu, který je členem skupiny **ADSyncAdmins** skupiny zabezpečení.
2. Spustit **synchronizační služba** z **Start** nabídky.
3. Vyberte **konektory**a **konektory** seznamu, vyberte konektor s typem **Active Directory Domain Services**. V **akce**vyberte **vlastnosti**.  
   ![Vlastnosti konektoru](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Klikněte na tlačítko **konfigurace oddílů adresářů**, vyberte doménu, kterou chcete konfigurovat a potom klikněte na **kontejnery**.
5. Po zobrazení výzvy, poskytněte žádné přihlašovací údaje s oprávněním ke čtení služby v místním Active Directory. Nemusí být uživatel, který je předem v dialogovém okně.
6. V **vybrat kontejnery** dialogovém okně Vymazat organizační jednotky, které nechcete synchronizovat s cloudovým adresářem, a potom klikněte na **OK**.  
   ![Organizační jednotky v dialogovém okně Vybrat kontejnery](./media/how-to-connect-sync-configure-filtering/ou.png)  
   * **Počítače** by měl být vybraný kontejner pro počítače s Windows 10 úspěšně synchronizovány se službou Azure AD. Pokud jsou počítače připojené k doméně v jiné organizační jednotky, ujistěte se, že jsou vybrány.
   * Pokud máte několik doménových struktur se vztahem důvěryhodnosti, měl by být vybraný kontejner **ForeignSecurityPrincipals**. Tento kontejner umožňuje řešit členství ve skupinách zabezpečení napříč doménovými strukturami.
   * **RegisteredDevices** organizační jednotky, měla by být vybrána, pokud jste povolili funkci zpětného zápisu zařízení. Pokud používáte jinou funkci zpětného zápisu, jako je například zpětný zápis skupin, zajistěte, aby že tato místa jsou vybrány.
   * Vyberte jakékoli jiné organizační jednotky ve kterém se nachází uživatelé, objektů InetOrgPerson, skupiny, kontakty a počítače. Na obrázku tyto organizační jednotky jsou umístěny v organizační jednotce ManagedObjects.
   * Pomocí filtrování podle skupin, organizační jednotky, kde se nachází skupina musí být zahrnut.
   * Všimněte si, že můžete nakonfigurovat, jestli se nové organizační jednotky, které se přidají po dokončení konfigurace filtrování synchronizovat, nebo není synchronizován. V části Další podrobnosti.
7. Až budete hotovi, zavřete **vlastnosti** dialogové okno kliknutím **OK**.
8. K dokončení konfigurace budete muset spustit **úplný import** a **rozdílová synchronizace**. Pokračujte ve čtení části [použít a ověřit změny](#apply-and-verify-changes).

### <a name="synchronize-new-ous"></a>Synchronizovat nové organizační jednotky
Ve výchozím nastavení jsou synchronizovány nové organizační jednotky, které jsou vytvořeny po dokončení konfigurace filtrování. Tento stav je indikován zaškrtnuté zaškrtávací políčko. Zrušit výběr některé dílčí organizační jednotky. Toto chování získáte kliknutím na políčko, dokud nebude bílá s modrá značka zaškrtnutí (výchozího stavu). Následně zrušte výběr žádné dílčí-organizační jednotky, které nechcete synchronizovat.

Pokud se synchronizují všechny dílčí-organizační jednotky, je pole bílá s modrá značka zaškrtnutí.  
![Všechna pole vybrané organizační jednotky](./media/how-to-connect-sync-configure-filtering/ousyncnewall.png)

Pokud některé dílčí organizační jednotky byl zrušen výběr cesty, do pole je šedá s bílou značkou zaškrtnutí.  
![Organizační jednotky některé dílčí oj nevybrané](./media/how-to-connect-sync-configure-filtering/ousyncnew.png)

S touto konfigurací se nové organizační jednotky, který byl vytvořený v rámci ManagedObjects synchronizuje.

Průvodce instalací Azure AD Connect vytvoří vždy tuto konfiguraci.

### <a name="dont-synchronize-new-ous"></a>Nesynchronizovat nové organizační jednotky
Můžete nakonfigurovat modul synchronizace, aby po dokončení konfigurace filtrování nové organizační jednotky synchronizovat. Tento stav je uvedeno v uživatelském rozhraní podle pole povolí plnou šedou bez zaškrtnutí. Toto chování získáte kliknutím na políčko, dokud nebude bílé bez zaškrtnutí. Vyberte sub-organizačních jednotek, které se mají synchronizovat.

![Organizační jednotky s kořenem nevybrané](./media/how-to-connect-sync-configure-filtering/oudonotsyncnew.png)

S touto konfigurací nové organizační jednotky, který byl vytvořený v rámci ManagedObjects není synchronizovaný.

## <a name="attribute-based-filtering"></a>Filtrování na základě atributů
Ujistěte se, že používáte listopad 2015 ([1.0.9125](reference-connect-version-history.md#1091250)) nebo novější sestavení pro tyto kroky při práci.

> [!IMPORTANT]
>Společnost Microsoft doporučuje nelze upravit výchozí pravidla vytvořil **Azure AD Connect**. Pokud chcete upravit pravidlo, naklonujte a původní pravidlo zakázat. Proveďte požadované změny z klonovaného pravidla. Upozorňujeme, že tímto způsobem (zakázání původní pravidlo) bude přeskočíte všechny opravy chyb a funkce přidané pomocí tohoto pravidla.

Filtrování podle atributů je nejflexibilnější způsob, jak filtrovat objekty. Můžete použít sílu [deklarativní zřizování](concept-azure-ad-connect-sync-declarative-provisioning.md) řídit skoro každý aspekt když je objekt synchronizovány do Azure AD.

Můžete použít [příchozí](#inbound-filtering) filtrování ze služby Active Directory do úložiště metaverse, a [odchozí](#outbound-filtering) filtrování z úložiště metaverse do služby Azure AD. Doporučujeme použít příchozí filtrování, protože to je nejjednodušší udržovat. Používejte pouze odchozí filtrování, pokud je potřeba před vyhodnocení může proběhnout připojte objektů z více než jednu doménovou strukturu.

### <a name="inbound-filtering"></a>Příchozí filtrování
Příchozí filtrování používá výchozí konfiguraci, kde objekty, že přejdete do služby Azure AD musíte mít cloudFiltered atribut úložiště metaverze není nastaven na hodnotu se dá provést synchronizace. Pokud je hodnota tohoto atributu nastavena na **True**, potom objekt není synchronizovaný. Neměla by být nastavená **False**, podle návrhu. Aby se zajistilo další pravidla se budou moct přispět hodnotu, tento atribut pouze by měl mít hodnoty **True** nebo **NULL** (zcela chybět).

Při filtrování příchozích, využijte sílu **oboru** rozhodnout, které objekty se mají synchronizovat, nebo není synchronizována. To je, kde můžete provádět úpravy podle požadavků vaší organizace. Má v oboru modulu **skupiny** a **klauzule** k určení, kdy je pravidlo synchronizace v oboru. Skupina obsahuje jednu či více klauzulí. Mezi více klauzulí a s logickým "nebo" mezi více skupin je logický operátor "a".

Podívejme se na příklad:  
![Rozsah](./media/how-to-connect-sync-configure-filtering/scope.png)  
To byste si měli přečíst jako **(oddělení = IT) nebo (oddělení = prodeje a c = US)**.

V následující ukázky a kroky použijte objekt uživatele jako příklad, ale to můžete použít pro všechny typy objektů.

Následující ukázky hodnota priority začíná 50. To může být jakékoli číslo, které se nepoužívá, ale by měla být nižší než 100.

#### <a name="negative-filtering-do-not-sync-these"></a>Záporná filtrování: "se nesynchronizují tyto"
V následujícím příkladu, můžete vyfiltrovat (není synchronizována) všech uživatelů kde **extensionAttribute15** má hodnotu **NoSync**.

1. Přihlaste se k serveru, na kterém je spuštěna synchronizace Azure AD Connect s použitím účtu, který je členem skupiny **ADSyncAdmins** skupiny zabezpečení.
2. Spustit **Editor pravidel synchronizace** z **Start** nabídky.
3. Ujistěte se, že **příchozí** je vybraná a klikněte na tlačítko **přidat nové pravidlo**.
4. Zadejte pravidla popisný název, jako například "*v ze služby AD – uživatel DoNotSyncFilter*". Vyberte správnou doménovou strukturu, vyberte **uživatele** jako **typ objektu CS**a vyberte **osoba** jako **typ objektu MV**. V **typu odkazu**vyberte **připojit**. V **prioritu**, zadejte hodnotu, která není aktuálně používán jinou synchronizačního pravidla (třeba 50) a potom klikněte na tlačítko **Další**.  
   ![Popis příchozí 1](./media/how-to-connect-sync-configure-filtering/inbound1.png)  
5. V **Scoping filtr**, klikněte na tlačítko **přidat skupinu**a klikněte na tlačítko **přidat klauzuli**. V **atribut**vyberte **ExtensionAttribute15**. Ujistěte se, že **operátor** je nastavena na **rovná**a zadejte hodnotu **NoSync** v **hodnotu** pole. Klikněte na **Další**.  
   ![Příchozí 2 oboru](./media/how-to-connect-sync-configure-filtering/inbound2.png)  
6. Nechte **připojení** pravidla prázdný a potom klikněte na **Další**.
7. Klikněte na tlačítko **přidat transformace**, vyberte **typ toku** jako **konstantní**a vyberte **cloudFiltered** jako **cíl Atribut**. V **zdroj** textového pole, typ **True**. Klikněte na tlačítko **přidat** uložíte pravidlo.  
   ![Příchozí 3 transformace](./media/how-to-connect-sync-configure-filtering/inbound3.png)
8. K dokončení konfigurace budete muset spustit **Full sync**. Pokračujte ve čtení části [použít a ověřit změny](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Filtrování pozitivní: "synchronizovat pouze ty"
Vyjádření kladné filtrování může být náročnější, protože je také nutné zvážit objekty, které nejsou zřejmé se dá provést synchronizace, jako je například konferenčních místnostech dojde. Také chcete přepsat výchozí filtr v pravidle out-of-box **v ze služby AD - uživatel připojit**. Když vytvoříte vlastní filtr, ujistěte se, že tak, aby nezahrnovala nezbytně nutné systémové objekty, objekty replikační konflikt, speciální poštovní schránky a účty služeb pro Azure AD Connect.

Pozitivní filtrování možnost vyžaduje dvě synchronizační pravidla. Musíte mít jedno pravidlo (nebo několik) s správný rozsah objektů, které chcete synchronizovat. Budete také potřebovat druhé pravidlo synchronizace pokrývající vše, který odfiltruje všechny objekty, které dosud nebyly určeny jako objekt, který se mají synchronizovat.

V následujícím příkladu, pouze synchronizovat uživatelské objekty, ve kterém oddělení atribut má hodnotu **Sales**.

1. Přihlaste se k serveru, na kterém je spuštěna synchronizace Azure AD Connect s použitím účtu, který je členem skupiny **ADSyncAdmins** skupiny zabezpečení.
2. Spustit **Editor pravidel synchronizace** z **Start** nabídky.
3. Ujistěte se, že **příchozí** je vybraná a klikněte na tlačítko **přidat nové pravidlo**.
4. Zadejte pravidla popisný název, jako například "*v ze služby AD – prodej uživatele synchronizovat*". Vyberte správnou doménovou strukturu, vyberte **uživatele** jako **typ objektu CS**a vyberte **osoba** jako **typ objektu MV**. V **typu odkazu**vyberte **připojit**. V **prioritu**, zadejte hodnotu, která není aktuálně používán dalším synchronizačním pravidle (například 51) a potom klikněte na tlačítko **Další**.  
   ![Příchozí 4 popis](./media/how-to-connect-sync-configure-filtering/inbound4.png)  
5. V **Scoping filtr**, klikněte na tlačítko **přidat skupinu**a klikněte na tlačítko **přidat klauzuli**. V **atribut**vyberte **oddělení**. Ujistěte se, že je operátor nastavený na **rovná**a zadejte hodnotu **Sales** v **hodnotu** pole. Klikněte na **Další**.  
   ![Příchozí 5 oboru](./media/how-to-connect-sync-configure-filtering/inbound5.png)  
6. Nechte **připojení** pravidla prázdný a potom klikněte na **Další**.
7. Klikněte na tlačítko **přidat transformace**vyberte **konstantní** jako **typ toku**a vyberte **cloudFiltered** jako **cíl Atribut**. V **zdroj** zadejte **False**. Klikněte na tlačítko **přidat** uložíte pravidlo.  
   ![Příchozí 6 transformace](./media/how-to-connect-sync-configure-filtering/inbound6.png)  
   Toto je zvláštní případ, ve kterém můžete explicitně nastavit cloudFiltered na **False**.
8. Teď máme vytvoření pravidla synchronizace pokrývající vše. Zadejte pravidla popisný název, jako například "*v ze služby AD – filtr uživatele pokrývající vše*". Vyberte správnou doménovou strukturu, vyberte **uživatele** jako **typ objektu CS**a vyberte **osoba** jako **typ objektu MV**. V **typu odkazu**vyberte **připojit**. V **prioritu**, zadejte hodnotu, která není aktuálně používán dalším synchronizačním pravidle (například 99). Vybrali jste hodnotu priority, která je vyšší (nižší prioritu) než předchozí pravidlo synchronizace. Ale jsme také zbývá uvolnit místo, kde můžete přidat další pravidla filtrování synchronizace později Pokud chcete zahájit synchronizaci další oddělení. Klikněte na **Další**.  
   ![Příchozí 7 popis](./media/how-to-connect-sync-configure-filtering/inbound7.png)  
9. Ponechte **Scoping filtr** prázdná a klikněte na tlačítko **Další**. Prázdný filtr označuje, že pravidlo se použije pro všechny objekty.
10. Nechte **připojení** pravidla prázdný a potom klikněte na **Další**.
11. Klikněte na tlačítko **přidat transformace**vyberte **konstantní** jako **typ toku**a vyberte **cloudFiltered** jako **cíl Atribut**. V **zdroj** zadejte **True**. Klikněte na tlačítko **přidat** uložíte pravidlo.  
    ![Příchozí 3 transformace](./media/how-to-connect-sync-configure-filtering/inbound3.png)  
12. K dokončení konfigurace budete muset spustit **Full sync**. Pokračujte ve čtení části [použít a ověřit změny](#apply-and-verify-changes).

Pokud potřebujete, můžete vytvořit další pravidla první typ, pokud zahrnete více objektů v rámci synchronizace.

### <a name="outbound-filtering"></a>Filtrování odchozího
V některých případech je potřeba, aby filtrování až poté, co máte v úložišti metaverse připojené objekty. Například může být potřeba podívejte se na atribut e-mailu z doménové struktury prostředku a atribut userPrincipalName z doménové struktury účtu k určení, pokud se mají synchronizovat objekt. V těchto případech můžete vytvořit filtrování podle odchozích pravidel.

V tomto příkladu změníte filtrování tak, aby pouze uživatelé, které mají jejich e-mailu a končící na atribut userPrincipalName @contoso.com se synchronizují:

1. Přihlaste se k serveru, na kterém je spuštěna synchronizace Azure AD Connect s použitím účtu, který je členem skupiny **ADSyncAdmins** skupiny zabezpečení.
2. Spustit **Editor pravidel synchronizace** z **Start** nabídky.
3. V části **typ pravidel**, klikněte na tlačítko **odchozí**.
4. V závislosti na verzi Connect můžete použít buď vyhledejte pravidlo s názvem **na AAD – uživatel připojit** nebo **navýšení kapacity na AAD – uživatel připojit SOAInAD**a klikněte na tlačítko **upravit**.
5. V automaticky otevíraném okně Odpovědět **Ano** vytvořit kopii tohoto pravidla.
6. Na **popis** stránce, změňte **prioritu** do nepoužitých hodnot, jako je 50.
7. Klikněte na tlačítko **Scoping filtr** na levém navigačním panelu a pak klikněte na tlačítko **přidat klauzuli**. V **atribut**vyberte **e-mailu**. V **operátor**vyberte **ENDSWITH**. V **hodnotu**, typ **@contoso.com**a potom klikněte na tlačítko **přidat klauzuli**. V **atribut**vyberte **userPrincipalName**. V **operátor**vyberte **ENDSWITH**. V **hodnotu**, typ **@contoso.com**.
8. Klikněte na **Uložit**.
9. K dokončení konfigurace budete muset spustit **Full sync**. Pokračujte ve čtení části [použít a ověřit změny](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Použít a ověřit změny
Po provedení změny konfigurace, musíte aplikovat na objekty, které jsou již přítomny v systému. Je také možné, že by se měly zpracovat objekty, které nejsou aktuálně v modulu synchronizace (a synchronizační modul je potřeba načíst zdrojový systém znovu a ověřte jeho obsah).

Pokud jste změnili konfiguraci pomocí **domény** nebo **organizační jednotka** filtrování, pak je třeba provést **úplný import**následovaný **rozdílů synchronizace**.

Pokud jste změnili konfiguraci pomocí **atribut** filtrování, pak je třeba provést **úplné synchronizace**.

Proveďte následující kroky:

1. Spustit **synchronizační služba** z **Start** nabídky.
2. Vyberte **konektory**. V **konektory** seznamu, vyberte konektor, kam jste provedli dříve změny konfigurace. V **akce**vyberte **spustit**.  
   ![Spuštění konektoru](./media/how-to-connect-sync-configure-filtering/connectorrun.png)  
3. V **profily spuštění**, vyberte operaci, která jsem už zmínili v předchozím oddílu. Pokud je potřeba spustit dvě akce, spusťte druhý po dokončení první z nich. ( **Stavu** sloupec je **nečinný** u vybrané spojnice.)

Po synchronizaci jsou připraveny všechny změny nelze exportovat. Před provedením změny ve skutečnosti ve službě Azure AD, budete chtít ověřit, že všechny tyto změny jsou správné.

1. Spusťte příkazový řádek a přejděte na `%Program Files%\Microsoft Azure AD Sync\bin`.
2. Spusťte `csexport "Name of Connector" %temp%\export.xml /f:x`.  
   Název konektoru se synchronizační služba. Má název "contoso.com – AAD" podobně jako pro Azure AD.
3. Spusťte `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`.
4. Nyní máte soubor ve složce % temp % s názvem export.csv, které se dají prozkoumat v aplikaci Microsoft Excel. Tento soubor obsahuje všechny změny, které mají být exportovány.
5. Proveďte potřebné změny data nebo konfigurace, a projít tyto kroky opakujte (Import, synchronizovat a ověřte, zda) až se změny, které mají být exportovány čekáte.

Jakmile budete spokojeni, exportujte změny do služby Azure AD.

1. Vyberte **konektory**. V **konektory** vyberte konektor služby Azure AD. V **akce**vyberte **spustit**.
2. V **profily spuštění**vyberte **exportovat**.
3. Pokud se změny konfigurace odstraňují mnoho objektů, pak se zobrazí chyba v exportu je-li číslo větší než nastavená prahová hodnota (ve výchozím nastavení 500). Pokud se zobrazí tato chyba, pak je třeba dočasně zakázat "[prevence náhodného odstranění](how-to-connect-sync-feature-prevent-accidental-deletes.md)" funkce.

Nyní je čas opět povolit plánovače.

1. Spustit **Plánovač úloh** z **Start** nabídky.
2. Přímo pod **Knihovna plánovače úloh**, najít úloha s názvem **Plánovač synchronizace Azure AD**, klikněte pravým tlačítkem a vyberte **povolit**.

## <a name="group-based-filtering"></a>Filtrování podle skupin
Filtrování podle skupin poprvé instalaci Azure AD Connect s použitím můžete nakonfigurovat [vlastní instalaci](how-to-connect-install-custom.md#sync-filtering-based-on-groups). Je určena pro pilotní nasazení. Pokud chcete pouze malou sadu objektů, které se mají synchronizovat. Pokud zakážete filtrování podle skupin, nelze znovu povolit. Má *nepodporuje* použití filtrování podle skupin v vlastní konfiguraci. Je podporován pouze na tuto funkci konfigurovat pomocí Průvodce instalací. Po dokončení vašeho pilotního nasazení, použijte jednu z dalších možností filtrování v tomto tématu. Při použití filtrování podle Organizačních ve spojení s filtrování podle skupin, musí být zahrnut OU(s), kde se nachází skupina a její členy.

Při synchronizaci více doménových struktur AD, můžete nakonfigurovat filtrování podle skupin zadáním jiné skupiny pro každý konektor služby AD. Pokud chcete synchronizovat uživatele ve službě AD jednu doménovou strukturu a stejného uživatele obsahuje jednu nebo další odpovídající objekty v jiných doménových struktur AD musíte zajistit, že objekt uživatele a jeho odpovídající objekty jsou v rámci skupinové filtrování oboru. Příklady:

* Požádejte uživatele v jedné doménové struktuře, která má odpovídající objekt FSP (cizí objekt zabezpečení) v jiné doménové struktuře. Oba objekty musí být v rámci skupinové filtrování oboru. Uživatel, jinak nebudou synchronizovány do Azure AD.

* Požádejte uživatele v jedné doménové struktuře, která má odpovídající účet zdroje (například propojená poštovní schránka) v jiné doménové struktuře. Dále nakonfigurujete propojit uživatele s prostředků účtu služby Azure AD Connect. Oba objekty musí být v rámci skupinové filtrování oboru. Uživatel, jinak nebudou synchronizovány do Azure AD.

* V jedné doménové struktuře, která má odpovídající e-mailu máte uživatele, kontaktu v jiné doménové struktuře. Kromě toho jste nakonfigurovali Azure AD Connect k propojení uživatele s e-mailu kontaktujte. Oba objekty musí být v rámci skupinové filtrování oboru. Uživatel, jinak nebudou synchronizovány do Azure AD.


## <a name="next-steps"></a>Další postup
- Další informace o [synchronizace Azure AD Connect](how-to-connect-sync-whatis.md) konfigurace.
- Další informace o [integrace místních identit s Azure AD](whatis-hybrid-identity.md).
