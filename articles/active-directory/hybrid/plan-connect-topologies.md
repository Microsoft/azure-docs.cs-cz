---
title: 'Azure AD Connect: Podporované topologie | Dokumenty společnosti Microsoft'
description: Toto téma podrobnosti podporované a nepodporované topologie pro Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 1034c000-59f2-4fc8-8137-2416fa5e4bfe
ms.service: active-directory
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.topic: conceptual
ms.date: 11/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9618e02f54fbb2a3b92771761c5fcf700d126b5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253829"
---
# <a name="topologies-for-azure-ad-connect"></a>Topologie pro Azure AD Connect
Tento článek popisuje různé místní topologie azure active directory (Azure AD), které používají synchronizaci Azure AD Connect jako řešení integrace klíčů. Tento článek obsahuje podporované i nepodporované konfigurace.


Zde je legenda pro obrázky v článku:

| Popis | Symbol |
| --- | --- |
| Místní doménová struktura služby Active Directory |![Místní doménová struktura služby Active Directory](./media/plan-connect-topologies/LegendAD1.png) |
| Místní služba Active Directory s filtrovaným importem |![Služba Active Directory s filtrovaným importem](./media/plan-connect-topologies/LegendAD2.png) |
| Synchronizační server Azure AD Connect |![Synchronizační server Azure AD Connect](./media/plan-connect-topologies/LegendSync1.png) |
| Synchronizační server Azure AD Connect "pracovní režim" |![Synchronizační server Azure AD Connect "pracovní režim"](./media/plan-connect-topologies/LegendSync2.png) |
| GALSync s Forefront Identity Manager (FIM) 2010 nebo Microsoft Identity Manager (MIM) 2016 |![GALSync s FIM 2010 nebo MIM 2016](./media/plan-connect-topologies/LegendSync3.png) |
| Synchronizační server Azure AD Connect, podrobný |![Synchronizační server Azure AD Connect, podrobný](./media/plan-connect-topologies/LegendSync4.png) |
| Azure AD |![Azure Active Directory](./media/plan-connect-topologies/LegendAAD.png) |
| Nepodporovaný scénář |![Nepodporovaný scénář](./media/plan-connect-topologies/LegendUnsupported.png) |


> [!IMPORTANT]
> Microsoft nepodporuje úpravy nebo provoz synchronizace Azure AD Connect mimo konfigurace nebo akce, které jsou formálně zdokumentovány. Všechny tyto konfigurace nebo akce může mít za následek nekonzistentní nebo nepodporovaný stav synchronizace Azure AD Connect. V důsledku toho společnost Microsoft nemůže poskytovat technickou podporu pro tato nasazení.


## <a name="single-forest-single-azure-ad-tenant"></a>Jedna doménová struktura, jeden klient Azure AD
![Topologie pro jednu doménovou strukturu a jednoho klienta](./media/plan-connect-topologies/SingleForestSingleDirectory.png)

Nejběžnější topologie je jedna místní doménová struktura s jednou nebo více doménami a jedním klientem Azure AD. Pro ověřování Azure AD se používá synchronizace hodnot hash hesel. Expresní instalace Azure AD Connect podporuje pouze tuto topologii.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Jedna doménová struktura, více synchronizačních serverů do jednoho klienta Azure AD
![Nepodporovaná filtrovaná topologie pro jednu doménovou strukturu](./media/plan-connect-topologies/SingleForestFilteredUnsupported.png)

