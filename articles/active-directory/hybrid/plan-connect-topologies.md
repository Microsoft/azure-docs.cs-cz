---
title: 'Azure AD Connect: podporované topologie | Microsoft Docs'
description: Toto téma podrobně popisuje podporované a nepodporované topologie pro Azure AD Connect
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
ms.openlocfilehash: 8d3f8e9441064a5d2d1372e3f177534b8dfefb93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92359828"
---
# <a name="topologies-for-azure-ad-connect"></a>Topologie pro Azure AD Connect
Tento článek popisuje různé místní a Azure Active Directory (Azure AD) topologie, které používají Azure AD Connect synchronizaci jako klíčové řešení pro integraci. Tento článek zahrnuje podporované i nepodporované konfigurace.


Tady je legenda k obrázkům v článku:

| Popis | Symbol |
| --- | --- |
| Místní doménová struktura služby Active Directory |![Místní doménová struktura služby Active Directory](./media/plan-connect-topologies/legendad1.png) |
| Místní služba Active Directory s filtrovaným importem |![Služba Active Directory s filtrovaným importem](./media/plan-connect-topologies/legendad2.png) |
| Server Azure AD Connect Sync |![Server Azure AD Connect Sync](./media/plan-connect-topologies/legendsync1.png) |
| Server Azure AD Connect Sync "pracovní režim" |![Server Azure AD Connect Sync "pracovní režim"](./media/plan-connect-topologies/legendsync2.png) |
| GALSync se službou Forefront Identity Manager (FIM) 2010 nebo Microsoft Identity Manager (MIM) 2016 |![GALSync s FIM 2010 nebo MIM 2016](./media/plan-connect-topologies/legendsync3.png) |
| Server Azure AD Connect Sync, podrobný |![Server Azure AD Connect Sync, podrobný](./media/plan-connect-topologies/legendsync4.png) |
| Azure AD |![Azure Active Directory](./media/plan-connect-topologies/legendaad.png) |
| Nepodporovaný scénář |![Nepodporovaný scénář](./media/plan-connect-topologies/legendunsupported.png) |


> [!IMPORTANT]
> Společnost Microsoft nepodporuje úpravu ani provozní Azure AD Connect synchronizaci mimo konfigurace nebo akce, které jsou zdokumentovány formálně. Kterákoli z těchto konfigurací nebo akcí může způsobit nekonzistentní nebo nepodporovaný stav Azure AD Connect synchronizace. V důsledku toho společnost Microsoft nemůže poskytnout technickou podporu pro taková nasazení.


## <a name="single-forest-single-azure-ad-tenant"></a>Jedna doménová struktura, jeden tenant Azure AD
![Topologie pro jednu doménovou strukturu a jednoho tenanta](./media/plan-connect-topologies/singleforestsingledirectory.png)

Nejběžnější topologie je jediná místní doménová struktura s jednou nebo více doménami a jedním tenanta Azure AD. Pro ověřování Azure AD se používá synchronizace hodnot hash hesel. Expresní instalace Azure AD Connect podporuje pouze tuto topologii.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Jedna doménová struktura, několik synchronizačních serverů do jednoho tenanta Azure AD
![Nepodporovaná, filtrovaná topologie pro jednu doménovou strukturu](./media/plan-connect-topologies/singleforestfilteredunsupported.png)

