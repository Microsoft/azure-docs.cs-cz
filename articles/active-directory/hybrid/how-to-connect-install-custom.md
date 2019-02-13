---
title: 'Azure AD Connect: Vlastní instalace | Dokumentace Microsoftu'
description: Tento dokument podrobně popisuje možnosti vlastní instalace Azure AD Connect. Použijte tyto pokyny, pokud chcete nainstalovat službu Active Directory přes Azure AD Connect.
services: active-directory
keywords: co je Azure AD Connect, instalace služby Active Directory, požadované součásti služby Azure AD
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 6d42fb79-d9cf-48da-8445-f482c4c536af
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/04/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91dd25eadd3842cf1a94608a6f0ad9cfcd25039a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56197228"
---
# <a name="custom-installation-of-azure-ad-connect"></a>Vlastní instalace služby Azure AD Connect
**Vlastní nastavení** Azure AD Connect se používá, pokud chcete využít další možnosti instalace. Používá se, pokud máte víc doménových struktur, nebo pokud chcete nakonfigurovat volitelné funkce, které nejsou zahrnuty v rychlé instalaci. Používá se ve všech případech, kde možnost [**rychlá instalace**](how-to-connect-install-express.md) nevyhovuje nasazení nebo topologii.

Před zahájením instalace Azure AD Connect, ujistěte se, že k [stáhnout Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) a provést požadovanou přípravu popsanou kroků [Azure AD Connect: Hardware a nezbytné předpoklady](how-to-connect-install-prerequisites.md). Taky se ujistěte, jestli máte požadované účty, které jsou popsané v tématu [Účty a oprávnění Azure AD Connect](reference-connect-accounts-permissions.md).

Pokud vlastní nastavení neodpovídá vaší topologii, třeba když chcete upgradovat DirSync, přečtěte si téma související dokumentace pro další scénáře.

## <a name="custom-settings-installation-of-azure-ad-connect"></a>Instalace Azure AD Connect s vlastním nastavením
### <a name="express-settings"></a>Expresní nastavení
Na této stránce kliknutím na **Přizpůsobit** spustíte instalaci s vlastním nastavením.

### <a name="install-required-components"></a>Instalace požadovaných součástí
Při instalaci služeb synchronizace můžete nechat volitelnou konfiguraci nezaškrtnutou a Azure AD Connect nastaví všechno automaticky. Nastaví instanci SQL Server 2012 Express LocalDB, vytvoří příslušné skupiny a přiřadí oprávnění. Pokud chcete změnit výchozí nastavení, následující tabulka vás seznámí s volitelnými možnostmi konfigurace, které jsou dostupné.

![Požadované součásti](./media/how-to-connect-install-custom/requiredcomponents.png)

| Volitelná konfigurace | Popis |
| --- | --- |
| Použít existující server SQL Server |Umožňuje zadat název serveru SQL Server a název instance. Tuto možnost zvolte, pokud už máte databázový server, který chcete použít. Pokud SQL Server nemá povoleno procházení, zadejte do položky **Název instance** požadovaný název instance, za nímž následuje čárka a číslo portu. |
| Použít existující účet služby |Ve výchozím nastavení použije Azure AD Connect virtuální účet služby, který můžou služby synchronizace používat. Pokud používáte vzdálený server SQL nebo používáte proxy server vyžadující ověření, potřebujete mít **účet spravované služby** nebo účet služby v dané doméně a znát heslo. V těchto případech zadejte účet, který chcete použít. Ujistěte se, jestli uživatel, který provádí instalaci, je SA v SQL, aby bylo možné vytvořit přihlašovací jméno pro účet služby.  Viz téma [Účty a oprávnění Azure AD Connect](reference-connect-accounts-permissions.md#adsync-service-account). </br></br>S nejnovějším sestavením teď může databáze vzdáleně zřizovat správce SQL a pak je instalovat správce služby Azure AD Connect s oprávněními vlastníka databáze.  Další informace najdete v tématu [Instalace služby Azure AD Connect pomocí oprávnění delegovaného správce SQL](how-to-connect-install-sql-delegation.md).|
| Zadat vlastní skupiny pro synchronizaci |Ve výchozím nastavení vytvoří Azure AD Connect při instalaci služeb synchronizace čtyři skupiny, které jsou místní pro server. Tyto skupiny jsou: Skupiny Administrators, skupina Operators, procházet skupiny a skupina Password Reset. Tady můžete zadat vlastní skupiny. Skupiny musí být místní na serveru a nemůžou být umístěny v doméně. |

### <a name="user-sign-in"></a>Přihlášení uživatele
Po instalaci požadovaných součástí budete vyzváni, abyste vybrali metodu jednotného přihlašování uživatelů. Následující tabulka obsahuje stručný popis dostupných možností. Úplný popis metod přihlášení najdete v tématu [Přihlášení uživatele](plan-connect-user-signin.md).

![Přihlášení uživatele](./media/how-to-connect-install-custom/usersignin4.png)

| Možnost jednotného přihlašování | Popis |
| --- | --- |
| Synchronizace hodnoty hash hesel |Uživatelé se můžou přihlašovat ke cloudovým službám Microsoft, například Office 365, stejným heslem jako v místní síti. Hesla uživatelů se synchronizují do Azure AD, protože ověření a hash hesla probíhá v cloudu. Další informace najdete v tématu [Synchronizace hodnoty hash hesel](how-to-connect-password-hash-synchronization.md). |
|Předávací ověřování|Uživatelé se můžou přihlašovat ke cloudovým službám Microsoft, například Office 365, stejným heslem jako v místní síti.  Heslo uživatele se předává k ověření do místního kontroleru domény Active Directory.
| Federace se službou AD FS |Uživatelé se můžou přihlašovat ke cloudovým službám Microsoft, například Office 365, stejným heslem jako v místní síti.  Uživatelé jsou k přihlášení přesměrováni do místní instance služby AD FS a ověření probíhá místně. |
| Federace s PingFederate|Uživatelé se můžou přihlašovat ke cloudovým službám Microsoft, například Office 365, stejným heslem jako v místní síti.  Uživatelé se k přihlášení přesměrují do místní instance PingFederate a ověření proběhne místně. |
| Nekonfigurovat |Nenainstaluje a nenakonfiguruje se žádná funkce přihlašování uživatelů. Tuto možnost zvolte, pokud už využíváte federační server třetí strany nebo jiné existující řešení. |
|Povolit jednotné přihlašování|Tato možnost je dostupná při synchronizaci hodnot hash hesel i předávacím ověřování a poskytuje jednotné přihlašovací prostředí pro uživatele stolních počítačů v podnikové síti. Další informace najdete v tématu [Jednotné přihlašování](how-to-connect-sso.md). </br>Poznámka: Pro zákazníky služby AD FS není tato možnost dostupná, protože AD FS už nabízí stejnou úroveň jednotného přihlašování.</br>

### <a name="connect-to-azure-ad"></a>Připojení k Azure AD
Na obrazovce Připojení k Azure AD zadejte účet a heslo globálního správce. Pokud jste na předchozí stránce vybrali **Federace se službou AD FS**, nepřihlašujte se pomocí účtu v doméně, kterou plánujete povolit pro federaci. Doporučuje se použít účet ve výchozí doméně **onmicrosoft.com**, která je součástí tenanta služby Azure AD.

Tento účet slouží jenom k vytvoření účtu služby v Azure AD, a po dokončení průvodce se už nepoužívá.  
![Přihlášení uživatele](./media/how-to-connect-install-custom/connectaad.png)

Pokud má účet globálního správce povolené ověřování MFA, bude nutné znovu zadat heslo v automaticky otevřeném okně pro přihlášení a provést ověřovací test MFA. Ověřovacím testem může být zadání ověřovacího kódu nebo telefonní hovor.  
![Přihlášení uživatele s MFA](./media/how-to-connect-install-custom/connectaadmfa.png)

Účet globálního správce může taky mít povolenou technologii [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

Pokud se zobrazí chyba a máte problémy s připojením, přečtěte si téma [Řešení problémů s připojením](tshoot-connect-connectivity.md).

## <a name="pages-under-the-sync-section"></a>Stránky v části Synchronizace

### <a name="connect-your-directories"></a>Připojení adresářů
Azure AD Connect pro připojení ke službě Active Directory Domain Services potřebuje název doménové struktury a přihlašovací údaje účtu s dostatečnými oprávněními.

![Připojení adresáře](./media/how-to-connect-install-custom/connectdir01.png)

Po vytvoření názvu doménové struktury a kliknutí na **Přidat adresář** se otevře vyskakovací dialogové okno s výzvou k výběru následujících možností:

| Možnost | Popis |
| --- | --- |
| Vytvořit nový účet | Tuto možnost vyberte, pokud chcete, aby průvodce Azure AD Connect vytvořil účet AD DS vyžadovaný pro připojení Azure AD Connect k doménové struktuře AD během synchronizace adresáře. Když je tato možnost vybrána, zadejte uživatelské jméno a heslo účtu podnikového správce. Zadaný účet podnikového správce použije průvodce Azure AD Connect k vytvoření požadovaného účtu AD DS. Součást domény můžete zadat buď ve formátu NetBios, nebo jako plně kvalifikovaný název domény, tj. FABRIKAM\administrator nebo fabrikam.com\administrator. |
| Použít existující účet | Tuto možnost vyberte, pokud chcete, aby Azure AD Connect pro připojení k doménové struktuře AD během synchronizace adresáře použil existující účet AD DS. Součást domény můžete zadat buď ve formátu NetBios, nebo jako plně kvalifikovaný název domény, tj. jako FABRIKAM\syncuser nebo fabrikam.com\syncuser. Tento účet může být běžný uživatelský účet, protože potřebuje pouze výchozí oprávnění ke čtení. Je ale možné, že v závislosti na scénáři budete potřebovat větší oprávnění. Další informace najdete v tématu [Účty a oprávnění v Azure AD Connect](reference-connect-accounts-permissions.md##create-the-ad-ds-connector-account). |

![Připojení adresáře](./media/how-to-connect-install-custom/connectdir02.png)

### <a name="azure-ad-sign-in-configuration"></a>Konfigurace přihlášení k Azure AD
Tato stránka vám umožní zkontrolovat domény hlavního názvu uživatele (UPN), které se nacházejí v místní službě AD DS a které byly ověřeny v Azure AD. Tato stránka vám také umožní konfigurovat atribut userPrincipalName, který chcete použít.

![Neověřené domény](./media/how-to-connect-install-custom/aadsigninconfig2.png)  
Zkontrolujte všechny domény označené jako **Nepřidáno** a **Neověřeno**. Ujistěte se, že domény, které používáte, byly ověřeny v Azure AD. Po ověření domén klikněte na symbol obnovení. Další informace najdete v tématu [přidání a ověření domény](../active-directory-domains-add-azure-portal.md)

**UserPrincipalName** – Atribut userPrincipalName je atribut, který uživatelé používají při přihlášení k Azure AD a Office 365. Použité domény, označované také jako přípona UPN, je nutné před synchronizací uživatelů ověřit ve službě Azure AD. Společnost Microsoft doporučuje ponechat výchozí atribut userPrincipalName. Pokud tento atribut není směrovatelný a nedá se ověřit, je možné vybrat jiný atribut. Jako atribut, který uchovává přihlašovací ID, můžete například vybrat e-mail. Použití jiného atributu než userPrincipalName se nazývá **Alternativní ID**. Hodnota atributu Alternativní ID se musí řídit standardem RFC822. Alternativní ID lze použít při synchronizaci hodnot hash hesel, předávacím ověřování a federaci. Tento atribut nesmí být v Active Directory definovaný jako atribut s více hodnotami, a to ani když obsahuje pouze jednu hodnotu.

>[!NOTE]
> Když povolíte předávací ověřování, musíte mít alespoň jednu ověřenou doménu, abyste mohli pokračovat v průvodci.

> [!WARNING]
> Použití atributu Alternativní ID není kompatibilní se všemi úlohami Office 365. Další informace najdete v tématu [Konfigurace alternativního přihlašovacího ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).
>
>

### <a name="domain-and-ou-filtering"></a>Filtrování domén a organizačních jednotek
Ve výchozím nastavení se synchronizují všechny domény a organizační jednotky. Pokud některé domény nebo organizační jednotky nechcete synchronizovat do Azure AD, můžete zrušit výběr těchto domén a organizačních jednotek.  
![Filtrování organizačních jednotek domén](./media/how-to-connect-install-custom/domainoufiltering.png)  
Tato stránka průvodce konfiguruje filtrování podle domén a organizačních jednotek. Pokud plánujete změny, před jejich provedením si přečtěte témata [filtrování podel domén](how-to-connect-sync-configure-filtering.md#domain-based-filtering) a [filtrování podle organizačních jednotek](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Některé organizační jednotky jsou zásadní pro funkčnost a měly by být vybrány.

Pokud použijete filtrování podle organizačních jednotek v Azure AD Connect verze starší než 1.1.524.0, nové organizační jednotky přidané později se budou synchronizovat automaticky. Pokud chcete, aby se nové organizační jednotky nesynchronizovaly, můžete je po dokončení průvodce nakonfigurovat pomocí [filtrování podle organizačních jednotek](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). V Azure AD Connect verze 1.1.524.0 nebo novější můžete určit, jestli se mají nové organizační jednotky synchronizovat, nebo ne.

Pokud chcete použít [filtrování podle skupin](#sync-filtering-based-on-groups), zajistěte, aby byly organizační jednotky zahrnuté ve skupinách a aby se nepoužívalo filtrování podle organizačních jednotek. Filtrování podle organizačních jednotek se vyhodnocuje dřív než filtrování podle skupin.

Je také možné, že některé domény nejsou dostupné kvůli omezení brány firewall. Tyto domény nejsou ve výchozím nastavení vybrané a je u nich zobrazené upozornění.  
![Nedostupné domény](./media/how-to-connect-install-custom/unreachable.png)  
Pokud se zobrazí toto upozornění, ujistěte se, jestli jsou tyto domény skutečně nedostupné a jestli je upozornění očekávané.

### <a name="uniquely-identifying-your-users"></a>Jednoznačná identifikace uživatelů

#### <a name="select-how-users-should-be-identified-in-your-on-premises-directories"></a>Vyberte způsob, jakým se mají uživatelé identifikovat v místních adresářích
Funkce párování napříč doménovými strukturami vám umožňuje definovat, jak jsou uživatelé z vašich doménových struktur AD DS reprezentováni v Azure AD. Uživatel buď může být reprezentován jenom jednou v rámci všech doménových struktur, nebo může mít kombinaci povolených a zakázaných účtů. Uživatel také může být v některých doménových strukturách reprezentován jako kontakt.

![Jedinečná](./media/how-to-connect-install-custom/unique2.png)

| Nastavení | Popis |
| --- | --- |
| [Uživatelé jsou reprezentováni jen jednou v rámci všech doménových struktur](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Všichni uživatelé jsou vytvořeni jako jednotlivé objekty v Azure AD. Tyto objekty nejsou v úložišti metaverse spojené. |
| [Atribut Mail](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Tato možnost spojí uživatele a kontakty, pokud má atribut mail v různých doménových strukturách stejnou hodnotu. Tuto možnost použijte, pokud byly kontakty vytvořeny pomocí GALSync. Pokud je tato možnost zvolená, uživatelské objekty s nevyplněným atributem Mail nebudou synchronizovány do Azure AD. |
| Atributy [ObjectSID a msExchangeMasterAccountSID/ msRTCSIP-OriginatorSid](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Tato možnost spojí povoleného uživatele v doménové struktuře účtu se zakázaným uživatelem v doménové struktuře prostředku. V systému Exchange se tato konfigurace označuje jako propojená poštovní schránka. Tato možnost se taky dá použít, pokud používáte pouze Lync a Exchange není v doménové struktuře prostředku dostupný. |
| Atributy sAMAccountName a MailNickName |Tato možnost spojuje atributy, u kterých se dá očekávat, že obsahují přihlašovací ID uživatele. |
| Konkrétní atribut |Tato možnost umožňuje vybrat vlastní atribut. Pokud je tato možnost zvolená, uživatelské objekty s nevyplněným (vybraným) atributem Mail nebudou synchronizovány do Azure AD. **Omezení:** Je nutné vybrat atribut, který se už nachází v úložišti metaverse. Pokud vyberete vlastní atribut (který není v úložišti metaverse), průvodce se nedá dokončit. |

#### <a name="select-how-users-should-be-identified-with-azure-ad---source-anchor"></a>Vyberte, jak se mají uživatelé identifikovat s Azure AD – zdrojové ukotvení
Atribut sourceAnchor je atribut, který se za dobu existence objektu uživatele nemění. Jedná se o primární klíč propojující místního uživatele s uživatelem v Azure AD.

| Nastavení | Popis |
| --- | --- |
| Nechat správu zdrojového ukotvení na Azure | Tuto možnost vyberte, pokud chcete, aby Azure AD vybral atribut za vás. Pokud vyberete tuto možnost, Průvodce Azure AD Connect použije logiku výběru atributu sourceAnchor popsanou v části článku [Azure AD Connect: Koncepty návrhu – použití ms-DS-ConsistencyGuid jako parametru sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). Jakmile se vlastní instalace dokončí, průvodce vás informuje, který atribut byl vybrán jako atribut zdrojového ukotvení. |
| Konkrétní atribut | Tuto možnost vyberte, pokud chcete jako atribut sourceAnchor zadat existující atribut AD. |

Protože atribut nejde změnit, je nutné naplánovat a použít dobrý atribut. Jednou z vhodných možností je objectGUID. Tento atribut se změní jenom tehdy, pokud se uživatelský účet přesune mezi doménovými strukturami nebo doménami. Vyhněte se atributům, které se mění, když uživatel uzavře manželství nebo se změní jeho přiřazení. Nelze použít atributy se symbolem @-sign, takže se nedá použít e-mail ani atribut userPrincipalName. V atributu se taky rozlišují velká a malá písmena, proto při přesunutí objektu mezi doménovými strukturami dejte pozor, abyste správně zachovali velká a malá písmena. Binární atributy se zakódují do formátu Base64, ale ostatní typy atributů zůstávají v nekódovaném stavu. Při federacích a v některých rozhraních Azure AD se tento atribut taky nazývá immutableID. Další informace o zdrojovém ukotvení najdete v [konceptech návrhu](plan-connect-design-concepts.md#sourceanchor).

### <a name="sync-filtering-based-on-groups"></a>Filtrování synchronizace podle skupin
Funkce filtrování podle skupin umožňuje synchronizovat pouze malou podmnožinu objektů pro pilotní nasazení. Pokud chcete tuto funkci použít, vytvořte pro tento účel skupinu v místní službě Active Directory. Jako přímé členy přidejte uživatele a skupiny, které chcete synchronizovat do Azure AD. Později můžete přidáváním uživatelů do této skupiny a jejich odebíráním spravovat seznam objektů, které mají být dostupné v Azure AD. Každý objekt, který chcete synchronizovat, musí být přímým členem skupiny. Přímými členy musí být uživatelé, skupiny, kontakty a počítače/zařízení. Členství ve vnořené skupině se nepřeloží. Když jako člena přidáte skupinu, přidá se jenom samotná skupina, a ne její členové.

![Filtrování synchronizace](./media/how-to-connect-install-custom/filter2.png)

> [!WARNING]
> Tato funkce je určená pouze k podpoře pilotního nasazení. Nepoužívejte ji v plnohodnotném produkčním nasazení.
>
>

V plnohodnotném produkčním nasazení bude obtížné spravovat jenom jednu skupinu se všemi objekty k synchronizaci. Místo toho je vhodné použít jednu z metod v [konfiguraci filtrování](how-to-connect-sync-configure-filtering.md).

### <a name="optional-features"></a>Volitelné funkce
Na této obrazovce můžete vybrat volitelné funkce pro konkrétní scénáře.

>[!WARNING]
>Azure AD Connect verze **1.0.8641.0** a starší využívá pro zpětný zápis hesla službu Azure Access Control.  Dne **7. listopadu 2018** dojde k vyřazení této služby z provozu.  Pokud používáte některou z těchto verzí služby Azure AD Connect a máte povolený zpětný zápis hesla, můžou uživatelé po vyřazení této služby z provozu přijít o možnost změny nebo resetování hesel. Zpětný zápis hesla v těchto verzích služby Azure AD Connect už se nebude podporovat.
>
>Další informace o najdete v Azure Access Control service [jak: Migrace ze služby Azure Access Control service](../develop/active-directory-acs-migration.md)
>
>Nejnovější verzi služby Azure AD Connect si můžete stáhnout po kliknutí [sem](https://www.microsoft.com/en-us/download/details.aspx?id=47594).

![Volitelné funkce](./media/how-to-connect-install-custom/optional2.png)

> [!WARNING]
> Pokud máte aktuálně aktivní nástroj DirSync nebo Azure AD Sync, neaktivujte žádnou z funkcí zpětného zápisu v Azure AD Connect.



| Volitelné funkce | Popis |
| --- | --- |
| Hybridní nasazení systému Exchange |Funkce Hybridní nasazení systému Exchange umožňuje souběžnou existenci poštovních schránek serveru Exchange jak místně, tak v Office 365. Azure AD Connect synchronizuje konkrétní sadu [atributů](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) z Azure AD zpátky do místního adresáře. |
| Veřejné složky e-mailu Exchange | Funke veřejné složky e-mailu Exchange umožňuje synchronizaci pro e-mail povolených objektů veřejných složek z místní služby Active Directory do Azure AD. |
| Filtrování aplikací a atributů Azure AD |Když zapnete filtrování aplikací a atributů Azure AD, můžete přizpůsobit sadu synchronizovaných atributů. Tato možnost rozšíří průvodce o další dvě stránky konfigurace. Další informace najdete v tématu [Filtrování aplikací a atributů Azure AD](#azure-ad-app-and-attribute-filtering). |
| Synchronizace hodnot hash hesel |Tuto možnost můžete povolit, pokud jste jako řešení přihlašování vybrali federaci. Synchronizaci hodnot hash hesel je pak možné použít jako záložní možnost. Další informace najdete v tématu [Synchronizace hodnot hash hesel](how-to-connect-password-hash-synchronization.md). </br></br>Pokud jste vybrali předávací ověřování, lze tuto možnost také povolit pro zajištění podpory starších klientů a jako záložní možnost. Další informace najdete v tématu [Synchronizace hodnot hash hesel](how-to-connect-password-hash-synchronization.md).|
| Zpětný zápis hesla |Když zapnete zpětný zápis hesla, změny hesel vzniklé ve službě Azure AD se zapíšou zpátky do místního adresáře. Další informace najdete v tématu [Začínáme se správou hesel](../authentication/quickstart-sspr.md). |
| Zpětný zápis skupin |Pokud použijete funkci **Skupiny Office 365**, tyto skupiny můžou být zastoupeny v místní službě Active Directory. Tato možnost je dostupná jenom v případě, že se v místní službě Active Directory nachází Exchange. Další informace najdete v tématu [Zpětný zápis skupin](how-to-connect-preview.md#group-writeback). |
| Zpětný zápis zařízení |Umožňuje zpětný zápis objektů zařízení ve službě Azure AD do místní služby Active Directory pro potřeby podmíněného přístupu. Další informace najdete v tématu [Povolení zpětného zápisu zařízení v Azure AD Connect](how-to-connect-device-writeback.md). |
| Synchronizace atributů rozšíření adresáře |Když povolíte synchronizaci atributů rozšíření adresáře, určené atributy se synchronizují do Azure AD. Další informace najdete v tématu [Rozšíření adresáře](how-to-connect-sync-feature-directory-extensions.md). |

### <a name="azure-ad-app-and-attribute-filtering"></a>Filtrování aplikací a atributů Azure AD
Pokud chcete omezit atributy, které se budou synchronizovat do Azure AD, začněte výběrem služeb, které používáte. Pokud na této stránce provedete změny konfigurace, je nutné znovu spustit průvodce instalací a explicitně vybrat novou službu.

![Volitelné funkce – aplikace](./media/how-to-connect-install-custom/azureadapps2.png)

Na základě služeb vybraných v předchozím kroku tato stránka zobrazuje všechny atributy, které se synchronizují. Tento seznam je kombinací všech synchronizovaných typů objektů. Pokud existují konkrétní atributy, které potřebujete nesynchronizovat, můžete zrušit výběr těchto atributů.

![Volitelné funkce – atributy](./media/how-to-connect-install-custom/azureadattributes2.png)

> [!WARNING]
> Odebrání atributů může mít vliv na funkčnost. Osvědčené postupy a doporučení najdete v tématu [synchronizované atributy](reference-connect-sync-attributes-synchronized.md#attributes-to-synchronize).
>
>

### <a name="directory-extension-attribute-sync"></a>Synchronizace atributů rozšíření adresáře
Schéma v Azure AD můžete rozšířit vlastními atributy, které přidala vaše organizace, nebo dalšími atributy ve službě Active Directory. Pokud chcete tuto funkci použít, vyberte možnost **Synchronizace atributů rozšíření adresáře** na stránce **Volitelné funkce**. Na této stránce můžete vybrat víc atributů k synchronizaci.

>[!NOTE]
>Pole Dostupné atributy rozlišuje velká a malá písmena.

![Rozšíření adresáře](./media/how-to-connect-install-custom/extension2.png)

Další informace najdete v tématu [Rozšíření adresáře](how-to-connect-sync-feature-directory-extensions.md).

### <a name="enabling-single-sign-on-sso"></a>Povolení jednotného přihlašování (SSO)
Konfigurace jednotného přihlašování pro použití se synchronizací hesel a s předávacím ověřováním je jednoduchý proces, který stačí pro každou doménovou strukturu synchronizovanou se službou AD provést pouze jednou. Konfigurace zahrnuje tyto dva kroky:

1.  Vytvořte potřebný účet počítače v místní službě Active Directory.
2.  Nakonfigurujte zónu intranetu klientského počítače pro podporu jednotného přihlašování.

#### <a name="create-the-computer-account-in-active-directory"></a>Vytvoření účtu počítače ve službě Active Directory
Pro každou doménovou strukturu přidanou v nástroji AAD Connect musíte zadat přihlašovací údaje správce domény, aby v ní bylo možné vytvořit účet počítače. Přihlašovací údaje slouží jenom k vytvoření účtu a neukládají se. Nepoužívají se ani pro žádné jiné operace. Jednoduše přidejte přihlašovací údaje v Průvodci nástrojem AAD Connect na stránce **Povolit jednotné přihlašování**, jak znázorňuje následující obrázek:

![Povolit jednotné přihlašování](./media/how-to-connect-install-custom/enablesso.png)

>[!NOTE]
>Pokud pro konkrétní doménovou strukturu nechcete použít jednotné přihlašování, můžete ji přeskočit.

#### <a name="configure-the-intranet-zone-for-client-machines"></a>Konfigurace zóny intranetu pro klientské počítače
Pokud chcete zajistit, aby se klient v zóně intranetu přihlásil automaticky, ujistěte se, že součástí zóny intranetu jsou dvě adresy URL. Tím se zajistí, že počítač připojený k doméně při připojení k podnikové síti automaticky odešle službě Azure AD lístek Kerberos.
Na počítači, který obsahuje Nástroje pro správu zásad skupiny:

1.  Spusťte Nástroje pro správu zásad skupiny.
2.  Upravte zásady skupiny, které se použijí na všechny uživatele. Například Výchozí zásady domény.
3.  Přejděte do **Konfigurace uživatele\Šablony pro správu\Komponenty Windows\Internet Explorer\Ovládací panely – Internet\Stránka zabezpečení** a vyberte **Seznam zařazení serverů do zón**, jak je znázorněné na následujícím obrázku.
4.  Povolte zásadu a do dialogového okna zadejte následující položku.

        Value: `https://autologon.microsoftazuread-sso.com`  
        Data: 1  


5.  Mělo by to vypadat nějak takto:  
![Zóny intranetu](./media/how-to-connect-install-custom/sitezone.png)

6.  Dvakrát klikněte na **OK**.

## <a name="configuring-federation-with-ad-fs"></a>Konfigurace federace se službou AD FS
Konfigurace služby AD FS se službou Azure AD Connect je jednoduchá a provedete ji několika kliknutími. Před konfigurací jsou vyžadovány následující položky.

* Windows Server 2012 R2 nebo novější pro federační server s povolenou vzdálenou správou
* Windows Server 2012 R2 nebo novější pro proxy server webové aplikace s povolenou vzdálenou správou
* Certifikát protokolu SSL pro název služby FS (Federation Service), který chcete použít (například sts.contoso.com)

>[!NOTE]
>Certifikát SSL pro farmu služby AD FS můžete aktualizovat pomocí služby Azure AD Connect i v případě, že ji nepoužíváte ke správě důvěryhodnosti federace.

### <a name="ad-fs-configuration-pre-requisites"></a>Předpoklady konfigurace služby AD FS
Chcete-li konfigurovat farmu služby AD FS pomocí služby Azure AD Connect, ujistěte se, že je na vzdálených serverech povolená služba WinRM. Ujistěte se, že jste dokončili další úkoly v [požadavcích federace](how-to-connect-install-prerequisites.md#prerequisites-for-federation-installation-and-configuration). Kromě toho si projděte požadavek na porty uvedený v oddílu [Tabulka 3 – Azure AD Connect a federační servery/protokol WAP](reference-connect-ports.md#table-3---azure-ad-connect-and-ad-fs-federation-serverswap).

### <a name="create-a-new-ad-fs-farm-or-use-an-existing-ad-fs-farm"></a>Vytvoření nové farmy služby AD FS nebo použití existující farmy služby AD FS
Můžete použít existující farmu služby AD FS nebo můžete vytvořit novou farmu služby AD FS. Pokud se rozhodnete vytvořit novou, je nutné zadat certifikát protokolu SSL. Pokud je certifikát protokolu SSL chráněný heslem, budete vyzváni k zadání hesla.

![Farma služby AD FS](./media/how-to-connect-install-custom/adfs1.png)

Pokud se rozhodnete použít existující farmu služby AD FS, přejdete přímo na obrazovku konfigurace vztahu důvěryhodnosti mezi službou AD FS a Azure AD.

>[!NOTE]
>Azure AD Connect je možné použít ke správě pouze jedné farmy služby AD FS. Pokud na vybrané farmě služby AD FS existuje nakonfigurovaná důvěryhodnost federace se službou Azure AD, služba Azure AD Connect důvěryhodnost vytvoří znovu od začátku.

### <a name="specify-the-ad-fs-servers"></a>Zadání serverů služby AD FS
Zadejte servery, na které chcete nainstalovat službu AD FS. Podle potřeb plánování kapacity můžete přidat jeden nebo víc serverů. Před provedením této konfigurace připojte všechny servery AD FS (servery WAP to nevyžadují) ke službě Active Directory. Společnost Microsoft doporučuje instalaci jednoho serveru služby AD FS pro zkušební a pilotní nasazení. Po počáteční konfiguraci znovu spusťte Azure AD Connect a podle potřeb škálování přidejte a nasaďte další servery.

> [!NOTE]
> Před provedením této konfigurace se ujistěte, jestli jsou všechny servery připojené k doméně AD.
>
>

![Servery služby AD FS](./media/how-to-connect-install-custom/adfs2.png)

### <a name="specify-the-web-application-proxy-servers"></a>Zadání proxy serverů webových aplikací
Zadejte servery, které chcete použít jako proxy servery webových aplikací. Proxy server webových aplikací je nasazen do (extranetového) DMZ a podporuje požadavky na ověření z extranetu. Podle potřeb plánování kapacity můžete přidat jeden nebo víc serverů. Společnost Microsoft doporučuje instalaci jednoho proxy serveru webové aplikace pro zkušební a pilotní nasazení. Po počáteční konfiguraci znovu spusťte Azure AD Connect a podle potřeb škálování přidejte a nasaďte další servery. Doporučujeme použít stejný počet proxy serverů k uspokojení ověřování z intranetu.

> [!NOTE]
> <li> Pokud používáte účet, který není místním správcem na serverech WAP, zobrazí se výzva k zadání přihlašovacích údajů správce.</li>
> <li> Před provedením tohoto kroku se ujistěte, že jsou dostupné možnosti připojení HTTP/HTTPS mezi serverem Azure AD Connect a proxy serverem webových aplikací.</li>
> <li> Ujistěte se, že jsou dostupné možnosti připojení HTTP/HTTPS mezi serverem webových aplikací a serverem služby AD FS, které umožní protékání požadavků na ověření.</li>
>

![Webová aplikace](./media/how-to-connect-install-custom/adfs3.png)

Zobrazí se výzva k zadání přihlašovacích údajů, aby server webových aplikací mohl navázat zabezpečené připojení k serveru služby AD FS. Tyto přihlašovací údaje musí být místním správcem na serveru služby AD FS.

![Proxy server](./media/how-to-connect-install-custom/adfs4.png)

### <a name="specify-the-service-account-for-the-ad-fs-service"></a>Zadání účtu služby AD FS
Služba AD FS vyžaduje účet doménové služby, aby mohla ověřovat uživatele a hledat informace o uživatelích ve službě Active Directory. Podporuje dva typy účtů služeb:

* **Skupinový účet spravované služby** – Zaveden ve službě Active Directory Domain Services se systémem Windows Server 2012. Tento typ účtu poskytuje službám (např. službě AD FS) jeden účet bez nutnosti pravidelně aktualizovat heslo účtu. Tuto možnost použijte, pokud se řadiče domény systému Windows Server 2012 už nacházejí v doméně, do které patří server služby AD FS.
* **Uživatelský účet domény** – Tento typ účtu vyžaduje, abyste zadali heslo a pravidelně ho aktualizovali, kdykoli se změní nebo kdykoli vyprší jeho platnost. Tuto možnost použijte jenom tehdy, pokud se v doméně, do které patří server služby AD FS, nenacházejí řadiče domény systému Windows Server 2012.

Pokud jste vybrali skupinový účet spravované služby a tato funkce se ve službě Active Directory ještě nikdy nepoužila, zobrazí se výzva k zadání pověření správce podniku. Tyto přihlašovací údaje slouží k inicializaci úložiště klíčů a povolení této funkce ve službě Active Directory.

> [!NOTE]
> Azure AD Connect provede kontrolu, aby se zjistilo, jestli je služba AD FS v doméně už registrovaná jako hlavní název služby (SPN).  Služba AD DS neumožní registraci duplicitních SPN.  Pokud se najde duplicitní SPN, nebude možné pokračovat, dokud ho neodeberete.

![Účet služby AD FS](./media/how-to-connect-install-custom/adfs5.png)

### <a name="select-the-azure-ad-domain-that-you-wish-to-federate"></a>Výběr domény služby Azure AD, kterou chcete federovat
Tato konfigurace slouží k nastavení federačního vztahu mezi službami AD FS a Azure AD. Službu AD FS nakonfiguruje tak, aby vydávala tokeny zabezpečení službě Azure AD, a službu Azure AD nakonfiguruje tak, aby důvěřovala tokenům od této konkrétní instance služby AD FS. Tato stránka při počáteční instalaci umožňuje nakonfigurovat jen jednu doménu. Později můžete znovu spustit Azure AD Connect a nakonfigurovat další domény.

![Doména služby Azure AD](./media/how-to-connect-install-custom/adfs6.png)

### <a name="verify-the-azure-ad-domain-selected-for-federation"></a>Výběr domény Azure AD vybrané k federaci
Při výběru domény k federaci služba Azure AD Connect poskytuje informace nezbytné k ověření neověřené domény. Téma [Přidání a ověření domény](../active-directory-domains-add-azure-portal.md) vás seznámí s tím, jak tyto informace používat.

![Doména služby Azure AD](./media/how-to-connect-install-custom/verifyfeddomain.png)

> [!NOTE]
> AD Connect zkusí ověřit doménu během fáze konfigurace. Pokud budete pokračovat v konfiguraci, ale nepřidáte nezbytné záznamy DNS, průvodce nemůže konfiguraci dokončit.
>
>

## <a name="configuring-federation-with-pingfederate"></a>Konfigurace federace s PingFederate
Konfigurace služby PingFederate se službou Azure AD Connect je jednoduchá a provedete ji několika kliknutími. Musíte ale splňovat následující předpoklady.
- PingFederate 8.4 nebo novější.  Další informace najdete v článku věnovaném [integraci PingFederate s Azure Active Directory a Office 365](https://docs.pingidentity.com/bundle/O365IG20_sm_integrationGuide/page/O365IG_c_integrationGuide.html).
- Certifikát protokolu SSL pro název služby FS (Federation Service), který chcete použít (například sts.contoso.com)

### <a name="verify-the-domain"></a>Ověření domény
Po výběru federace s PingFederate se zobrazí výzva k ověření domény, kterou chcete federovat.  Vyberte doménu z rozevíracího seznamu.

![Ověření domény](./media/how-to-connect-install-custom/ping1.png)

### <a name="export-the-pingfederate-settings"></a>Export nastavení PingFederate


Služba PingFederate musí být nakonfigurovaná jako federační server pro každou federovanou doménu Azure.  Klikněte na tlačítko Exportovat nastavení a sdělte tyto informace správci PingFederate.  Správce federačního serveru aktualizuje konfiguraci a pak zadá číslo portu a adresu URL serveru PingFederate, aby služba Azure AD Connect mohla ověřit nastavení metadat.  

![Ověření domény](./media/how-to-connect-install-custom/ping2.png)

Případné problémy s ověřením řešte se správcem PingFederate.  Následuje příklad serveru PingFederate, který nemá platný vztah důvěryhodnosti s Azure:

![Důvěryhodnost](./media/how-to-connect-install-custom/ping5.png)




### <a name="verify-federation-connectivity"></a>Ověření připojení federace
Azure AD Connect se pokusí ověřit koncové body ověřování načtené z metadat PingFederate v předchozím kroku.  Azure AD Connect se nejprve pokusí koncové body přeložit pomocí místních serverů DNS.  Pak se pokusí koncové body přeložit pomocí externího poskytovatele DNS.  Případné problémy s ověřením řešte se správcem PingFederate.  

![Ověření připojení](./media/how-to-connect-install-custom/ping3.png)

### <a name="verify-federation-login"></a>Ověření přihlášení federace
Nakonec můžete ověřit nově nakonfigurovaný tok přihlášení federace tím, že se přihlásíte k federované doméně. Pokud to proběhne úspěšně, federace s PingFederate je úspěšně nakonfigurovaná.
![Ověření přihlášení](./media/how-to-connect-install-custom/ping4.png)

## <a name="configure-and-verify-pages"></a>Konfigurace a ověření stránek
Na této stránce probíhá konfigurace.

> [!NOTE]
> Než budete pokračovat v instalaci a pokud jste nakonfigurovali federaci, ujistěte se, že jste nakonfigurovali [Překlad IP adres pro federační servery](how-to-connect-install-prerequisites.md#name-resolution-for-federation-servers).
>
>


![Připraveno ke konfiguraci](./media/how-to-connect-install-custom/readytoconfigure2.png)

### <a name="staging-mode"></a>Pracovní režim
Nový synchronizační server se dá nastavit souběžně s pracovním režimem. Je podporován pouze jeden synchronizační server exportující do jednoho adresáře v cloudu. Pokud se ale chcete přesunout z jiného serveru, například ze serveru se spuštěným nástrojem DirSync, můžete povolit službu Azure AD Connect v pracovním režimu. Pokud je povolena, synchronizační modul importuje a synchronizuje data běžným způsobem, ale neexportuje nic do služby Azure AD ani AD. V pracovním režimu jsou zakázány funkce synchronizace hesla a zpětného zápisu hesla.

![Pracovní režim](./media/how-to-connect-install-custom/stagingmode.png)

V pracovním režimu je možné provést požadované změny synchronizačního modulu a zkontrolovat, co se bude exportovat. Když jste s konfigurací spokojeni, znovu spusťte průvodce instalací a vypněte pracovní režim. Data se z tohoto serveru nyní exportují do služby Azure AD. Nezapomeňte současně zakázat druhý server tak, aby pouze jeden server prováděl aktivní export.

Další informace najdete v tématu [Pracovní režim](how-to-connect-sync-operations.md#staging-mode).

### <a name="verify-your-federation-configuration"></a>Ověření konfigurace federace
Když kliknete na tlačítko Ověřit, služba Azure AD Connect ověří nastavení DNS za vás.

**Kontrola připojení k intranetu**

* Přeložit plně kvalifikovaný název domény federace: Azure AD Connect zkontroluje, pokud plně kvalifikovaný název domény federace se dají vyřešit DNS pro zajištění možnosti připojení. Pokud Azure AD Connect nedokáže přeložit plně kvalifikovaný název domény, ověření se nezdaří. Aby bylo možné úspěšné dokončení ověření, ujistěte se, že je přítomný záznam DNS pro plně kvalifikovaný název domény federační služby.
* DNS A record: Azure AD Connect zkontroluje, jestli záznam A pro své služby FS. Pokud záznam A chybí, ověření se nezdaří. Aby bylo možné úspěšné dokončení ověření, vytvořte pro plně kvalifikovaný název domény federace záznam A, nikoli záznam CNAME.

**Kontrola připojení k extranetu**

* Přeložit plně kvalifikovaný název domény federace: Azure AD Connect zkontroluje, pokud plně kvalifikovaný název domény federace se dají vyřešit DNS pro zajištění možnosti připojení.

![Dokončit](./media/how-to-connect-install-custom/completed.png)

![Ověřit](./media/how-to-connect-install-custom/adfs7.png)

Pokud chcete ověřit, že je kompletní ověřování úspěšné, měli byste ručně provést jeden nebo několik z následujících testů:

* Po dokončení synchronizace použijte další úlohu Ověření federovaného přihlášení v Azure AD Connect k ověření ověřování libovolného místního uživatelského účtu.
* Ověřte, že se můžete přihlásit z prohlížeče z počítače připojeného k doméně v síti intranet: Připojte se k https://myapps.microsoft.com a ověřte přihlášení pomocí přihlášeného účtu. Předdefinovaný účet správce služby AD DS není synchronizovaný a nejde použít k ověření.
* Ověřte, že se můžete přihlásit ze zařízení z extranetu. Na domácím počítači nebo na mobilním zařízení se připojte k https://myapps.microsoft.com a zadejte přihlašovací údaje.
* Ověřte přihlášení plně funkčního klienta. Připojte se k https://testconnectivity.microsoft.com, vyberte kartu **Office 365** a vyberte možnost **Test jednotného přihlašování Office 365**.

## <a name="troubleshooting"></a>Řešení potíží
Následující část popisuje řešení potíží a obsahuje informace, které můžete využít, pokud narazíte na problém s instalací Azure AD Connect.

### <a name="the-adsync-database-already-contains-data-and-cannot-be-overwritten"></a>„Databáze ADSync již obsahuje data a není možné ji přepsat.“
Pokud zvolíte vlastní instalaci Azure AD Connect a na stránce **Instalace požadovaných komponent** vyberete možnost **Použít existující SQL Server**, může dojít k chybě se zprávou **Databáze ADSync již obsahuje data a není možné ji přepsat. Odeberte existující databázi a zkuste to znovu.**

![Chyba](./media/how-to-connect-install-custom/error1.png)

Důvodem je, že v instanci serveru SQL, kterou jste zadali ve výše uvedených textových polích, již existuje databáze **ADSync**.

K tomu obvykle dochází po odinstalaci Azure AD Connect.  Při odinstalaci se databáze neodstraní z SQL Serveru.

Pokud chcete tento problém vyřešit, nejprve ověřte, že se již nepoužívá databáze **ADSync**, kterou před odinstalací používal nástroj Azure AD Connect.

Dále doporučujeme databázi před odstraněním zálohovat.

Nakonec musíte databázi odstranit.  Můžete to provést pomocí aplikace **Microsoft SQL Server Management Studio** po připojení k instanci SQL. Vyhledejte databázi **ADSync**, klikněte na ni pravým tlačítkem a v místní nabídce vyberte **Odstranit**.  Pak ji kliknutím na **OK** odstraňte.

![Chyba](./media/how-to-connect-install-custom/error2.png)

Po odstranění databáze **ADSync** můžete zkusit instalaci zopakovat kliknutím na tlačítko **Nainstalovat**.

## <a name="next-steps"></a>Další postup
Po dokončení instalace se odhlaste a znovu přihlaste do Windows. Teprve pak použijte Synchronization Service Manager nebo Synchronization Rule Editor.

Nyní, když máte nainstalovanou službu Azure AD Connect, můžete si [ověřit instalaci a přiřadit licence](how-to-connect-post-installation.md).

Další informace o těchto funkcích, které byly povoleny v rámci instalace: [Prevence náhodného odstranění](how-to-connect-sync-feature-prevent-accidental-deletes.md) a [Azure AD Connect Health](how-to-connect-health-sync.md).

Zjistěte více o těchto běžných tématech: [plánovač a jak aktivovat synchronizaci](how-to-connect-sync-feature-scheduler.md).

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
