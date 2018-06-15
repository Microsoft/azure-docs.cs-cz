---
title: 'Microsoft Azure virtuální Datacenter: Sítě perspektivy | Microsoft Docs'
description: Naučte se vytvářet virtuální datové centrum v Azure
services: networking
author: tracsman
manager: rossort
tags: azure-resource-manager
ms.service: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/3/2018
ms.author: jonor
ms.openlocfilehash: a62d52e30b04b525dc8ff685ed6c3033d6029542
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942441"
---
# <a name="microsoft-azure-virtual-datacenter-a-network-perspective"></a>Microsoft Azure virtuální Datacenter: Hlediska sítě
**Microsoft Azure**: rychlejší, šetřit peníze, integraci místní aplikace a data

## <a name="overview"></a>Přehled
Migrace místní aplikace do Azure, i bez žádné významné změny (přístup označuje jako "navýšení a posunutí"), poskytuje organizacím výhody zabezpečené a nákladově efektivní infrastruktury. Chcete-li možné většinu flexibility s cloud computing, ale podniky by se měl vyvíjet jejich architektury využívat možnosti Azure. Microsoft Azure poskytuje flexibilně škálovatelné služby a infrastruktury, možnosti podnikové úrovni a spolehlivost a mnoho možností pro hybridní připojení. Tato volba se přístup k těmto službám cloudu přes Internet nebo s Azure ExpressRoute, které poskytuje připojení k privátní síti. Platforma Microsoft Azure umožňuje zákazníkům bezproblémově rozšířit jejich infrastrukturu do cloudu a vytvářet vícevrstvé architektury. Kromě toho partnery společnosti Microsoft nabízí rozšířené možnosti prostřednictvím nabídky zabezpečení služeb a virtuálních zařízení, které jsou optimalizovány pro spuštění v Azure.

Tento článek obsahuje přehled vzory a návrhy, které lze použít k řešení architektury obavy škálování, výkonu a zabezpečení mnoho se zákazníci setkávají u o en masse přechodu do cloudu. Přehled o tom, jak začlenit jiné organizační rolemi v oddělení IT do správy a zásad správného řízení systému je také popsané, s důrazem na požadavky na zabezpečení a nákladů optimalizace.

## <a name="what-is-a-virtual-data-center"></a>Co je virtuální datové centrum?
V současné době byly navrženy cloudové řešení pro hostitele jeden, relativně izolované aplikace v veřejné spektra. Tento přístup fungovala správně pro několik let. Však jako výhody technologie cloud se objevily řešení a více rozsáhlé úlohy byly hostované v cloudu, adresování zabezpečení, spolehlivost, výkon a náklady riziko z hlediska nasazení v jedné nebo více oblastí se aktivovala důležitých v průběhu životního cyklu cloudové služby.

Následující diagram nasazení cloudu znázorňuje některé příklady zabezpečení mezer (červeným rámečkem) a místo pro optimalizace sítě virtuálního zařízení napříč úlohy (žlutý pole).

[![0]][0]

Z této nezbytné pro škálování k podpoře podnikové úlohy a není nutné se k řešení problémů zavedená při podpoře aplikace ve velkém měřítku ve veřejném cloudu se narodil virtuální datového centra (vDC).

VDC není právě úlohy aplikací v cloudu, ale také sítě, zabezpečení, správy a infrastruktury (třeba DNS a Directory Services). Obvykle poskytuje také privátní připojení zpět do místní sítě nebo datového centra. Jako další a další úlohy přesunout do Azure, je důležité zvážit podpůrnou infrastrukturu a objekty, které tyto úlohy jsou umístěny v. Přemýšlení pečlivě o tom, jak jsou strukturovaná prostředky se můžete vyhnout tím, jak narůstá stovek "zatížení ostrovy", které se musí spravovat samostatně nezávislé datový tok, modely zabezpečení a problémy dodržování předpisů.

Virtuální datového centra je v podstatě kolekcí entity, ale běžné podpůrné funkce, funkce a infrastruktury. Zobrazením vašich úloh jako integrované vDC můžou realizovat snížené náklady z důvodu velkých úspor optimalizované zabezpečení prostřednictvím součásti a data toku centralizace, společně s jednodušší operace, správu a audity dodržování předpisů.

> [!NOTE]
> Je důležité si uvědomit, že je virtuálního **není** diskrétní Azure produktu, ale kombinaci různých funkcí a možností pro splnění požadavků na přesný. vDC představuje způsob přemýšlení o Azure využití a úloh maximalizovat schopnostmi v cloudu a zdroje. Virtuální řadiče domény je proto modulární přístup, o tom, jak vytvořit IT služeb ve službě Azure, bere ohledy organizační role a zodpovědnosti.

Virtuálního může pomoci podnikům získat úlohy a aplikace do Azure pro následující scénáře:

-   Hostování více souvisejících úloh
-   Migrace úloh z místního prostředí do Azure
-   Implementaci sdílených nebo centralizované zabezpečení a požadavky na přístup v rámci úlohy
-   Kombinování DevOps a centralizované IT správně pro velký podnik

Klíč k odemknutí výhody virtuálních řadičů domény, je centralizované topologie (rozbočovače a koncových) se smíšenými funkce Azure: [virtuální síť Azure][VNet], [skupiny Nsg][NSG], [VNet Peering][VNetPeering], [trasy definované uživatelem (UDR)][UDR]a Azure Identity s [řízení přístupu základní Role (RBAC)][RBAC].

## <a name="who-needs-a-virtual-data-center"></a>Kdo potřebuje virtuální datové centrum?
Všechny Azure zákazníkovi, který musí přesunout více než několik úloh do Azure můžete využívat výhod přemýšlení o pomocí společných prostředků. V závislosti na velikosti se i jedné aplikace můžete využít pomocí vzorce a součásti sloužící k vytvoření virtuálních řadičů domény.

Pokud má vaše organizace centralizované IT, sítě, zabezpečení, nebo tým nebo oddělení pro dodržování předpisů, virtuálních řadičů domény může pomoct vynutit zásady body, oddělení cla a zajistit jednotnost součástí základní běžné při poskytnutí aplikace týmy tolik volnost a řízení, jako je vhodné pro vaše požadavky.

Organizace, které se zaměřujete na DevOps můžete využít vDC koncepty poskytuje oprávnění kapsami prostředků Azure a zajišťuje mají úplnou kontrolu v rámci dané skupiny (skupina předplatné nebo prostředek v rámci běžných předplatného), ale zůstávají kompatibilní podle definice centralizovaných zásad v rozbočovači virtuální sítě a skupině prostředků hranicích sítě a zabezpečení.

## <a name="considerations-on-implementing-a-virtual-data-center"></a>Požadavky na implementaci virtuální datového centra
Při navrhování vDC, existuje několik hrají problémy, které je třeba zvážit:

-   Identitu a adresářové služby
-   Infrastruktura zabezpečení
-   Připojení ke cloudu
-   Připojení v rámci cloudu

