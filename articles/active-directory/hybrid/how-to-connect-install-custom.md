---
title: Přizpůsobení instalace Azure Active Directory Connect
description: Tento článek popisuje možnosti vlastní instalace pro Azure AD Connect. Použijte tyto pokyny, pokud chcete nainstalovat službu Active Directory přes Azure AD Connect.
services: active-directory
keywords: co je Azure AD Connect, instalace služby Active Directory, požadované součásti služby Azure AD
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 6d42fb79-d9cf-48da-8445-f482c4c536af
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/10/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3afeadff71bd373354b891bd6690d94d28fc0805
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92096347"
---
# <a name="custom-installation-of-azure-active-directory-connect"></a>Vlastní instalace Azure Active Directory Connect
Pokud chcete mít další možnosti instalace, použijte *vlastní nastavení* v Azure Active Directory (Azure AD) Connect. Tato nastavení použijte například v případě, že máte více doménových struktur nebo pokud chcete nakonfigurovat volitelné funkce. Použijte vlastní nastavení ve všech případech, kde [Expresní instalace](how-to-connect-install-express.md) nevyhovuje vašim požadavkům na nasazení nebo topologii.

Požadavky:
- [Stáhněte si Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771).
- Splnění požadovaných kroků v [Azure AD Connect: hardware a požadavky](how-to-connect-install-prerequisites.md). 
- Ujistěte se, že máte účty popsané v [Azure AD Connect účty a oprávnění](reference-connect-accounts-permissions.md).

## <a name="custom-installation-settings"></a>Vlastní nastavení instalace 

Pokud chcete nastavit vlastní instalaci pro Azure AD Connect, přečtěte si stránky průvodce, které popisuje následující oddíly.

### <a name="express-settings"></a>Expresní nastavení
Na stránce **expresní nastavení** vyberte **přizpůsobit** a spusťte instalaci přizpůsobeného nastavení.  Zbývající část tohoto článku vás provede procesem vlastní instalace. Pomocí následujících odkazů můžete rychle přejít na informace o konkrétní stránce:

- [Požadované součásti](#install-required-components)
- [Přihlášení uživatele](#user-sign-in)
- [Připojení k Azure AD](#connect-to-azure-ad)
- [Synchronizovat](#sync-pages)

### <a name="install-required-components"></a>Instalace požadovaných součástí
Při instalaci služeb synchronizace můžete nechat nevybranou možnost volitelný konfigurační oddíl. Azure AD Connect nastaví vše automaticky. Nastaví SQL Server instanci LocalDB 2012 Express, vytvoří příslušné skupiny a přiřadí oprávnění. Chcete-li změnit výchozí nastavení, zrušte zaškrtnutí příslušných políček.  Následující tabulka shrnuje tyto možnosti a obsahuje odkazy na Další informace. 

![Snímek obrazovky s volitelnými volbami pro požadované součásti instalace v Azure AD Connect.](./media/how-to-connect-install-custom/requiredcomponents2.png)

| Volitelná konfigurace | Description |
| --- | --- |
|Zadat vlastní umístění instalace| Umožňuje změnit výchozí instalační cestu pro Azure AD Connect.|
| Použít existující server SQL Server |Umožňuje zadat název SQL Server a název instance. Tuto možnost vyberte, pokud již máte databázový server, který chcete použít. Do pole **název instance** zadejte název instance, čárku a číslo portu, pokud vaše instance SQL Server nemá povoleno procházení.  Pak zadejte název databáze Azure AD Connect.  Vaše oprávnění SQL určují, jestli je možné vytvořit novou databázi, nebo že správce SQL musí databázi vytvořit předem.  Pokud máte oprávnění správce SQL Server (SA), přečtěte si téma [instalace Azure AD Connect pomocí existující databáze](how-to-connect-install-existing-database.md).  Pokud máte delegovaná oprávnění (DBO), přečtěte si téma [instalace Azure AD Connect pomocí oprávnění delegovaného správce SQL](how-to-connect-install-sql-delegation.md). |
| Použít existující účet služby |Ve výchozím nastavení Azure AD Connect poskytuje účet virtuální služby pro synchronizační služby. Pokud používáte vzdálenou instanci SQL Server nebo používáte proxy server, který vyžaduje ověření, můžete použít *účet spravované služby* nebo účet služby chráněný heslem v doméně. V těchto případech zadejte účet, který chcete použít. Pokud chcete instalaci spustit, musíte být v SQL serveru SA, abyste mohli vytvořit přihlašovací údaje pro účet služby. Další informace najdete v tématu [Azure AD Connect účty a oprávnění](reference-connect-accounts-permissions.md#adsync-service-account). </br></br>Pomocí nejnovějšího sestavení může správce SQL nyní zřídit databázi mimo IP síť. Správce Azure AD Connect ho může nainstalovat s právy vlastníka databáze.  Další informace najdete v tématu [instalace Azure AD Connect pomocí oprávnění delegovaného správce SQL](how-to-connect-install-sql-delegation.md).|
| Zadat vlastní skupiny pro synchronizaci |Ve výchozím nastavení, když jsou nainstalovány synchronizační služby, Azure AD Connect vytvoří čtyři skupiny, které jsou místní pro server. Tyto skupiny jsou správci, operátoři, procházení a resetování hesla. Tady můžete zadat vlastní skupiny. Skupiny musí být na serveru místní. Nemůžou být umístěné v doméně. |
|Importovat nastavení synchronizace (Preview)|Umožňuje importovat nastavení z jiných verzí Azure AD Connect.  Další informace najdete v tématu [Import a export nastavení konfigurace Azure AD Connect](how-to-connect-import-export-config.md).|

### <a name="user-sign-in"></a>Přihlášení uživatele
Po instalaci požadovaných součástí vyberte metodu jednotného přihlašování uživatelů. Následující tabulka stručně popisuje dostupné možnosti. Úplný popis metod přihlášení najdete v tématu [Přihlášení uživatele](plan-connect-user-signin.md).

![Snímek obrazovky zobrazující stránku přihlášení uživatele Je vybraná možnost synchronizace hodnot hash hesel.](./media/how-to-connect-install-custom/usersignin4.png)

| Možnost jednotného přihlašování | Description |
| --- | --- |
| Synchronizace hodnot hash hesel |Uživatelé se mohou přihlásit ke cloudovým službám Microsoftu, jako je například Microsoft 365, pomocí stejného hesla, které používají ve své místní síti. Uživatelská hesla se synchronizují do Azure AD jako hodnota hash hesla. Ověřování probíhá v cloudu. Další informace najdete v tématu [synchronizace hodnot hash hesel](how-to-connect-password-hash-synchronization.md). |
|Předávací ověřování|Uživatelé se mohou přihlásit ke cloudovým službám Microsoftu, jako je například Microsoft 365, pomocí stejného hesla, které používají ve své místní síti.  Hesla uživatelů se ověřují pomocí předávání do místního řadiče domény služby Active Directory.
| Federace se službou AD FS |Uživatelé se mohou přihlásit ke cloudovým službám Microsoftu, jako je například Microsoft 365, pomocí stejného hesla, které používají ve své místní síti.  Uživatelé budou přesměrováni do své místní instance služby Azure Directory Federation Services (AD FS), aby se mohli přihlásit. Ověřování probíhá místně. |
| Federace s PingFederate|Uživatelé se mohou přihlásit ke cloudovým službám Microsoftu, jako je například Microsoft 365, pomocí stejného hesla, které používají ve své místní síti.  Uživatelé budou přesměrováni na svou místní instanci PingFederate, aby se mohli přihlásit. Ověřování probíhá místně. |
| Nekonfigurovat |Není nainstalovaná nebo nakonfigurovaná žádná funkce přihlášení uživatele. Tuto možnost vyberte, pokud už máte federační server jiného výrobce nebo jiné řešení. |
|Povolit jednotné přihlašování|Tato možnost je k dispozici pro synchronizaci hodnot hash hesel i předávací ověřování. Nabízí prostředí s jednotným přihlašováním pro stolní uživatele v podnikových sítích. Další informace najdete v tématu [jednotné přihlašování](how-to-connect-sso.md). </br></br>**Poznámka:** Pro AD FS zákazníky Tato možnost není k dispozici. AD FS už nabízí stejnou úroveň jednotného přihlašování.</br>

### <a name="connect-to-azure-ad"></a>Připojení k Azure AD
Na stránce **připojit ke službě Azure AD** zadejte účet a heslo globálního správce. Pokud jste na předchozí stránce vybrali možnost **federace s AD FS** , nemusíte se přihlašovat pomocí účtu, který je v doméně, kterou plánujete povolit pro federaci. 

Je možné, že budete chtít použít účet ve výchozí doméně *onmicrosoft.com* , která je součástí vašeho TENANTA Azure AD. Tento účet se používá jenom k vytvoření účtu služby ve službě Azure AD. Po dokončení instalace se nepoužívá.
  
![Snímek obrazovky zobrazující stránku připojit k Azure AD](./media/how-to-connect-install-custom/connectaad.png)

Pokud má váš účet globálního správce povolené vícefaktorové ověřování, zadejte ho znovu v přihlašovacím okně a musíte dokončit vícefaktorové ověřování. Výzvou by mohla být ověřovací kód nebo telefonní hovor.  

![Snímek obrazovky zobrazující stránku připojit k Azure AD Pole vícefaktorového ověřování vyzve uživatele k zadání kódu.](./media/how-to-connect-install-custom/connectaadmfa.png)

Účet globálního správce může mít také povolenou [správu privilegovaných identit](../privileged-identity-management/pim-getting-started.md) .

Pokud se zobrazí chyba nebo máte problémy s připojením, přečtěte si téma [řešení problémů s připojením](tshoot-connect-connectivity.md).

## <a name="sync-pages"></a>Synchronizovat stránky

V následujících částech jsou popsány stránky v části **synchronizace** .

### <a name="connect-your-directories"></a>Připojení adresářů
Pokud se chcete připojit k Active Directory Domain Services (Azure služba AD DS), Azure AD Connect potřebovat název doménové struktury a přihlašovací údaje účtu s dostatečnými oprávněními.

![Snímek obrazovky, na kterém se zobrazuje stránka "připojit adresáře"](./media/how-to-connect-install-custom/connectdir01.png)

Po zadání názvu doménové struktury a výběru  **Přidat adresář** se zobrazí okno. Následující tabulka popisuje vaše možnosti.

| Možnost | Popis |
| --- | --- |
| Vytvořit nový účet | Vytvořte účet Azure služba AD DS, který Azure AD Connect musí během synchronizace adresářů připojit k doménové struktuře služby Active Directory. Po výběru této možnosti zadejte uživatelské jméno a heslo pro účet správce podnikové sítě.  Azure AD Connect pomocí zadaného účtu Enterprise Admin vytvoří požadovaný účet Azure služba AD DS. Součást domény můžete zadat buď ve formátu NetBIOS, nebo ve formátu plně kvalifikovaného názvu domény. To znamená, že zadáte *FABRIKAM\administrator* nebo *fabrikam. com\administrator*. |
| Použít existující účet | Zadejte existující účet Azure služba AD DS, který Azure AD Connect můžete použít pro připojení k doménové struktuře služby Active Directory během synchronizace adresářů. Součást domény můžete zadat buď ve formátu NetBIOS, nebo ve formátu plně kvalifikovaného názvu domény. To znamená, že zadáte *FABRIKAM\syncuser* nebo *fabrikam. com\syncuser*. Tento účet může být běžný uživatelský účet, protože potřebuje jenom výchozí oprávnění ke čtení. V závislosti na vašem scénáři ale možná budete potřebovat další oprávnění. Další informace najdete v tématu [Azure AD Connect účty a oprávnění](reference-connect-accounts-permissions.md#create-the-ad-ds-connector-account). |

![Snímek obrazovky se stránkou "připojit adresář" a oknem účtu doménové struktury D, kde se můžete rozhodnout vytvořit nový účet nebo použít existující účet.](./media/how-to-connect-install-custom/connectdir02.png)

>[!NOTE]
> Od 1.4.18.0 sestavení nemůžete jako účet služby Azure služba AD DS Connector použít účet správce rozlehlé sítě ani správce domény. Když vyberete **použít existující účet**, pokud se pokusíte zadat účet Enterprise Admin nebo účet správce domény, zobrazí se následující chyba: "použití účtu správce organizace nebo domény pro účet doménové struktury AD není povoleno. Umožněte vám Azure AD Connect vytvořit účet nebo zadat synchronizační účet se správnými oprávněními. "
>

### <a name="azure-ad-sign-in-configuration"></a>Konfigurace přihlášení k Azure AD
Na stránce **Konfigurace přihlášení k Azure AD** zkontrolujte domény hlavního názvu uživatele (UPN) v místní službě Azure služba AD DS. Tyto domény hlavního názvu uživatele (UPN) byly ověřeny v Azure AD. Na této stránce nakonfigurujete atribut, který má být použit pro userPrincipalName.

![Snímek obrazovky se zobrazenými neověřenými doménami na stránce Konfigurace přihlášení Azure A D](./media/how-to-connect-install-custom/aadsigninconfig2.png)  

Zkontrolujte všechny domény označené jako **Nepřidáné** nebo **neověřené**. Ujistěte se, že domény, které používáte, byly ověřeny v Azure AD. Po ověření domén vyberte ikonu kruhové aktualizace. Další informace najdete v tématu [Přidání a ověření domény](../fundamentals/add-custom-domain.md).

Uživatelé používají atribut *userPrincipalName* při přihlášení k Azure AD a Microsoft 365. Před synchronizací uživatelů by služba Azure AD měla ověřit domény, označované také jako přípona hlavního názvu uživatele (UPN). Společnost Microsoft doporučuje, abyste zachovali výchozí atribut userPrincipalName. 

Pokud je atribut userPrincipalName nonroutable a nelze ho ověřit, můžete vybrat jiný atribut. Můžete například vybrat možnost e-mail jako atribut, který obsahuje přihlašovací ID. Použijete-li jiný atribut než userPrincipalName, je označováno jako *alternativní ID*. 

Hodnota atributu alternativní ID musí odpovídat standardu RFC 822. Můžete použít alternativní ID s funkcí synchronizace hodnot hash hesel, předávacího ověřování a federace. Ve službě Active Directory se atribut nedá definovat jako vícehodnotový, i když má jenom jednu hodnotu. Další informace o alternativním ID najdete v tématu [předávací ověřování – Nejčastější dotazy](./how-to-connect-pta-faq.md#does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname).

>[!NOTE]
> Pokud povolíte předávací ověřování, musíte mít alespoň jednu ověřenou doménu, abyste mohli pokračovat v procesu vlastní instalace.

> [!WARNING]
> Alternativní ID nejsou kompatibilní se všemi Microsoft 365 úlohami. Další informace najdete v tématu [Konfigurace alternativních přihlašovacích ID](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).
>

### <a name="domain-and-ou-filtering"></a>Filtrování domén a organizačních jednotek
Ve výchozím nastavení se synchronizují všechny domény a organizační jednotky (OU). Pokud nechcete synchronizovat některé domény nebo organizační jednotky s Azure AD, můžete zrušit příslušné výběry.  

![Snímek obrazovky se stránkou filtrování domény a O-U](./media/how-to-connect-install-custom/domainoufiltering.png)  

Tato stránka nakonfiguruje filtrování založené na doméně a organizační jednotce. Pokud máte v plánu provádět změny, přečtěte si téma [filtrování založené na doméně](how-to-connect-sync-configure-filtering.md#domain-based-filtering) a [filtrování podle organizačních jednotek](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Některé organizační jednotky jsou zásadní pro funkčnost, takže byste je měli nechat zaškrtnuté.

Použijete-li filtrování založené na organizační jednotce s Azure AD Connect verzí starší než 1.1.524.0, budou nové organizační jednotky ve výchozím nastavení synchronizovány. Pokud nechcete, aby se nové organizační jednotky synchronizovaly, můžete po kroku [filtrování na základě organizační jednotky](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) upravit výchozí chování. Pro Azure AD Connect 1.1.524.0 nebo novější můžete určit, jestli chcete nové organizační jednotky synchronizovat.

Pokud plánujete použít [filtrování na základě skupin](#sync-filtering-based-on-groups), ujistěte se, že organizační jednotka se skupinou je zahrnutá a není filtrována pomocí filtrování organizačních jednotek. Filtrování organizačních jednotek je vyhodnoceno před vyhodnocením filtrování na základě skupin.

Je také možné, že některé domény jsou nedosažitelné z důvodu omezení brány firewall. Ve výchozím nastavení se tyto domény nevýběrují a zobrazují se upozornění.  

![Snímek obrazovky znázorňující nedostupné domény](./media/how-to-connect-install-custom/unreachable.png)  

Pokud se zobrazí toto upozornění, ujistěte se, že tyto domény jsou skutečně nedosažitelné a že je očekávané upozornění.

### <a name="uniquely-identifying-your-users"></a>Jednoznačná identifikace uživatelů

Na stránce **identifikace uživatelů** vyberte způsob identifikace uživatelů v místních adresářích a jejich identifikaci pomocí atributu sourceAnchor.

#### <a name="select-how-users-should-be-identified-in-your-on-premises-directories"></a>Vyberte způsob, jakým se mají uživatelé identifikovat v místních adresářích
Pomocí funkce *Shoda napříč doménovými strukturami* můžete definovat, jak budou uživatelé z doménových struktur Azure služba AD DS ve službě Azure AD zastoupeni. Uživatel může být ve všech doménových strukturách reprezentován jenom jednou nebo může mít kombinaci povolených a zakázaných účtů. Uživatel také může být v některých doménových strukturách reprezentován jako kontakt.

![Snímek obrazovky zobrazující stránku, kde můžete jednoznačně identifikovat uživatele.](./media/how-to-connect-install-custom/unique2.png)

| Nastavení | Popis |
| --- | --- |
| [Uživatelé se v rámci všech doménových struktur reprezentují jenom jednou.](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Všichni uživatelé jsou vytvořeni jako jednotlivé objekty v Azure AD. Objekty nejsou připojené do úložiště metaverse. |
| [Atribut Mail](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Tato možnost spojí uživatele a kontakty, pokud má atribut mail v různých doménových strukturách stejnou hodnotu. Tuto možnost použijte, když se kontakty vytvořily pomocí GALSync. Pokud zvolíte tuto možnost, uživatelské objekty, jejichž atribut pošty se vyplní, se nesynchronizují do Azure AD. |
| [Atributy ObjectSID a msExchangeMasterAccountSID/msRTCSIP-OriginatorSID](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Tato možnost spojí povoleného uživatele v doménové struktuře účtu se zakázaným uživatelem v doménové struktuře prostředku. V systému Exchange se tato konfigurace označuje jako propojená poštovní schránka. Tuto možnost můžete použít, pokud používáte pouze Lync a pokud v doménové struktuře prostředků není k dispozici server Exchange. |
| Atributy SAMAccountName a MailNickName |Tato možnost se připojí k atributům, kde se očekává, že se najde ID přihlášení pro uživatele. |
| Zvolit konkrétní atribut |Tato možnost umožňuje vybrat vlastní atribut. Pokud zvolíte tuto možnost, uživatelské objekty, jejichž (vybraný) atribut není naplněné, se synchronizují do Azure AD. **Omezení:** Pro tuto možnost jsou k dispozici pouze atributy, které jsou již v úložišti Metaverse. |

#### <a name="select-how-users-should-be-identified-by-using-a-source-anchor"></a>Vyberte, jak se mají uživatelé identifikovat pomocí zdrojového ukotvení.
Atribut *sourceAnchor* je během životnosti objektu uživatele neměnný. Jedná se o primární klíč, který odkazuje na místního uživatele s uživatelem v Azure AD.

| Nastavení | Popis |
| --- | --- |
| Umožněte Azure spravovat zdrojové kotvy. | Tuto možnost vyberte, pokud chcete, aby Azure AD vybral atribut za vás. Pokud vyberete tuto možnost, Azure AD Connect použije logiku výběru atributů sourceAnchor, která je popsána v tématu [použití MS-DS-ConsistencyGuid jako sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). Po dokončení vlastní instalace uvidíte, který atribut byl vybrán jako atribut sourceAnchor. |
| Zvolit konkrétní atribut | Tuto možnost vyberte, pokud chcete jako atribut sourceAnchor zadat existující atribut AD. |

Vzhledem k tomu, že atribut sourceAnchor nelze změnit, je nutné zvolit odpovídající atribut. Jednou z vhodných možností je objectGUID. Tento atribut se nemění, pokud uživatelský účet není přesunut mezi doménovou strukturou nebo doménami. Nevybírejte atributy, které se mohou změnit, když osoba manželství nebo změní přiřazení. 

Nemůžete použít atributy, které zahrnují znak @ (@), takže nemůžete použít e-mail a userPrincipalName. U atributů se rozlišují i velká a malá písmena, takže když přesunete objekt mezi doménovými strukturami, nezapomeňte zachovat velká a malá písmena. Binární atributy jsou zakódované v kódování Base64, ale jiné typy atributů zůstávají v nekódovaném stavu. 

Ve federačních scénářích a některých rozhraních Azure AD se atribut sourceAnchor označuje taky jako *immutableID*. 

Další informace o zdrojovém kotvě najdete v tématu [Koncepty návrhu](plan-connect-design-concepts.md#sourceanchor).

### <a name="sync-filtering-based-on-groups"></a>Filtrování synchronizace podle skupin
Funkce filtrování na základě skupin umožňuje synchronizovat pouze malou podmnožinu objektů pro pilotní nasazení. Chcete-li tuto funkci použít, vytvořte skupinu pro tento účel v místní instanci služby Active Directory. Jako přímé členy přidejte uživatele a skupiny, které chcete synchronizovat do Azure AD. Později můžete přidat uživatele nebo odebrat uživatele z této skupiny, abyste zachovali seznam objektů, které by se měly nacházet ve službě Azure AD. 

Všechny objekty, které chcete synchronizovat, musí být přímí členové skupiny. Uživatelé, skupiny, kontakty a počítače nebo zařízení musí být všichni přímí členové. Členství ve vnořené skupině není vyřešeno. Když přidáte skupinu jako člena, přidá se jenom samotná skupina. Jeho členové nejsou přidáni.

![Snímek obrazovky zobrazující stránku, kde můžete zvolit, jak filtrovat uživatele a zařízení.](./media/how-to-connect-install-custom/filter2.png)

> [!WARNING]
> Tato funkce je určená pro podporu pouze pilotního nasazení. Nepoužívejte ho v plném produkčním nasazení.
>

V plném produkčním nasazení by bylo obtížné udržovat jednu skupinu a všechny její objekty, které se mají synchronizovat. Místo funkce filtrování na základě skupin použijte jednu z metod popsaných v tématu [Konfigurace filtrování](how-to-connect-sync-configure-filtering.md).

### <a name="optional-features"></a>Volitelné funkce
Na další stránce můžete vybrat volitelné funkce pro váš scénář.

>[!WARNING]
>Azure AD Connect verze 1.0.8641.0 a starší spoléhají na Azure Access Control Service pro zpětný zápis hesla.  Tato služba byla vyřazena 7. listopadu 2018.  Pokud používáte některou z těchto verzí Azure AD Connect a povolíte zpětný zápis hesla, můžou uživatelé ztratit možnost změnit nebo resetovat hesla, když je služba vyřazená. Tyto verze Azure AD Connect nepodporují zpětný zápis hesla.
>
>Další informace najdete v tématu [migrace z Azure Access Control Service](../azuread-dev/active-directory-acs-migration.md).
>
>Pokud chcete použít zpětný zápis hesla, Stáhněte si [nejnovější verzi Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

![Snímek obrazovky znázorňující stránku volitelné funkce](./media/how-to-connect-install-custom/optional2a.png)

> [!WARNING]
> Pokud jsou aktivní Azure AD Sync nebo Přímá synchronizace (DirSync), neaktivujte v Azure AD Connect žádné funkce zpětného zápisu.



| Volitelné funkce | Description |
| --- | --- |
| Hybridní nasazení Exchange |Funkce hybridního nasazení systému Exchange umožňuje souběžnou existenci poštovních schránek Exchange místních i v Microsoft 365. Azure AD Connect synchronizuje konkrétní sadu [atributů](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) z Azure AD zpátky do místního adresáře. |
| Veřejné složky pošty Exchange | Funkce veřejné složky pošty Exchange umožňuje synchronizovat objekty veřejné složky s povolenými e-maily z místní instance služby Active Directory do Azure AD. |
| Filtrování aplikací a atributů Azure AD |Povolením filtrování aplikací a atributů Azure AD můžete přizpůsobit sadu synchronizovaných atributů. Tato možnost rozšíří průvodce o další dvě stránky konfigurace. Další informace najdete v tématu [Filtrování aplikací a atributů Azure AD](#azure-ad-app-and-attribute-filtering). |
| Synchronizace hodnot hash hesel |Pokud jste jako řešení pro přihlášení vybrali možnost federace, můžete povolit synchronizaci hodnot hash hesel. Pak ji můžete použít jako možnost zálohování.  </br></br>Pokud jste vybrali předávací ověřování, můžete tuto možnost povolit, aby se zajistila podpora pro starší klienty a poskytování zálohy.</br></br> Další informace najdete v tématu [synchronizace hodnot hash hesel](how-to-connect-password-hash-synchronization.md).|
| Zpětný zápis hesla |Pomocí této možnosti zajistíte, že změny hesel, které pocházejí z Azure AD, se zapisují zpátky do místního adresáře. Další informace najdete v tématu [Začínáme se správou hesel](../authentication/tutorial-enable-sspr.md). |
| Zpětný zápis skupin |Pokud používáte Microsoft 365 skupiny, můžete reprezentovat skupiny v místní instanci služby Active Directory. Tato možnost je dostupná jenom v případě, že máte Exchange v místní instanci Active Directory. Další informace najdete v tématu [zpětný zápis skupin Azure AD Connect](how-to-connect-group-writeback.md).|
| Zpětný zápis zařízení |V případě scénářů podmíněného přístupu použijte tuto možnost k zápisu objektů zařízení ve službě Azure AD do místní instance služby Active Directory. Další informace najdete v tématu [Povolení zpětného zápisu zařízení v Azure AD Connect](how-to-connect-device-writeback.md). |
| Synchronizace atributů rozšíření adresáře |Tuto možnost vyberte, pokud chcete synchronizovat zadané atributy s Azure AD. Další informace najdete v tématu [Rozšíření adresáře](how-to-connect-sync-feature-directory-extensions.md). |

### <a name="azure-ad-app-and-attribute-filtering"></a>Filtrování aplikací a atributů Azure AD
Pokud chcete omezit, které atributy se mají synchronizovat do Azure AD, začněte tím, že vyberete služby, které používáte. Pokud změníte výběry na této stránce, musíte explicitně vybrat novou službu tak, že znovu spustíte Průvodce instalací.

![Snímek obrazovky s volitelnými funkcemi Azure A D pro aplikace](./media/how-to-connect-install-custom/azureadapps2.png)

Na základě služeb, které jste vybrali v předchozím kroku, na této stránce se zobrazí všechny atributy, které jsou synchronizované. Tento seznam je kombinací všech typů objektů, které se synchronizují. Pokud potřebujete, aby některé atributy zůstaly nesynchronizované, můžete výběr z těchto atributů vymazat.

![Snímek obrazovky s volitelnými funkcemi atributů Azure A D](./media/how-to-connect-install-custom/azureadattributes2.png)

> [!WARNING]
> Odebrání atributů může mít vliv na funkčnost. Doporučené postupy a doporučení najdete v tématu [atributy, které se mají synchronizovat](reference-connect-sync-attributes-synchronized.md#attributes-to-synchronize).
>

### <a name="directory-extension-attribute-sync"></a>Synchronizace atributů rozšíření adresáře
Schéma v Azure AD můžete roztáhnout pomocí vlastních atributů, které vaše organizace přidala nebo pomocí dalších atributů ve službě Active Directory. Chcete-li použít tuto funkci, na stránce **volitelné funkce** vyberte možnost **synchronizace atributů rozšíření adresáře**. Na stránce **rozšíření adresáře** můžete vybrat další atributy, které se mají synchronizovat.

>[!NOTE]
>V poli **dostupné atributy** se rozlišují velká a malá písmena.

![Snímek obrazovky se stránkou přípony adresářů](./media/how-to-connect-install-custom/extension2.png)

Další informace najdete v tématu [Rozšíření adresáře](how-to-connect-sync-feature-directory-extensions.md).

### <a name="enabling-single-sign-on"></a>Povolení jednotného přihlašování
Na stránce **jednotného přihlašování** nakonfigurujete jednotné přihlašování pro použití se synchronizací hesel nebo předávacím ověřováním. Tento krok provedete jednou pro každou doménovou strukturu, která je synchronizovaná do služby Azure AD. Konfigurace zahrnuje dva kroky:

1.  Vytvořte potřebný účet počítače v místní instanci služby Active Directory.
2.  Nakonfigurujte intranetovou zónu klientských počítačů pro podporu jednotného přihlašování.

#### <a name="create-the-computer-account-in-active-directory"></a>Vytvoření účtu počítače ve službě Active Directory
Pro každou doménovou strukturu, která byla přidaná v Azure AD Connect, je nutné zadat přihlašovací údaje správce domény, aby mohl být účet počítače vytvořen v každé doménové struktuře. Přihlašovací údaje se používají jenom k vytvoření účtu. Nejsou uložené ani používané pro žádné jiné operace. Přidejte přihlašovací údaje na stránce **Povolit jednotné přihlašování** , jak ukazuje následující obrázek.

![Snímek obrazovky se stránkou povolit jednotné přihlašování Přidají se přihlašovací údaje doménové struktury.](./media/how-to-connect-install-custom/enablesso.png)

>[!NOTE]
>Můžete přeskočit doménové struktury, u kterých nechcete používat jednotné přihlašování.

#### <a name="configure-the-intranet-zone-for-client-machines"></a>Konfigurace zóny intranetu pro klientské počítače
Pokud chcete zajistit, aby se klient automaticky přihlásí v zóně intranetu, ujistěte se, že je adresa URL součástí zóny intranetu. Tento krok zajistí, že počítač připojený k doméně při připojení k podnikové síti automaticky odešle lístek Kerberos do služby Azure AD.

V počítači, který má nástroje pro správu Zásady skupiny:

1.  Otevřete Zásady skupiny nástroje pro správu.
2.  Upravte zásady skupiny, které se použijí pro všechny uživatele. Například výchozí zásada domény.
3.  Přejít na **konfiguraci uživatele**  >  **šablony pro správu**  >  **součásti systému Windows**  >    >  Stránka zabezpečení **ovládacích panelů Internetu** aplikace Internet Explorer  >  . Pak vyberte **seznam přiřazení lokality k zóně**.
4.  Povolte tuto zásadu. Potom v dialogovém okně zadejte název `https://autologon.microsoftazuread-sso.com` a hodnotu hodnoty `1` . Instalační program by měl vypadat jako na následujícím obrázku.
  
    ![Snímek obrazovky zobrazující zóny intranetu](./media/how-to-connect-install-custom/sitezone.png)

6.  Dvakrát vyberte **OK** .

## <a name="configuring-federation-with-ad-fs"></a>Konfigurace federace se službou AD FS
AD FS můžete nakonfigurovat pomocí Azure AD Connect jenom několika kliknutími. Než začnete, budete potřebovat:

* Windows Server 2012 R2 nebo novější pro federační server. Měla by být povolená Vzdálená správa.
* Windows Server 2012 R2 nebo novější pro server služby Proxy webových aplikací. Měla by být povolená Vzdálená správa.
* Certifikát TLS/SSL pro název služby FS (Federation Service), který chcete použít (například sts.contoso.com).

>[!NOTE]
>Certifikát TLS/SSL pro AD FSovou farmu můžete aktualizovat pomocí Azure AD Connect, a to i v případě, že ji nepoužíváte ke správě důvěryhodnosti federace.

### <a name="ad-fs-configuration-prerequisites"></a>Požadavky na konfiguraci AD FS
Pokud chcete AD FS farmu nakonfigurovat pomocí Azure AD Connect, ujistěte se, že je na vzdálených serverech povolená služba WinRM. Ujistěte se, že jste dokončili ostatní úkoly v rámci [federačních požadavků](how-to-connect-install-prerequisites.md#prerequisites-for-federation-installation-and-configuration). Také se ujistěte, že dodržujete požadavky portů, které jsou uvedeny v tabulce [servery Azure AD Connect a federace/WAP](reference-connect-ports.md#table-3---azure-ad-connect-and-ad-fs-federation-serverswap) .

### <a name="create-a-new-ad-fs-farm-or-use-an-existing-ad-fs-farm"></a>Vytvoření nové farmy služby AD FS nebo použití existující farmy služby AD FS
Můžete použít existující AD FS farmu nebo vytvořit novou. Pokud se rozhodnete vytvořit nový, musíte zadat certifikát TLS/SSL. Pokud je certifikát TLS/SSL chráněný heslem, budete vyzváni k zadání hesla.

![Snímek obrazovky zobrazující stránku A D F S farmou](./media/how-to-connect-install-custom/adfs1.png)

Pokud se rozhodnete použít existující AD FSovou farmu, uvidíte stránku, kde můžete nakonfigurovat vztah důvěryhodnosti mezi AD FS a Azure AD.

>[!NOTE]
>Pomocí Azure AD Connect můžete spravovat jenom jednu farmu AD FS. Pokud máte existující vztah důvěryhodnosti federace, kde je služba Azure AD nakonfigurovaná na vybrané AD FS farmě, Azure AD Connect znovu vytvoří vztah důvěryhodnosti od začátku.

### <a name="specify-the-ad-fs-servers"></a>Zadání serverů služby AD FS
Zadejte servery, na které chcete nainstalovat AD FS. V závislosti na potřebách kapacity můžete přidat jeden nebo více serverů. Před nastavením této konfigurace připojte všechny AD FS servery ke službě Active Directory. Tento krok není vyžadován pro proxy servery webových aplikací. 

Společnost Microsoft doporučuje instalaci jednoho serveru služby AD FS pro zkušební a pilotní nasazení. Po počáteční konfiguraci můžete přidat a nasadit další servery, aby splňovaly požadavky na škálování, a to spuštěním Azure AD Connect znovu.

> [!NOTE]
> Před nastavením této konfigurace zajistěte, aby byly všechny servery připojené k doméně služby Azure AD.
>


![Snímek obrazovky zobrazující stránku federační servery](./media/how-to-connect-install-custom/adfs2.png)

### <a name="specify-the-web-application-proxy-servers"></a>Zadání proxy serverů webových aplikací
Zadejte proxy servery webových aplikací. Server služby Proxy webových aplikací je nasazený v hraniční síti, který je přístupný pro extranet. Podporuje žádosti o ověření z extranetu. V závislosti na potřebách kapacity můžete přidat jeden nebo více serverů. 

Microsoft doporučuje nainstalovat jeden proxy server webové aplikace pro testovací a pilotní nasazení. Po počáteční konfiguraci můžete přidat a nasadit další servery, aby splňovaly požadavky na škálování, a to spuštěním Azure AD Connect znovu. Doporučujeme, abyste měli stejný počet proxy serverů, aby bylo možné vyhovět ověřování z intranetu.

> [!NOTE]
> - Pokud účet, který používáte, není místní správce na proxy serverech webových aplikací, zobrazí se výzva k zadání přihlašovacích údajů správce.
> - Než začnete zadávat proxy servery webových aplikací, zajistěte, aby mezi Azure AD Connect serverem a proxy serverem webové aplikace bylo připojení HTTP/HTTPS.
> - Zajistěte, aby bylo připojení HTTP/HTTPS mezi serverem webové aplikace a serverem AD FS, aby bylo možné do toku požadavků na ověření.
>


![Snímek obrazovky se stránkou proxy servery webových aplikací](./media/how-to-connect-install-custom/adfs3.png)

Zobrazí se výzva k zadání přihlašovacích údajů, aby server webové aplikace mohl vytvořit zabezpečené připojení k AD FS serveru. Tyto přihlašovací údaje musí být pro účet místního správce na serveru AD FS.

![Snímek obrazovky zobrazující stránku pověření Přihlašovací údaje správce se zadávají do pole uživatelské jméno a heslo.](./media/how-to-connect-install-custom/adfs4.png)

### <a name="specify-the-service-account-for-the-ad-fs-service"></a>Zadání účtu služby AD FS
Služba AD FS vyžaduje účet doménové služby pro ověřování uživatelů a vyhledávání informací o uživateli ve službě Active Directory. Podporuje dva typy účtů služeb:

* **Skupinový účet spravované služby**: Tento typ účtu byl představený služba AD DS Windows Serverem 2012. Tento typ účtu poskytuje služby, jako je například AD FS. Je to jediný účet, ve kterém nemusíte pravidelně aktualizovat heslo. Tuto možnost použijte, pokud se řadiče domény systému Windows Server 2012 už nacházejí v doméně, do které patří server služby AD FS.
* **Uživatelský účet domény**: Tento typ účtu vyžaduje, abyste zadali heslo a pravidelně ho aktualizovali, až vyprší jeho platnost. Tuto možnost použijte pouze v případě, že řadiče domény se systémem Windows Server 2012 nejsou v doméně, do které patří server AD FS.

Pokud jste vybrali možnost **vytvořit skupinový účet spravované služby** a tato funkce se ve službě Active Directory nikdy nepoužila, zadejte svoje přihlašovací údaje podnikového správce. Tyto přihlašovací údaje slouží k inicializaci úložiště klíčů a povolení této funkce ve službě Active Directory.

> [!NOTE]
> Azure AD Connect kontroluje, zda je služba AD FS v doméně již registrována jako hlavní název služby (SPN).  Azure služba AD DS nepovoluje registraci duplicitních názvů SPN současně.  Pokud se najde duplicitní hlavní název služby (SPN), nemůžete pokračovat, dokud se hlavní název služby neodebere.

![Snímek obrazovky zobrazující stránku účet služby A D F S](./media/how-to-connect-install-custom/adfs5.png)

### <a name="select-the-azure-ad-domain-that-you-want-to-federate"></a>Vyberte doménu služby Azure AD, kterou chcete federovat.
Pomocí stránky **doména Azure AD** můžete nastavit vztah federace mezi AD FS a Azure AD. Tady můžete nakonfigurovat AD FS pro poskytování tokenů zabezpečení pro Azure AD. Můžete taky nakonfigurovat, aby služba Azure AD důvěřovala tokenům z této instance AD FS. 

Na této stránce můžete při počáteční instalaci konfigurovat jenom jednu doménu. Později můžete znovu spustit Azure AD Connect a nakonfigurovat další domény.

![Snímek obrazovky zobrazující stránku Azure A D Domain](./media/how-to-connect-install-custom/adfs6.png)

### <a name="verify-the-azure-ad-domain-selected-for-federation"></a>Výběr domény Azure AD vybrané k federaci
Když vyberete doménu, kterou chcete federovat, Azure AD Connect poskytne informace, které můžete použít k ověření neověřené domény. Další informace najdete v tématu [Přidání a ověření domény](../fundamentals/add-custom-domain.md).

![Snímek obrazovky se stránkou "Azure A D Domain", včetně informací, které můžete použít k ověření domény.](./media/how-to-connect-install-custom/verifyfeddomain.png)

> [!NOTE]
> Azure AD Connect se pokusí ověřit doménu během fáze konfigurace. Pokud nepřidáte potřebné záznamy DNS (Domain Name System), konfiguraci nelze dokončit.
>


## <a name="configuring-federation-with-pingfederate"></a>Konfigurace federace s PingFederate
PingFederate můžete nakonfigurovat pomocí Azure AD Connect jenom několika kliknutími. Vyžadují se tyto požadavky:
- PingFederate 8,4 nebo novější.  Další informace najdete v tématu [PingFederate Integration with Azure Active Directory and Microsoft 365](https://docs.pingidentity.com/bundle/O365IG20_sm_integrationGuide/page/O365IG_c_integrationGuide.html).
- Certifikát TLS/SSL pro název služby FS (Federation Service), který chcete použít (například sts.contoso.com).

### <a name="verify-the-domain"></a>Ověření domény
Až se rozhodnete nastavit federaci pomocí PingFederate, budete požádáni o ověření domény, kterou chcete federovat.  V rozevírací nabídce vyberte doménu.

![Snímek obrazovky zobrazující stránku Azure A D Domain Je vybrána ukázková doména "contoso.com".](./media/how-to-connect-install-custom/ping1.png)

### <a name="export-the-pingfederate-settings"></a>Export nastavení PingFederate


Nakonfigurujte PingFederate jako federační server pro každou federované doménu Azure.  Vyberte **Nastavení exportu** a sdílejte tyto informace s vaším správcem PingFederate.  Správce federačního serveru aktualizuje konfiguraci a potom poskytne adresu URL a číslo portu serveru PingFederate, aby Azure AD Connect mohl ověřit nastavení metadat.  

![Snímek obrazovky se stránkou nastavení PingFederate V horní části stránky se zobrazí tlačítko Exportovat nastavení.](./media/how-to-connect-install-custom/ping2.png)

Případné problémy s ověřením řešte se správcem PingFederate.  Následující obrázek ukazuje informace o serveru PingFederate, který nemá žádný platný vztah důvěryhodnosti s Azure.

![Snímek obrazovky zobrazující informace o serveru: Server PingFederate byl nalezen, ale připojení poskytovatele služeb pro Azure chybí nebo je zakázáno.](./media/how-to-connect-install-custom/ping5.png)




### <a name="verify-federation-connectivity"></a>Ověření připojení federace
Azure AD Connect se pokusí ověřit koncové body ověřování, které načte z metadat PingFederate v předchozím kroku.  Azure AD Connect se nejdřív pokusí tyto koncové body vyřešit pomocí místních serverů DNS.  V dalším kroku se pokusí tyto koncové body přeložit pomocí externího poskytovatele DNS.  Případné problémy s ověřením řešte se správcem PingFederate.  

![Snímek obrazovky zobrazující stránku ověřit připojení](./media/how-to-connect-install-custom/ping3.png)

### <a name="verify-federation-sign-in"></a>Ověřit přihlášení federace
Nakonec můžete ověřit nově nakonfigurovaný tok přihlášení federace tím, že se přihlásíte k federované doméně. Pokud se vaše přihlášení zdaří, je federace s PingFederate úspěšně nakonfigurovaná.

![Snímek obrazovky zobrazující stránku "ověřit federované přihlášení". Zpráva v dolní části indikuje úspěšné přihlášení.](./media/how-to-connect-install-custom/ping4.png)

## <a name="configure-and-verify-pages"></a>Konfigurace a ověření stránek
Konfigurace proběhne na stránce **Konfigurace** .

> [!NOTE]
> Pokud jste nakonfigurovali federaci, ujistěte se, že jste před pokračováním v instalaci nakonfigurovali taky [Překlad adres IP pro federační servery](how-to-connect-install-prerequisites.md#name-resolution-for-federation-servers) .
>



![Snímek obrazovky se stránkou připravenou ke konfiguraci](./media/how-to-connect-install-custom/readytoconfigure2.png)

### <a name="use-staging-mode"></a>Použít pracovní režim
Je možné nastavit nový synchronizační Server paralelně s pracovním režimem. Pokud chcete použít tuto instalaci, může exportovat jenom jeden synchronizační Server do jednoho adresáře v cloudu. Pokud ale chcete přejít z jiného serveru, například na server se systémem DirSync, můžete povolit Azure AD Connect v pracovním režimu. 

Když povolíte pracovní nastavení, synchronizační modul importuje a synchronizuje data jako normální. Ale neexportuje žádná data do služby Azure AD nebo Active Directory. Funkce synchronizace hesel a zpětný zápis hesla jsou v pracovním režimu zakázané.

![Snímek obrazovky s možností "Povolit pracovní režim"](./media/how-to-connect-install-custom/stagingmode.png)

V pracovním režimu můžete provést požadované změny synchronizačního modulu a zkontrolovat, co bude exportováno. Když jste s konfigurací spokojeni, znovu spusťte průvodce instalací a vypněte pracovní režim. 

Data se teď exportují do služby Azure AD ze serveru. Nezapomeňte současně zakázat druhý server tak, aby pouze jeden server prováděl aktivní export.

Další informace najdete v tématu [Pracovní režim](how-to-connect-sync-staging-server.md).

### <a name="verify-your-federation-configuration"></a>Ověření konfigurace federace
Azure AD Connect ověří nastavení DNS, když vyberete tlačítko **ověřit** . Kontroluje následující nastavení:

* **Připojení k intranetu**
    * Přeložit plně kvalifikovaný název domény federace: Azure AD Connect ověří, jestli může DNS přeložit plně kvalifikovaný název domény federace, aby se zajistilo připojení. Pokud Azure AD Connect nedokáže přeložit plně kvalifikovaný název domény, ověření se nepovede. K dokončení ověření zajistěte, aby byl pro plně kvalifikovaný název domény federační služby k dispozici záznam DNS.
    * Záznam DNS A: Azure AD Connect ověří, jestli vaše federační služba obsahuje záznam A. V případě absence záznamu A se ověření nepovede. K dokončení ověření vytvořte záznam A (nikoli záznam CNAME) pro váš plně kvalifikovaný název domény federace.
* **Připojení extranetu**
    * Přeložit plně kvalifikovaný název domény federace: Azure AD Connect ověří, jestli může DNS přeložit plně kvalifikovaný název domény federace, aby se zajistilo připojení.

      ![Snímek obrazovky zobrazující stránku instalace byla dokončena.](./media/how-to-connect-install-custom/completed.png)

      ![Snímek obrazovky zobrazující stránku instalace byla dokončena. Zpráva znamená, že konfigurace intranetu byla ověřena.](./media/how-to-connect-install-custom/adfs7.png)

Chcete-li ověřit komplexní ověřování, proveďte ruční provedení jednoho nebo více následujících testů:

* Po dokončení synchronizace v Azure AD Connect použijte k ověření ověřování pro místní uživatelský účet, který jste zvolili, další úkol ověřit navýšení **federovaného přihlášení** .
* Z počítače připojeného k doméně v intranetu se ujistěte, že se můžete přihlásit z prohlížeče. Připojte se k https://myapps.microsoft.com . Pak pomocí přihlášeného účtu ověřte přihlášení. Předdefinovaný účet správce Azure služba AD DS není synchronizovaný a nemůžete ho použít k ověření.
* Ujistěte se, že se můžete přihlásit ze zařízení v extranetu. V domácím počítači nebo mobilním zařízení se připojte k https://myapps.microsoft.com . Pak zadejte svoje přihlašovací údaje.
* Ověřte přihlášení plně funkčního klienta. Připojte se k https://testconnectivity.microsoft.com . Pak vyberte možnost **Microsoft 365**  >  **Office 365 Single Sign-On test**.

## <a name="troubleshoot"></a>Řešení potíží
Tato část obsahuje informace o řešení potíží, které můžete použít, pokud máte problém při instalaci Azure AD Connect.

Při přizpůsobování instalace Azure AD Connect můžete na stránce **instalace požadovaných součástí** vybrat možnost **použít existující SQL Server**. Může se zobrazit následující chyba: "databáze ADSync již obsahuje data a nelze ji přepsat. Odeberte prosím stávající databázi a zkuste to znovu. "

![Snímek obrazovky zobrazující stránku nainstalovat požadované součásti V dolní části stránky se zobrazí chyba.](./media/how-to-connect-install-custom/error1.png)

Tato chyba se zobrazí, protože databáze s názvem *AdSync* již existuje v instanci SQL SQL Server, kterou jste zadali.

Tato chyba se obvykle zobrazí po odinstalování Azure AD Connect.  Databáze není odstraněna z počítače, který je spuštěn SQL Server při odinstalaci Azure AD Connect.

Chcete-li tento problém vyřešit:

1. Ověřte databázi ADSync, kterou Azure AD Connect použít před odinstalací. Ujistěte se, že databáze již není používána.

2. Zazálohujte databázi.

3. Odstranit databázi:
    1. Pro připojení k instanci SQL použijte **Microsoft SQL Server Management Studio** . 
    1. Vyhledejte databázi **AdSync** a klikněte na ni pravým tlačítkem.
    1. V místní nabídce vyberte **Odstranit**.
    1. Vyberte **OK** , aby se databáze odstranila.

![Snímek obrazovky zobrazující Microsoft SQL Server Management Studio Je vybrána synchronizace D.](./media/how-to-connect-install-custom/error2.png)

Po odstranění databáze ADSync vyberte **nainstalovat** a instalaci opakujte.

## <a name="next-steps"></a>Další kroky
Po dokončení instalace se odhlaste z Windows. Pak se znovu přihlaste, než použijete Synchronization Service Manager nebo editor pravidel synchronizace.

Teď, když máte nainstalovanou Azure AD Connect, můžete [ověřit instalaci a přiřadit licence](how-to-connect-post-installation.md).

Další informace o funkcích, které jste povolili během instalace, najdete v tématu [prevence náhodného odstranění](how-to-connect-sync-feature-prevent-accidental-deletes.md) a [Azure AD Connect Health](how-to-connect-health-sync.md).

Další informace o dalších běžných tématech najdete v tématu [Azure AD Connect Sync: Scheduler](how-to-connect-sync-feature-scheduler.md) a [Integrujte své místní identity s Azure AD](whatis-hybrid-identity.md).
