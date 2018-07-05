---
title: Virtuální datové centrum Azure – Z hlediska sítě
description: Další informace o vytváření vašeho virtuálního datového centra v Azure
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
ms.openlocfilehash: 2c8ca8bcce43596d521fa9c81438ac6a16f6dcdf
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445377"
---
# <a name="azure-virtual-datacenter-a-network-perspective"></a>Virtuální datové centrum Azure: Z hlediska sítě
**Microsoft Azure**: jděte rychleji dopředu, ušetřit peníze, integrace místních aplikací a dat

## <a name="overview"></a>Přehled
Migrace místních aplikací do Azure, i bez žádné významné změny (přístup známý jako "lift and shift"), poskytuje organizacím výhody zabezpečeného a cenově efektivní infrastruktury. Chcete-li maximálně využít flexibilitu možné díky cloud computingu, ale by se měl podniky vyvíjet jejich architektury, abyste mohli využívat možnosti Azure. Microsoft Azure poskytuje vysoce škálovatelné služby a infrastruktury, možnosti na podnikové úrovni a spolehlivost a spoustu možností pro hybridní připojení. Zákazníci můžou zvolit přístup k těmto cloudovým službám prostřednictvím Internetu nebo s využitím Azure ExpressRoute, které poskytuje připojení privátní sítě. Platforma Microsoft Azure umožňuje zákazníkům bezproblémově rozšířit svoji infrastrukturu do cloudu a vytvářet vícevrstvé architektury. Kromě toho partneři Microsoftu poskytují vylepšené funkce tím, že nabízí zabezpečení služeb a virtuálních zařízení, které jsou optimalizované pro spouštění v Azure.

Tento článek obsahuje přehled modelů a návrhů, které lze použít k řešení architektury škálování, výkon a zabezpečení nemuseli dělat starosti o mnoho se zákazníci setkávají při úvahách o kompletním do cloudu. Přehled o tom, jak začlenit různé role v IT organizaci do rozhraní pro správu a zásady správného řízení v systému je také popsáno, s důrazem na požadavky na zabezpečení a optimalizaci nákladů.

## <a name="what-is-a-virtual-data-center"></a>Co je virtuální datové centrum?
V těchto raných dobách Cloudová řešení byly navrženy k hostování jeden, relativně izolované aplikací v veřejné spektra. Tento přístup pracoval pro několik let. Však jako výhody cloudu se objevily řešení a více rozsáhlé úlohy byly hostované v cloudu, adresování zabezpečení, spolehlivost, výkon a náklady obavy z nasazení v jedné nebo více oblastí začal být důležité v celém životním cyklu cloudové služby.

Následující diagram nasazení cloudu dokládá několik příkladů mezery v zabezpečení (červeným rámečkem) a místa pro optimalizaci síťových virtuálních zařízení napříč úlohami (žlutým rámečkem).

[![0]][0]

Virtuální datové centrum (vDC) se zrodil z této nezbytná pro škálování pro podporu firemních procesů a není nutné řešit problémy při podpoře rozsáhlé aplikace ve veřejném cloudu.

VDC není právě úloh aplikací v cloudu, ale také sítě, zabezpečení, správy a infrastruktury (třeba DNS a Directory Services). Obvykle obsahuje také soukromého připojení zpět k místní síti nebo datového centra. Jako další a další úlohy přesunout do Azure, je důležité zvážit podpůrnou infrastrukturu a objekty, které tyto úlohy jsou umístěny v. Přemýšlet pečlivě o tom, jak jsou strukturované zdroje se můžete vyhnout tím, jak narůstá stovky ostrovů"úloh", které se musí spravovat odděleně nezávislý datový tok, modely zabezpečení a dodržování předpisů problémy.

Virtuální datové centrum je v podstatě kolekce samostatných, ale související entity, které běžné podpůrné funkce, funkce a infrastruktury. Zobrazením vašich úloh jako integrované vDC můžete zaznamenat snížení nákladů z důvodu cenové výhody optimalizované zabezpečení prostřednictvím centralizace komponent a datové tok společně s jednodušší operací, řízení a audity dodržování předpisů.

> [!NOTE]
> Je důležité informace o tom, že se virtuálního **není** samostatný produkt Azure, ale kombinace různých funkcí a možností pro splnění požadavků na přesný. vDC se způsobem přemýšlení o své pracovní postupy a využití Azure pro maximalizaci prostředků a možnosti v cloudu. Virtuální řadiče domény je proto modulární přístup o tom, jak sestavuješ IT služeb v Azure, ale současně zachovává organizační role a zodpovědnosti.

Virtuálního pomáhá podnikům získat úlohy a aplikace do Azure v následujících scénářích:

-   Hostování více souvisejících úloh
-   Migrace úloh z místního prostředí do Azure
-   Implementace sdílené nebo centralizované zabezpečení a požadavky na přístup napříč úlohami
-   Smíšené používání DevOps a centralizované IT odpovídajícím způsobem pro velké podniky

Klíč k odemknutí výhody vDC, je centralizované topologie (hvězdicové) s využitím kombinace funkce Azure: [virtuální sítě Azure][VNet], [skupin zabezpečení sítě] [ NSG], [VNet Peering][VNetPeering], [trasy definované uživatelem (UDR)][UDR]a Azure Identity s [základ rolí Přístup k řízení][RBAC].

## <a name="who-needs-a-virtual-data-center"></a>Kdo potřebuje virtuální datové centrum?
Kterou je potřeba přesunout více než několik úloh do Azure využívat přemýšlení o používání společných prostředků Azure zákazníka. V závislosti na velikosti se může přinést i jednotlivých aplikací pomocí vzorů a komponenty sloužící k sestavení vDC.

Pokud má vaše organizace centralizované zabezpečení IT, sítě, nebo tým nebo oddělení pro dodržování předpisů, vDC může pomoct vynutit zásady body, oddělení provozu a zajistit jednotnost základní běžné součásti přitom aplikační týmy co nejvíce volného a řídit, jak je vhodné pro vaše požadavky.

Organizace, které chcete DevOps se můžou využívat koncepty vDC poskytovat autorizovaným po kapsách prostředků Azure a zajistit, aby měly úplnou kontrolu v rámci této skupiny (buď předplatné nebo skupinu prostředků v předplatném běžné), ale v síti a hranice zabezpečení zajistěte dodržování předpisů v souladu s definicemi centralizovaných zásad ve virtuální síti centra a skupině prostředků.

## <a name="considerations-on-implementing-a-virtual-data-center"></a>Důležité informace o implementaci virtuální datové centrum
Při navrhování vDC, existují nejrůznější pivotal problémy, které byste měli zvážit:

-   Identitu a adresářové služby
-   Zabezpečení infrastruktury
-   Připojení ke cloudu
-   Připojení v rámci cloudu