##### <a name="identity-and-directory-service"></a>*Identitu a adresářové služby*
Identitu a adresářové služby jsou klíčovým prvkem všechna data centra, jak místně a v cloudu. Identity se týká všech aspektů přístupu a autorizaci ke službám v rámci virtuálního. Abyste zajistili, že pouze autorizovaným uživatelům a procesů přístup k účtu Azure a prostředků, Azure používá několik typů přihlašovacích údajů pro ověřování. Mezi ně patří hesel (a přístup k účtu Azure), kryptografické klíče, digitální podpisy a certifikáty. [*Azure Multi-Factor Authentication* (MFA)] [ MFA] představuje další vrstvu zabezpečení pro přístup ke službám Azure. Azure MFA poskytuje silné ověřování s celou řadu možností snadno ověření – telefonní hovor, textová zpráva nebo oznámení mobilní aplikace, a povolit zákazníci zvolit metodu dávají přednost.

Všechny velký podnik musí definovat proces správy identit, které popisuje správu jednotlivých identit, jejich ověřování, autorizace, rolí a oprávnění v rámci nebo virtuálního. Pokud chcete zvýšit zabezpečení a produktivitu při snížení nákladů, výpadku a opakující se ruční úlohy by měl být cílů tohoto procesu.

Enterprise nebo organizace můžou vyžadovat náročné směs služby pro různé řádku z – firmy pole (LOB) a zaměstnanci často mají různé role při zapojená do různých projektů. Virtuálních řadičů domény vyžaduje funkční spolupráce mezi různými týmy, každý s definicemi konkrétní roli, chcete-li získat systémy s operačním systémem s funkčním zásad správného řízení. Matice odpovědnosti, přístupu a oprávnění může být velmi složité. Správa identit v vDC se implementuje pomocí [ *Azure Active Directory* (AAD)] [ AAD] a řízení přístupu na základě Role (RBAC).

Adresářová služba je infrastruktura sdílené informace pro hledání, správa, správě a uspořádání položek každý den a síťovým prostředkům. Tyto prostředky mohou zahrnovat svazky, složky, soubory, tiskárny, uživatelé, skupiny, zařízení a jiné objekty. Všechny prostředky v síti se považuje za objekt adresářovým serverem. Informace o zdroji, jsou uloženy jako kolekce atributů přiřazených k této prostředků nebo objekt.

Všechny služby Microsoft online obchodní spoléhají na Azure Active Directory (AAD) pro přihlášení a je třeba další identity. Azure Active Directory je komplexní, vysoce dostupné cloudové řešení pro správu identit a přístupu, které představuje kombinaci základních adresářových služeb, rozšířené správy identit a správy přístupu k aplikacím. AAD se dá integrovat s místní služby Active Directory umožňující jednotné přihlašování pro všechny cloudové a místní hostované (místní) aplikace. Atributy uživatele místní služby Active Directory lze automaticky synchronizovat aad.

Jeden globální správce není potřeba přiřadit všechna oprávnění v virtuálních řadičů domény. Místo toho každý určitého oddělení (nebo skupiny uživatelů nebo služeb v adresářové službě) může mít oprávnění potřebná ke správě svých vlastních prostředků v rámci virtuální kopie disků. Strukturování oprávnění vyžaduje vyrovnávání. Příliš mnoho oprávnění může mít dopad na výkon efektivitu a příliš málo nebo přijít oprávnění může zvýšit rizika zabezpečení. Chcete-li vyřešit tento problém, prostřednictvím nabídky vyladění správy přístupu pro prostředky vDC pomáhá Azure na základě rolí řízení přístupu (RBAC).

##### <a name="security-infrastructure"></a>*Infrastruktura zabezpečení*
Infrastruktura zabezpečení v souvislosti s vDC, souvisí především se oddělení přenosů dat v segmentu vDC konkrétní virtuální sítě a tom, jak řídit příchozí a odchozí toky v rámci virtuálního. Azure je založen na víceklientské architektuře, která brání neoprávněným i neúmyslně se překrývající provoz mezi nasazení, pomocí izolace virtuální síť (VNet), seznamy řízení přístupu (ACL), zatížení nástroje pro vyrovnávání a filtry IP, společně s zásady tok provozu. Překlad síťových adres (NAT) odděluje interní síťové přenosy od externích přenosů.

Prostředky infrastruktury Azure přiděluje prostředky infrastruktury pro úlohy klientů a spravuje komunikace do a z virtuálních počítačů (VM). Azure hypervisor vynucuje paměti a proces oddělení mezi virtuálními počítači a bezpečně trasy síťový provoz klientům hostovaného operačního systému.

##### <a name="connectivity-to-the-cloud"></a>*Připojení ke cloudu*
Virtuálního potřebuje připojení s externími sítěmi nabízet služby zákazníkům, partnerům a/nebo interní uživatele. To obvykle znamená připojení jenom k Internetu, ale taky do místní sítě a datacentry.

Zákazníci mohou vytvářet své zásady zabezpečení, které určují, co a jak konkrétní vDC hostované služby jsou přístupné z Internetu pomocí virtuálních síťových zařízení (s filtrování a provoz kontroly) a vlastní směrování zásady a sítě filtrování (směrování definované uživatele a skupiny zabezpečení sítě).

Podniky často potřebují pro připojení virtuálních místní datových centrech nebo jiným prostředkům. Připojení mezi Azure a místními sítěmi je proto velmi důležitý aspekt při navrhování efektivní architektura. Podniků má dva různé způsoby vytvoření propojení mezi vDC a místně v Azure: přenosu přes Internet nebo privátní přímé připojení.

[ **Azure VPN Site-to-Site** ] [ VPN] je služba propojení prostřednictvím Internetu mezi místními sítěmi a vDC, navázat prostřednictvím zabezpečeného šifrované připojení (tunelových propojení protokolu IPsec/IKE). Azure připojení Site-to-Site je flexibilní, rychle vytvořit a nevyžaduje žádné další nákup, protože všechna připojení připojit přes internet.

[**ExpressRoute** ] [ ExR] je služba Azure připojení, která umožňuje vytvářet privátní připojení mezi vDC a místní sítě. Připojení ExpressRoute nezadávejte přejděte prostřednictvím veřejného Internetu a nabízí vyšší zabezpečení, spolehlivost a vyšší rychlosti (až 10 GB/s) společně s konzistentní latence. ExpressRoute je velmi užitečná pro virtuálních, jako zákazníci můžete získat výhody pravidla dodržování předpisů, které jsou přidružené k privátní připojení ExpressRoute.

Nasazení připojení ExpressRoute zahrnuje zapojení u poskytovatele služeb ExpressRoute. Pro zákazníky, které je potřeba rychle začít je běžné původně používat Site-to-Site VPN k navázání připojení mezi virtuálního a místních prostředků a potom migrovat do připojení ExpressRoute.

##### <a name="connectivity-within-the-cloud"></a>*Připojení v rámci cloudu*
[Virtuální sítě] [ VNet] a [VNet Peering] [ VNetPeering] jsou základní síťové služby připojení uvnitř virtuální kopie disků. Virtuální síť, která zaručí přirozené hranice izolace pro prostředky v DC a partnerský vztah virtuální sítě umožňuje spojovacího mezi různých virtuálních sítí v rámci stejné oblasti Azure nebo dokonce i v jiných oblastech. Řízení přenosů dat uvnitř virtuální sítě a mezi virtuálními sítěmi musí odpovídat sada pravidel zabezpečení zadaná pomocí seznamů řízení přístupu ([skupinu zabezpečení sítě][NSG]), [síťových virtuálních zařízení][NVA]a vlastní směrovacích tabulek ([UDR][UDR]).