Více Azure AD Connect synchronizačních serverů připojených ke stejnému tenantovi služby Azure AD není podporováno, s výjimkou [přípravného serveru](#staging-server). Nepodporovaná ani v případě, že tyto servery jsou nakonfigurovány pro synchronizaci s vzájemně exkluzivní sadou objektů. Pokud se nemůžete spojit se všemi doménami v doménové struktuře z jednoho serveru nebo pokud chcete distribuovat zatížení mezi několik serverů, možná se tato topologie považuje za považovat za tuto topologii.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Víc doménových struktur, jeden tenant Azure AD
![Topologie pro více doménových struktur a jednoho tenanta](./media/plan-connect-topologies/multiforestsingledirectory.png)

Mnoho organizací má prostředí s několika místními doménovými strukturami služby Active Directory. Existují různé důvody pro používání více než jedné místní doménové struktury služby Active Directory. Typické příklady jsou návrhy s doménovými strukturami prostředků a výsledkem fúze nebo akvizice.

Pokud máte více doménových struktur, musí být všechny doménové struktury dosažitelné jedním Azure AD Connect synchronizačním serverem. Server musí být připojený k doméně. Pokud je to nutné pro dosažení všech doménových struktur, můžete server umístit do hraniční sítě (označované také jako DMZ, demilitarizovaná Zone a monitorovaná podsíť).

Průvodce instalací Azure AD Connect nabízí několik možností pro konsolidaci uživatelů, kteří jsou zastoupeni ve více doménových strukturách. Cílem je, že se uživatel ve službě Azure AD reprezentuje jenom jednou. Existují některé běžné topologie, které lze konfigurovat v cestě k vlastní instalaci v Průvodci instalací nástroje. Na stránce **jedinečně identifikující uživatele** vyberte odpovídající možnost, která představuje vaši topologii. Konsolidace je nakonfigurovaná jenom pro uživatele. Duplicitní skupiny nejsou konsolidovány s výchozí konfigurací.

Běžné topologie jsou popsány v oddílech týkajících se samostatných topologií, [celé sítě](#multiple-forests-full-mesh-with-optional-galsync)a [topologie prostředků účtů](#multiple-forests-account-resource-forest).

Výchozí konfigurace v Azure AD Connect synchronizace předpokládá:

* Každý uživatel má pouze jeden povolený účet a doménová struktura, kde se tento účet nachází, slouží k ověření uživatele. Tento předpoklad je pro synchronizaci hodnot hash hesel, předávací ověřování a federaci. Třídy UserPrincipalName a sourceAnchor/immutableID pocházejí z této doménové struktury.
* Každý uživatel má pouze jednu poštovní schránku.
* Doménová struktura, která hostuje poštovní schránku pro uživatele, má nejlepší kvalitu dat pro atributy viditelné v seznamu globálních adres (globálního adresáře) systému Exchange. Pokud není k dispozici poštovní schránka pro uživatele, lze použít jakoukoli doménovou strukturu k přispívání těchto hodnot atributů.
* Pokud máte propojenou poštovní schránku, je k dispozici také účet v jiné doménové struktuře, který se používá pro přihlášení.

Pokud vaše prostředí neodpovídá těmto předpokladům, dojde k následujícím akcím:

* Pokud máte více než jeden aktivní účet nebo více než jednu poštovní schránku, synchronizační modul ho vybere a ignoruje druhý.
* Propojená poštovní schránka bez dalšího aktivního účtu se do Azure AD neexportuje. Uživatelský účet není reprezentován jako člen v žádné skupině. Propojená poštovní schránka v DirSync je vždy reprezentována jako normální poštovní schránka. Tato změna je záměrně jiné chování pro lepší podporu scénářů s více doménovými strukturami.

Další podrobnosti najdete v [části Principy výchozí konfigurace](concept-azure-ad-connect-sync-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Několik doménových struktur, několik synchronizačních serverů do jednoho tenanta Azure AD
![Nepodporovaná topologie pro více doménových struktur a více synchronizačních serverů](./media/plan-connect-topologies/multiforestmultisyncunsupported.png)

K jednomu klientovi Azure AD se nepodporuje víc než jeden Azure AD Connect synchronizační Server. Výjimkou je použití [přípravného serveru](#staging-server).

Tato topologie se liší od toho, že **více synchronizačních serverů** připojených k jednomu klientovi Azure AD není podporováno.

### <a name="multiple-forests-single-sync-server-users-are-represented-in-only-one-directory"></a>Více doménových struktur, jeden synchronizační Server, uživatelé jsou zastoupeni pouze v jednom adresáři.
![Možnost pro reprezentaci uživatelů jenom ve všech adresářích](./media/plan-connect-topologies/multiforestusersonce.png)

![Znázornění několika doménových struktur a samostatných topologií](./media/plan-connect-topologies/multiforestseparatetopologies.png)

V tomto prostředí se všechny místní doménové struktury považují za samostatné entity. Žádný uživatel není přítomen v žádné jiné doménové struktuře. Každá doménová struktura má svou vlastní organizaci Exchange a mezi doménovými strukturami neexistuje žádný GALSync. Tato topologie může představovat situaci, kdy se fúze nebo akvizice nebo v organizaci, kde každá obchodní jednotka pracuje nezávisle na sobě. Tyto doménové struktury jsou ve stejné organizaci ve službě Azure AD a zobrazují se v rámci sjednoceného globálního adresáře. V předchozím obrázku je každý objekt v každé doménové struktuře v úložišti Metaverse uveden jednou a agregovaný v cílovém tenantovi služby Azure AD.

### <a name="multiple-forests-match-users"></a>Více doménových struktur: Vyhledání uživatelů
Společné pro všechny tyto scénáře je, že distribuce a skupiny zabezpečení můžou obsahovat kombinaci uživatelů, kontaktů a cizích objektů zabezpečení (FSPs). FSPs se používají v Active Directory Domain Services (služba AD DS) k zastupování členů z jiných doménových struktur ve skupině zabezpečení. Všechny FSPs se přeloží na skutečný objekt ve službě Azure AD.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Více doménových struktur: celá síť s volitelným GALSync
![Možnost použití atributu mail pro porovnání, pokud existují identity uživatelů v několika adresářích](./media/plan-connect-topologies/multiforestusersmail.png)

![Úplná topologie sítě pro více doménových struktur](./media/plan-connect-topologies/multiforestfullmesh.png)

Topologie celé sítě umožňuje umístění uživatelů a prostředků do jakékoli doménové struktury. Mezi doménovými strukturami obvykle existují obousměrné vztahy důvěryhodnosti.

Pokud je Exchange k dispozici ve více než jedné doménové struktuře, může to být (volitelně) místní řešení GALSync. Každý uživatel je pak reprezentován jako kontakt ve všech ostatních doménových strukturách. GALSync se běžně implementuje prostřednictvím FIM 2010 nebo MIM 2016. Azure AD Connect nelze použít pro místní GALSync.

V tomto scénáři jsou objekty identity připojené prostřednictvím atributu mail. Uživatel, který má poštovní schránku v jedné doménové struktuře, je připojen ke kontaktům v jiných doménových strukturách.

### <a name="multiple-forests-account-resource-forest"></a>Více doménových struktur: účet-doménová struktura prostředků
![Možnost použití atributů ObjectSID a msExchMasterAccountSID pro porovnání, pokud existují identity v několika adresářích](./media/plan-connect-topologies/multiforestusersobjectsid.png)

![Účet – topologie doménové struktury prostředků pro více doménových struktur](./media/plan-connect-topologies/multiforestaccountresource.png)

V topologii doménové struktury prostředků účtů máte jednu nebo více doménových struktur *účtů* s aktivními uživatelskými účty. Máte také jednu nebo více doménových struktur *prostředků* se zakázanými účty.

V tomto scénáři jedna (nebo víc) doménová struktura prostředků důvěřuje všem doménovým strukturám účtů. Doménová struktura prostředků má obvykle rozšířené schéma služby Active Directory se systémem Exchange a Lync. Všechny služby Exchange a Lync spolu s dalšími sdílenými službami se nacházejí v této doménové struktuře. Uživatelé mají v této doménové struktuře zakázaný uživatelský účet a poštovní schránka je propojená s doménovou strukturou účtu.

## <a name="microsoft-365-and-topology-considerations"></a>Otázky Microsoft 365 a topologie
Některé Microsoft 365 úlohy mají určitá omezení pro podporované topologie:

| Úloha | Omezení |
| --------- | --------- |
| Exchange Online | Další informace o hybridních topologiích podporovaných systémem Exchange Online najdete v tématu [hybridní nasazení s několika doménovými strukturami služby Active Directory](/Exchange/hybrid-deployment/hybrid-with-multiple-forests). |
| Skype pro firmy | Pokud používáte více místních doménových struktur, je podporována pouze topologie doménové struktury prostředků účtů. Další informace najdete v tématu [požadavky na životní prostředí pro Skype pro firmy Server 2015](/skypeforbusiness/plan-your-deployment/requirements-for-your-environment/environmental-requirements). |

Pokud jste větší organizaci, měli byste zvážit použití funkce [Microsoft 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) . Umožňuje definovat, ve které oblasti datacentra se nacházejí prostředky uživatele.

## <a name="staging-server"></a>Pracovní server
![Pracovní server v topologii](./media/plan-connect-topologies/multiforeststaging.png)

Azure AD Connect podporuje instalaci druhého serveru v *pracovním režimu*. Server v tomto režimu čte data ze všech připojených adresářů, ale do připojených adresářů nezapisuje cokoli. Používá normální synchronizační cyklus a proto má aktualizovanou kopii dat identity.

V případě havárie, kde selže primární server, můžete převzít služby při selhání na pracovním serveru. To provedete v průvodci Azure AD Connect. Tento druhý server se může nacházet v jiném datovém centru, protože na primárním serveru se nesdílí žádná infrastruktura. Všechny změny konfigurace provedené na primárním serveru musíte ručně zkopírovat na druhý server.

Pomocí přípravného serveru můžete otestovat novou vlastní konfiguraci a její vliv na vaše data. Můžete zobrazit náhled změn a upravit konfiguraci. Až budete s novou konfigurací spokojeni, můžete nastavit pracovní server jako aktivní a nastavit původní aktivní server na pracovní režim.

Tuto metodu můžete použít také k nahrazení aktivního synchronizačního serveru. Připravte nový server a nastavte ho do pracovního režimu. Ujistěte se, že je v dobrém stavu, zakažte pracovní režim (aktivní) a vypněte aktuálně aktivní server.

Pokud chcete mít více záloh v různých datových centrech, je možné mít více než jeden pracovní server.

## <a name="multiple-azure-ad-tenants"></a>Několik tenantů Azure AD
V rámci organizace doporučujeme mít v Azure AD jeden tenant.
Než budete chtít používat víc tenantů Azure AD, přečtěte si článek [Správa administrativních jednotek v Azure AD](../roles/administrative-units.md). Zahrnuje běžné scénáře, kdy můžete použít jednoho tenanta.

![Topologie pro více doménových struktur a více tenantů](./media/plan-connect-topologies/multiforestmultidirectory.png)

Mezi Azure AD Connect synchronizačním serverem a klientem služby Azure AD je 1:1 vztah. Pro každého tenanta Azure AD potřebujete jednu Azure AD Connect synchronizaci serveru. Instance tenanta Azure AD jsou izolované podle návrhu. To znamená, že uživatelé v jednom tenantovi neuvidí uživatele v jiném tenantovi. Pokud chcete toto oddělení, jedná se o podporovanou konfiguraci. V opačném případě byste měli použít jeden model tenanta Azure AD.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Každý objekt jenom jednou v tenantovi Azure AD
![Filtrovaná topologie pro jednu doménovou strukturu](./media/plan-connect-topologies/singleforestfiltered.png)

V této topologii je jeden Azure AD Connect synchronizační server připojen ke každému tenantovi služby Azure AD. Azure AD Connect synchronizace serverů musí být nakonfigurovány pro filtrování, takže každá z nich má vzájemně exkluzivní sadu objektů pro provoz. Můžete například oborovat každý server na určitou doménu nebo organizační jednotku.

Doménu DNS je možné zaregistrovat jenom v jednom tenantovi Azure AD. UPN uživatelů v místní instanci služby Active Directory musí také používat samostatné obory názvů. Například na předchozím obrázku jsou tři samostatné přípony UPN registrovány v místní instanci služby Active Directory: contoso.com, fabrikam.com a wingtiptoys.com. Uživatelé v každé místní doméně služby Active Directory používají jiný obor názvů.

>[!NOTE]
>Synchronizace globálního seznamu adres (GalSync) se v této topologii neprovádí automaticky a vyžaduje další vlastní implementaci MIM, aby měl každý tenant úplný globální seznam adres (globálního adresáře) v Exchange Online a Online Skypu pro firmy.


Tato topologie má následující omezení pro jiné podporované scénáře:

* S místní instancí Active Directory může mít služba Exchange Hybrid maximálně 5 Azure Active Directory tenantů. Tento scénář je popsaný v tématu [aktualizace Průvodce hybridní konfigurací v září 2020](https://techcommunity.microsoft.com/t5/exchange-team-blog/september-2020-hybrid-configuration-wizard-update/ba-p/1687698).
* Server Exchange, na kterém běží Průvodce hybridními konfiguracemi, musí být 2016 CU18 nebo 2019 CU7 nebo novější.
* Každá instance Azure AD Connect by měla být spuštěná na počítači připojeném k doméně.
* Aby bylo možné filtrovat uživatele z místního adresáře, Azure AD Connect musí být nakonfigurovány pomocí možnosti filtrování domény nebo organizační jednotky. Pomocí této možnosti zajistíte, že se uživatelé budou zobrazovat jenom v jednom klientovi online Exchange.
* Zařízení s Windows 10 se dají přidružit jenom k jednomu klientovi Azure AD.
* Možnost jednotného přihlašování (SSO) pro synchronizaci hodnot hash hesel a předávací ověřování se dá použít jenom s jedním tenanta Azure AD.

Požadavek na vzájemně exkluzivní sadu objektů platí také pro zpětný zápis. Některé funkce zpětného zápisu nejsou v této topologii podporované, protože předpokládají jednu místní konfiguraci. Patří k nim:

* Zpětný zápis skupin s výchozí konfigurací.
* Zpětný zápis zařízení

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Každý objekt vícekrát v tenantovi Azure AD
![Nepodporovaná topologie pro jednu doménovou strukturu a více tenantů](./media/plan-connect-topologies/singleforestmultidirectoryunsupported.png) ![Nepodporovaná topologie pro jednu doménovou strukturu a více konektorů](./media/plan-connect-topologies/singleforestmulticonnectorsunsupported.png)

Tyto úlohy nejsou podporované:

* Synchronizovat stejného uživatele s více klienty služby Azure AD.
* Proveďte změnu konfigurace, aby se uživatelé v jednom tenantovi služby Azure AD zobrazili jako kontakty v jiném tenantovi služby Azure AD.
* Upravte Azure AD Connect synchronizace pro připojení k více klientům služby Azure AD.

### <a name="galsync-by-using-writeback"></a>GALSync pomocí zpětného zápisu
![Nepodporovaná topologie pro více doménových struktur a více adresářů s GALSync se zaměřením na Azure AD](./media/plan-connect-topologies/multiforestmultidirectorygalsync1unsupported.png) ![Nepodporovaná topologie pro více doménových struktur a více adresářů s GALSync se zaměřením na místní službu Active Directory](./media/plan-connect-topologies/multiforestmultidirectorygalsync2unsupported.png)

Klienti Azure AD jsou izolováni podle návrhu. Tyto úlohy nejsou podporované:

* Změňte konfiguraci Azure AD Connect synchronizace, aby se načetla data z jiného tenanta Azure AD.
* Exportujte uživatele jako kontakty do jiné místní instance služby Active Directory pomocí Azure AD Connect synchronizace.

### <a name="galsync-with-on-premises-sync-server"></a>GALSync pomocí místního synchronizačního serveru
![GALSync v topologii pro více doménových struktur a více adresářů](./media/plan-connect-topologies/multiforestmultidirectorygalsync.png)

K synchronizaci uživatelů (přes GALSync) mezi dvěma organizacemi Exchange můžete použít produkt FIM 2010 nebo místní MIM 2016. Uživatelé v jedné organizaci se zobrazí jako cizí uživatelé/kontakty v jiné organizaci. Tyto různé místní instance služby Active Directory se pak dají synchronizovat s vlastními klienty Azure AD.

### <a name="using-unauthorized-clients-to-access-the-azure-ad-connect-backend"></a>Použití neautorizovaných klientů pro přístup k Azure AD Connect back-endu
![Použití neautorizovaných klientů pro přístup k Azure AD Connect back-endu](./media/plan-connect-topologies/other-client-unsupported.png)

Azure Active Directory Connect Server komunikuje s Azure Active Directory prostřednictvím Azure Active Directory Connect back-endu. Jediný software, který se dá použít ke komunikaci s tímto back-end, je Azure Active Directory Connect. Komunikace s Azure Active Directory Connect back-endu není podporovaná pomocí jiného softwaru nebo metody. 

## <a name="next-steps"></a>Další kroky
Informace o tom, jak nainstalovat Azure AD Connect pro tyto scénáře, najdete v tématu [vlastní instalace Azure AD Connect](how-to-connect-install-custom.md).

Přečtěte si další informace o konfiguraci [Azure AD Connect synchronizace](how-to-connect-sync-whatis.md) .

Přečtěte si další informace o [integraci místních identit s Azure Active Directory](whatis-hybrid-identity.md).