##### <a name="identity-and-directory-service"></a>*Identitu a adresářové služby*
Klíčový aspekt všechna data jsou identitu a adresářové služby centra, v místním prostředí i v cloudu. Identity se vztahuje na všechny aspekty přístupu a autorizace pro služby v rámci virtuálního. K zajištění, že jenom Autorizovaní uživatelé a procesy přístup k účtu Azure a prostředků, Azure využívá několik typů přihlašovacích údajů pro ověřování. Patří mezi ně hesel (pro přístup k účtu Azure), šifrovací klíče, digitální podpisy a certifikáty. [*Azure Multi-Factor Authentication* (MFA)] [ MFA] představuje další vrstvu zabezpečení pro přístup ke službám Azure. Azure MFA poskytuje silné ověřování s řadou jednoduchých možností – telefonní hovor, textová zpráva nebo oznámení přes mobilní aplikaci – a umožňuje zákazníkům zvolit metodu, co jim vyhovuje.

Jakékoli velké organizace je potřeba definovat proces správy identit, které popisuje správu jednotlivých identit, jejich ověřování, autorizace, rolí a oprávnění v rámci nebo virtuálního. Cíle tohoto procesu musí být pro zvýšení zabezpečení a produktivitu při snížení nákladů, výpadky a opakující se ruční úlohy.

Organizace a může vyžadovat náročné kombinaci služeb pro různé řádku sady firmy (objekty LOBs) a zaměstnanci často mají rozdílné role, když péči o různých projektech. VDC vyžaduje funkční spolupráce mezi různými týmy, každý s definicemi konkrétní roli, chcete-li získat systémy s operačním systémem s dobré zásady správného řízení. V matici zodpovědnosti, přístupu a oprávnění, mohou být extrémně složitá. Správa identit v vDC se implementuje pomocí [ *Azure Active Directory* (AAD)] [ AAD] a řízení přístupu na základě Role (RBAC).

Adresářová služba je do sdílené informace infrastruktury pro hledání, správa, Správa a uspořádání položek každý den a síťové prostředky. Tyto prostředky mohou zahrnovat svazky, složky, soubory, tiskárny, uživatelé, skupiny, zařízení a dalších objektů. Každý prostředek v síti se považuje za objekt adresářovým serverem. Informace o prostředku se ukládá jako kolekce atributů přiřazených k prostředek nebo objekt.

Všechny Microsoft online firemní služby závisí na Azure Active Directory (AAD) pro přihlášení a potřebuje jinou identitu. Azure Active Directory je komplexní, vysoce dostupné cloudové řešení pro správu identit a přístupu, které představuje kombinaci základních adresářových služeb, rozšířené správy identit a správy přístupu k aplikacím. AAD je možné integrovat s místní Active Directory k povolení jednotného přihlašování pro všechny cloudové a místní prostředí (místní) aplikace. Atributy uživatele z místní služby Active Directory můžete automaticky synchronizovat s AAD.

Přiřadit všechna oprávnění v vDC není vyžadován jeden globální správce. Místo toho každý určitého oddělení (nebo skupiny uživatelů nebo služeb v adresářové službě) může mít oprávnění potřebná ke správě svoje vlastní prostředky v rámci vDC. Strukturování oprávnění vyžaduje vyrovnávání. Příliš mnoho oprávnění může bránit efektivity, výkonu a příliš málo nebo dojde ke ztrátě oprávnění mohou zvýšit rizika zabezpečení. Azure na základě rolí řízení přístupu (RBAC) pomáhá tento problém vyřešit tím, že nabízí propracovanou správu přístupu pro prostředky vDC.

##### <a name="security-infrastructure"></a>*Zabezpečení infrastruktury*
Infrastruktura zabezpečení v rámci vDC, hlavně má vztah k oddělení provozu vDC konkrétnímu virtuálnímu síťovému segmentu a tom, jak řídit příchozí a odchozí toky v rámci virtuálního. Azure je založen na víceklientské architektury, které brání neoprávněnému a nechtěnému přenosu mezi různými nasazeními, izolaci virtuálních sítí (VNet) používáte, seznamy řízení přístupu (ACL), zatížení nástroje pro vyrovnávání a filtrů IP adres spolu s zásady toku provozu. Překlad síťových adres (NAT) odděluje vnitřního síťového provozu z externích přenosů.

Prostředky infrastruktury Azure přiděluje prostředků infrastruktury pro úlohy klientů a spravuje komunikace do a z virtuálních počítačů (VM). Azure hypervisor vynucuje oddělení paměti a zpracování mezi virtuálními počítači a zajišťuje zabezpečení směrování síťového provozu do hostovaného operačního systému klientů.

##### <a name="connectivity-to-the-cloud"></a>*Připojení ke cloudu*
Virtuálního potřebuje připojení s externími sítěmi k nabízení služeb zákazníkům, partnerům a/nebo interní uživatele. To obvykle znamená připojení nejen k Internetu, ale také do místní sítě a datových center.

Zákazníci mohou vytvářet jejich zásady zabezpečení, které určují, co a jak konkrétní vDC hostované služby jsou přístupné z Internetu pomocí síťových virtuálních zařízení (pomocí filtrování a provoz kontroly) a vlastní směrování zásady a síť filtrování) Směrování definované uživatelem a skupin zabezpečení sítě).

Podniky se často potřebují k připojení virtuálních místních datových center nebo jiným prostředkům. Připojení mezi Azure a místní sítě je proto zásadní aspekt při navrhování efektivní architektura. Podniky mají dva různé způsoby vytváření propojení mezi vDC i v místním prostředí v Azure: přenosu přes Internet nebo privátní připojení s přímým přístupem.

[ **Azure VPN typu Site-to-Site** ] [ VPN] je propojení služby prostřednictvím Internetu mezi místními sítěmi a zašifrovaný vDC, navázané prostřednictvím zabezpečené připojení (tunelových propojení IPsec/IKE). Připojení Site-to-Site Azure je flexibilní, pokud chcete vytvořit, rychlý a nevyžaduje žádné další zakázek, všechna připojení připojit přes internet.

[**ExpressRoute** ] [ ExR] je služba Azure připojení, která umožňuje vytvářet privátní připojení mezi vDC a místními sítěmi. Připojení ExpressRoute nemáte se přenášejí prostřednictvím veřejného Internetu a nabízí vyšší zabezpečení, spolehlivost a vyšších rychlostí (až 10 GB/s) společně s konzistentní latence. ExpressRoute je velmi užitečné pro virtuálních, jako zákazníci mohou získat výhody pravidla dodržování předpisů, které jsou přidružené k privátní připojení ExpressRoute.

Nasazení připojení ExpressRoute vyžaduje zapojení poskytovatele služeb ExpressRoute. Pro zákazníky, které je potřeba rychle začít je běžné zpočátku použití sítě Site-to-Site VPN k navázání připojení mezi virtuálního a místním prostředkům a potom migrovat do připojení ExpressRoute.