## <a name="virtual-data-center-overview"></a>Přehled virtuální datového centra

### <a name="topology"></a>Topologie
Model rozbočovače a koncových rozšířené virtuální datového centra v rámci jedné oblasti Azure

[![1]][1]

Rozbočovače je centrální zóny, která řídí a kontroluje příchozí a odchozí provoz mezi různým zónám: Internet, místní a větve. Hvězdicová topologie dává oddělení IT efektivní způsob, jak vynutit zásady zabezpečení v centrálním umístění, a současně potenciální chybné konfigurace a ohrožení.

Rozbočovač obsahuje společné součásti služby spotřebovávají větve. Tady je několik příkladů typické běžné centrální služby:

-   Infrastrukturu služby Active Directory systému Windows (s související služby AD FS), požadované pro ověřování uživatelů třetích stran, přístup k z nedůvěryhodné sítě před získáním přístupu k úlohy v ramenem
-   Služba DNS přeložit názvy pro zatížení v koncových, přístup k prostředkům na pracovišti a na Internetu
-   Infrastrukturu veřejných KLÍČŮ, k implementaci jednotného přihlašování na úlohy
-   Řízení toku (TCP/UDP) mezi koncových a Internet
-   Řízení toku mezi místními a ramenem
-   V případě potřeby toku řízení mezi jeden ramenem a další

Virtuálního snižuje celkové náklady na pomocí infrastruktury sdílené rozbočovače mezi více servery.

Role každý paprsek může být na hostiteli různé typy úloh. Větve můžete také zadat modulární přístup pro opakovatelná nasazení (například vývoje a testování, testování přijetí uživateli předprodukční režim a produkci) stejné úlohy. Větve lze také oddělit a povolte různé skupiny v rámci vaší organizace (například skupiny DevOps). Uvnitř ramenem je možné nasadit základní zatížení nebo složité úlohy několika vrstvami s řízení přenosů dat mezi vrstvami.

##### <a name="subscription-limits-and-multiple-hubs"></a>Limity předplatného a více rozbočovače
V Azure všechny komponenty jakéhokoli typu, nasadí předplatné Azure. Izolace součástí Azure v různých předplatných Azure můžete vyhovět jejich požadavkům různé objekty LOBs, jako je například nastavení různých úrovní přístupu a autorizaci.

Jeden vDC můžete škálovat velký počet koncových, i když stejně jako u každé IT systému existují omezení platformy. Nasazení centra je vázána na konkrétní předplatné Azure, který má omezení a limity (například maximální počet partnerských vztahů VNet - najdete v části [předplatného Azure a omezení služby, kvóty a omezení] [ Limits] podrobnosti). V případech, kdy omezení může být problém, můžete škálovat architekturu až další rozšířením modelu z jednoho rozbočovače koncových do clusteru s podporou rozbočovače a koncových. Pomocí partnerský vztah virtuální síť, ExpressRoute nebo VPN typu site-to-site, může být připojen více centra v jedné nebo více oblastech Azure.

[![2]][2]

Zavedení více centra zvyšuje náklady a správa úsilí systému a by pouze oprávněné podle škálovatelnost (příklady: omezení systému nebo redundance) a místní replikace (příklady: výkonu nebo havárii obnovení koncového uživatele). Ve scénářích nutnosti více rozbočovače, všechny rozbočovače měli snažit nabízejí stejnou sadu služeb pro provozní snadné.

##### <a name="interconnection-between-spokes"></a>Propojení mezi servery
V jednom ramenem je možné implementovat složité úlohy několika vrstev. Vícevrstvé konfigurace se dá implementovat pomocí podsítě (jeden pro každou vrstvu) ve stejné virtuální síti a filtrování toky pomocí skupin Nsg.

Na druhé straně na architekt chtít nasadit vícevrstvé zatížení mezi více virtuálních sítí. Pomocí virtuální sítě partnerský vztah, koncových můžete připojit k servery ve stejném centru nebo jiné rozbočovače. Typickým příkladem tohoto scénáře je případ, kde jsou servery aplikace zpracování v jedné ramenem (VNet), při nasazení databáze v různých ramenem (VNet). V takovém případě je snadné propojení koncových s partnerský vztah virtuální sítě a tím procházejících rozbočovače. Pečlivě zkontrolujte architektuře a zabezpečení je třeba provést k zajištění, že obcházení rozbočovače není vynechat důležité zabezpečení nebo auditování body, které může existovat pouze v rozbočovači.

[![3]][3]

Koncových může být taky připojen do ramenem, který funguje jako centrum. Tento postup vytvoří dvě úrovně hierarchie: ramenem na vyšší úrovni (úroveň 0) se rozbočovače nižší koncových (úroveň 1) v hierarchii. Větve vDC potřebovat pro přenos dat do centrální rozbočovač k oslovení buď k místní síti nebo Internetu. Architekturu s dvě úrovně rozbočovače představuje komplexní směrování, která odebere výhod vztahu jednoduché hvězdicovou rozbočovače.

Azure umožňuje komplexních topologiích, principů základní konceptu vDC sice opakovatelnost a jednoduchost. Minimalizovat úsilí vynaložené na správu, je jednoduchý rozbočovače ramenem doporučené vDC referenční architektura.

### <a name="components"></a>Komponenty
Virtuální datového centra se skládá ze čtyř typů základní součásti: **infrastruktury**, **hraniční sítě**, **úlohy**, a **monitorování**.

Každý typ součásti se skládá z různých funkce Azure a prostředky. Vaše vDC se skládá z instance více typů součásti a více variace stejný typ součásti. Například můžete mít velký počet instancí jiné, logicky oddělených zatížení, které představují různé aplikace. Použijete k výsledku sestavení virtuálního tyto typy různé součásti a instance.

[![4]][4]

Předchozí Architektura vysoké úrovně vDC ukazuje různé součásti typy používané v jiných zónách topologie rozbočovače koncových. Diagram zobrazuje součásti infrastruktury v různých částí architektury.

Pro přístup k osvědčených postupů (pro místní řadič domény nebo vDC) práva a oprávnění musí být na základě skupiny. Plánování práce se skupinami, místo jednotlivým uživatelům pomáhá zachování zásady přístupu konzistentně napříč týmy a pomůckách v minimalizovat chyby konfigurace. Přiřazení a odebrání uživatelů z příslušných skupin a pomáhá udržovat aktuální oprávnění konkrétních uživatelů.

Každou skupinu role by měl mít jedinečnou předponu na jejich názvy, a usnadňuje tak určit, které skupiny zatížením, které souvisí. Například zatížení hostování ověřovací služba možná skupiny s názvem *AuthServiceNetOps, AuthServiceSecOps, AuthServiceDevOps a AuthServiceInfraOps.* Stejně tak pro centralizované role nebo rolí, které nesouvisí s konkrétní službu, může být uvedena "Corp" *CorpNetOps* třeba.