Mít více synchronizačních serverů Azure AD Connect připojených ke stejnému tenantovi Azure AD není podporováno, s výjimkou [pracovního serveru](#staging-server). Není podporována ani v případě, že tyto servery jsou nakonfigurovány pro synchronizaci s vzájemně se vylučující sadu objektů. Tuto topologii jste mohli zvážit, pokud se nemůžete dostat do všech domén v doménové struktuře z jednoho serveru nebo pokud chcete distribuovat zatížení mezi několik serverů.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Více doménových struktur, jeden klient Azure AD
![Topologie pro více doménových struktur a jednoho klienta](./media/plan-connect-topologies/MultiForestSingleDirectory.png)

Mnoho organizací má prostředí s více místními doménovými strukturami služby Active Directory. Existují různé důvody pro více než jednu místní doménovou strukturu služby Active Directory. Typickými příklady jsou návrhy s doménovými strukturami prostředků obchodních prostředků a výsledek fúze nebo akvizice.

Pokud máte více doménových struktur, všechny doménové struktury musí být dosažitelné pomocí jednoho synchronizačního serveru Azure AD Connect. Server musí být připojen k doméně. Pokud je to nutné k dosažení všech doménových struktur, můžete umístit server do hraniční sítě (označované také jako DMZ, demilitarizovaná zóna a stíněná podsíť).

Průvodce instalací služby Azure AD Connect nabízí několik možností konsolidace uživatelů, kteří jsou zastoupeni ve více doménových strukturách. Cílem je, že uživatel je reprezentován pouze jednou ve službě Azure AD. Existují některé běžné topologie, které můžete nakonfigurovat ve vlastní instalační cestě v průvodci instalací. Na stránce **Jednoznačně identifikující uživatele** vyberte odpovídající možnost, která představuje topologii. Konsolidace je konfigurována pouze pro uživatele. Duplicitní skupiny nejsou sloučeny s výchozí konfigurací.

Společné topologie jsou popsány v částech o samostatné topologie, [úplné sítě](#multiple-forests-full-mesh-with-optional-galsync)a [topologie account-resource](#multiple-forests-account-resource-forest).

Výchozí konfigurace v synchronizaci Azure AD Connect předpokládá:

* Každý uživatel má pouze jeden povolený účet a doménová struktura, kde je tento účet umístěn, se používá k ověření uživatele. Tento předpoklad je určen pro synchronizaci hash hesel, předávací ověřování a federaci. UserPrincipalName a sourceAnchor/immutableID pocházejí z této doménové struktury.
* Každý uživatel má pouze jednu poštovní schránku.
* Doménová struktura, která je hostitelem poštovní schránky pro uživatele, má nejlepší kvalitu dat pro atributy viditelné v globálním seznamu adres serveru Exchange (GAL). Pokud pro uživatele neexistuje žádná poštovní schránka, lze k tomu, aby tyto hodnoty atributů přispívala, použita libovolná doménová struktura.
* Pokud máte propojenou poštovní schránku, je k dispozici také účet v jiné doménové struktuře, který se používá pro přihlášení.

Pokud vaše prostředí neodpovídá těmto předpokladům, stane se následující věci:

* Pokud máte více než jeden aktivní účet nebo více než jednu poštovní schránku, synchronizační modul vybere jeden a ignoruje ostatní.
* Propojená poštovní schránka bez jiného aktivního účtu se do Azure AD neexportuje. Uživatelský účet není reprezentován jako člen v žádné skupině. Propojená poštovní schránka v DirSync je vždy reprezentována jako normální poštovní schránka. Tato změna je záměrně jiné chování pro lepší podporu více doménových scénářů.

Další podrobnosti naleznete v [části Principy výchozí konfigurace](concept-azure-ad-connect-sync-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Více doménových struktur, více synchronizačních serverů do jednoho klienta Azure AD
![Nepodporovaná topologie pro více doménových struktur a více synchronizačních serverů](./media/plan-connect-topologies/MultiForestMultiSyncUnsupported.png)

S více než jeden synchronizační server Azure AD Connect připojené k jednomu tenantovi Azure AD není podporována. Výjimkou je použití [pracovního serveru](#staging-server).

Tato topologie se liší od níže uvedené v tom, že **více synchronizačních serverů připojených** k jednomu tenantovi Azure AD není podporováno.

### <a name="multiple-forests-single-sync-server-users-are-represented-in-only-one-directory"></a>Více doménových struktur, jeden synchronizační server, uživatelé jsou reprezentováni pouze v jednom adresáři
![Možnost reprezentovat uživatele pouze jednou ve všech adresářích](./media/plan-connect-topologies/MultiForestUsersOnce.png)

![Zobrazení více lesů a samostatných topologií](./media/plan-connect-topologies/MultiForestSeparateTopologies.png)

V tomto prostředí jsou všechny místní doménové struktury považovány za samostatné entity. V žádné jiné doménové struktuře není přítomen žádný uživatel. Každá doménová struktura má svou vlastní organizaci exchange a mezi doménovými strukturami neexistuje žádná galsync. Tato topologie může být situace po fúzi/akvizici nebo v organizaci, kde každá obchodní jednotka působí nezávisle. Tyto doménové struktury jsou ve stejné organizaci ve službě Azure AD a zobrazí se s jednotným globálním seznamu adres. V předchozím obrázku každý objekt v každé doménové struktuře je reprezentován jednou v metaverse a agregované v cílovém tenantovi Azure AD.

### <a name="multiple-forests-match-users"></a>Více doménových struktur: shoda uživatelů
Společné pro všechny tyto scénáře je, že distribuční a bezpečnostní skupiny mohou obsahovat kombinaci uživatelů, kontaktů a objektů zabezpečení (FSP). Fsp se používají ve službě AD DS (Active Directory Domain Services) k zastupování členů z jiných doménových struktur ve skupině zabezpečení. Všechny FSP jsou vyřešeny na skutečný objekt ve službě Azure AD.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Více doménových struktur: plná síť s volitelným GALSync
![Možnost použití atributu mail pro párování, pokud existují identity uživatelů ve více adresářích](./media/plan-connect-topologies/MultiForestUsersMail.png)

![Plná síťová topologie pro více doménových struktur](./media/plan-connect-topologies/MultiForestFullMesh.png)

Plná síťová topologie umožňuje uživatelům a prostředkům, které mají být umístěny v libovolné doménové struktuře. Mezi doménovými strukturami jsou běžně obousměrné vztahy důvěryhodnosti.

Pokud exchange je k dispozici ve více než jedné doménové struktuře, může být (volitelně) místní řešení GALSync. Každý uživatel je pak reprezentován jako kontakt ve všech ostatních doménových strukturách. GALSync se běžně implementuje prostřednictvím FIM 2010 nebo MIM 2016. Azure AD Connect nelze použít pro místní GALSync.

V tomto scénáři identity objekty jsou spojeny prostřednictvím atributu mail. Uživatel, který má poštovní schránku v jedné doménové struktuře, je spojen s kontakty v ostatních doménových strukturách.

### <a name="multiple-forests-account-resource-forest"></a>Více doménových struktur: doménová struktura prostředků účtu
![Možnost použití atributů ObjectSID a msExchMasterAccountSID pro párování, pokud existují identity ve více adresářích](./media/plan-connect-topologies/MultiForestUsersObjectSID.png)

![Topologie doménové struktury prostředků účtů pro více doménových struktur](./media/plan-connect-topologies/MultiForestAccountResource.png)

V topologii doménové struktury prostředků účtů máte jednu nebo více doménových struktur *účtů* s aktivními uživatelskými účty. Máte také jednu nebo více doménových struktur *prostředků* se zakázanými účty.

V tomto scénáři jeden (nebo více) doménové struktury prostředků důvěřuje všechny doménové struktury účtu. Doménová struktura prostředků má obvykle rozšířené schéma služby Active Directory se servery Exchange a Lync. Všechny služby Exchange a Lync spolu s dalšími sdílenými službami se nacházejí v této doménové struktuře. Uživatelé mají v této doménové struktuře zakázaný uživatelský účet a poštovní schránka je propojena s doménovou doménovou doménovou doménovou strukturu účtu.

## <a name="office-365-and-topology-considerations"></a>Důležité informace o Office 365 a topologii
Některé úlohy Office 365 mají určitá omezení podporovaných topologie:

| Úloha | Omezení |
| --------- | --------- |
| Exchange Online | Další informace o hybridních topologiích podporovaných službou Exchange Online naleznete v [tématu Hybridní nasazení s více doménovými strukturami služby Active Directory](https://technet.microsoft.com/library/jj873754.aspx). |
| Skype pro firmy | Pokud používáte více místních doménových struktur, je podporována pouze topologie doménové struktury prostředků účtu. Další informace najdete [v tématu Požadavky na životní prostředí pro Skype pro obchodní server 2015](https://technet.microsoft.com/library/dn933910.aspx). |

Pokud jste větší organizace, měli byste zvážit použití funkce [Office 365 PreferredDataLocation.](how-to-connect-sync-feature-preferreddatalocation.md) Umožňuje definovat, ve které oblasti datového centra jsou umístěny prostředky uživatele.

## <a name="staging-server"></a>Pracovní server
![Pracovní server v topologii](./media/plan-connect-topologies/MultiForestStaging.png)

Azure AD Connect podporuje instalaci druhého serveru v *pracovním režimu*. Server v tomto režimu čte data ze všech připojených adresářů, ale do připojených adresářů nic nezapisuje. Používá normální cyklus synchronizace a proto má aktualizovanou kopii dat identity.

Při havárii, při které primární server selže, můžete převzetí služeb při selhání na pracovní server. To provedete v průvodci Azure AD Connect. Tento druhý server může být umístěn v jiném datovém centru, protože s primárním serverem není sdílena žádná infrastruktura. Je nutné ručně zkopírovat všechny změny konfigurace provedené na primárním serveru na druhý server.

Pracovní server můžete použít k testování nové vlastní konfigurace a vliv, který má na vaše data. Můžete zobrazit náhled změn a upravit konfiguraci. Až budete s novou konfigurací spokojeni, můžete nastavit pracovní server jako aktivní server a nastavit starý aktivní server do pracovního režimu.

Tuto metodu můžete také použít k nahrazení aktivního synchronizačního serveru. Připravte nový server a nastavte jej do pracovního režimu. Ujistěte se, že je v dobrém stavu, zakažte pracovní režim (jeho aktivní) a vypněte aktuálně aktivní server.

Je možné mít více než jeden pracovní server, pokud chcete mít více záloh v různých datových centrech.

## <a name="multiple-azure-ad-tenants"></a>Více klientů Azure AD
Doporučujeme mít jednoho klienta ve službě Azure AD pro organizaci.
Než plánujete použít více klientů Azure AD, přečtěte si článek [Správa jednotek pro správu ve službě Azure AD](../users-groups-roles/directory-administrative-units.md). Zahrnuje běžné scénáře, kde můžete použít jednoho klienta.

![Topologie pro více doménových struktur a více klientů](./media/plan-connect-topologies/MultiForestMultiDirectory.png)

Existuje vztah 1:1 mezi synchronizačním serverem Azure AD Connect a klientem Azure AD. Pro každý klient Azure AD potřebujete jednu instalaci synchronizačního serveru Azure AD Connect. Instance klienta Azure AD jsou izolované podle návrhu. To znamená, že uživatelé v jednom tenantovi neuvidí uživatele v druhém tenantovi. Pokud chcete toto oddělení, jedná se o podporovanou konfiguraci. V opačném případě byste měli použít model klienta Azure AD.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Každý objekt pouze jednou v tenantovi Azure AD
![Filtrovaná topologie pro jednu doménovou strukturu](./media/plan-connect-topologies/SingleForestFiltered.png)

V této topologii je jeden synchronizační server Azure AD Connect připojený ke každému tenantovi Azure AD. Synchronizační servery Azure AD Connect musí být nakonfigurované pro filtrování tak, aby každý z nich má vzájemně se vylučující sadu objektů pro provoz. Můžete například obor každý server na konkrétní domény nebo organizační jednotky.

Doména DNS může být registrovaná pouze v jednom tenantovi Azure AD. Hlavní názvy názvů uživatelů v místní instanci služby Active Directory musí také používat samostatné obory názvů. Například na předchozím obrázku jsou v místní instanci služby Active Directory registrovány tři samostatné přípony hlavního názvového zařízení: contoso.com, fabrikam.com a wingtiptoys.com. Uživatelé v každé místní doméně služby Active Directory používají jiný obor názvů.

>[!NOTE]
>Globální synchronizace seznamu adres (GalSync) se v této topologii neprovádí automaticky a vyžaduje další vlastní implementaci MIM, aby bylo zajištěno, že každý klient má úplný globální seznam adres (GAL) v Exchange Online a Skype pro firmy online.


Tato topologie má následující omezení pro jinak podporované scénáře:

* Pouze jeden z klientů Azure AD můžete povolit exchange hybrid s místní instanci služby Active Directory.
* Zařízení s Windows 10 můžou být přidružená jenom k jednomu tenantovi Azure AD.
* Možnost jednotného přihlašování (SSO) pro synchronizaci hodnot hash hesla a předávací ověřování lze použít pouze s jedním klientem Azure AD.

Požadavek na vzájemně se vylučující sadu objektů platí také pro zpětný zápis. Některé funkce zpětného zápisu nejsou podporovány s touto topologii, protože předpokládají jednu místní konfiguraci. Mezi tyto funkce patří:

* Zpětný zápis skupiny s výchozí konfigurací.
* Zpětný zápis zařízení.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Každý objekt vícekrát v tenantovi Azure AD
![Nepodporovaná topologie pro jednu doménovou strukturu a více klientů](./media/plan-connect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Nepodporovaná topologie pro jednu doménovou strukturu a více spojnic](./media/plan-connect-topologies/SingleForestMultiConnectorsUnsupported.png)

Tyto úkoly nejsou podporovány:

* Synchronizujte stejného uživatele s více klienty Azure AD.
* Proveďte změnu konfigurace tak, aby se uživatelé v jednom tenantovi Azure AD zobrazovali jako kontakty v jiném tenantovi Azure AD.
* Upravte synchronizaci Azure AD Connect a připojte se k více klientům Azure AD.

### <a name="galsync-by-using-writeback"></a>GALSync pomocí zpětného zápisu
![Nepodporovaná topologie pro více doménových struktur a více adresářů, s GALSync se zaměřením na Azure AD](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![Nepodporovaná topologie pro více doménových struktur a více adresářů se zaměřením GALSync na místní službu Active Directory](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Tenanti Azure AD jsou izolované podle návrhu. Tyto úkoly nejsou podporovány:

* Změňte konfiguraci synchronizace Azure AD Connect na čtení dat z jiného klienta Azure AD.
* Exportujte uživatele jako kontakty do jiné místní instance služby Active Directory pomocí synchronizace Azure AD Connect.

### <a name="galsync-with-on-premises-sync-server"></a>GALSync s místním synchronizačním serverem
![GALSync v topologii pro více doménových struktur a více adresářů](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync.png)

Můžete použít FIM 2010 nebo MIM 2016 místně synchronizovat uživatele (přes GALSync) mezi dvěma organizacemi Exchange. Uživatelé v jedné organizaci se zobrazují jako zahraniční uživatelé nebo kontakty v jiné organizaci. Tyto různé místní instance služby Active Directory pak můžete synchronizovat s vlastními klienty Služby Azure AD.

## <a name="next-steps"></a>Další kroky
Informace o tom, jak nainstalovat Azure AD Connect pro tyto scénáře, najdete [v tématu vlastní instalace Azure AD Connect](how-to-connect-install-custom.md).

Přečtěte si další informace o konfiguraci [synchronizace Azure AD Connect.](how-to-connect-sync-whatis.md)

Přečtěte si další informace o [integraci místních identit pomocí služby Azure Active Directory](whatis-hybrid-identity.md).
