---
title: 'Synchronizace Azure AD Connect: Konfigurace filtrování | Microsoft Docs'
description: Vysvětluje, jak nakonfigurovat filtrování v synchronizaci Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
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
ms.openlocfilehash: f54f0fb16838b245ac09c08c7afd9e6a53449afc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34594223"
---
# <a name="azure-ad-connect-sync-configure-filtering"></a>Synchronizace Azure AD Connect: Konfigurace filtrování
Pomocí filtrování můžete řídit objektů, které se zobrazí v Azure Active Directory (Azure AD) z vašeho místního adresáře. Výchozí konfigurace trvá všechny objekty ve všech doménách v doménové struktuře nakonfigurované. Obecně platí to je doporučená konfigurace. Uživatele, kteří používají úlohami Office 365, jako je Exchange Online a Skype pro firmy, těžit z úplný seznam globální adresy tak, aby jejich odeslání e-mailu a volání everyone. U výchozí konfigurace že by měla mít stejné prostředí, které se mají s implementace místní Exchange nebo Lync.

V některých případech ale jste požadované provedeme některé změny na výchozí konfiguraci. Zde je několik příkladů:

* Máte v úmyslu použít [více Azure directory topologie AD](active-directory-aadconnect-topologies.md#each-object-only-once-in-an-azure-ad-tenant). Pak budete muset použít filtr k řízení, které objekty jsou synchronizovány pro konkrétní adresář Azure AD.
* Spustit pilotní nasazení pro Azure nebo Office 365 a chcete jen podmnožinu uživatelů ve službě Azure AD. V malých pilotním projektu není důležité, aby byly úplný seznam globální adresa k předvedení funkcí.
* Máte velký počet účtů služby a jiné neosobní účty, které nechcete použít ve službě Azure AD.
* Kvůli dodržování předpisů neodstraníte všechny uživatelské účty na místě. Zakážete jenom je. Ale ve službě Azure AD, chcete pouze aktivní účty nacházet.

Tento článek popisuje postup konfigurace metod filtrování.

> [!IMPORTANT]
> Microsoft nepodporuje úpravy ani provoz synchronizace služby Azure AD Connect mimo formálně zdokumentované akce. Některé z těchto akcí můžou způsobit nekonzistentní nebo nepodporovaný stav synchronizace služby Azure AD Connect. Microsoft proto nemůže pro taková nasazení poskytovat technickou podporu.

## <a name="basics-and-important-notes"></a>Základní informace a důležité poznámky
Synchronizace Azure AD Connect můžete povolit filtrování kdykoli. Pokud spustíte s výchozí konfigurací synchronizace adresářů a pak nakonfigurujte filtrování, objekty, které jsou odfiltrována synchronizovány do Azure AD. Z důvodu této změny se odstraní všechny objekty ve službě Azure AD, které byly dříve synchronizovaných položek, ale pak byly filtrovány ve službě Azure AD.

Než začnete, provedení změn filtrování, ujistěte se, že jste [zakázat naplánované úlohy](#disable-scheduled-task) tak nejsou omylem exportovat změny, které ještě nebyly ověřit správné.

Protože filtrování můžete odebrat mnoho objektů ve stejnou dobu, budete chtít Ujistěte se, že nové filtry jsou správné, než začnete, export všechny změny do Azure AD. Po dokončení kroků konfigurace, důrazně doporučujeme, že [postup ověření](#apply-and-verify-changes) před exportovat a proveďte změny do Azure AD.

Chránit před odstraněním mnoho objektů omylem odstraněný, funkci "[prevence náhodného odstranění](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)" ve výchozím nastavení zapnutý. Pokud odstraníte mnoho objektů z důvodu filtrování (500 ve výchozím nastavení), budete muset postupujte podle kroků v tomto článku Povolit odstranění projít, do Azure AD.

Pokud používáte sestavení před listopad 2015 ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)), změňte konfiguraci filtru a používat synchronizaci hodnoty hash hesla, budete muset po dokončení konfigurace spustit úplnou synchronizaci všech hesel. Pokyny o tom, jak spustit úplnou synchronizaci hesel, najdete v části [spustit úplnou synchronizaci hesel všech](active-directory-aadconnectsync-troubleshoot-password-hash-synchronization.md#trigger-a-full-sync-of-all-passwords). Pokud jste při sestavování 1.0.9125 nebo novější, pak regular **úplné synchronizace** akce také vypočítá, jestli by měl synchronizovat hesla a pokud tento další krok se už nevyžaduje.

Pokud **uživatele** měla z důvodu chyby filtrování nechtěně odstranit objekty ve službě Azure AD, můžete znovu vytvořit uživatelské objekty ve službě Azure AD odebráním vaše konfigurace filtrování. Pak můžete znovu synchronizujte adresáře. Tato akce obnoví uživatele z koše ve službě Azure AD. Nelze však zrušení odstranění jiné typy objektů. Například Pokud omylem odstraníte skupinu zabezpečení a byla použita k seznamu ACL prostředku, skupiny a její seznamy ACL nelze obnovit.

Azure AD Connect odstraní pouze objekty, které má jednou považuje za v oboru. Pokud nejsou objekty ve službě Azure AD, které byly vytvořeny jiný modul synchronizace a tyto objekty nejsou v oboru, přidání filtrování neodstraní je. Například pokud spustíte s serveru nástroje DirSync, která vytvoří kompletní kopie celý adresář ve službě Azure AD a nainstalovat nový server Azure AD Connect sync paralelně s povoleným od začátku filtrováním, Azure AD Connect neodstraní doplňující objekty které jsou vytvořené pomocí nástroje DirSync.

Filtrování konfiguraci se zachová při instalaci nebo upgrade na novější verzi služby Azure AD Connect. Vždycky je osvědčeným postupem ověřte, že konfigurace nebyl změněn nechtěně po upgradu na novější verzi před spuštěním první synchronizační cyklus.

Pokud máte více než jedné doménové struktuře, musíte použít filtrování konfigurace, které jsou popsané v tomto tématu pro každou doménovou strukturu (za předpokladu, že chcete stejnou konfiguraci pro všechny z nich).

### <a name="disable-the-scheduled-task"></a>Zakázat naplánované úlohy
Zakázat předdefinované plánovače, který aktivuje synchronizační cyklus každých 30 minut, postupujte takto:

1. Přejděte do prostředí PowerShell výzva.
2. Spustit `Set-ADSyncScheduler -SyncCycleEnabled $False` zakázat plánovače.
3. Proveďte požadované změny, které jsou popsané v tomto článku.
4. Spustit `Set-ADSyncScheduler -SyncCycleEnabled $True` Plánovač znovu zapnout.

**Pokud použijete sestavení Azure AD Connect před 1.1.105.0**  
Pokud chcete zakázat naplánované úlohy, která aktivuje synchronizační cyklus každé tři hodiny, postupujte takto:

1. Spustit **Plánovač úloh** z **spustit** nabídky.
2. Přímo pod **Knihovna plánovače úloh**, najít úloha s názvem **Azure AD Sync Scheduler**, klikněte pravým tlačítkem a vyberte **zakázat**.  
   ![Plánovač úloh](./media/active-directory-aadconnectsync-configure-filtering/taskscheduler.png)  
3. Teď můžete provádět změny konfigurace a spustit ručně z synchronizační modul **Synchronization Service Manager** konzoly.

Po dokončení všech filtrování změny, nezapomeňte se vrátit a **povolit** úlohu opakujte.

## <a name="filtering-options"></a>Možnosti filtrování
Můžete použít následující typy filtrování konfigurace pro nástroj pro synchronizaci adresáře:

* [**Na základě skupiny**](#group-based-filtering): filtrování založené na jednu skupinu se dá nakonfigurovat jenom na počáteční instalaci pomocí Průvodce instalací.
* [**Založené na doméně**](#domain-based-filtering): pomocí této možnosti můžete vybrat, které domény synchronizovat do Azure AD. Můžete také přidat a odebrat domény z konfigurace modulu synchronizace, když provedete změny v místní infrastruktuře po instalaci synchronizace Azure AD Connect.
* [**Organizační jednotka (OU) – na základě**](#organizational-unitbased-filtering): pomocí této možnosti můžete vybrat, kterou organizační jednotky synchronizovat do Azure AD. Tato možnost je pro všechny typy objektů ve vybrané organizační jednotky.
* [**Na základě atributů**](#attribute-based-filtering): pomocí této možnosti můžete filtrovat objekty podle hodnot atributů na objektech. Můžete taky nechat různých filtrů pro různé typy objektů.

Můžete vytvořit více možností filtrování ve stejnou dobu. Například můžete na základě organizační jednotky filtrování zahrnout pouze objekty v jedné organizační jednotce. Ve stejnou dobu můžete na základě atributů filtrování filtrovat další objekty. Pokud používáte více metod filtrování, použijte filtry logické "AND" mezi filtry.

## <a name="domain-based-filtering"></a>Filtrování podle domén
Tato část vám poskytne postup konfigurace domény filtru. Je-li přidat nebo odebrat domény v doménové struktuře, po instalaci Azure AD Connect, máte také aktualizovat konfiguraci filtrování.

Upřednostňovaný způsob, jak změnit, filtrování podle domén je spuštěním Průvodce instalací a změna [domény a filtrování organizační jednotky](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). Průvodce instalací automatizuje všechny úlohy, které jsou popsané v tomto tématu.

Pouze postupujte podle těchto kroků Pokud jste schopni spustit Průvodce instalací z nějakého důvodu.

Konfigurace filtrování založené na doméně se skládá z těchto kroků:

1. [Vyberte domény](#select-domains-to-be-synchronized) , které chcete zahrnout do synchronizace.
2. Každý přidat nebo odebrat domény, upravte [profily spuštění](#update-run-profiles).
3. [Použít a ověřte změny](#apply-and-verify-changes).

### <a name="select-the-domains-to-be-synchronized"></a>Vyberte domény k synchronizaci
Pokud chcete nastavit filtr domény, proveďte následující kroky:

1. Přihlaste se k serveru, který je spuštěna synchronizace Azure AD Connect pomocí účtu, který je členem skupiny **ADSyncAdmins** skupiny zabezpečení.
2. Spustit **synchronizační služba** z **spustit** nabídky.
3. Vyberte **konektory**a v **konektory** seznam, vyberte konektor s typem **Active Directory Domain Services**. V **akce**, vyberte **vlastnosti**.  
   ![Vlastnosti konektoru](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Klikněte na tlačítko **konfigurace oddílů adresáře**.
5. V **Vybrat oddíly adresářů** vyberte a zrušte výběr domén podle potřeby. Ověřte, jestli jsou vybrané pouze oddíly, které chcete synchronizovat.  
   ![Oddíly](./media/active-directory-aadconnectsync-configure-filtering/connectorpartitions.png)  
   Pokud jste změnili na místní infrastruktuře služby Active Directory a přidat nebo odebrat domény z doménové struktury, klikněte **aktualizovat** tlačítko získat aktualizovaný seznam. Když aktualizujete, budete vyzváni k zadání pověření. Zadejte všechny přihlašovací údaje, s přístupem pro čtení na Windows Server Active Directory. Nemá být uživatel, který je předem v dialogovém okně.  
   ![Aktualizace potřeba](./media/active-directory-aadconnectsync-configure-filtering/refreshneeded.png)  
6. Když jste hotovi, zavřete **vlastnosti** dialogové okno kliknutím **OK**. Pokud jste odstranili domény z doménové struktury, zprávu automaticky otevírané okno říká, že byla odebrána domény a tato konfigurace se vyčistí.
7. Pokračujte v nastavení [profily spuštění](#update-run-profiles).

### <a name="update-the-run-profiles"></a>Aktualizovat profilů spuštění
Pokud jste aktualizovali domény filtru, musíte také aktualizovat profilů spuštění.

1. V **konektory** seznamu, ujistěte se, že je vybraný konektor, který jste změnili v předchozím kroku. V **akce**, vyberte **konfigurovat profily spuštění**.  
   ![Konektor profily 1 spuštění](./media/active-directory-aadconnectsync-configure-filtering/connectorrunprofiles1.png)  
2. Vyhledat a identifikovat následující profily:
    * Úplný Import
    * Úplná synchronizace
    * Rozdílový Import
    * Rozdílová synchronizace
    * Export
3. Pro každý profil upravit **přidat** a **odebrat** domén.
    1. Pro každou z pět profilů provést následující kroky pro každý **přidat** domény:
        1. Vyberte profil spuštění a klikněte na tlačítko **nový krok**.
        2. Na **krok konfigurace** stránky v **typ** rozevírací nabídce vyberte krok typ se stejným názvem jako profil, který konfigurujete. Pak klikněte na tlačítko **Další**.  
        ![Konektor profily 2 spuštění](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep1.png)  
        3. Na **konfigurace konektoru** stránky v **oddílu** rozevírací nabídky vyberte název domény, který jste přidali do vaší domény filtru.  
        ![Konektor profily 3 spuštění](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep2.png)  
        4. Zavřete **konfigurací profilu spuštění** dialogové okno, klikněte na tlačítko **Dokončit**.
    2. Pro každou z pět profilů provést následující kroky pro každý **odebrat** domény:
        1. Vyberte profil spuštění.
        2. Pokud **hodnotu** z **oddílu** atribut je identifikátor GUID, vyberte kroku spuštění a klikněte na **odstranit krok**.  
        ![Konektor profily 4 spuštění](./media/active-directory-aadconnectsync-configure-filtering/runprofilesdeletestep.png)  
    3. Zkontrolujte změny. Každá doména, která se mají synchronizovat by měl být uvedený jako krok v každý profil spuštění.
4. Zavřete **konfigurovat profily spuštění** dialogové okno, klikněte na tlačítko **OK**.
5.  Chcete-li dokončit konfiguraci, je potřeba spustit **úplný import** a **rozdílová synchronizace**. Pokračujte ve čtení části [použít a ověřte změny](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Filtrování organizační jednotka se systémem
Upřednostňovaný způsob, jak změnit, filtrování podle organizační jednotky je spuštěním Průvodce instalací a změna [domény a filtrování organizační jednotky](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). Průvodce instalací automatizuje všechny úlohy, které jsou popsané v tomto tématu.

Pouze postupujte podle těchto kroků Pokud jste schopni spustit Průvodce instalací z nějakého důvodu.

Konfigurace filtrování organizační jednotka se systémem, proveďte následující kroky:

1. Přihlaste se k serveru, který je spuštěna synchronizace Azure AD Connect pomocí účtu, který je členem skupiny **ADSyncAdmins** skupiny zabezpečení.
2. Spustit **synchronizační služba** z **spustit** nabídky.
3. Vyberte **konektory**a v **konektory** seznam, vyberte konektor s typem **Active Directory Domain Services**. V **akce**, vyberte **vlastnosti**.  
   ![Vlastnosti konektoru](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Klikněte na tlačítko **konfigurace oddílů adresářů**, vyberte doménu, kterou chcete konfigurovat a potom klikněte na **kontejnery**.
5. Když se zobrazí výzva, zadejte všechny přihlašovací údaje, s přístupem pro čtení služby Active Directory v místě. Nemá být uživatel, který je předem v dialogovém okně.
6. V **vybrat kontejnery** dialogové okno pole, zrušte zaškrtnutí políčka organizační jednotky, které nechcete synchronizovat s adresářem v cloudu a pak klikněte na tlačítko **OK**.  
   ![Organizační jednotky v dialogové okno Vybrat kontejnery](./media/active-directory-aadconnectsync-configure-filtering/ou.png)  
   * **Počítače** kontejneru, měla by být vybrána pro vaše počítače s Windows 10 úspěšně synchronizovat do Azure AD. Pokud počítače připojené k doméně se nachází v jiné organizační jednotky, ujistěte se, že ty, které jsou vybrány.
   * Pokud máte několik doménových struktur se vztahem důvěryhodnosti, měl by být vybraný kontejner **ForeignSecurityPrincipals**. Tento kontejner umožňuje řešit členství ve skupinách zabezpečení napříč doménovými strukturami.
   * **RegisteredDevices** organizační jednotky, měla by být vybrána, pokud jste povolili funkci zpětného zápisu zařízení. Pokud používáte další funkcí zpětného zápisu, jako je například zpětný zápis skupin, zkontrolujte, zda že jsou vybrané těchto umístění.
   * Vyberte jiné OU, kde jsou umístěny uživatelů, objektů InetOrgPerson, skupiny, kontakty a počítače. Na obrázku jsou všechny tyto organizační jednotky umístěny v organizační jednotce ManagedObjects.
   * Pomocí filtrování podle skupiny, organizační jednotky, kde se nachází skupiny musí být zahrnuty.
   * Všimněte si, že můžete nakonfigurovat, jestli se nové organizační jednotky, které jsou přidány po dokončení konfigurace filtrování synchronizována, nebo nejsou synchronizovány. Najdete v části Další podrobnosti.
7. Když jste hotovi, zavřete **vlastnosti** dialogové okno kliknutím **OK**.
8. Chcete-li dokončit konfiguraci, je potřeba spustit **úplný import** a **rozdílová synchronizace**. Pokračujte ve čtení části [použít a ověřte změny](#apply-and-verify-changes).

### <a name="synchronize-new-ous"></a>Synchronizovat nové organizační jednotky
Ve výchozím nastavení jsou synchronizovány nové organizační jednotky, které jsou vytvořeny po filtrování byl nakonfigurován. Tento stav je indikován zaškrtnuté políčko. Také můžete zrušit zaškrtnutí některé dílčí organizační jednotky. Toto chování získáte kliknutím na políčko, dokud nebude bílé se modré zaškrtnutím (jeho výchozí stav). Poté zrušte výběr všech sub-organizačním jednotkám, které nechcete synchronizovat.

Pokud se synchronizují všechny dílčí-organizační jednotky, je pole bílé s modrá značka zaškrtnutí.  
![Organizační jednotku s všechna políčka vybrané](./media/active-directory-aadconnectsync-configure-filtering/ousyncnewall.png)

Pokud některé dílčí organizační jednotky nezaškrtnuté, pole je šedá s bílým zaškrtnutí.  
![Organizační jednotku s některé dílčí ou zrušit](./media/active-directory-aadconnectsync-configure-filtering/ousyncnew.png)

Pomocí této konfigurace se synchronizují nové organizační jednotky, která byla vytvořena v rámci ManagedObjects.

Průvodce instalací služby Azure AD Connect vždycky vytvoří tuto konfiguraci.

### <a name="dont-synchronize-new-ous"></a>Nesynchronizovat nové organizační jednotky
Můžete nakonfigurovat synchronizační modul není synchronizovat nové organizačními jednotkami po dokončení konfigurace filtrování. Tento stav je uvedené v uživatelském rozhraní podle pole zobrazování plnou šedou bez zaškrtnutí. Toto chování získáte kliknutím na políčko, dokud nebude bílé bez zaškrtnutí. Pak vyberte dílčí ou, které chcete synchronizovat.

![Organizační jednotky s kořenovým zrušit](./media/active-directory-aadconnectsync-configure-filtering/oudonotsyncnew.png)

Pomocí této konfigurace nové organizační jednotky, která byla vytvořena v rámci ManagedObjects není synchronizován.

## <a name="attribute-based-filtering"></a>Filtrování podle atributů
Ujistěte se, že používáte listopad 2015 ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)) nebo novější sestavení pro tyto kroky při práci.

Filtrování podle atributů je nejpružnější způsob, jak filtrovat objekty. Můžete použít možnosti [deklarativní zřizování](active-directory-aadconnectsync-understanding-declarative-provisioning.md) k řízení skoro každý aspekt když je objekt synchronizovat do Azure AD.

Můžete použít [příchozí](#inbound-filtering) filtrování ze služby Active Directory do úložiště metaverse, a [odchozí](#outbound-filtering) filtrování z úložiště metaverse do služby Azure AD. Doporučujeme použít příchozí filtrování, protože se jedná o nejsnadnější udržovat. Používejte pouze odchozí filtrování, pokud je potřeba připojit objekty z více než jedné doménové struktuře než vyhodnocení může proběhnout.

### <a name="inbound-filtering"></a>Příchozí filtrování
Příchozí filtrování používá výchozí konfigurace, kde objekty přejdete do služby Azure AD musí mít cloudFiltered atribut úložiště metaverse, není nastavena na hodnotu k synchronizaci. Pokud hodnota tohoto atributu je nastavena na **True**, pak objekt není synchronizován. Neměla by být nastavená **False**, návrh. Pokud chcete mít jistotu, ostatní pravidla mít možnost přispívat hodnotu, tento atribut pouze by měl mít hodnoty **True** nebo **NULL** (chybí).

V příchozí filtrování použijete možnosti **oboru** k určení, které objekty se mají synchronizovat, nebo nebude synchronizovat. Toto je, kde můžete provádět úpravy podle požadavků vaší vlastní organizaci. Má modul oboru **skupiny** a **klauzule** k určení, kdy je synchronizační pravidlo v oboru. Skupina obsahuje jednu nebo více klauzulích. Mezi více klauzulí a logickým operátorem "nebo" mezi více skupin je logické "a".

Dejte nám podívejte se na příklad:  
![Rozsah](./media/active-directory-aadconnectsync-configure-filtering/scope.png)  
To byste si měli přečíst jako **(oddělení = IT) nebo (oddělení = prodeje a c = US)**.

V následující ukázky a kroky použijte objekt uživatele jako příklad, ale můžete použít pro všechny typy objektů.

V následující ukázky hodnotu priority, která začíná 50. To může být jakékoli číslo, které nejsou používány, ale musí být menší než 100.

#### <a name="negative-filtering-do-not-sync-these"></a>Záporné filtrování: "není synchronizována tyto"
V následujícím příkladu můžete filtrovat (nesynchronizovat) všech uživatelů kde **extensionAttribute15** má hodnotu **NoSync**.

1. Přihlaste se k serveru, který je spuštěna synchronizace Azure AD Connect pomocí účtu, který je členem skupiny **ADSyncAdmins** skupiny zabezpečení.
2. Spustit **editoru pravidel synchronizace** z **spustit** nabídky.
3. Zajistěte, aby **příchozí** je vybrána a klikněte na tlačítko **přidat nové pravidlo**.
4. Zadejte pro pravidlo popisný název, například "*v ze služby Active Directory – uživatel DoNotSyncFilter*". Vyberte správný doménové struktuře, vyberte **uživatele** jako **typ objektu CS**a vyberte **osoba** jako **typ objektu MV**. V **typu propojení**, vyberte **připojení**. V **přednost**, zadejte hodnotu, která není aktuálně používán jiným pravidlem synchronizace (například 50) a pak klikněte na tlačítko **Další**.  
   ![Příchozí 1 popis](./media/active-directory-aadconnectsync-configure-filtering/inbound1.png)  
5. V **Scoping filtru**, klikněte na tlačítko **přidat skupinu**a klikněte na tlačítko **přidat klauzuli**. V **atribut**, vyberte **ExtensionAttribute15**. Ujistěte se, že **operátor** je nastaven na **ROVNA**a zadejte hodnotu **NoSync** v **hodnotu** pole. Klikněte na **Další**.  
   ![Příchozí 2 oboru](./media/active-directory-aadconnectsync-configure-filtering/inbound2.png)  
6. Ponechte **připojení** pravidla prázdný a potom klikněte na **Další**.
7. Klikněte na tlačítko **přidat transformace**, vyberte **typ toku** jako **konstantní**a vyberte **cloudFiltered** jako **cíl Atribut**. V **zdroj** textového pole, typ **True**. Klikněte na tlačítko **přidat** uložíte pravidlo.  
   ![Příchozí 3 transformace](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)
8. Chcete-li dokončit konfiguraci, je potřeba spustit **úplné synchronizace**. Pokračujte ve čtení části [použít a ověřte změny](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Kladné filtrování: "synchronizovat pouze ty"
Vyjádření kladné filtrování může být více náročné, protože máte také vzít v úvahu objekty, které nejsou zřejmé synchronizaci, jako je například konferenční místnosti. Také chcete přepsat výchozí filtr v pravidle out-of-box **v ze služby Active Directory - připojení uživatele k**. Když vytvoříte vlastního filtru, ujistěte se, že jste nebudou obsahovat speciální poštovních schránek, důležitých systémových objektů, objekty konflikt replikace a účty služby Azure AD Connect.

Kladné filtrování možnost vyžaduje dvě pravidla synchronizace. Musíte jedno pravidlo (nebo několik) s správný rozsah objektů, které chcete synchronizovat. Budete také potřebovat druhé pravidlo synchronizace catch-all, který odfiltruje všechny objekty, které dosud nebyly určeny jako objekt, který se má synchronizovat.

V následujícím příkladu, kde atribut oddělení má hodnotu uživatelské objekty pouze synchronizovat **prodej**.

1. Přihlaste se k serveru, který je spuštěna synchronizace Azure AD Connect pomocí účtu, který je členem skupiny **ADSyncAdmins** skupiny zabezpečení.
2. Spustit **editoru pravidel synchronizace** z **spustit** nabídky.
3. Zajistěte, aby **příchozí** je vybrána a klikněte na tlačítko **přidat nové pravidlo**.
4. Zadejte pro pravidlo popisný název, například "*v ze služby Active Directory – prodej uživatele synchronizovat*". Vyberte správný doménové struktuře, vyberte **uživatele** jako **typ objektu CS**a vyberte **osoba** jako **typ objektu MV**. V **typu propojení**, vyberte **připojení**. V **přednost**, zadejte hodnotu, která není aktuálně používán jiným pravidlem synchronizace (například 51) a pak klikněte na tlačítko **Další**.  
   ![Příchozí 4 popis](./media/active-directory-aadconnectsync-configure-filtering/inbound4.png)  
5. V **Scoping filtru**, klikněte na tlačítko **přidat skupinu**a klikněte na tlačítko **přidat klauzuli**. V **atribut**, vyberte **oddělení**. Ujistěte se, že operátor je nastavená na **ROVNA**a zadejte hodnotu **prodej** v **hodnotu** pole. Klikněte na **Další**.  
   ![Příchozí 5 oboru](./media/active-directory-aadconnectsync-configure-filtering/inbound5.png)  
6. Ponechte **připojení** pravidla prázdný a potom klikněte na **Další**.
7. Klikněte na tlačítko **přidat transformace**, vyberte **konstantní** jako **typ toku**a vyberte **cloudFiltered** jako **cíl Atribut**. V **zdroj** zadejte **False**. Klikněte na tlačítko **přidat** uložíte pravidlo.  
   ![Příchozí 6 transformace](./media/active-directory-aadconnectsync-configure-filtering/inbound6.png)  
   Toto je zvláštní případ, kde můžete explicitně nastavit cloudFiltered na **False**.
8. Máme teď k vytvoření pravidla synchronizace catch-all. Zadejte pro pravidlo popisný název, například "*v ze služby Active Directory – všechny uživatele Catch filtru*". Vyberte správný doménové struktuře, vyberte **uživatele** jako **typ objektu CS**a vyberte **osoba** jako **typ objektu MV**. V **typu propojení**, vyberte **připojení**. V **přednost před**, zadejte hodnotu, která není aktuálně používán jiným pravidlem synchronizace (například 99). Vybrali jste přednost před hodnotu, která je vyšší (nižší prioritu) než předchozí synchronizační pravidlo. Ale některé místnosti jste zbývajících také tak, aby můžete přidat více pravidel filtrování synchronizace později Pokud chcete zahájit synchronizaci další oddělení. Klikněte na **Další**.  
   ![Příchozí 7 popis](./media/active-directory-aadconnectsync-configure-filtering/inbound7.png)  
9. Nechte **Scoping filtru** prázdná a klikněte na tlačítko **Další**. Filtr prázdný označuje, že pravidlo je má být použita pro všechny objekty.
10. Ponechte **připojení** pravidla prázdný a potom klikněte na **Další**.
11. Klikněte na tlačítko **přidat transformace**, vyberte **konstantní** jako **typ toku**a vyberte **cloudFiltered** jako **cíl Atribut**. V **zdroj** zadejte **True**. Klikněte na tlačítko **přidat** uložíte pravidlo.  
    ![Příchozí 3 transformace](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)  
12. Chcete-li dokončit konfiguraci, je potřeba spustit **úplné synchronizace**. Pokračujte ve čtení části [použít a ověřte změny](#apply-and-verify-changes).

Pokud potřebujete, můžete vytvořit další pravidla první typu vložíte více objektů v synchronizaci.

### <a name="outbound-filtering"></a>Odchozí filtrování
V některých případech je potřeba provést filtrování až po připojení objektů v úložišti metaverse. Například může být potřeba podívejte se na atribut e-mailu z doménové struktury prostředku a atribut userPrincipalName z doménové struktury účtu k určení, pokud objekt by měl synchronizovat. V těchto případech můžete vytvořit filtrování podle odchozí pravidlo.

V tomto příkladu můžete měnit filtrování tak, aby pouze uživatelé, které mají své e-mailu a userPrincipalName končící na @contoso.com jsou synchronizovány:

1. Přihlaste se k serveru, který je spuštěna synchronizace Azure AD Connect pomocí účtu, který je členem skupiny **ADSyncAdmins** skupiny zabezpečení.
2. Spustit **editoru pravidel synchronizace** z **spustit** nabídky.
3. V části **typ pravidel**, klikněte na tlačítko **odchozí**.
4. V závislosti na verzi připojení můžete použít buď najít na pravidlo s názvem **Out aad – připojení uživatele k** nebo **Out aad - uživatel připojit SOAInAD**a klikněte na tlačítko **upravit**.
5. V místní nabídce zodpovědět **Ano** vytvoření kopie pravidla.
6. Na **popis** změňte **přednost** nepoužívané hodnotu, jako je 50.
7. Klikněte na tlačítko **Scoping filtru** na levé navigaci a potom klikněte na **přidat klauzuli**. V **atribut**, vyberte **e-mailu**. V **operátor**, vyberte **ENDSWITH**. V **hodnotu**, typ **@contoso.com**a potom klikněte na **přidat klauzuli**. V **atribut**, vyberte **userPrincipalName**. V **operátor**, vyberte **ENDSWITH**. V **hodnotu**, typ **@contoso.com**.
8. Klikněte na **Uložit**.
9. Chcete-li dokončit konfiguraci, je potřeba spustit **úplné synchronizace**. Pokračujte ve čtení části [použít a ověřte změny](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Použít a ověřte změny
Po provedení změny konfigurace, je nutné použít pro objekty, které jsou již přítomny v systému. Je také možné, že má být zpracován objekty, které nejsou aktuálně v synchronizační modul (a synchronizační modul, musí přečíst zdrojovém systému znovu a ověřte jeho obsah).

Pokud jste změnili konfiguraci pomocí **domény** nebo **organizační jednotky** filtrování, pak musíte udělat **úplný import**, za nímž následují **rozdílů synchronizace**.

Pokud jste změnili konfiguraci pomocí **atribut** filtrování, pak musíte udělat **úplné synchronizace**.

Proveďte následující kroky:

1. Spustit **synchronizační služba** z **spustit** nabídky.
2. Vyberte **konektory**. V **konektory** vyberte konektor, kde jste provedli dříve změnu konfigurace. V **akce**, vyberte **spustit**.  
   ![Konektor spustit](./media/active-directory-aadconnectsync-configure-filtering/connectorrun.png)  
3. V **profily spuštění**, vyberte operaci, která byla uvedená v předchozí části. Pokud potřebujete spustit dvě akce, spusťte druhý po dokončení první z nich. ( **Stavu** sloupec je **nečinnosti** pro vybrané konektor.)

Po synchronizaci všechny změny jsou připraveny pro export. Dříve, než ve skutečnosti změny ve službě Azure AD, budete chtít ověřte správnost tyto změny.

1. Spusťte příkazový řádek a přejděte na `%Program Files%\Microsoft Azure AD Sync\bin`.
2. Spusťte `csexport "Name of Connector" %temp%\export.xml /f:x`.  
   Název konektoru se synchronizační služba. Je podobná "contoso.com – AAD" název pro Azure AD.
3. Spusťte `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`.
4. Nyní máte soubor v adresáři % temp % s názvem export.csv, který může být prověřen v aplikaci Microsoft Excel. Tento soubor obsahuje všechny změny, které mají být exportovány.
5. Proveďte potřebné změny data nebo konfigurace a spusťte tyto kroky opakujte (Import, synchronizovat a ověřte, zda) dokud se změny, které mají být exportovány očekávat.

Až budete spokojeni, exportujte změny do Azure AD.

1. Vyberte **konektory**. V **konektory** vyberte konektor služby Azure AD. V **akce**, vyberte **spustit**.
2. V **profily spuštění**, vyberte **exportovat**.
3. Pokud vaše změny konfigurace odstraňují mnoho objektů, pak zobrazí chybu v exportu Pokud je počet větší než nastavená prahová hodnota (ve výchozím nastavení 500). Pokud se zobrazí tato chyba, pak je nutné dočasně zakázat "[prevence náhodného odstranění](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)" funkce.

Nyní je čas Plánovač znovu zapnout.

1. Spustit **Plánovač úloh** z **spustit** nabídky.
2. Přímo pod **Knihovna plánovače úloh**, najít úloha s názvem **Azure AD Sync Scheduler**, klikněte pravým tlačítkem a vyberte **povolit**.

## <a name="group-based-filtering"></a>Filtrování podle skupiny
Můžete nakonfigurovat na základě skupiny poprvé nainstalujte Azure AD Connect pomocí filtrování [vlastní instalaci](active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups). Je určený pro pilotní nasazení místo, kam chcete jenom malé sadu objektů, které chcete synchronizovat. Pokud zakážete filtrování podle skupiny, nelze znovu povolena. Má *nepodporuje* pro použití na základě skupiny filtrování ve vlastní konfiguraci. Je podporován pouze pro tuto funkci konfigurovat pomocí Průvodce instalací. Když jste dokončili vašeho pilotního nasazení, použijte jednu z dalších možností filtrování v tomto tématu. Pokud používáte založené na organizační jednotku filtrování ve spojení s filtrování podle skupiny, musí být zahrnut OU(s), kde se nachází skupiny a její členy.

Při synchronizaci více doménových struktur služby AD, můžete nakonfigurovat filtrování podle skupiny zadáním jiné skupiny pro každý konektor AD. Nechcete-li synchronizovat uživatele ve službě AD jeden doménové struktury a stejného uživatele obsahuje jednu nebo více odpovídající objekty v jiných doménových strukturách AD, musíte zajistit, že objekt uživatele a všechny jeho odpovídající objekty jsou v rámci na základě skupiny filtrování oboru. Příklady:

* Máte uživatel v jedné doménové struktuře, která má odpovídající objekt FSP (cizí objekt zabezpečení) v jiné doménové struktuře. Oba objekty musí být v rámci na základě skupiny filtrování oboru. Uživatel, jinak nebudou synchronizovány do Azure AD.

* Máte uživatel v jedné doménové struktuře, která má odpovídající účet prostředků (například propojená poštovní schránka) v jiné doménové struktuře. Navíc jste nakonfigurovali Azure AD Connect propojení uživatele s účtem prostředků. Oba objekty musí být v rámci na základě skupiny filtrování oboru. Uživatel, jinak nebudou synchronizovány do Azure AD.

* Máte-li uživatel v jedné doménové struktuře, která má odpovídající e-mailu kontaktujte v jiné doménové struktuře. Navíc jste nakonfigurovali Azure AD Connect propojení uživatele s e-mailu kontakt. Oba objekty musí být v rámci na základě skupiny filtrování oboru. Uživatel, jinak nebudou synchronizovány do Azure AD.


## <a name="next-steps"></a>Další postup
- Další informace o [synchronizace Azure AD Connect](active-directory-aadconnectsync-whatis.md) konfigurace.
- Další informace o [integrace místních identit s Azure AD](active-directory-aadconnect.md).