Mnoho organizací použijte k poskytnutí hlavní rozdělení rolí varianta následujících skupin:

-   *Centrální skupinu IT (Corp)* má vlastnická práva k řízení součásti infrastruktury (například sítě a zabezpečení) a proto musí mít role Přispěvatel na předplatné (a mít kontrolu nad rozbočovače) a sítě oprávněním přispěvatele v větve. Velké organizace často rozdělit tyto odpovědnosti správy mezi několik týmů. například; Skupina síťových operací (CorpNetOps) (s výhradní aktivní sítě) a skupinu zabezpečení operací (CorpSecOps) (zodpovědná za zásady brány firewall a zabezpečení). V tomto případě konkrétní dvě různé skupiny musí vytvořit pro přiřazení vlastních rolí.
-   *Dev & testovací (AppDevOps) skupina* odpovídá za nasazení úloh (aplikace nebo služby). Tato skupina přebírá roli Přispěvatel virtuálních počítačů pro nasazení IaaS a/nebo jednu nebo více PaaS Přispěvatel rolí (najdete v části [předdefinované role pro řízení přístupu][Roles]). Volitelně může týmem vývojářů & test musí být viditelnost na zásady zabezpečení (Nsg) a zásady směrování (UDR) uvnitř rozbočovače nebo konkrétní ramenem. Kromě role Přispěvatel pro úlohy, proto tato skupina by také potřebovat roli čtečky sítě.
-   *Operace a údržba skupiny (CorpInfraOps nebo AppInfraOps)* mají odpovědnost za správu úlohy v produkčním prostředí. Tato skupina musí být Přispěvatel předplatné na úlohy v žádné produkční předplatné. Některé organizace mohou také měli zvážit, pokud je nutné skupinu team další eskalaci podpory k roli Přispěvatel předplatného v produkčním prostředí a v rámci předplatného centrální rozbočovač, aby bylo možné opravte potenciální problémy s konfigurací v provozním prostředí.

VDC strukturovaná tak, aby skupiny vytvořené pro centrální správu rozbočovače skupiny IT odpovídající skupiny na úrovni pracovního vytížení. Kromě správy prostředků rozbočovače by být schopné řídit nejvyšší úrovně oprávnění u předplatného a externí přístup jenom centrální skupiny IT. Skupiny úloh by však být schopné řídit prostředků a oprávnění své virtuální síti nezávisle na centrálního oddělení IT.

Virtuálního potřebuje k rozdělení na oddíly pro bezpečně hostování více projektů mezi různé řádku z – firmy (pole LOB). Všechny projekty vyžadují různé izolované prostředí (vývojářů, UAT, produkční). Samostatné předplatná Azure pro každou z těchto prostředích poskytují přirozené izolaci.

[![5]][5]

Předchozí diagram znázorňuje vztah mezi projekty v organizaci, uživatelé, skupiny a prostředí, kde jsou nasazeny komponenty Azure.

Obvykle v oddělení IT prostředí (nebo vrstvě) je systém, ve kterém je více aplikací, nasadit a spustit. Velké podniky použít vývojové prostředí (kde změny původně vytvářeny a testovány) a provozním prostředí (co koncoví uživatelé použít). Tato prostředí jsou často oddělené s několika přípravná prostředí mezi je umožnit postupné nasazování (zavedení), testování a vrácení zpět v případě problémů. Nasazení architektury výrazně liší, ale obvykle je stále následovaný základní proces začínající na vývoj (vývoj) a končí na produkční (PRODUKČNÍMU).

Běžné architektura pro tyto typy vícevrstvé prostředí se skládá z DevOps (vývoj a testování), UAT (pracovní) a provozní prostředí. Organizace mohou využívat jeden nebo více klientů Azure AD definovat přístup a práva do těchto prostředí. Předchozí diagram ukazuje případu tam, kde dva různé klienty Azure AD se používají: jeden pro DevOps a UAT a druhou výhradně pro produkční.

Přítomnosti různých Azure AD klienty vynucuje oddělení mezi prostředími. Stejnou skupinu uživatelů (jako třeba centrálního oddělení IT) musí provést ověření pomocí na jiný identifikátor URI pro přístup k jiné klient AD upravit role nebo oprávnění buď DevOps nebo produkční prostředí projektu. Přítomnost ověřování jiného uživatele pro přístup k různých prostředích snižuje možné výpadky a jiné potíže způsobené lidské chyby.

#### <a name="component-type-infrastructure"></a>Typ součásti: infrastruktury
Tento typ součásti je, kde se nachází většina podpůrné infrastruktury. Je také kde vaše centralizované IT, zabezpečení, nebo dodržování předpisů týmy tráví většinu své doby.

[![6]][6]

Součásti infrastruktury zajišťují propojení mezi různými součástmi služby vDC a jsou k dispozici v centru a větve. Centrální obvykle přiřazená zodpovědnost za správu a údržbu součásti infrastruktury IT nebo tým pro zabezpečení.

Jedním z primární úlohy týmu IT infrastruktury je zaručit konzistenci IP adres schémat celém podniku. Privátní IP adresní prostor virtuálních řadičů domény musí být v souladu se přiřadila a není překrývající se s privátní IP adresy přiřazené na vaše místní sítě.

Zatímco NAT hraniční směrovače místně nebo v prostředí Azure se můžete vyhnout konfliktům IP adres, přidá komplikace u součástí infrastruktury. Zjednodušení správy je jedním z klíče cílů virtuálních řadičů domény, takže použití překladu síťových adres pro zpracování obavy IP není doporučené řešení.

Součásti infrastruktury obsahují následující funkce:

-   [**Identitu a adresářové služby**][AAD]. Přístup pro každý typ prostředku v Azure je řízen pomocí identity uložené v adresářové službě. Adresářová služba ukládá pouze seznam uživatelů, ale také přístupová práva k prostředkům v rámci konkrétní předplatného Azure. Tyto služby může existovat jenom pro cloud nebo mohou být synchronizovány s identitou místně uložené ve službě Active Directory.
-   [**Virtuální síť**][VPN]. Virtuální sítě jsou jedním z hlavních komponent vDC a umožňují vytvořit hranici izolace přenosů na platformě Azure. Virtuální síť se skládá z jedné nebo více segmentech virtuální sítě, každý s konkrétní IP předpony sítě (podsítě). Virtuální síť definuje oblast interní hraniční kde virtuální počítače IaaS a PaaS služby můžete vytvořit privátní komunikaci. Virtuální počítače (a služby PaaS) v jedné virtuální sítě nemůže komunikovat přímo na virtuální počítače (a PaaS services) v jinou virtuální síť, i když jsou obě virtuální sítě vytvořené pomocí stejné zákazníka, v rámci stejného předplatného. Izolace je kritické vlastnosti, které zajišťuje, aby virtuální počítače zákazníka a komunikace zůstane privátní virtuální sítě.
-   [**UDR**][UDR]. Ve výchozím nastavení založené na systémovou tabulku směrování se směruje provoz ve virtuální síti. Trasy se definují uživatele je vlastní směrovací tabulku, která správci sítě můžete přidružit k jedné nebo několika podsítích přepsat chování systémovou tabulku směrování a zadejte cestu k komunikace v rámci virtuální sítě. Přítomnost udr zaručuje, aby odchozí provoz z přenosu ramenem prostřednictvím konkrétní vlastní virtuální počítače nebo virtuální zařízení sítě a nástroje pro vyrovnávání zatížení nachází v centru a větve.
-   [**NSG**][NSG]. Skupina zabezpečení sítě je seznam pravidel zabezpečení, které fungují jako provoz filtrování zdrojů IP, cílové IP, protokoly, porty zdrojové IP a cílové IP porty. NSG můžete použít k podsíti, karty virtuální síťovou kartu spojené s virtuální počítač Azure, nebo obojí. Skupin Nsg je nezbytné k implementaci správné toku řízení v centru a větve. Úroveň zabezpečení poskytované NSG je funkce, které porty, otevřete a pro jaké účely. Zákazníci by se měly používat filtry další jednotlivé virtuální počítače s založené na hostiteli brány firewall například IPtables nebo brány Windows Firewall.
-   [**DNS**][DNS]. Překlad prostředků do virtuální sítě vDC je zajišťováno prostřednictvím DNS. Azure poskytuje služby DNS pro obě [veřejné][DNS] a [privátní] [ PrivateDNS] překlad názvů. Privátní zón překladu názvů v rámci virtuální sítě i do virtuálních sítí. Privátní zón pouze rozpětí může mít virtuální sítí ve stejné oblasti, ale také napříč oblasti a odběry. Pro překlad IP adres veřejný Azure DNS poskytuje hostitelské služby domén DNS poskytnutí překladu názvů pomocí infrastruktury Microsoft Azure. Pokud svoje domény hostujete v Azure, můžete spravovat svoje DNS záznamy pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure.
-   [**Předplatné** ] [ SubMgmt] a [ **správu skupiny prostředků**][RGMgmt]. Předplatné definuje hranici přirozené vytvořit více skupin prostředků v Azure. Prostředky v předplatném se sestaví společně v logické kontejnery s názvem skupiny prostředků. Skupina prostředků představuje logické skupiny pro uspořádání prostředků virtuálních řadičů domény.
-   [**RBAC**][RBAC]. Prostřednictvím RBAC je možné mapy organizační roli společně s práva pro přístup k určité prostředky Azure, což umožňuje omezit přístup jenom určité podmnožiny akce uživatele. S RBAC můžete udělit přístup přiřazením příslušné role uživatele, skupiny a aplikace v rámci oboru relevantní. Předplatné Azure, skupinu prostředků nebo jediný zdroj, může být oboru přiřazení role. RBAC umožňuje dědičnosti oprávnění. Role přiřazené v nadřazeném oboru podřízené objekty jsou v něm obsažena také uděluje přístup. RBAC můžete oddělit povinností a poskytnout pouze takovou úroveň přístupu pro uživatele, kteří potřebují k provádění svých úloh. Například použijte funkci RBAC umožníte jednoho zaměstnance spravovat virtuální počítače v předplatném, zatímco jiné můžete spravovat databáze SQL v rámci stejného předplatného.
-   [**VNet Peering**][VNetPeering]. Základní funkce umožňuje vytvořit infrastrukturu vDC je ve stejné oblasti prostřednictvím sítě Azure datového centra, nebo pomocí páteřní strukturu Azure světě v oblastech sítě VNet partnerský vztah, mechanismus, který se připojuje dvě virtuální sítě (virtuální sítě).

#### <a name="component-type-perimeter-networks"></a>Typ součásti: Hraniční sítě
[Hraniční síť] [ DMZ] součásti (také označované jako zóna DMZ síť) umožňují poskytovat připojení k síti s místními nebo sítě fyzické datového centra, společně s žádné připojení do a z Internetu. Je také kde vaší sítě a zabezpečení pravděpodobně týmy tráví většinu jejich doby.

Příchozí pakety musí procházet skrz zabezpečovací zařízení v rozbočovači, jako je například Brána firewall, ID a IP adresy, dříve, než dorazila back-end serverů v větve. Internetový pakety z úlohy by také procházet skrz zabezpečovací zařízení v hraniční síti pro vynucení zásad, kontrolu a auditování, před opuštěním sítě.

Komponenty hraniční sítě poskytují následující funkce:

-   [Virtuální sítě][VNet], [UDR][UDR], [NSG][NSG]
-   [Virtuální síťové zařízení][NVA]
-   [Nástroj pro vyrovnávání zatížení][ALB]
-   [Aplikační brána][AppGW] / [firewall webových aplikací][WAF]
-   [Veřejné IP adresy][PIP]

Obvykle centrální IT a zabezpečení týmy mají odpovědnost za definice požadavek a operací hraniční sítě.

[![7]][7]

Na předchozím obrázku uvádí vynucení dvě okruhu s přístupem k Internetu a místní sítě, jak v centru trvalé. V jednom rozbočovači hraniční sítě do Internetu můžete postupně škálovat pro podporu velkého počtu objekty LOBs, pomocí více farmy brány Web Application firewall (WAFs) nebo brány firewall.

[**Virtuální sítě** ] [ VNet] rozbočovače je obvykle založený na virtuální síť s více podsítěmi k hostování různých typ služeb filtrování a kontroly přenosů dat do nebo z Internetu prostřednictvím NVAs, WAFs a Azure Application Gateway.

[**UDR** ] [ UDR] pomocí UDR zákazníci můžou nasazovat brány firewall, ID nebo IP adresy a jiné virtuální zařízení a směrovat síťový provoz prostřednictvím těchto zabezpečovací zařízení pro vynucení hranic zásad zabezpečení, auditování a kontroly. Udr lze vytvořit v centru a koncových zaručit, že tranzitů provoz přes konkrétní vlastní virtuální počítače, virtuální zařízení sítě a používané virtuálního nástroje pro vyrovnávání zatížení. Zaručit, že přenosy dat vytvářené z virtuálních počítačů v přenosu ramenem trvalé na správnou virtuální zařízení, UDR musí být nastavena v podsítě ramenem nastavování front-end IP adresy služby Vyrovnávání zatížení pro vnitřní jako další směrování. Nástroje pro vyrovnávání zatížení pro vnitřní distribuuje interní provoz na virtuální zařízení (fond back-end pro vyrovnávání zatížení).

[![8]][8]

[**Síťových virtuálních zařízení** ] [ NVA] v centru, v hraniční síti s přístupem k Internetu obvykle spravovat prostřednictvím farmu brány firewall nebo brány firewall systému webové aplikace (WAFs).

Různé objekty LOBs běžně používají mnoho webových aplikací a tyto aplikace jsou obvykle dochází z různých ohrožení zabezpečení a potenciální zneužití. Brány firewall webových aplikací je zvláštní druh produktu použít k detekci útoky na webové aplikace (HTTP či HTTPS) do větší hloubky než obecné brány firewall. Porovnání s tradiční Typografie technologii brány firewall, WAFs mají sadu konkrétní funkcí k ochraně před hrozbami interní webové servery.

