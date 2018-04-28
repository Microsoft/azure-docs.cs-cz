---
title: 'Azure AD Connect: Podporované topologie | Microsoft Docs'
description: Toto téma podrobnosti podporované a nepodporované topologie pro Azure AD Connect
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
ms.author: billmath
ms.openlocfilehash: f47cf18f70572ad93f5075c2f2c883d80af8220e
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="topologies-for-azure-ad-connect"></a>Topologie pro Azure AD Connect
Tento článek popisuje různé místní a topologie služby Azure Active Directory (Azure AD), které využívají synchronizace Azure AD Connect jako klíče integrační řešení. Tento článek obsahuje podporované a nepodporované konfigurace.

Tady je legendu pro obrázky v článku:

| Popis | Symbol |
| --- | --- |
| Místní doménové struktuře služby Active Directory |![Místní doménové struktuře služby Active Directory](./media/active-directory-aadconnect-topologies/LegendAD1.png) |
| V místní službě Active Directory filtrované importu |![Služby Active Directory filtrované importu](./media/active-directory-aadconnect-topologies/LegendAD2.png) |
| Server synchronizace Azure AD Connect |![Server synchronizace Azure AD Connect](./media/active-directory-aadconnect-topologies/LegendSync1.png) |
| Server synchronizace Azure AD Connect "pracovní režim" |![Server synchronizace Azure AD Connect "pracovní režim"](./media/active-directory-aadconnect-topologies/LegendSync2.png) |
| GALSync produktu Forefront Identity Manager (FIM) 2010 nebo Microsoft Identity Manager (MIM) 2016 |![GALSync s FIM 2010 nebo MIM 2016](./media/active-directory-aadconnect-topologies/LegendSync3.png) |
| Azure AD Connect synchronizační server, podrobné |![Azure AD Connect synchronizační server, podrobné](./media/active-directory-aadconnect-topologies/LegendSync4.png) |
| Azure AD |![Azure Active Directory](./media/active-directory-aadconnect-topologies/LegendAAD.png) |
| Nepodporované scénáře |![Nepodporované scénáře](./media/active-directory-aadconnect-topologies/LegendUnsupported.png) |


> [!IMPORTANT]
> Microsoft nepodporuje úpravy nebo operační synchronizace Azure AD Connect mimo konfigurace nebo akce, které jsou popsané dříve. Některé z těchto konfigurací nebo akce, může dojít nekonzistentní nebo v nepodporovaném stavu synchronizace Azure AD Connect. Microsoft proto nemůže pro taková nasazení poskytovat technickou podporu.


## <a name="single-forest-single-azure-ad-tenant"></a>Jediná doménová struktura, jeden klient Azure AD
![Topologie pro jednu doménovou strukturu a jednoho klienta](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

Nejběžnější topologie je jedné místní doménové struktuře s jedním nebo několika domén a jeden Azure AD klienta. Pro ověřování Azure AD se používá synchronizaci hesel. Expresní instalace služby Azure AD Connect podporuje jenom tato topologie.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Jedna doménová struktura, víc synchronizačních serverů do jednoho klienta Azure AD
![Nepodporované, která jsou filtrovaná topologie pro jednu doménovou strukturu](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

S více servery synchronizace Azure AD Connect připojené k stejné klienta Azure AD není podporován, s výjimkou [pracovní server](#staging-server). Má nepodporovaný, i když jsou tyto servery nakonfigurovány k synchronizaci se vzájemně vylučují sadu objektů. Může mít považuje Tato topologie Pokud nebudete mít přístup všechny domény v doménové struktuře z jednoho serveru, nebo pokud chcete distribuovat zatížení mezi několik serverů.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Několik doménových struktur, jeden klient Azure AD
![Topologie pro více doménových struktur a jednoho klienta](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

Mnoho organizací má prostředí s více doménovými strukturami služby Active Directory v místě. Existují různé důvody s více než jedné doménové struktuře místní služby Active Directory. Typickým příkladem jsou návrhy s doménových struktur účtu prostředků a výsledek fúze nebo akvizice.

Pokud máte více doménových struktur, všechny doménové struktury musí být dosažitelný jediný server synchronizace Azure AD Connect. Nemáte připojení serveru k doméně. V případě potřeby k dosažení všech doménových struktur, můžete umístit server v hraniční síti (označované také jako DMZ, demilitarizovaná zóna a monitorovaná podsíť).

Průvodce instalací služby Azure AD Connect nabízí několik možností pro konsolidaci uživatelů, kteří jsou reprezentované ve více doménových strukturách. Cílem je, že uživatel je reprezentován jenom jednou ve službě Azure AD. Existují některé běžné topologie, které můžete nakonfigurovat vlastní instalační cesta v Průvodci instalací. Na **Jednoznačná identifikace uživatelů** vyberte odpovídající možnost, která představuje vaší topologie. Konsolidací je nakonfigurován pouze pro uživatele. Duplicitní skupiny nejsou konsolidovat s výchozí konfigurací.

Běžné topologie jsou popsané v částech o [oddělit topologie](#multiple-forests-separate-topologies), [úplné OK](#multiple-forests-full-mesh-with-optional-galsync), a [účet prostředků topologie](#multiple-forests-account-resource-forest).

Předpokládá výchozí konfigurace v synchronizaci Azure AD Connect:

* Každý uživatel má povolené pouze jeden účet a doménové struktury, kde se nachází tento účet se používá k ověření uživatele. Tento předpoklad je pro synchronizaci hesla i federací. UserPrincipalName a sourceAnchor/immutableID pocházet z této doménové struktury.
* Každý uživatel má pouze jedna poštovní schránka.
* Doménová struktura, která je hostitelem poštovních schránek pro uživatele má nejlepší kvalitu dat pro atributy, které jsou viditelné v systému Exchange globální seznam adres. Neexistuje žádné poštovní schránky pro uživatele, lze nastavit všech doménových strukturách přispívání tyto hodnoty atributů.
* Pokud máte propojená poštovní schránka, existuje také účet v jiné doménové struktuře použít pro přihlášení.

Pokud vaše prostředí neodpovídá tyto předpoklady, provedou se následující věci:

* Pokud máte více než jeden aktivní účet nebo více než jedna poštovní schránka, synchronizační modul vybere jeden a dalších ignoruje.
* Poštovní schránku propojenou s žádné aktivní účet není exportovány do služby Azure AD. Uživatelský účet není reprezentován jako člen v kterékoli skupině. Poštovní schránku propojenou v DirSync, je vždy reprezentován jako normální poštovní schránky. Tato změna se záměrně různé chování lepší podpory scénáře s více doménovými strukturami.

Můžete najít další podrobnosti v [Principy výchozí konfigurace](active-directory-aadconnectsync-understanding-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Několik doménových struktur, víc synchronizačních serverů do jednoho klienta Azure AD
![Nepodporované topologie pro více doménových struktur a víc synchronizačních serverů](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

Více než jeden server Azure AD Connect sync připojené k jedné klienta Azure AD není podporován. Výjimkou je použití [pracovní server](#staging-server).

### <a name="multiple-forests-separate-topologies"></a>Několik doménových struktur, samostatné topologie
![Možnost pro zastoupení jenom jednou uživatelů ve všech adresářích](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![Popis shromažďování více doménovými strukturami a samostatné topologie](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

V tomto prostředí jsou všechny místní doménové struktury považovány za samostatné entity. Žádný uživatel nachází v jiné doménové struktury. Každé doménové struktuře má vlastní organizace Exchange a neexistuje žádný GALSync mezi doménovými strukturami. Tato topologie může být situace po fúze nebo akvizice, nebo v organizaci, kde jednotlivé obchodní jednotky pracuje nezávisle. Těchto doménových strukturách jsou ve stejné organizaci ve službě Azure AD a zobrazují se jednotná GAL. Na předchozím obrázku je každý objekt v každé doménové struktuře zastoupení jednou v úložišti metaverse a agregovat v klientovi cíl Azure AD.

### <a name="multiple-forests-match-users"></a>Několik doménových struktur: vyhledání uživatelů
Na všech těchto scénářích platí, že distribuce a skupiny zabezpečení může obsahovat kombinaci uživatele, kontakty a cizí objekty zabezpečení (FSP). FSP jsou ve službě Active Directory Domain Services (AD DS) používá k reprezentování členy z jiných doménových struktur ve skupině zabezpečení. Všechny FSP jsou vyřešeny skutečné objektu ve službě Azure AD.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Několik doménových struktur: úplné OK s volitelné GALSync
![Možnost pro použití atributu e-mailu pro párování, pokud existují uživatelských identit v několika adresářích](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![Vícecestná topologie pro více doménových struktur](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

Vícecestná topologie umožňuje uživatelům a prostředky nacházely ve všech doménových strukturách. Mezi doménovými strukturami se běžně, obousměrné vztahy důvěryhodnosti.

Pokud Exchange se nachází ve více než jedné doménové struktuře, mohou existovat (volitelně) místním řešením GALSync. Každý uživatel je pak reprezentován jako kontakt v jiných doménových strukturách. GALSync se běžně implementuje pomocí FIM 2010 nebo MIM 2016. Azure AD Connect nelze použít pro místní GALSync.

V tomto scénáři jsou objekty identity připojené prostřednictvím atribut mail. Uživatel, který má poštovní schránku v jedné doménové struktuře je spojen s kontakty v jiných doménových strukturách.

### <a name="multiple-forests-account-resource-forest"></a>Několik doménových struktur: doménové struktury účtu prostředku
![Možnost pro používání ObjectSID a msExchMasterAccountSID atributy pro odpovídající při identity existovat v několika adresářích](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![Topologie doménové struktury prostředků účtu pro více doménových struktur](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

V účtu prostředků-doménovou strukturu, máte jeden nebo více *účet* doménové struktury s aktivní uživatelské účty. Máte také jeden nebo více *prostředků* doménové struktury s účty zakázané.

V tomto scénáři důvěřuje doménové struktury prostředku (nejméně jeden) všech doménových struktur účtů. Doménové struktury prostředku má obvykle rozšířené schéma služby Active Directory s Exchange a Lync. Všechny Exchange a Lync služby, spolu s jiné sdílené služby jsou umístěny v této doménové struktuře. Uživatelé mají zakázáno uživatelského účtu v této doménové struktuře a poštovní schránky je propojený s doménové struktury účtu.

## <a name="office-365-and-topology-considerations"></a>Office 365 a aspekty topologie
Některé úlohy Office 365 mají určitá omezení na podporované topologie:

| Úloha | Omezení |
| --------- | --------- |
| Exchange Online | Další informace o hybridní topologie nepodporuje Exchange Online, najdete v části [hybridní nasazení s více doménovými strukturami služby Active Directory](https://technet.microsoft.com/library/jj873754.aspx). |
| Skype pro firmy | Pokud používáte více místními doménovými strukturami, je podporována pouze topologie doménové struktury účtu prostředku. Další informace najdete v tématu [prostředí požadavky pro Skype pro firmy Server 2015](https://technet.microsoft.com/library/dn933910.aspx). |

Pokud jsou větší organizaci, pak byste měli zvážit použití [Office 365 PreferredDataLocation](active-directory-aadconnectsync-feature-preferreddatalocation.md) funkce. Umožňuje definovat v konkrétních oblastech datacenter jsou umístěny prostředky uživatele.

## <a name="staging-server"></a>Pracovní server
![Pracovní server v topologii](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

Azure AD Connect podporuje instalaci na druhý server v *pracovním režimu*. Čte data ze všech připojených adresářů serveru v tomto režimu, ale nic zapisovat do připojeného adresáře. Pomocí normální synchronizační cyklus a proto má aktualizovanou verzi identifikační údaje.

V po havárii, kde primární server selže může selhat přes pracovní server. Můžete to udělat v průvodce službou Azure AD Connect. Tento druhý server může být umístěn v jiném datovém centru, protože žádné infrastruktury je sdílena s primárním serverem. Změny konfigurace provedené na primárním serveru na druhý server musíte ručně zkopírovat.

Na testovacím serveru můžete použít k testování novou vlastní konfiguraci a o tom, že je na vaše data. Můžete zobrazit náhled změn a upravit konfiguraci. Když budete spokojeni s novou konfiguraci, můžete nastavit pracovní server jako aktivní a nastavit starý active server pro pracovní režim.

Tuto metodu můžete také použít k nahrazení serveru active sync. Připravte nový server a nastavte ji na pracovní režim. Zkontrolujte, zda je v pořádku, zakažte pracovní režim (což aktivní) a aktuálně aktivní server vypnout.

Je možné, že více než jeden testovací server, pokud chcete mít více záloh v různých datových centrech.

## <a name="multiple-azure-ad-tenants"></a>Několik klientů Azure AD
Doporučujeme, abyste s jednoho klienta ve službě Azure AD pro organizaci.
Než budete chtít použít více klientů Azure AD, najdete v článku [Správa administrativních jednotek ve službě Azure AD](../active-directory-administrative-units-management.md). Pokrývá běžné scénáře, kde může používat jednoho klienta.

![Topologie pro více doménových struktur a více klienty](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Je vztah 1:1 mezi server synchronizace služby Azure AD Connect a klient služby Azure AD. Pro každý klient Azure AD budete potřebovat jeden server instalace nástroje Azure AD Connect sync. Instance klienta Azure AD jsou izolované záměrné. To znamená uživatelé v jednoho klienta nemohou zobrazit uživatelům v jiným klientem. Pokud chcete toto oddělení, jedná se o podporovanou konfiguraci. Jinak, měli byste použít jedné model klienta Azure AD.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Každý objekt pouze jednou v klient služby Azure AD
![Filtrované topologie pro jednu doménovou strukturu](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

V této topologii jeden server synchronizace Azure AD Connect je připojený k každý klient Azure AD. Synchronizační servery Azure AD Connect byla nakonfigurována pro filtrování tak, aby každá z nich má vzájemně se vylučuje sadu objektů, které chcete pracovat. Můžete například určit obor každý server pro konkrétní doménu nebo organizační jednotku.

Doménu DNS lze zaregistrovat pouze do jedné klienta Azure AD. Názvy UPN uživatelů v místní instanci služby Active Directory, musíte taky použít samostatné obory názvů. Na předchozím obrázku, například tři samostatné přípony UPN jsou zaregistrovány v místní instanci služby Active Directory: contoso.com, fabrikam.com a wingtiptoys.com. Uživatelé v každé doméně služby Active Directory v místě používat jiný obor názvů.

>[!NOTE]
>Globální synchronizace adresáře (GalSync) není v této topologii provedeno automaticky a vyžaduje další vlastní implementaci MIM a zda že má každý klient dokončení globální seznam adres v systému Exchange Online a Skype for Business Online.


Tato topologie má následující omezení jinak Podporované scénáře:

* Pouze jeden z klientů Azure AD můžete povolit hybridní Exchange s místní instancí Active Directory.
* Zařízení s Windows 10 může být přidružen pouze jeden klient Azure AD.
* -Možnost jednotného přihlašování (SSO) pro synchronizaci a předávací ověřování hesla lze použít pouze jeden klientovi Azure AD.

Požadavek na vzájemně se vylučuje sadu objektů, platí také pro zpětný zápis. Některé funkce zpětného zápisu nejsou podporované v této topologii, protože převzaly jednomu místnímu konfigurace. Tyto funkce patří:

* Skupiny zpětný zápis s výchozí konfigurací.
* Zpětný zápis zařízení.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Každý objekt v vícekrát klient služby Azure AD
![Nepodporované topologie pro jednu doménovou strukturu a více klienty](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Nepodporované topologie pro jednu doménovou strukturu a více konektorů](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

Tyto úlohy nejsou podporovány:

* Synchronizujte stejného uživatele do více klientů Azure AD.
* Zkontrolujte konfiguraci změnit tak, aby uživatelé v jednoho klienta Azure AD se zobrazí jako kontakty ve jiného klienta Azure AD.
* Upravte synchronizace Azure AD Connect se připojit k více klientů Azure AD.

### <a name="galsync-by-using-writeback"></a>GALSync pomocí zpětného zápisu
![Nepodporované topologie pro více doménových struktur a víc adresářů, pomocí GALSync zaměřené na Azure AD](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![Nepodporované topologie pro více doménových struktur a víc adresářů, pomocí GALSync zaměřené na místní služby Active Directory](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Záměrně jsou izolované klienty Azure AD. Tyto úlohy nejsou podporovány:

* Změňte konfiguraci synchronizace služby Azure AD Connect číst data z jiného klienta Azure AD.
* Exportujte uživatele jako kontakty do jiné instance místní služby Active Directory pomocí synchronizace Azure AD Connect.

### <a name="galsync-with-on-premises-sync-server"></a>GALSync s místními synchronizační server
![GALSync v topologii pro několik doménových struktur a několik adresářů](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

FIM 2010 nebo MIM 2016 místní můžete použít k synchronizaci uživatelů (prostřednictvím GALSync) mezi dvěma organizacemi Exchange. Uživatelé v jedné organizaci se zobrazí jako cizí uživatele a kontakty v jiné organizaci. Tyto jiné místní služby Active Directory instance může synchronizováni s vlastní klienty Azure AD.

## <a name="next-steps"></a>Další postup
Informace o instalaci Azure AD Connect pro tyto scénáře, najdete v tématu [vlastní instalace Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

Další informace o [synchronizace Azure AD Connect](active-directory-aadconnectsync-whatis.md) konfigurace.

Další informace o [integrace místních identit s Azure Active Directory](active-directory-aadconnect.md).