##### <a name="connectivity-within-the-cloud"></a>*Připojení v rámci cloudu*
[Virtuální sítě] [ VNet] a [VNet Peering] [ VNetPeering] jsou základní síťové služby připojení uvnitř vDC. Přirozené hranice izolace vDC prostředků zaručuje virtuální sítě a partnerský vztah virtuální sítě umožňuje spojovacího mezi různých virtuálních sítích ve stejné oblasti Azure nebo dokonce napříč oblastmi. Řízení přenosů dat v síti VNet a mezi virtuálními sítěmi se musí shodovat sadu pravidel zabezpečení zadaná pomocí seznamů řízení přístupu ([skupinu zabezpečení sítě][NSG]), [síťových virtuálních zařízení ] [ NVA]a vlastní směrovací tabulky ([uživatelem definovaná TRASA][UDR]).

## <a name="virtual-data-center-overview"></a>Přehled virtuálního datového centra

### <a name="topology"></a>Topologie
Rozšířené hvězdicové model virtuálního datového centra v rámci jedné oblasti Azure

[![1]][1]

Centrum je ústřední zóny, která řídí a kontroluje příchozí nebo odchozí provoz mezi různými zónami: Internet, místní a aby se paprsky mezi. Hvězdicová topologie dává oddělení IT účinný způsob, jak vynutit zásady zabezpečení v centrálním umístění, a snižuje riziko chybné konfigurace a ohrožení.

Centrum obsahuje společné součásti služby používané paprsky. Tady je pár příkladů typické společných služeb, ústřední:

-   Infrastruktura Windows Active Directory (s související služby AD FS) požadované pro ověřování uživatelů přístup k z nedůvěryhodné sítě před získáním přístupu k úlohy v paprsku třetích stran
-   Služba DNS přeložit názvy pro zatížení v paprsky, pro přístup k prostředkům na pracovišti a na Internetu
-   Infrastrukturu veřejných KLÍČŮ, k implementaci jednotného přihlašování u úloh
-   Řízení toku (TCP/UDP) mezi paprsky a Internet
-   Tok řízení mezi paprsky i v místním
-   V případě potřeby tok řízení mezi jednoho paprsku a další

Virtuálního snižuje celkové náklady s využitím infrastruktury centra sdílené mezi více paprsků.

Tato role každého paprsku může být na hostiteli různé typy úloh. Paprsky může také poskytnout modulární přístup pro opakovatelná nasazení (například vývoj a testování, testování přijetí u zákazníků, předprodukčních a produkčních) ze stejné úlohy. Paprsky je také možné oddělit a povolení různých skupin v rámci vaší organizace (například DevOps skupiny). Uvnitř jednoho paprsku je možné nasadit základní úlohy nebo složitých několikaúrovňových úloh s řízení přenosů dat mezi vrstvami.

##### <a name="subscription-limits-and-multiple-hubs"></a>Limity předplatného a několika rozbočovače
V Azure všechny komponenty, bez ohledu typ nasazení v rámci předplatného Azure. Izolace komponenty Azure v různých předplatných Azure můžete splňovat požadavky různé objekty LOBs, jako je třeba nastavení různých úrovní přístupu a autorizace.

Jeden vDC vertikálně navýšit kapacitu až větší počet paprsků, i když stejně jako všechny systémy IT existují omezení platformy. Nasazení centra je vázán na konkrétní předplatné Azure, který má omezení a omezení (například maximální počet partnerských vztahů virtuální sítě – viz [předplatného Azure a limity, kvóty a omezení] [ Limits] podrobnosti). V případech, kdy omezení může být problém, můžete architektury škálovat nahoru ještě rozšířením modelu z jednoho Centrum – paprsky do clusteru hvězdicové. Pomocí VNet Peering, ExpressRoute nebo VPN typu site-to-site, může být připojen více centra v jedné nebo několika oblastech Azure.

[![2]][2]

Po zavedení služby více Center zvyšuje úsilí náklady a správa systému a by pouze zarovnané podle škálovatelnost (příklady: omezení systému nebo redundanci) a místní replikace (příklady: výkonu nebo havárii obnovení koncového uživatele). Ve scénářích, které vyžadují více rozbočovače, všechna centra přiklonit nabízí stejnou sadu služeb pro provozní snadné.

##### <a name="interconnection-between-spokes"></a>Propojení mezi paprsky
Uvnitř jednoho paprsku je možné provádět složité úlohy několika vrstev. Konfigurace úrovně je možné implementovat pomocí podsítí (jeden pro každou vrstvu) ve stejné virtuální síti a filtrování toky pomocí skupin zabezpečení sítě.

Na druhé straně architekta chtít nasadit vícevrstvé úlohy mezi několika virtuálními sítěmi. Pomocí partnerského vztahu virtuální sítě, můžete pro ostatní paprsky ve stejném centru nebo různými uzly připojení paprsků. Typickým příkladem tohoto scénáře je případ, ve kterém aplikace zpracování servery jsou v jednoho paprsku (VNet), při nasazení databáze v různých paprsku (VNet). V takovém případě je snadné propojení paprsky s využitím partnerského vztahu virtuální sítě a tím zabránit přechodem mezi protokoly prostřednictvím centra. Pečlivě zkontrolujte architektuře a zabezpečení je třeba provést k zajištění, že obcházení centra není obejít důležité zabezpečení nebo auditování body, které může existovat pouze v centru.

[![3]][3]

Paprsky může být také připojen do paprsku, který funguje jako centrum. Tento postup vytvoří dvojúrovňové hierarchii: paprsku na vyšší úrovni (úroveň 0) se Centrum nižší paprsky (úroveň 1) v hierarchii. Paprsky vDC potřebovat přesměrování provozu do centrálního bodu oslovit buď do místní sítě nebo Internetu. Architektura se dvěma úrovněmi centra představuje komplexní směrování, která odebere výhod vztahu jednoduché centra s paprsky.

I když vám Azure povolí složité topologie, je jedním z principů základní koncepce vDC opakovatelnost a jednoduchost. Chcete-li minimalizovat úsilí vynaložené na správu, je jednoduchý centra s paprsky doporučené vDC referenční architektura.

### <a name="components"></a>Komponenty
Virtuální datové centrum se skládá ze čtyř typů základní součást: **infrastruktury**, **hraniční sítě**, **úlohy**, a **monitorování**.

Každý typ součásti se skládá z různých prostředků a funkcí Azure. Vaše vDC se skládá z instance různé typy komponent a několik variant stejný typ součásti. Například může mít mnoho instancí jiné logicky oddělené, úlohy, které představují různé aplikace. Nakonec vytvářet virtuálního použijete tyto různé typy komponent a instancí.

[![4]][4]

Předchozí Základní architektura vDC znázorňuje různé typy komponent použít v různých oblastech topologie Centrum – paprsky. Diagram znázorňuje komponenty infrastruktury v různých částech architektury.

Jako přístup osvědčených postupů (pro místní řadič domény nebo vDC) práva a oprávnění by mělo být na základě skupin. Práce se skupinami, namísto jednotlivých uživatelů pomáhá Správa zásad přístupu konzistentně napříč týmy a pomáhá při minimalizovat chyby v konfiguraci. Přiřazení a odebrání uživatelů z příslušných skupin a pomáhá udržovat aktuální oprávnění konkrétního uživatele.