Brány firewall farmy je skupina práci současně v rámci stejné společné správy sadu pravidel zabezpečení k ochraně zatížení hostovaná v koncových, brány firewall a řízení přístupu pro místní sítě. Brány firewall farmy má menší specializuje softwaru ve srovnání s firewall webových aplikací, ale má obor široký aplikace k filtrování a zkontrolovat libovolného typu provoz ve výstupní a vstupní. Brány firewall farmy jsou obvykle implementované v Azure pomocí síťových virtuálních zařízení (NVAs), které jsou k dispozici v Azure marketplace.

Doporučuje se použít jednu sadu NVAs pro přenosy na Internetu, a druhý pro provoz pocházející místně. Použití jen jednu sadu NVAs pro obě je bezpečnostní riziko, protože poskytuje žádné zabezpečení hraniční mezi dvěma sadami síťových přenosů. Pomocí samostatných NVAs snižuje složitost zabezpečení pravidla pro kontrolu a udělá z něj vymazat, která pravidla bude odpovídat které příchozí žádosti o síti.

Většina velké podniky spravovat víc domén. Azure DNS můžete použít k hostování záznamů DNS pro konkrétní doménu. Například můžete zaregistrovat virtuální IP adresa (VIP) Vyrovnávání zatížení Azure externí (nebo WAFs) ve záznam A záznam Azure DNS.

[**Azure Vyrovnávání zatížení** ] [ ALB] pro vyrovnávání zatížení Azure nabízí vysokou dostupnost služby vrstvy 4 (TCP, UDP), která můžete distribuovat příchozí komunikaci mezi instance služby, které jsou definované v sadě s vyrovnáváním zatížení. Data odesílaná do nástroje pro vyrovnávání zatížení z front-endu koncových bodů (veřejné koncové body IP nebo privátní IP koncových bodů) mohou být znovu distribuovány s nebo bez překladu adres k sadě fond back-end IP adres (příklady probíhá; Virtuální síťová zařízení nebo virtuálních počítačů).

Azure Vyrovnávání zatížení můžete testovat stav také různé instance serveru a když sondu přestane reagovat nástroje pro vyrovnávání zatížení zastaví odesílání provozu do instance není v pořádku. V vDC máme přítomnost externím vyrovnáváním zatížení v centru (například vyrovnávat přenosy do NVAs) a v koncových (k provedení úlohy, jako je vyrovnávání přenosů mezi různé virtuální počítače vícevrstvé aplikace).

[**Aplikační brána** ] [ AppGW] Microsoft Azure Application Gateway je vyhrazené virtuální zařízení poskytuje aplikace doručení řadiče (ADC) jako služba nabízí různé vrstvy 7 Vyrovnávání zatížení možnosti pro vaši aplikaci. Umožňuje optimalizovat webové farmy produktivitu přesměrováním zátěže procesoru náročné ukončení protokolu SSL pro službu application gateway. Nabízí také další možnosti přesměrování vrstvy 7, jako je kruhové dotazování na distribuci příchozích přenosů, spřažení relací na základě souborů cookie, přesměrování založené na cestách URL a možnost hostování několika webů za jedinou službou Application Gateway. Firewall webových aplikací (WAF) je také součástí skladové položky WAF služby Application Gateway. Tato SKU poskytuje ochranu k webovým aplikacím z běžných chyb zabezpečení webové a zneužití. Application Gateway je možné nakonfigurovat jako internetovou bránu nebo jen jako interní bránu, případně jako kombinaci obojího. 

[**Veřejné IP adresy** ] [ PIP] funkce některá Azure umožňují přidružení koncové body služby veřejné IP adresy umožňuje k prostředku získat přístup z Internetu. Tento koncový bod používá překládání adres (NAT) pro směrování provozu na interní adresu a port na virtuální síť Azure. Tato cesta je primární způsob pro externí přenos dat do virtuální sítě. Veřejné IP adresy se dá nakonfigurovat určit, jaký provoz, je předaná a jak a kde je přeložená k virtuální síti.

#### <a name="component-type-monitoring"></a>Typ součásti: monitorování
Monitorování komponenty poskytují viditelnost a výstrah z všechny ostatní typy součásti. Všechny týmy mají mít přístup k monitorování součástí a služeb, kterým mají přístup. Pokud máte centralizovanou pomoc HelpDesk nebo operations týmy, by musí mít přístup k datům poskytuje tyto součásti integrované.

Azure nabízí různé typy protokolování a monitorování služeb ke sledování chování Azure hostovaným prostředkům. Zásady správného řízení a kontrolu nad úlohy v Azure je na základě jenom na shromažďování dat protokolu, ale také schopnost akce aktivační události na základě konkrétní hlášené událostí.

[**Azure monitorování** ] [ Monitor] -Azure obsahuje více služeb, které jednotlivě provádět určité role nebo úkolu v prostoru pro monitorování. Společně tyto služby poskytovat komplexní řešení pro shromažďování, analýze a funguje na telemetrie z vaší aplikace a prostředky Azure, které je podporují. Může spolupracovat taky monitorovat kritické místních prostředků s cílem poskytnout hybridním monitorování prostředí. Pochopení nástroje a data, která jsou k dispozici je prvním krokem při vývoji dokončení strategie monitorování pro vaši aplikaci.

Existují dva hlavní typy protokolů v Azure:

-   [**Protokoly aktivity**][ActLog] (označované také jako "Operační protokol") umožní získat přehled o operace, které byly provedeny na prostředky v rámci předplatného Azure. Tyto protokoly sestavy události rovině řízení pro vaše předplatné. Každý prostředek Azure vytvoří protokoly auditu.

-   [**Azure diagnostické protokoly** ] [ DiagLog] protokoly generované prostředku, které poskytují bohatou a často data o operaci prostředku. Obsah tyto protokoly se liší podle typu prostředku.

[![9]][9]

V vDC je velmi důležité ke sledování protokolů Nsg, zejména tyto informace:

-   [**Protokoly událostí**][NSGLog]: poskytuje informace o jaké pravidla NSG se použijí u virtuálních počítačů a instance rolí na základě adresy MAC.
-   [**Čítač protokoly**][NSGLog]: sleduje kolikrát každého pravidla NSG byl proveden zakázat nebo povolit provoz.

Všechny protokoly mohou být uloženy v účtech úložiště Azure pro audit, statické analýzy nebo pro účely zálohování. Když protokoly jsou uložené v účtu úložiště Azure, můžete zákazníci používají různé typy rozhraní k načtení, Příprava, analýze a vizualizovat tato data do sestavy stavu a stavu prostředků cloudu.

Velké podniky by měl mít již získal standardní rozhraní pro monitorování místních systémů a můžete rozšířit dané platformy pro integraci protokoly Cloudová nasazení. Pro organizace, které chcete zachovat všechny protokolování v cloudu, je – Log Analytics [LogAnalytics] je služba skvělou volbou. Vzhledem k tomu, že analýzy protokolů je implementovaný jako cloudová služba, můžete jej budete mít spuštěný a funkční rychle s minimálním investice do infrastruktury služby. Analýzy protokolů můžete také integrovat s součástí produktu System Center, jako je například System Center Operations Manager k rozšíření stávajících investic správy do cloudu.

