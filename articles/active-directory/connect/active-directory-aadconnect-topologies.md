---
title: 'Služby Azure AD Connect: Podporované topologie | Dokumentace Microsoftu'
description: Toto téma podrobně popisuje podporované a nepodporované topologie pro Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 1034c000-59f2-4fc8-8137-2416fa5e4bfe
ms.service: active-directory
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.topic: article
ms.date: 02/27/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 6a0f18a47bb50144d0a4d428617e0dc5c7509196
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442242"
---
# <a name="topologies-for-azure-ad-connect"></a>Topologie pro Azure AD Connect
Tento článek popisuje různé místní a topologie služby Azure Active Directory (Azure AD), ve kterých pomocí synchronizace Azure AD Connect jako klíče integrační řešení. Tento článek popisuje podporované a nepodporované konfigurace.

Tady je legendy pro obrázky v následujícím článku:

| Popis | Symbol |
| --- | --- |
| Místní doménové struktuře služby Active Directory |![Místní doménové struktuře služby Active Directory](./media/active-directory-aadconnect-topologies/LegendAD1.png) |
| V místní službě Active Directory filtrované importu |![Active Directory filtrované importu](./media/active-directory-aadconnect-topologies/LegendAD2.png) |
| Server synchronizace Azure AD Connect |![Server synchronizace Azure AD Connect](./media/active-directory-aadconnect-topologies/LegendSync1.png) |
| Server synchronizace Azure AD Connect "pracovní režim" |![Server synchronizace Azure AD Connect "pracovní režim"](./media/active-directory-aadconnect-topologies/LegendSync2.png) |
| GALSync s Forefront Identity Manageru (FIM) 2010 nebo Microsoft Identity Manageru (MIM) 2016 |![GALSync s FIM 2010 a program MIM 2016](./media/active-directory-aadconnect-topologies/LegendSync3.png) |
| Azure AD Connect synchronizační server, podrobné |![Azure AD Connect synchronizační server, podrobné](./media/active-directory-aadconnect-topologies/LegendSync4.png) |
| Azure AD |![Azure Active Directory](./media/active-directory-aadconnect-topologies/LegendAAD.png) |
| Nepodporovaný scénář |![Nepodporovaný scénář](./media/active-directory-aadconnect-topologies/LegendUnsupported.png) |


> [!IMPORTANT]
> Microsoft nepodporuje úpravy ani provoz synchronizace služby Azure AD Connect mimo konfigurace nebo akce, které jsou formálně zdokumentované. Některé z těchto konfigurací nebo akce může způsobit nekonzistentní nebo nepodporovaný stav synchronizace Azure AD Connect. Microsoft proto nemůže pro taková nasazení poskytovat technickou podporu.


## <a name="single-forest-single-azure-ad-tenant"></a>Jedna doménová struktura, jeden tenant Azure AD
![Topologie pro jednu doménovou strukturu a jednoho tenanta](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

Nejběžnější topologie je jedné místní doménové struktury s jednou nebo více domén a jedné službě Azure AD tenanta. Pro ověřování Azure AD se používá synchronizaci hodnot hash hesel. Expresní instalace služby Azure AD Connect podporuje pouze této topologie.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Jedna doménová struktura, víc synchronizačních serverů do jednoho tenanta Azure AD
![Nepodporovaná, která jsou filtrovaná topologie pro jednu doménovou strukturu](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

S víc synchronizačních serverů služby Azure AD Connect je připojen ke stejnému tenantovi Azure AD se nepodporuje, s výjimkou [pracovní server](#staging-server). To má nepodporovanou i v případě, že tyto servery jsou nakonfigurovány pro synchronizaci se vzájemně se vylučující sadu objektů. Může mít za tuto topologii Pokud nebudete mít přístup všechny domény v doménové struktuře než jeden server nebo pokud chcete distribuovat zatížení napříč několika servery.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Více doménových struktur, jeden tenant Azure AD
![Topologie pro více doménových struktur a jednoho tenanta](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

Řada organizací má prostředí s více místními doménovými strukturami služby Active Directory. Existují různé důvody pro s více než jednu místní doménovou strukturu služby Active Directory. Typické příklady jsou návrhy s doménových struktur prostředků účtu a výsledek je fúze nebo akvizice.

Pokud máte několik doménových struktur, všechny doménové struktury musí být dostupná pro jeden server synchronizace Azure AD Connect. Nemáte připojení serveru k doméně. V případě potřeby kontaktovat všechny doménové struktury, můžete umístit na server v hraniční síti (označované také jako DMZ, demilitarizovaná zóna a monitorovaná podsíť).

Průvodce instalací Azure AD Connect nabízí několik možností, jak konsolidovat uživatelé, kteří jsou reprezentovány ve více doménových strukturách. Cílem je, že uživatel je reprezentován jenom jednou ve službě Azure AD. Zde jsou některé běžné topologie, ve kterých můžete nakonfigurovat vlastní instalační cestě v Průvodci instalací. Na **Jednoznačná identifikace uživatelů** vyberte odpovídající možnost, která představuje topologii. Sloučení je nakonfigurován pouze pro uživatele. Duplicitní skupiny nejsou konsolidovat s výchozí konfigurací.

Běžné topologie jsou popsány v oddílech o [samostatné topologie](#multiple-forests-separate-topologies), [úplné síť](#multiple-forests-full-mesh-with-optional-galsync), a [topologie prostředek účtu](#multiple-forests-account-resource-forest).

Předpokládá se výchozí konfigurace ve službě Azure AD Connect sync:

* Každý uživatel má pouze jeden povolený účet a doménové struktury, ve kterém se nachází tento účet se používá k ověření uživatele. Tento předpoklad je synchronizace hodnot hash hesel, předávacího ověřování a federace. UserPrincipalName a sourceAnchor/immutableID pocházet z této doménové struktuře.
* Každý uživatel má pouze jedna poštovní schránka.
* Doménové struktury, který je hostitelem poštovních schránek pro uživatele je nejlepší kvalita dat pro atributy, které jsou viditelné v globální adresy seznamu (GAL) systému Exchange. Pokud neexistuje žádný poštovní schránky pro uživatele, lze použít jakoukoli doménovou strukturu přispívat tyto hodnoty atributů.
* Pokud máte propojená poštovní schránka, je zde i účet použít pro přihlášení do jiné doménové struktuře.

Pokud vaše prostředí se neshoduje s těchto předpokladů, stane se následující věcí:

* Pokud máte více než jeden aktivní účet nebo poštovní schránky více než jeden synchronizační modul jednu vybere a ignoruje druhý.
* Poštovní schránku propojenou s žádný aktivní účet není exportovány do služby Azure AD. Uživatelský účet není reprezentován jako člena v libovolné skupině. Propojená poštovní schránky v DirSync vždy reprezentované jako normální poštovní schránky. Tato změna je záměrně různé chování pro zajištění lepší podpory pro scénáře s více doménovými strukturami.

Můžete najít další informace naleznete v [Principy výchozí konfigurace](active-directory-aadconnectsync-understanding-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Více doménových struktur, víc synchronizačních serverů do jednoho tenanta Azure AD
![Nepodporovaná topologie pro více doménových struktur a víc synchronizačních serverů](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

S více než jeden server synchronizace Azure AD Connect připojené do jednoho tenanta Azure AD se nepodporuje. Výjimkou je použití [pracovní server](#staging-server).

### <a name="multiple-forests-separate-topologies"></a>Více doménových struktur, samostatné topologie
![Možnost představující ve všech adresářích jenom jednou uživatelů](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![Znázornění více doménových struktur a samostatné topologie](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

V tomto prostředí jsou všechny místní doménové struktury považovány za samostatné entity. Žádný uživatel se nenachází v jiné doménové struktury. Každé doménové struktuře má své vlastní organizace Exchange a neexistuje žádná GALSync mezi doménovými strukturami. Tato topologie může být situace po fúze nebo akvizice, nebo v organizaci, kde jednotlivé obchodní jednotky funguje nezávisle na sobě. Těchto doménových strukturách jsou ve stejné organizaci ve službě Azure AD a objeví se jednotné GAL. Na předchozím obrázku každý objekt v každé doménové struktuře je reprezentována jednou v úložišti metaverse a agregovat v cílovém tenantovi Azure AD.

### <a name="multiple-forests-match-users"></a>Více doménových struktur: vyhledání uživatelů
Na všech těchto scénářích platí, že distribuce a skupiny zabezpečení může obsahovat kombinaci uživatele, kontakty a cizí objekty zabezpečení (FSP). FSP se používají ve službě Active Directory Domain Services (AD DS) k reprezentaci členů z jiných doménových struktur ve skupině zabezpečení. Všechny FSP, jsou vyhodnoceny na reálnému objektu ve službě Azure AD.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Více doménových struktur: úplné síť s volitelné GALSync
![Možnost pro použití atributu e-mailu pro porovnávání, pokud existuje identit uživatelů napříč více adresářů](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![Vícecestná topologie pro více doménových struktur](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

Vícecestná topologie umožňuje uživatelům a prostředky nacházely ve všech doménových strukturách. Mezi doménovými strukturami se běžně, obousměrný vztah důvěryhodnosti.

Pokud je k dispozici ve více doménových strukturách systému Exchange, může být (volitelně) místním řešením GALSync. Každý uživatel je pak reprezentován jako kontakt v jiných doménových strukturách. GALSync je obvykle implementovány pomocí FIM 2010 a program MIM 2016. Azure AD Connect nelze použít pro místní GALSync.

V tomto scénáři jsou připojené objekty identity prostřednictvím atributu e-mailu. Uživatel, který má poštovní schránku v jedné doménové struktuře je spojen s kontakty v jiných doménových strukturách.

### <a name="multiple-forests-account-resource-forest"></a>Více doménových struktur: doménové struktury účtu prostředku
![Možnost použití atributy ObjectSID a msExchMasterAccountSID pro porovnávání, pokud existuje identit napříč více adresářů](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![Prostředek účtu doménovou strukturu pro více doménových struktur](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

V účtu prostředku-doménovou strukturu, budete mít jeden nebo více *účet* doménových strukturách s účty aktivního uživatele. Máte také jeden nebo více *prostředků* doménové struktury se zakázanými účty.

V tomto scénáři vztahy důvěryhodnosti doménové struktury prostředku (nejméně) všech doménových struktur účtů. Doménové struktury prostředku má obvykle rozšířeným schématem služby Active Directory s Exchange a Lync. Všechny služby Lync a Exchange služby, společně s dalšími službami sdílené jsou umístěné v této doménové struktuře. Uživatelé mají zakázáno uživatelský účet v této doménové struktuře a poštovní schránku je propojený s účtem doménové struktury.

## <a name="office-365-and-topology-considerations"></a>Office 365 a aspekty topologie
Některé úlohy Office 365 mají určitá omezení na podporované topologie:

| Úloha | Omezení |
| --------- | --------- |
| Exchange Online | Další informace o hybridní topologie podporované službou Exchange Online najdete v tématu [hybridní nasazení s více doménovými strukturami služby Active Directory](https://technet.microsoft.com/library/jj873754.aspx). |
| Skype pro firmy | Pokud používáte více místními doménovými strukturami, je podporována pouze doménovou strukturu prostředků účtu. Další informace najdete v tématu [prostředí požadavky pro Skype pro Business Server 2015](https://technet.microsoft.com/library/dn933910.aspx). |

Pokud máte větší organizaci, pak byste měli zvážit použití [Office 365 PreferredDataLocation](active-directory-aadconnectsync-feature-preferreddatalocation.md) funkce. Umožňuje definovat v konkrétních oblastech datacenter jsou umístěny prostředky uživatele.

## <a name="staging-server"></a>Pracovní server
![Pracovní server v topologii](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

Azure AD Connect podporuje instalaci druhého serveru v *pracovním režimu*. Server v tomto režimu čte data ze všech připojených adresářů ale nezapíše nic do připojených adresářů. Použijí se normální synchronizační cyklus a proto má aktualizovanou kopii dat identity.

V po havárii, kde dojde k selhání primárního serveru vás může převzetí služeb při selhání na testovacím serveru. To provedete v průvodce službou Azure AD Connect. Tento druhý server může být umístěný v jiném datovém centru, protože žádnou infrastrukturu se sdílí s primárním serverem. Změny konfigurace provedené na primárním serveru na druhý server musíte ručně zkopírovat.

Pracovní server můžete použít k testování novou vlastní konfiguraci a jeho vliv na vaše data. Můžete zobrazit náhled změn a upravovat konfiguraci. Až budete spokojení s novou konfigurací, můžete nastavit pracovní server jako aktivní a nastavit starý server aktivní pracovní režim.

Tuto metodu lze také použít k nahrazení aktivní synchronizační server. Připravte nový server a nastavte ho na pracovní režim. Ujistěte se, že je v dobrém stavu, zakázat pracovní režim (toho, že aktivní) a vypnout aktivní server.

Je možné mít víc než jeden pracovní server, pokud chcete mít více záloh v různých datových centrech.

## <a name="multiple-azure-ad-tenants"></a>Více tenantů Azure AD
Doporučujeme, abyste s jedním tenantem ve službě Azure AD pro organizaci.
Než budete chtít použít více tenantů Azure AD, najdete v článku [Správa administrativních jednotek v Azure AD](../users-groups-roles/directory-administrative-units.md). Zahrnuje obvyklé scénáře, ve kterém můžete použít jednoho tenanta.

![Topologie pro více doménových struktur a více tenantů](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Existuje vztah 1:1 mezi server synchronizace služby Azure AD Connect a tenanta služby Azure AD. Pro každého tenanta Azure AD budete potřebovat jeden instalace serveru synchronizace služby Azure AD Connect. Instance tenanta Azure AD jsou izolovány záměrné. To znamená neuvidí uživatelé do jednoho tenanta uživatelé v druhém tenantovi. Pokud chcete toto oddělení, jedná se o podporované konfiguraci. V opačném případě byste měli použít jednoho modelu tenanta Azure AD.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Každý objekt pouze jednou v tenantovi Azure AD
![Filtrované topologie pro jednu doménovou strukturu](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

V této topologii je jeden synchronizační server Azure AD Connect připojen ke každému tenantovi Azure AD. Synchronizační servery Azure AD Connect nastavené pro filtrování tak, aby každý má vzájemně se vylučující sadu objektů má operace provést. Můžete například omezit rozsah každého serveru pro určitou doménu nebo organizační jednotka.

Doména DNS mohou být registrovány v jedné tenanta Azure AD. Názvy UPN uživatelů v místní službě Active Directory, musíte taky použít samostatné obory názvů. V předchozím obrázku, například tři samostatné přípony UPN jsou registrovány v místní instanci Active Directory: contoso.com, fabrikam.com a wingtiptoys.com. Uživatelé v každé doméně místní služby Active Directory použít jiný obor názvů.

>[!NOTE]
>Globální synchronizace adresáře (GalSync) v této topologii neprovádí automaticky a vyžaduje další vlastní implementaci MIM k zajištění, že každý tenant má dokončení globálního seznamu adres v systému Exchange Online a Skype for Business Online.


Tato topologie má následující omezení pro jiné podporované scénáře:

* Hybridní Exchange s místní instancí Active Directory můžete povolit pouze jeden z tenantů Azure AD.
* Zařízení s Windows 10 může být přidružená jenom k jednomu tenantovi Azure AD.
* Jednotné přihlašování (SSO) možnost pro synchronizace a předávací ověřování hodnoty hash hesla je možné pomocí pouze jednoho tenanta Azure AD.

Požadavek na vzájemně se vylučující sadu objektů platí také pro zpětný zápis. Některé funkce zpětný zápis nejsou podporovány s touto topologií, protože předpokládají jednomu místnímu konfigurace. Tyto funkce patří:

* Skupina zpětný zápis s výchozí konfigurací.
* Zpětný zápis zařízení.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Každý objekt více než jednou v tenantovi Azure AD
![Nepodporovaná topologie pro jednu doménovou strukturu a více tenantů](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Nepodporovaná topologie pro jednu doménovou strukturu a více konektorů](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

Tyto úlohy nejsou podporovány:

* Synchronizace stejného uživatele s více tenanty Azure AD.
* Zkontrolujte konfiguraci změnit tak, aby uživatelé do jednoho tenanta Azure AD se zobrazí jako kontakty v jiném tenantovi Azure AD.
* Upravte synchronizace Azure AD Connect pro připojení k více tenantů Azure AD.

### <a name="galsync-by-using-writeback"></a>GALSync s použitím zpětný zápis
![Nepodporovaná topologie pro více doménovými strukturami a adresářů více, pomocí GALSync, zaměřuje se na Azure AD](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![Nepodporovaná topologie pro více doménových struktur a více adresářů, pomocí GALSync zaměřením na místní služby Active Directory](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Záměrně jsou izolované klienty Azure AD. Tyto úlohy nejsou podporovány:

* Změňte konfiguraci synchronizace Azure AD Connect číst data z jiného tenanta Azure AD.
* Exportujte uživatele jako kontakty do jiné instance místní služby Active Directory pomocí synchronizace Azure AD Connect.

### <a name="galsync-with-on-premises-sync-server"></a>GALSync s místním synchronizační server
![GALSync v topologii pro několik doménových struktur a více adresářů](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

FIM 2010 nebo MIM 2016 místní můžete použít pro synchronizaci uživatelů (prostřednictvím GALSync) mezi dvěma organizacemi Exchange. Uživatelé v jedné organizaci se zobrazí jako cizí uživatelé/contacts v jiné organizaci. Tyto různé místní služby Active Directory instance můžete synchronizováni s vlastní tenanty Azure AD.

## <a name="next-steps"></a>Další postup
Zjistěte, jak nainstalovat Azure AD Connect pro tyto scénáře, naleznete v tématu [vlastní instalace služby Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

Další informace o [synchronizace Azure AD Connect](active-directory-aadconnectsync-whatis.md) konfigurace.

Další informace o [integrace místních identit s Azure Active Directory](active-directory-aadconnect.md).