Každá skupina role by měla mít jedinečnou předponu pro jejich názvy, což usnadňuje identifikaci, která skupina je spojen s úlohou, které. Například zatížení, hostování ověřovací služba může mít skupiny s názvem *AuthServiceNetOps, AuthServiceSecOps, AuthServiceDevOps a AuthServiceInfraOps.* Podobně pro centralizované rolím nebo rolím pro konkrétní službu nesouvisí, může být uvozena řetězcem "Corp", *CorpNetOps* třeba.

Řada organizací používá k poskytování hlavní rozdělení rolí varianta z následujících skupin:

-   *Centrální skupina IT (Corp)* má vlastnická práva k řízení komponenty infrastruktury (například sítě a zabezpečení) a proto musí mít roli přispěvatele v předplatném (a mají kontrolu nad centra) a oprávněním přispěvatele sítě v paprsky. Velké organizace často rozdělit tyto povinnosti správy mezi několik týmů, jako; Skupina síťových operací (CorpNetOps) (s výhradním zaměřením na sítě) a skupinu zabezpečení operací (CorpSecOps) (zodpovědná za zásadu brány firewall a zabezpečení). V tomto konkrétním případě dvou různých skupin muset vytvořit pro přiřazení vlastních rolí.
-   *Dev & testu (AppDevOps) skupiny* odpovídá za nasadit úlohy o velikosti (aplikacím nebo službám). Tato skupina má role Přispěvatel virtuálních počítačů pro nasazení IaaS a/nebo jednu nebo více PaaS Přispěvatel rolí (viz [předdefinované role pro řízení přístupu][Roles]). Volitelně vývojového a testovacího týmu muset mají viditelnost na zásady zabezpečení (Nsg) a zásady směrování (UDR) do rozbočovače nebo konkrétní paprsku. Kromě role přispěvatele pro úlohy, proto se tato skupina budete také potřebovat role čtečky sítě.
-   *Operace a údržba skupiny (CorpInfraOps nebo AppInfraOps)* mají na starost správu úloh v produkčním prostředí. Tato skupina musí být přispěvatelem předplatného u úloh v jakékoli předplatná pro produkční prostředí. Některé organizace může také měli zvážit, pokud potřebují skupinu týmu další eskalaci podpory s rolí přispěvatele předplatného v produkčním prostředí a v daném předplatném centrální rozbočovač, abyste mohli vyřešit potenciální problémy s konfigurací v produkční prostředí.

Strukturovaná vDC tak, aby skupiny vytvořené pro centrální správu centra skupiny IT odpovídajících skupin na úrovni zpracovávaných úloh. Kromě správy prostředků centra by centrální skupiny IT řídit externí přístup a oprávnění nejvyšší úrovně v rámci předplatného. Skupiny úloh, ale bude řídit prostředků a oprávnění své virtuální síti nezávisle na z centrálního oddělení IT.

Virtuálního potřebuje k rozdělení na oddíly pro bezpečné hostování více projektů přes různé řádku sady firmy (objekty LOBs). Všechny projekty vyžadují různé izolovaného prostředí (vývoj, UAT, produkčním prostředí). Samostatné předplatná Azure pro každou z těchto prostředí zajišťují izolaci fyzické.

[![5]][5]

Předchozí diagram znázorňuje vztah mezi projekty v organizaci, uživatelé, skupiny a prostředí, ve které jsou nasazené komponenty Azure.

Obvykle v IT prostředí (nebo vrstvy) je systém, ve kterém se nasazují a spuštění více aplikací. Velké podniky pomocí vývojového prostředí (změny původně provedli a testování) a produkčním prostředí (co koncoví uživatelé použít). Těchto prostředích jsou často oddělené několik přípravných prostředí mezi ho, aby povolil postupného nasazení (zavádění), testování a vrácení zpět v případě problémů. Nasazení architektury výrazně lišit, ale obvykle je stále a potom základní proces počínaje development (vývoj) a konče v produkčním prostředí (produkční).

Běžné architektury pro tyto typy vícevrstvé prostředí se skládá z DevOps (vývoj a testování), UAT (pracovní) a produkční prostředí. Organizace můžou využívat jeden nebo více tenantů Azure AD k definování přístup a práva k těmto prostředím. Předchozí diagram ukazuje případ tam, kde dva různé tenanty Azure AD se používají: jeden pro DevOps a UAT a další výhradně pro produkční prostředí.

Přítomnost z různých služeb Azure AD tenanty zajišťuje oddělení mezi prostředími. Stejnou skupinu uživatelů (například z centrálního oddělení IT) potřebuje ověřit pomocí jiného identifikátoru URI pro přístup k na jiného tenanta AD upravit role nebo oprávnění buď DevOps nebo produkční prostředí projektu. Přítomnost jiné ověření uživatele pro přístup k jednotlivým prostředím snižuje případnému výpadku a další potíže způsobené službou lidské chyby.

#### <a name="component-type-infrastructure"></a>Typ součásti: infrastruktury
Tento typ součásti je, ve které se nachází většina podpůrnou infrastrukturu. Je také kde vaše centralizované IT, zabezpečení, nebo dodržování předpisů týmy tráví většinu svého času.

[![6]][6]

Součásti infrastruktury poskytují propojení mezi různými součástmi vDC a jsou k dispozici v centru a aby se paprsky mezi. Centrální obvykle přiřazená zodpovědnost za správu a údržbu součásti infrastruktury IT a/nebo bezpečnostní tým.

Jedním z primární úlohy IT infrastruktury tým je zaručit konzistenci schémat IP adres napříč celým podnikem. Privátní IP adresa adresní prostor přiřazený k vDC musí být konzistentní vzhledem k aplikacím a ne překrývá s privátními IP adresami přiřazenými na vaše místní sítě.

Při překladu adres na místní hraniční směrovače nebo v prostředí Azure můžete nedošlo ke konfliktům IP adres, přidá komplikace u součástí infrastruktury. Zjednodušení správy je jedním z klíčových cílů vDC, tak použití NAT pro zpracování obavy IP není doporučená řešení.

Součásti infrastruktury obsahují následující funkce:

-   [**Identitu a adresářové služby**][AAD]. Přístup pro všemi typy prostředků v Azure je řízen identitu uložená v adresářové službě. Adresářová služba ukládá pouze seznam uživatelů, ale také přístupová práva k prostředkům v rámci konkrétního předplatného Azure. Tyto služby může existovat jenom pro cloud, nebo mohou být synchronizovány s do místních identit, které jsou uložené ve službě Active Directory.
-   [**Virtuální síť**][VPN]. Virtuální sítě jsou jedním z hlavních komponentách vDC a umožňují vytvářet hranicí izolace provozu na platformě Azure. Virtuální síť se skládá z jednoho nebo více segmentů virtuální sítě, každý s určitou předponou IP sítě (podsítě). Virtuální síť vymezuje interní hraniční oblast, kde virtuální počítače IaaS a PaaS služby můžete vytvořit privátní komunikaci. Virtuální počítače (a služby PaaS) v jedné virtuální síti nemohou komunikovat přímo do virtuálních počítačů (a služby PaaS) v jiné virtuální síti, i když obě virtuální sítě jsou vytvořeny tentýž zákazník pod stejné předplatné. Izolace je důležité vlastnost, která zajišťuje virtuální počítače zákazníků a komunikace zůstávají privátní virtuální sítě.
-   [**UDR**][UDR]. Ve výchozím nastavení založené na systémovou tabulku směrování se směruje provoz ve virtuální síti. Trasy se definují uživatele je vlastní směrovací tabulku, která správci sítě můžete přidružit jen k jedné nebo několika podsítí přepsat chování systémovou tabulku směrování a definovat komunikační trasa virtuální sítě. Přítomnost trasy definované uživatelem zaručuje této výchozí přenos z přenosu paprsku po konkrétní vlastní virtuální počítače a/nebo síťových virtuálních zařízení a nástroje pro vyrovnávání zatížení k dispozici v centru a v paprsky.
-   [**NSG**][NSG]. Skupina zabezpečení sítě je seznam pravidel zabezpečení, které se chovají jako provoz filtrování podle zdroje IP, cílové IP, protokoly, porty zdrojové IP a cílové IP porty. Skupiny zabezpečení sítě lze použít k podsíti, karty virtuální síťovou kartu spojené s Virtuálním počítači Azure, nebo obojí. Skupiny zabezpečení sítě jsou nezbytné k implementaci ovládacího prvku správné toku v centru a v paprsky. Úroveň zabezpečení poskytované skupiny zabezpečení sítě je funkce, které porty otevřete a pro jaké účely. Zákazníci by měli použít filtry další jednotlivé virtuální počítače založené na hostiteli brány firewall například IPtables nebo brány Windows Firewall.
-   [**DNS**][DNS]. Překlad prostředků v rámci virtuální sítě vDC je zajišťováno prostřednictvím DNS. Azure poskytuje služby DNS pro obě [veřejné][DNS] a [privátní] [ PrivateDNS] překlad názvů. Privátní zóny poskytují překlad v rámci virtuální sítě a mezi virtuálními sítěmi. Mezi virtuálními sítěmi ve stejné oblasti, ale také napříč oblastmi a předplatné může mít pouze span privátních zón. Pro veřejné rozlišení poskytuje Azure DNS hostitelská služba určená pro domény DNS a zajišťuje překlad názvů využitím infrastruktury Microsoft Azure. Pokud svoje domény hostujete v Azure, můžete spravovat svoje DNS záznamy pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure.
-   [**Předplatné** ] [ SubMgmt] a [ **Správa skupin prostředků**][RGMgmt]. Předplatné definuje přirozené hranice v Azure vytvořit více skupin prostředků. Prostředky v předplatném jsou složeny dohromady v logické kontejnery s názvem skupiny prostředků. Skupina prostředků představuje logické skupiny k uspořádání prostředků vDC.
-   [**RBAC**][RBAC]. Pomocí RBAC je možné do mapy organizační role spolu s práva pro přístup ke konkrétním prostředkům Azure, abyste mohli omezit uživatele na jenom určité podmnožiny akce. Pomocí RBAC můžete udělit přístup přiřazením příslušné role uživatele, skupiny nebo aplikace v rámci oboru relevantní. Obor přiřazení role může být předplatné Azure, skupinu prostředků nebo jediný prostředek. RBAC umožňuje dědičnost oprávnění. Role přiřazená na nadřazeném oboru uděluje přístup také podřízené objekty jsou v něm obsaženy. RBAC můžete oddělit a udělit pouze takovou úroveň přístupu pro uživatele, kteří potřebují k provádění svých úloh. Například pomocí RBAC nechat jednoho zaměstnance spravovat virtuální počítače v předplatném, zatímco jiné můžete spravovat databáze SQL v rámci stejného předplatného.
-   [**VNet Peering**][VNetPeering]. Základní funkce použité k vytvoření infrastruktury vDC je VNet Peering, mechanismus, který spojuje dvě virtuální sítě (Vnet) ve stejné oblasti prostřednictvím sítě Azure datového centra nebo napříč oblastmi pomocí páteřní síť Azure na celém světě.

#### <a name="component-type-perimeter-networks"></a>Typ součásti: Hraniční sítě
[Hraniční síť] [ DMZ] komponenty (označované také jako DMZ sítě) umožňují poskytovat připojení k síti s místním nebo fyzické sítě System center, spolu s žádné připojení k Internetu nebo z něj. Je také kde vaší sítě a zabezpečení pravděpodobně týmy tráví většinu svého času.

Příchozí pakety jakým způsobem se předávají prostřednictvím zabezpečovací zařízení v rozbočovači, jako je například Brána firewall, ID a IP adresy, před dosažením back-end serverů v paprsky. Pakety směřující na Internet z úlohy byste také tok prostřednictvím zabezpečovací zařízení v hraniční síti pro vynucení zásad, kontrolu a auditování, před opuštěním sítě.

Komponenty hraniční sítě poskytují následující funkce:

-   [Virtuální sítě][VNet], [uživatelem definovaná TRASA][UDR], [skupiny zabezpečení sítě][NSG]
-   [Síťové virtuální zařízení][NVA]
-   [Nástroj pro vyrovnávání zatížení][ALB]
-   [Služba Application Gateway][AppGW] / [WAF][WAF]
-   [Veřejné IP adresy][PIP]

Obvykle centrální IT a zabezpečení týmy mají odpovědnost za operace hraniční sítě a definice požadavků.

[![7]][7]

Předchozí diagram znázorňuje vynucení dvě perimetry s přístupem k Internetu a v místní síti, jak v centru. V jednom rozbočovači hraniční sítě do Internetu vertikálně navýšit kapacitu pro podporu velkého počtu objekty LOBs, použití více farmy brány firewall webových aplikací (Waf) a/nebo brány firewall.

[**Virtuální sítě** ] [ VNet] rozbočovače obvykle postavena virtuální sítě s několika podsítěmi hostovat jiný typ služby, filtrování a že provoz do nebo z Internetu prostřednictvím síťových virtuálních zařízení, brány Waf a Azure Application Gateway.

[**Uživatelem definovaná TRASA** ] [ UDR] pomocí uživatelem definovaná TRASA, zákazníci můžou nasadit brány firewall, IDS/IPS a dalších virtuálních zařízení a směrovat síťový provoz prostřednictvím těchto zabezpečovací zařízení pro vynucení hranic zásad zabezpečení, auditování a kontroly. V centru a aby se paprsky mezi zaručit, že tranzitů provoz přes konkrétní vlastní virtuální počítače, síťová virtuální zařízení a nástroje pro vyrovnávání zatížení používá virtuálního možné vytvářet trasy definované uživatelem. Pokud chcete zajistit přenosy generované z virtuálních počítačů v paprsku přenosu na správnou virtuální zařízení, trasu UDR se musí nastavit v podsítích paprsků nastavením front-endovou IP adresu interního nástroje jako další směrování. Interní služby load balancer distribuuje interního provozu do virtuálních zařízení (back endového fondu nástroje pro vyrovnávání zatížení).

[![8]][8]

[**Síťová virtuální zařízení** ] [ NVA] v rozbočovači, hraniční síti s přístupem k Internetu obvykle spravuje prostřednictvím farmy brány firewall a/nebo brány firewall webových aplikací (Waf).

Různé objekty LOBs běžně používají mnoho webových aplikací a tyto aplikace jsou obvykle dochází z různých ohrožení zabezpečení a potenciální zneužití. Brány firewall webových aplikací je zvláštní druh produkt slouží ke zjištění útoků, které webové aplikace (HTTP/HTTPS) do větší hloubky než obecné brány firewall. Ve srovnání s technologií tradici brány firewall, řešení Waf mají sadu specifické funkce ochrany před hrozbami interní webové servery.

Brány firewall farmy je skupina práci při vytvoření celostní v rámci stejné běžné práci se sadou pravidel zabezpečení k ochraně úloh hostovaných v paprsky, brány firewall a řízení přístupu k místním sítím. Brána firewall farmy má méně specializovanou software ve srovnání s WAF, ale má obor široké aplikace můžete filtrovat a kontrolovat libovolného typu v příchozího a odchozího provozu. Farmy brány firewall jsou obvykle implementovány v Azure prostřednictvím síťových virtuálních zařízení (Nva), které jsou dostupné v Tržišti Azure marketplace.

Doporučuje se použít jedna sada síťových virtuálních zařízení pro přenosy z Internetu, a jinou pro přenosy pocházející místní. Použití jenom jedné sady síťových virtuálních zařízení pro oba představuje bezpečnostní riziko, protože nabízí neexistuje žádná bezpečnostní hranice mezi těmito dvěma sadami síťových přenosů. Pomocí samostatných síťových virtuálních zařízení snižuje složitost pravidel pro kontrolu zabezpečení a vyjasňuje, která pravidla platí pro které příchozí žádosti v síti.

Většina velkých podniků spravovat víc domén. Azure DNS umožňuje hostovat záznamy DNS pro konkrétní doménu. Například lze registrovat virtuální IP adresa (VIP) Azure externím vyrovnáváním zatížení (nebo brány Waf) v záznamu A záznam Azure DNS.

[**Nástroj Azure Load Balancer** ] [ ALB] nástroje pro vyrovnávání zatížení Azure nabízí vysokou dostupnost služby vrstvy 4 (TCP, UDP), která může distribuce příchozího provozu mezi instance služby, které jsou definovány v sadě s vyrovnáváním zatížení. Data odesílaná do nástroje pro vyrovnávání zatížení z front-endových koncových bodů (veřejné koncové body IP nebo privátní koncovými body IP) mohou být znovu distribuovány s nebo bez překladu adres k sadě fond back-end IP adres (příklady se; Síťová virtuální zařízení nebo virtuálních počítačů).

Nástroj Azure Load Balancer můžete testovat stav také různé instance serveru, a když sonda přestane reagovat, nástroj pro vyrovnávání zatížení zastaví odesílání provozu do instance není v pořádku. V vDC máme přítomnost externím vyrovnáváním zatížení v centru (například vyrovnávat provoz do síťových virtuálních zařízení) a aby se paprsky mezi (provádět úkoly, jako je vyrovnávání mezi virtuálními počítači v různých vícevrstvé aplikace).

[**Služba Application Gateway** ] [ AppGW] Microsoft Azure Application Gateway je vyhrazené virtuální zařízení poskytující kontroler doručování aplikací (ADC) jako službu, nabízí různé vrstvy 7 Vyrovnávání zatížení možnosti pro vaši aplikaci. To umožňuje optimalizovat produktivitu webové farmy tím, že se ukončování náročné na CPU SSL ke službě application gateway. Nabízí také další možnosti přesměrování vrstvy 7, jako je kruhové dotazování na distribuci příchozích přenosů, spřažení relací na základě souborů cookie, přesměrování založené na cestách URL a možnost hostování několika webů za jedinou službou Application Gateway. Firewall webových aplikací (WAF) je také součástí skladové položky WAF služby Application Gateway. Tato skladová položka zajišťuje ochranu webových aplikací před běžnými webovými ohroženími zabezpečení a zneužitím. Application Gateway je možné nakonfigurovat jako internetovou bránu nebo jen jako interní bránu, případně jako kombinaci obojího. 

[**Veřejné IP adresy** ] [ PIP] funkce některé Azure umožňují přiřadit koncových bodů služby pro veřejné IP adresy, které umožňuje na váš prostředek přistupuje z Internetu. Tento koncový bod používá překládání adres (NAT) Pokud chcete směrovat provoz na interní adresu a port ve službě Azure virtual network. Tato cesta je hlavní způsob, jak pro externí přenos dat do virtuální sítě. Veřejné IP adresy se dá určit, jaký provoz je předáno a jak a kde je přeložen do virtuální sítě.

#### <a name="component-type-monitoring"></a>Typ součásti: monitorování
Monitorování komponenty nabízejí viditelnost a výstrah z všechny ostatní typy komponenty. Všechny týmy mají mít přístup k monitorování pro komponenty a služby, kterým mají přístup. Pokud máte centralizovanou pomoc HelpDesk nebo operace týmy by potřebují integrovanou přístup k datům poskytuje tyto komponenty.

Azure nabízí různé druhy protokolování a monitorování služeb ke sledování chování Azure hostovala prostředky. Zásady správného řízení a kontrolu nad úloh v Azure je na základě jenom na shromažďování dat protokolu, ale také schopnost aktivujte akce na základě konkrétních ohlášené událostí.

[**Azure Monitor** ] [ Monitor] -Azure zahrnuje několik služeb, které jednotlivě provádět konkrétní role nebo úkolu v prostoru pro monitorování. Tyto služby společně poskytují komplexní řešení pro shromažďování dat, analýzy a akce na základě telemetrie z vaší aplikace a prostředků Azure, které je podporují. Můžou také provádět monitorování důležitých místních prostředků s cílem poskytovat prostředí hybridního monitorování. Důkladné seznámení s dostupnými nástroji a daty je prvním krokem při vývoji úplné strategie monitorování pro vaši aplikaci.

Existují dva hlavní typy protokolů v Azure:

-   [**Protokoly aktivity**][ActLog] (označované také jako "Operační protokol") umožní získat přehled o operace, které byly provedeny na prostředky v rámci předplatného Azure. Tyto protokoly hlášení událostí rovina řízení pro vaše předplatná. Každý prostředek Azure vytvoří protokoly auditu.

-   [**Diagnostické protokoly Azure** ] [ DiagLog] jsou protokoly generované prostředek, které poskytují bohatě vybaveným a časté informace o fungování tohoto prostředku. Obsah tyto protokoly se liší podle typu prostředku.

[![9]][9]

V vDC je velmi důležité pro sledování protokolů skupiny zabezpečení sítě, zvláště tyto informace:

-   [**Protokoly událostí**][NSGLog]: poskytuje informace o jaká pravidla skupiny zabezpečení sítě se použijí na virtuální počítače a instance rolí na základě adresy MAC.
-   [**Čítač protokoly**][NSGLog]: sleduje, kolikrát se každé pravidlo NSG provedl zakázat nebo povolit provoz.

Všechny protokoly mohou být uloženy v účtech úložiště Azure pro audit, statické analýzy nebo zálohování. Když protokoly ukládají v účtu služby Azure storage, Zákazníci můžete použít různé typy architektur pro načtení, přípravu, analyzovat a vizualizovat tato data hlásit stav a stav prostředků cloudu.

Velké podniky musí už získali standardní rozhraní pro místní systémy pro monitorování a můžete rozšířit tímto rozhraním integrovat protokoly generované nasazeními v cloudu. Pro organizace, které chcete zachovat všechny protokolování v cloudu, – Log Analytics [LogAnalytics] je skvělou volbou. Od Log Analytics je implementovaná jako cloudová služba, můžete mít ji zprovoznit rychle s minimálními investicemi do infrastrukturních služeb. Log Analytics můžete také integrovat s komponentami nástroje System Center, jako je System Center Operations Manager a rozšířit vaše stávající investice do správy do cloudu.

Log Analytics je služba v Azure, která pomáhá shromažďovat, korelovat, vyhledávat a reagovat na data protokolů a výkonu generovaných operačních systémů, aplikací a komponent infrastruktury cloudu. Poskytuje zákazníkům provozní informace v reálném čase pomocí integrovaného vyhledávání a vlastních řídicích panelů k analýze všech záznamů napříč vašimi úlohami vDC.

[Network Performance Monitor (NPM)] [ NPM] řešení v OMS může poskytovat podrobné sítě informace end až do konce, včetně ucelený přehled o sítích Azure a místními sítěmi. S konkrétní monitory pro ExpressRoute a veřejné služby.

#### <a name="component-type-workloads"></a>Typ součásti: úlohy
Úloha součásti jsou, kde jsou umístěné vaše vlastní aplikace a služby. Je také kde vaše aplikace vývojové týmy tráví většinu svého času.

Možnosti úlohy jsou skutečně nekonečné. Tady jsou jen některé typy možných úloh:

**Interní obchodní aplikace**

Obchodní aplikace jsou aplikací pro počítače kritická pro probíhající operace podniku. OBCHODNÍ aplikace mají některé společné vlastnosti:

-   **Interaktivní**. OBCHODNÍ aplikace jsou interaktivní povahou: Zadaná data a výsledek nebo sestavy jsou vráceny.
-   **Na základě dat**. OBCHODNÍ aplikace jsou data s častým přístupem do databáze nebo jiného úložiště náročné.
-   **Integrované**. OBCHODNÍ aplikace nabídka integrace s jinými systémy v rámci nebo mimo organizaci.

**Webové stránky (internetové nebo interní přístupem) určených pro zákazníky** většina aplikací, které komunikují s Internetem jsou webové servery. Azure nabízí možnost spustit webový server na Virtuálním počítači IaaS nebo ze [Azure Web Apps] [ WebApps] lokality (PaaS). Azure Web Apps nepodporují integraci s virtuálními sítěmi, které umožní nasazení webové aplikace v paprsku vDC. Při pohledu na interní různé weby, integrace virtuální sítě, není potřeba zveřejnit koncový bod Internet pro vaše aplikace, ale můžete místo toho použít prostředky prostřednictvím privátních adres směrovatelný bez Internetu z vaší privátní virtuální sítě.

**Analýza velkých objemůdat/** když je potřeba vertikálně navýšit kapacitu na velmi velké objemy dat, nemusí správně škálování databáze. Technologie Hadoop poskytuje systém ke spouštění distribuovaných dotazů paralelně na velký počet uzlů. Zákazníci mají možnost spouštět úlohy dat ve virtuální počítače IaaS nebo PaaS ([HDInsight][HDI]). HDInsight podporuje nasazování do virtuální sítě založená na poloze, je možné nasadit do clusteru v paprsku virtuálního.

**Události a zasílání zpráv**
[Azure Event Hubs] [ EventHubs] je služba příjmu telemetrických dat vysoce škálovatelné, který shromažďuje, transformuje a ukládá miliony událostí. Jako platforma pro distribuované streamování nabízí nízkou latenci a konfigurovatelné časové uchovávání, která vám umožní ingestovat velké objemy telemetrických dat do Azure a načtení dat z více aplikací. S Event Hubs může jeden datový proud podporovat kanály v reálném čase i na základě služby batch.

Vysoce spolehlivé cloudové služby mezi aplikacemi a službami, zasílání zpráv je možné implementovat pomocí [Azure Service Bus] [ ServiceBus] , že nabízí asynchronní zasílání zprostředkovaných zpráv mezi klientem a serverem, spolu s strukturované zasílání zpráv first-in-first-out (FIFO) a možnosti publikace nebo odběru.

[![10]][10]

### <a name="multiple-vdc"></a>Více vDC
Zatím Tento článek se zaměřuje na jedné vDC, popisující základních komponent a architektury, které přispívají k odolné vDC. Funkce Azure, jako je Azure load balancer, síťová virtuální zařízení, skupiny dostupnosti, škálovací sady, společně s další mechanismy přispívat do systému, který umožňuje vytvářet plné úrovně SLA do produkčního prostředí služeb.

Však jeden vDC hostována v rámci jedné oblasti a je zranitelný vůči velký výpadek, který může mít vliv na tuto celou oblast. Zákazníci, kteří k dosažení vysoké smlouvy o úrovni služeb potřebují chránit služby prostřednictvím nasazení stejného projektu ve virtuálních dva (nebo více), umístěné v různých oblastech.

Rovněž na starosti smlouvy SLA existuje několik běžných scénářů, kde nasazení více virtuálních dává smysl:

-   Místní a globální přítomnosti
-   Zotavení po havárii
-   Mechanismus pro rozdělení provozu mezi řadiče domény

#### <a name="regionalglobal-presence"></a>Místní a globální přítomnosti
Dat do Datacenter Azure jsou k dispozici v mnoha oblastech po celém světě. Při výběru více datových centrech Azure, zákazníci musí vzít v úvahu dva faktory související s: geografické vzdálenosti a latenci. Zákazníci se musí vyhodnotit geografické vzdálenosti mezi virtuálních a vzdálenost mezi virtuálního a koncovým uživatelům nabídnout nejlepší uživatelské prostředí.

Oblast Azure, kde jsou hostované virtuálních je také potřeba splňovat zákonné požadavky stanovené žádné právní jurisdikci, pod kterým vaše organizace pracuje.

#### <a name="disaster-recovery"></a>Zotavení po havárii
Implementace plánu zotavení po havárii se týká silného typu úlohy obavy a možnost synchronizace stavu úloh mezi různých virtuálních. V ideálním případě by většina zákazníků chcete synchronizovat data aplikací mezi spuštěných ve dvou různých virtuálních implementovat mechanismus rychlé převzetí služeb při selhání nasazení. Většina aplikací jsou citlivá na latenci, a to může způsobit potenciální časový limit a zpoždění synchronizace dat.

Synchronizace nebo monitorování prezenčních signálů v různých virtuálních aplikací vyžaduje komunikace mezi nimi. Prostřednictvím dá připojit dva virtuálních v různých oblastech:

-   Partnerský vztah virtuální sítě – vytvoření partnerského vztahu virtuální sítě můžete připojit rozbočovače napříč oblastmi
-   ExpressRoute privátní partnerský vztah při vDC hubs jsou připojené ke stejnému okruhu ExpressRoute
-   připojení víc okruhů ExpressRoute prostřednictvím podnikové páteřní a vaše síť vDC připojení ke okruhy ExpressRoute
-   Připojení Site-to-Site VPN mezi vaší vDC hubs v každé oblasti Azure

Obvykle připojení VNet Peering nebo ExpressRoute jsou upřednostňované mechanismu kvůli větší šířku pásma a konzistentní latence při přes páteřní infrastrukturu Microsoftu.

Neexistuje žádný recept magic k ověření aplikace distribuovat mezi dva (nebo více) různých virtuálních umístěné v různých oblastech. By měli zákazníci spouštět sítě kvalifikace testů pro ověření latencí a šířkou pásma připojení a cíl Určuje, zda je příslušná data synchronní nebo asynchronní replikace a plánovaná doba optimální obnovení (RTO), může být pro vaše úlohy.

#### <a name="mechanism-to-divert-traffic-between-dc"></a>Mechanismus pro rozdělení provozu mezi řadiče domény
Jeden efektivní technikou k rozdělení příchozí provoz v jeden řadič domény do druhého vychází z DNS. [Azure Traffic Manager] [ TM] používá mechanismus systému DNS (Domain Name) směruje provoz koncového uživatele na nejvhodnější veřejný koncový bod v konkrétní vDC. Pomocí sond Traffic Manager pravidelně kontroluje stav služby veřejné koncové body v různých virtuálních a v případě selhání těchto koncových bodů, směruje automaticky na sekundární vDC.

Traffic Manager pracuje na veřejné koncové body Azure a je možné, například do ovládacího prvku nebo přesměrovat provoz do virtuálních počítačů Azure a Web Apps v příslušné vDC. Traffic Manager je odolné, dokonce i v případě selhání celé oblasti Azure zůstane a můžete řídit distribuci uživatelského provozu do koncových bodů služby v různých virtuálních na základě několika kritérií (například Chyba služby v konkrétní vDC nebo výběrem virtuálního s nejnižší síťovou latencí pro klienta).

### <a name="conclusion"></a>Závěr
Virtuální datové centrum je přístup k přenesení datového centra do cloudu, který používá kombinaci funkcí a možností vytvoření škálovatelnou architekturu v Azure, která maximalizuje využití prostředků cloudu, snížení nákladů a zjednodušení systému zásad správného řízení. Koncept vDC podle topologie Centrum – paprsky, poskytuje společné sdílené služby v centru a povolení konkrétní aplikace a úlohy v paprsky. VDC odpovídá struktuře rolí společnosti, kde různá oddělení (z centrálního oddělení IT, DevOps, operace a údržba) spolupracují, každý s konkrétní seznam rolí a povinností. VDC splňuje požadavky pro migrace "metodou Lift a Shift", ale také nabízí celou řadu výhod pro nativní cloudové nasazení.

## <a name="references"></a>Odkazy
Následující funkce byly probírané v tomto dokumentu. Kliknutím na odkazy na další.

| | | |
|-|-|-|
|Funkce sítě|Vyrovnávání zatížení|Připojení|
|[Virtuální sítě Azure][VNet]</br>[Skupiny zabezpečení sítě][NSG]</br>[Protokoly skupiny zabezpečení sítě][NSGLog]</br>[Směrování definované uživatelem][UDR]</br>[Síťová virtuální zařízení][NVA]</br>[Veřejné IP adresy][PIP]</br>[DNS]|[Azure Load Balancer (L3) ][ALB]</br>[Služba Application Gateway (L7) ][AppGW]</br>[Firewall webových aplikací][WAF]</br>[Azure Traffic Manageru][TM] |[VNet Peering][VNetPeering]</br>[Virtuální privátní síť][VPN]</br>[ExpressRoute][ExR]
|Identita</br>|Monitorování</br>|Osvědčené postupy</br>|
|[Azure Active Directory][AAD]</br>[Ověřování službou Multi-Factor Authentication][MFA]</br>[Řízení přístupu na základní role][RBAC]</br>[Výchozí role AAD][Roles] |[Azure Monitor][Monitor]</br>[Protokoly aktivit][ActLog]</br>[Diagnostické protokoly][DiagLog]</br>[Microsoft Operations Management Suite][OMS]</br>[Network Performance monitoru][NPM]|[Hraniční sítě, osvědčené postupy][DMZ]</br>[Správa předplatného][SubMgmt]</br>[Správa skupin prostředků][RGMgmt]</br>[Limity předplatného Azure][Limits] |
|Další služby Azure|
|[Azure Web Apps][WebApps]</br>[HDInsights (Hadoop) ][HDI]</br>[Event Hubs][EventHubs]</br>[Service Bus][ServiceBus]|



## <a name="next-steps"></a>Další kroky
 - Prozkoumejte [VNet Peering][VNetPeering], rozšiřováním technologie pro návrhy vDC střed a paprsek
 - Implementace [AAD] [ AAD] začít s [RBAC] [ RBAC] průzkum
 - Vývoj modelu správy předplatného a prostředků a RBAC model pro splnění strukturu, požadavky a zásad vaší organizace. Plánování je nejdůležitější aktivit. Podobně jako praktické plánování uspořádání, spojení, nové řádky produktu, atd.

<!--Image References-->
[0]: ./media/networking-virtual-datacenter/redundant-equipment.png "příklady překrytí komponenty" 
[1]: ./media/networking-virtual-datacenter/vdc-high-level.png "podrobný ukázkový hvězdicové vDC"
[2]: ./media/networking-virtual-datacenter/hub-spokes-cluster.png "clusteru rozbočovače a paprsky"
[3]: ./media/networking-virtual-datacenter/spoke-to-spoke.png "paprsku – paprsky"
[4]: ./media/networking-virtual-datacenter/vdc-block-level-diagram.png "úrovně diagram virtuálního bloku"
[5]: ./media/networking-virtual-datacenter/users-groups-subsciptions.png "uživatelů, skupin, předplatných a projekty"
[6]: ./media/networking-virtual-datacenter/infrastructure-high-level.png "diagram základní infrastruktury"
[7]: ./media/networking-virtual-datacenter/highlevel-perimeter-networks.png "diagram základní infrastruktury"
[8]: ./media/networking-virtual-datacenter/vnet-peering-perimeter-neworks.png "partnerský vztah virtuálních sítí a hraniční sítě"
[9]: ./media/networking-virtual-datacenter/high-level-diagram-monitoring.png "vysokoúrovňový diagram pro monitorování"
[10]: ./media/networking-virtual-datacenter/high-level-workloads.png "vysokoúrovňový diagram úloh"

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