Analýzy protokolů je služba v Azure, který pomáhá shromažďování, korelovat, vyhledávání a fungovat na data protokolu a výkonu generovaných operačních systémů, aplikací a součástí infrastruktury cloudu. Nabízí zákazníkům v reálném čase statistiky provozu analyzovat všechny záznamy mezi všechny úlohy v virtuálních řadičů domény pomocí integrovaného hledání a vlastní řídicí panely.

[Monitorování výkonu v síti (NPM)] [ NPM] řešení uvnitř OMS může poskytovat sítě podrobné informace na kompletní, včetně jednoduchého zobrazení sítě Azure a místními sítěmi. S konkrétní monitorování pro ExpressRoute a veřejné služby.

#### <a name="component-type-workloads"></a>Typ součásti: úlohy
Zatížení součásti jsou, kde jsou umístěné vaše vlastní aplikace a služby. Je také kde vaše aplikace vývojové týmy tráví většinu jejich doby.

Možnosti zatížení jsou skutečně nekonečná. Následující je uvedeno několik možných zatížení typů:

**Interní obchodní aplikace**

Obchodní aplikace jsou aplikace počítače kritická pro probíhající operace podniku. OBCHODNÍCH aplikací mít některé společné vlastnosti:

-   **Interaktivní**. OBCHODNÍ aplikace, jsou interaktivní svou povahou: zadávání dat, a se vrátí výsledek nebo sestavy.
-   **Řízených daty**. Aplikace LOB jsou data s častým přístupem do databáze nebo jiného úložiště náročné.
-   **Integrované**. OBCHODNÍ aplikace nabídka integrace s jinými systémy uvnitř nebo vně organizace.

**Určeno webů (Internet nebo interní přístupem) pro odběratele** většina aplikací, které komunikovat přes Internet, jsou webové servery. Azure nabízí možnost spuštění webu na virtuální počítač IaaS nebo ze [Azure Web Apps] [ WebApps] lokality (PaaS). Azure Web Apps podporují integraci s virtuální sítě, které umožní nasazení webové aplikace v ramenem systému vDC. Při vyhledávání v interní přístupných weby, s integrací virtuální síť, není nutné vystavit Internet koncový bod pro vaše aplikace, ale můžete místo toho použít prostředkům přes privátní adresy směrovatelné Internetu jiných z vaší privátní virtuální sítě.

**Big Data nebo Analytics** když potřebuje vertikálně navýšit kapacitu na velmi velký objem dat, nemusí správně škálování databáze. Hadoop technologie nabízí systému ke spouštění distribuovaných dotazů paralelně na velký počet uzlů. Zákazníci, mají možnost spouštět úlohy dat. ve virtuálních počítačů IaaS nebo PaaS ([HDInsight][HDI]). HDInsight podporuje nasazování do virtuální sítě na základě polohy, se dá nasadit na cluster ramenem virtuálního.

**Události a zasílání zpráv**
[Azure Event Hubs] [ EventHubs] je služba přijímání velkého rozsahu telemetrii, která shromažďuje, transformuje a ukládá miliony událostí. Jako distribuované streamování platformu nabízí nízkou latencí a uchování nastavitelném časovém, což umožňuje ingestování masivní objemy telemetrická data do Azure a čtení dat z více aplikací. Službě Event Hubs může podporovat jeden datový proud v reálném čase i na základě batch kanály.

Vysoce spolehlivé cloudové služby mezi aplikací a služeb pro zasílání zpráv se dají implementovat pomocí [Azure Service Bus] [ ServiceBus] , nabízí asynchronní zprostředkované zasílání zpráv mezi klientem a serverem, spolu s strukturovaná možnosti zasílání zpráv a publikování a přihlášení k odběru objektů first in first out (FIFO).

[![10]][10]

### <a name="multiple-vdc"></a>Více vDC
Pokud má tento článek zaměřuje na jedné vDC, které popisují základní komponenty a architektura, která můžete přispět k odolné vDC. Funkce Azure, jako je například Azure zatížení vyrovnávání NVAs, skupiny dostupnosti sady škálování, společně s jiným mechanismem podílet se na systém, který vám umožní sestavit plnou SLA úrovně do služeb produkční.

Ale jeden vDC je hostovaná v jedné oblasti a je snadno napadnutelný hlavní výpadku, které by mohly ovlivnit danou celou oblast. Zákazníkům, kteří k dosažení vysoké SLA nutné k ochraně služeb prostřednictvím nasazení stejného projektu ve virtuálních dva (nebo více), umístěny v různých oblastech.

Kromě SLA obavy existuje několik běžné scénáře, kde nasazení více virtuálních dává smysl:

-   Místní nebo globální přítomnosti
-   Zotavení po havárii
-   Mechanismus pro přesměrovat provoz mezi řadiči domény

#### <a name="regionalglobal-presence"></a>Místní nebo globální přítomnosti
Datových center Azure jsou k dispozici v mnoha oblastech po celém světě. Když vyberete několik datových center Azure, zákazníky, který je potřeba zvážit dva faktory související s: zeměpisné vzdálenosti a latenci. Zákazníci se třeba vyhodnotit zeměpisné vzdálenost mezi virtuálních a vzdálenost mezi virtuálního a koncových uživatelích, a nabídnout nejlepších výsledků.

Oblast Azure, kde jsou hostované virtuálních také tak, aby odpovídala zákonným požadavkům vymezenému žádné právní jurisdikce, pod kterou vaše organizace provozuje potřebovat.

#### <a name="disaster-recovery"></a>Zotavení po havárii
Implementace plánu zotavení po havárii se týká silného typu příslušné úlohy a možnost synchronizace stavu zatížení mezi různé virtuálních. V ideálním případě většina zákazníků chcete synchronizovat data aplikací mezi systémem ve dvou různých virtuálních implementovat mechanismus rychlého převzetí služeb při selhání nasazení. Většina aplikací jsou citlivá na latenci a které můžou způsobit potenciální časový limit a prodlevu synchronizace dat.

Synchronizace nebo monitorování prezenčního signálu v různých virtuálních aplikací vyžaduje komunikaci mezi nimi. Dva uloženými v různých oblastech mohou připojené prostřednictvím:

-   Partnerský vztah VNet - virtuální síť partnerského vztahu můžete připojit rozbočovače v oblastech
-   ExpressRoute privátní partnerský vztah při vDC centrech jsou připojeny ke stejnému okruhu ExpressRoute
-   připojení přes vaše podnikové páteřní více okruhů ExpressRoute a vaší vDC mřížky připojené k okruhy ExpressRoute
-   Připojení Site-to-Site VPN mezi vaší vDC centra v každé oblasti Azure

Obvykle partnerský vztah virtuální síť nebo ExpressRoute připojení jsou upřednostňované mechanismu kvůli větší šířku pásma a konzistentní latence při přes páteřní společnosti Microsoft.

Neexistuje žádné magic recepturách k ověření aplikace distribuovány mezi dva (nebo více) různých virtuálních umístěné v různých oblastech. Zákazníci měli spustit testy kvalifikace sítě pro ověření latenci a šířky pásma připojení a cílová, zda je příslušná data synchronní nebo asynchronní replikaci a plánovanou dobu optimální obnovení (RTO), může být pro zatížení.

#### <a name="mechanism-to-divert-traffic-between-dc"></a>Mechanismus pro přesměrovat provoz mezi řadiči domény
Jeden efektivní technika, jak přesměrovat příchozí provoz v jeden řadič domény do jiné vychází z DNS. [Azure Traffic Manager] [ TM] používá k směrovat provoz koncového uživatele na nejvhodnější veřejný koncový bod v konkrétní vDC systému DNS (Domain Name). Prostřednictvím sondy Traffic Manager pravidelně kontroluje stav služby veřejné koncové body v různých virtuálních a v případě selhání těchto koncových bodů, směruje automaticky na sekundární virtuálních řadičů domény.

Traffic Manager lze například ovládací prvek nebo přesměrovat provoz na virtuálních počítačích Azure a webové aplikace v příslušné vDC a funguje na veřejné koncové body Azure. Traffic Manager odolný proti i při krátkodobém selhání celou oblast Azure a můžete řízení distribuce provozu generovaného uživateli pro koncové body služby v různých virtuálních na základě několika kritérií (například Chyba služby v konkrétní vDC nebo výběr virtuálního s nejnižší latenci sítě pro klienta).

### <a name="conclusion"></a>Závěr
Virtuální datového centra je přístup k migraci dat center do cloudu, který používá kombinaci funkcí a možností vytvoření škálovatelná architektura v Azure, který maximalizuje využití prostředků cloudu, snížení nákladů a zjednodušení zásad správného řízení systému. Koncept vDC je založena na topologii koncových rozbočovače, poskytuje společné sdílených služeb v centru a povolení konkrétní aplikace nebo zatížení v větve. V DC odpovídá struktuře role společnosti, kde různá oddělení (centrálního oddělení IT, DevOps, operace a údržba) pracují společně se na konkrétní seznam rolí a úloh. VDC splňuje požadavky pro migraci "Navýšení a Shift", ale také poskytuje celou řadu výhod nativní Cloudová nasazení.

## <a name="references"></a>Odkazy
Následující funkce byly popsané v tomto dokumentu. Kliknutím na odkazy na další informace.

| | | |
|-|-|-|
|Funkce sítě|Vyrovnávání zatížení|Připojení|
|[Virtuální sítě Azure][VNet]</br>[Skupiny zabezpečení sítě][NSG]</br>[Protokolů NSG][NSGLog]</br>[Směrování definované uživatelem][UDR]</br>[Virtuální síťová zařízení][NVA]</br>[Veřejné IP adresy][PIP]</br>[DNS]|[Pro vyrovnávání zatížení Azure (L3) ][ALB]</br>[Aplikační brána (L7) ][AppGW]</br>[Brány Firewall webových aplikací][WAF]</br>[Azure Traffic Manager][TM] |[Partnerský vztah virtuální sítě][VNetPeering]</br>[Virtuální privátní síť][VPN]</br>[ExpressRoute][ExR]
|Identita</br>|Monitorování</br>|Osvědčené postupy</br>|
|[Azure Active Directory][AAD]</br>[Vícefaktorové ověřování][MFA]</br>[Ovládací prvky přístupu na základě role][RBAC]</br>[Výchozí role AAD][Roles] |[Azure monitorování][Monitor]</br>[Protokoly aktivity][ActLog]</br>[Diagnostické protokoly][DiagLog]</br>[Microsoft Operations Management Suite][OMS]</br>[Sledování výkonu sítě][NPM]|[Hraniční sítě osvědčené postupy][DMZ]</br>[Správa předplatného][SubMgmt]</br>[Správa skupin prostředků][RGMgmt]</br>[Limity předplatného Azure][Limits] |
|Jinými službami Azure|
|[Webové aplikace Azure][WebApps]</br>[HDInsights (Hadoop) ][HDI]</br>[Event Hubs][EventHubs]</br>[Service Bus][ServiceBus]|



## <a name="next-steps"></a>Další kroky
 - Prozkoumejte [VNet Peering][VNetPeering], podpora technologie pro návrhy hvězdicové vDC
 - Implementace [AAD] [ AAD] začít pracovat s [RBAC] [ RBAC] zkoumání
 - Vyvinout model správy předplatného a prostředků a RBAC modelu ke splnění strukturu, požadavky a zásady vaší organizace. Plánování je nejdůležitější aktivity. Podobně jako praktické naplánujte pro uspořádání, při slučování, nové řádky produktu atd.

<!--Image References-->
[0]: ./media/networking-virtual-datacenter/redundant-equipment.png "příklady překrývají součásti" 
[1]: ./media/networking-virtual-datacenter/vdc-high-level.png "podrobný příklad hvězdicové vDC"
[2]: ./media/networking-virtual-datacenter/hub-spokes-cluster.png "clusteru rozbočovače a koncových"
[3]: ./media/networking-virtual-datacenter/spoke-to-spoke.png "ramenem ramenem"
[4]: ./media/networking-virtual-datacenter/vdc-block-level-diagram.png "úrovně Blokový diagram serveru virtuálních řadičů domény"
[5]: ./media/networking-virtual-datacenter/users-groups-subsciptions.png "uživatelů, skupin, odběry a projektů"
[6]: ./media/networking-virtual-datacenter/infrastructure-high-level.png "diagram základní infrastruktury"
[7]: ./media/networking-virtual-datacenter/highlevel-perimeter-networks.png "diagram základní infrastruktury"
[8]: ./media/networking-virtual-datacenter/vnet-peering-perimeter-neworks.png "partnerský vztah virtuální sítě a hraniční sítě"
[9]: ./media/networking-virtual-datacenter/high-level-diagram-monitoring.png "vysokoúrovňový diagram pro monitorování"
[10]: ./media/networking-virtual-datacenter/high-level-workloads.png "vysokoúrovňový diagram pro pracovní vytížení"

<!--Link References-->
[Limits]: https://docs.microsoft.com/azure/azure-subscription-service-limits
[Roles]: https://docs.microsoft.com/azure/role-based-access-control/built-in-roles
[VNet]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview
[NSG]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg
[DNS]: https://docs.microsoft.com/azure/dns/dns-overview
[PrivateDNS]: https://docs.microsoft.com/azure/dns/private-dns-overview
[VNetPeering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview 
[UDR]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview 
[RBAC]: https://docs.microsoft.com/azure/role-based-access-control/overview
[MFA]: https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication
[AAD]: https://docs.microsoft.com/azure/active-directory/active-directory-whatis
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways 
[ExR]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction 
[NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[SubMgmt]: https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-governance 
[RGMgmt]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview
[DMZ]: https://docs.microsoft.com/azure/best-practices-network-security
[ALB]: https://docs.microsoft.com/azure/load-balancer/load-balancer-overview
[PIP]: https://docs.microsoft.com/azure/virtual-network/resource-groups-networking#public-ip-address
[AppGW]: https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[WAF]: https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview
[Monitor]: https://docs.microsoft.com/azure/monitoring-and-diagnostics/
[ActLog]: https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs 
[DiagLog]: https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs
[NSGLog]: https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log
[OMS]: https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview
[NPM]: https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor
[WebApps]: https://docs.microsoft.com/azure/app-service/
[HDI]: https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-introduction
[EventHubs]: https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs 
[ServiceBus]: https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview
[TM]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview
