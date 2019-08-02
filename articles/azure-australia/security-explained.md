---
title: Vysvětlení zabezpečení pro Azure (Austrálie)
description: Informace o australských oblastech a splnění konkrétních požadavků na zásady, předpisy a právní předpisy australské vlády.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 212d14e31c152e50c216f2f34fb225c29fe054b5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571429"
---
# <a name="azure-australia-security-explained"></a>Vysvětlení zabezpečení pro Azure (Austrálie)

Tento článek popisuje některé běžné otázky a oblasti zájmu pro úřady australské vlády, které se vyšetřují, navrhují a nasazují do Microsoft Azure Austrálie.

## <a name="irap-and-certified-cloud-services-list-ccsl-documents"></a>Dokumenty IRAP a certifikovaných seznamů pro Cloud Services (CCSL)

Australský Internet Security Center (ACSC) poskytuje certifikát pro certifikaci, zprávu o certifikaci a uživatelský průvodce pro službu, když se přidá do CCSL.

Microsoft je aktuálně uvedený na CCSL pro Azure, Office 365 a Dynamics 365 CRM.

Společnost Microsoft zpřístupňuje naše dokumenty pro audit, posouzení a ACSC k dispozici zákazníkům a partnerům na straně [portálu Microsoft Service Trust](https://aka.ms/au-irap)na konkrétní Austrálii.

## <a name="dissemination-limiting-markers-dlm-and-protected-certification"></a>Značky omezení šíření (DLM) a CHRÁNĚNá certifikace

Proces používání systémů, včetně cloudových služeb schválených pro použití v organizacích státní správy, je definovaný v [příručce Information Security Manual (ISM)](https://acsc.gov.au/infosec/ism/) , kterou vytvořila a publikovala centrem acsc (australský Internet Security Center). Australský Internet Security Center (ACSC) je subjektem v rámci programu ASD, který je zodpovědný za certifikaci a zabezpečení v cloudu.

Proces schvalování má dva kroky:

1. Posouzení zabezpečení (IRAP) – proces, ve kterém registrovaní profesionálové vyhodnocují systémy, služby a řešení v rámci technického řízení v rámci ISM a vyhodnocují, zda byly ovládací prvky efektivně implementovány. Posouzení také identifikuje všechna konkrétní rizika pro schvalovací autoritu, aby bylo možné je před vydáním schválení provozu (ATO) zvážit.

1. Schválení k provozování – proces, kdy vedoucí pracovník státní správy formálně uznává a přijímá reziduální riziko systému pro informace, které zpracovává, ukládá a komunikuje.  Vstupem tohoto procesu je posouzení zabezpečení.

Posouzení služeb Azure na chráněné úrovni označuje, že implementace kontrolních mechanismů zabezpečení vyžadovaných pro ukládání a zpracování CHRÁNĚNých a níže uvedených dat byla potvrzena a efektivně funguje.

## <a name="australian-data-classification-changes"></a>Změny klasifikace australskéch dat

Od 1. října 2018 oznámilo oddělení obecného zabezpečení Obecné informace o PSPF (Security Policy Policy Framework), konkrétně o novém [citlivých a klasifikovaných informačních systémech](https://www.protectivesecurity.gov.au/information/sensitive-classified-information/Pages/default.aspx).

![Revidované klasifikace PSPF](media/pspf-classifications.png)

Všechny australské úřady a organizace, které provozují PSPF, mají vliv na tyto změny. Primární změna, která má dopad na naše aktuální certifikace IRAP/CCSL, je zastaralá o aktuální příznačení DLM (rozšiřování). Oficiální označení **: Důvěrné** nahradí různé DLMs používané pro ochranu citlivých informací. Tato změna také zavedla tři značky správy informací, které lze použít na všechny oficiální informace na všech úrovních citlivosti a klasifikace. **Chráněná** klasifikace zůstane beze změny.

Termín neklasifikovaný je odebraný z nového systému a termín neoficiální je použit pro informace mimo státní správu, i když nevyžaduje formální označení.

## <a name="choosing-an-azure-region-for-my-official-sensitive-and-protected-workloads"></a>Výběr oblasti Azure pro OFICIÁLNÍho: Citlivá a CHRÁNĚNá zatížení

Oficiální pracovník **Azure: Citlivé** a **chráněné** certifikované služby se nasazují do všech čtyř australských oblastí datových Center (Austrálie – východ, Austrálie – jih, Austrálie – střed a Austrálie – střed). certifikace ale vystavila Tato zpráva: ACSC doporučuje, aby se chráněná data nasadila do oblastí Azure Government v Canberra, pokud je zde k dispozici služba. Podrobnější informace o **chráněných** certifikovaných službách Azure jsou k dispozici [na stránce Austrálie na STP](https://aka.ms/au-irap).

>[!NOTE]
>Společnost Microsoft doporučuje, aby státní data všech Sensitivities a klasifikací byla nasazena do oblastí Austrálie (střední a Austrálie – střed), jak jsou navržena a provozována speciálně pro potřeby státní správy.

Další informace o zvláštní povaze Azure australských oblastí najdete na adrese [Azure pro oblasti Střední Austrálie](https://azure.microsoft.com/global-infrastructure/australia/).

## <a name="how-microsoft-separates-classified-and-official-data"></a>Jak společnost Microsoft odděluje klasifikovaná a oficiální data

Microsoft provozuje Azure a Office 365 v Austrálii jako v případě, že jsou všechna data citlivá nebo klasifikovaná, což zvyšuje naše ovládací prvky zabezpečení na tento velký pruh.

Infrastruktura podporující Azure potenciálně obsluhuje data s více klasifikací.  I když je naše předpokládá, že zákaznická data jsou klasifikována, jsou příslušné ovládací prvky vedeny jako takové. Microsoft přijal základní stav zabezpečení pro naše služby, které jsou v souladu s požadavky PSPF na ukládání a zpracování **chráněných** utajovaných informací.

Abychom zákazníkům zajistili, že jeden tenant v Azure nehrozí v nebezpečí od ostatních tenantů, Microsoft implementuje komplexní podrobné kontroly.

Nad rámec funkcí implementovaných v rámci Microsoft Azure platformy můžou další uživatelsky konfigurovatelné ovládací prvky, jako je šifrování pomocí klíčů spravovaných zákazníkem, vnořená virtualizace a přístup správce za běhu, dál snížit riziko. V rámci Azure Government oblasti Austrálie v Canberra se jedná o proces formálního přidávání na seznam povolených pouze australských & státních úřadů na Novém Zélandu a národních důležitých organizacích infrastruktury. Tento Cloud komunity poskytuje další záruky pro organizace, které jsou citlivé na rizika spolupracujících tenantů.

Zpráva o certifikaci Microsoft Azure Protected potvrzuje, že tyto ovládací prvky platí pro ukládání a zpracování **chráněných** klasifikovaných dat a jejich izolaci.

## <a name="relevance-of-irapccsl-to-state-government-and-critical-infrastructure-providers"></a>Relevance IRAP/CCSL pro státní státní správu a kritické poskytovatele infrastruktury

Mnoho státních státních institucí a důležitých poskytovatelů infrastruktury zakládá požadavky federální správy do svých zásad zabezpečení a bezpečnostního rozhraní. Tyto organizace také řídí **úřední**, **oficiální: Citlivá** a určitá množství **chráněných** utajovaných dat, ať už od jejich interakce s federální vládou nebo vlastními právy.

Australské vlády se stále stále zaměřuje na zásady a právní předpisy na ochraně neoficiálních dat, která mají zásadní dopad na bezpečnost a ekonomickou prosperitu Austrálie. Jako takové oblasti Austrálie Azure a certifikace CCSL jsou relevantní pro všechny tyto obory.

![Důležité sektory infrastruktury](media/nci-sectors.png)

Certifikace Microsoftu ukazují, že služby Azure byly podrobeny důkladnému, přísně a formálnímu posouzení ochrany zabezpečení a že byly schváleny pro zpracování těchto vysoce citlivých dat.

## <a name="location-and-control-of-microsoft-data-centres"></a>Umístění a kontrola datových center Microsoftu

Je povinným požadavkem na vládu a důležitou infrastrukturu, aby explicitně znala umístění datových center a vlastnictví pro cloudové služby zpracovávající svá data.  Microsoft je jedinečný jako poskytovatel cloudu s měřítkem a poskytuje rozsáhlé informace o těchto umístěních a vlastnictví.

Oblasti Austrálie Microsoftu (Austrálie – střed a Austrálie – střed) jsou provozovány v rámci zařízení pro datacentra CDC.  Vlastnictví DataCentre technologie CDC je australské řízení s 48% vlastnictvím ze služby Superannuation Corporation, 48% vlastnictvím z Infratil (na bázi NZ, duální Austrálie a Nový Zéland burzy cenných papírů, které jsou uvedené dlouhodobou infrastrukturou prostředků infrastruktury). a 4% australské správy.  

Správa datacentra CDC má na místě smluvní záruky, které omezují budoucí přenos vlastnictví a řízení. Tato průhlednost dodavatelských řetězců a vlastnictví prostřednictvím partnerství Microsoftu s datacentremi CDC je v souladu se zásadami [celé strategie hostování státní správy](https://www.dta.gov.au/our-projects/whole-government-hosting-strategy) a definicí **certifikovaného datacentra**.

## <a name="the-azure-services-that-are-included-in-the-current-ccsl-certification"></a>Služby Azure, které jsou zahrnuté v aktuální certifikaci CCSL

V červnu 2017 acsc Certified 41 služby Azure pro ukládání a zpracování dat **bez klasifikace: Úroveň** DLM. V dubnu 2018 byly pro **chráněná** klasifikovaná data certifikovány 24 těchto služeb.

Dostupnost certifikovaných služeb Azure pro ACSC v rámci našich oblastí Azure v Austrálii je následující: (tučně jsou certifikoványv chráněných verzích):

|Azure ve středových oblastech Austrálie|Jiné než regionální služby a jiné oblasti|
|---|---|
|API Management, App Gateway, Aplikační služby, **Automation, Azure Portal, Backup, Batch, Cloud Services**, Cosmos DB, Event Hubs, **ExpressRoute**, HDInsight, **Key Vault**, Load Balancer, Log Analytics, **Multi-Factor Ověřování**, Redis Cache, **Správce prostředků, Service Bus, Service Fabric, Site Recovery, SQL Database, úložiště**, Traffic Manager, **Virtual Machines, Virtual Network, VPN Gateway**|**Azure Active Directory**, CDN, Data Catalog, **Import exportu, Information Protection, služba IOT hub**, Machine Learning, Media Services, **Notification Hubs**, Power BI, **Security Center, Scheduler**, Search, Stream Analytics|
|

Microsoft zveřejňuje [přehled Microsoft Azure dodržování předpisů](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/44/Microsoft%20Azure%20Compliance%20Offerings.pdf) , které uvádí všechny služby v oboru pro všechny obory dodržování předpisů a hodnocení, které Azure prochází, včetně IRAP/CCSL.

## <a name="azure-service-not-listed-or-assessed-at-a-lower-level-than-i-need"></a>Služba Azure není uvedená nebo zhodnocena na nižší úrovni, než potřebuji.

Služby, které nejsou certifikované nebo které byly certifikovány v oficiálním umístění **: Citlivá** ,ale nechráněná, se dá použít společně nebo jako součást řešení hostujícího **chráněná** data, která poskytují tyto služby:
1. neukládají ani nezpracovávají **chráněná** data bez šifrování nebo
1. Dokončili jste posouzení rizik a schválili jste službu pro ukládání **chráněných** dat sami.

Pokud chcete použít službu, která není součástí CCSL k ukládání a zpracování oficiálních dat, můžete , ale certifikát ISM vyžaduje, abyste si ACSCi předem oznámili, že jste to provedli, než zadáte nebo obnovíte kontrakt s poskytovatelem cloudových služeb.

Všechny služby používané agenturou pro **chráněné** úlohy stále musí být vyhodnoceny a schváleny v souladu s procesy popsanými v části ISM a v procesu posouzení IRAP spravovaného agenturou v [strategii zabezpečeného cloudu odložených](https://www.dta.gov.au/files/cloud-strategy/secure-cloud-strategy.pdf)služeb.

![Proces certifikace strategie zabezpečení neaktivující cloudovou strategii](media/certification.png)

Společnost Microsoft průběžně vyhodnocuje naše služby, aby zajistila bezpečnost a vhodnost pro použití v australské vládě, takže se od Microsoftu dosáhnete, pokud potřebujete pomoc se službou, která aktuálně není v CCSL chráněná.

Vzhledem k tomu, že společnost Microsoft obsahuje řadu služeb certifikovaných na CCSL jak v neklasifikované **DLM** , tak i v **chráněných** klasifikacích, společnost ISM vyžaduje, abychom v každé dva roky vyplnili IRAP posouzení našich služeb. Microsoft se zavazuje každoroční posouzení, což je také příležitost zahrnout další služby k posouzení.

## <a name="certified-protected-gateway-in-azure"></a>Certifikovaná CHRÁNĚNá brána v Azure

Společnost Microsoft neprovozuje zabezpečenou internetovou bránu (SIG) certifikovaný státní správou v důsledku omezení počtu SIGs přípustných v rámci programu pro konsolidaci brány.  Ale očekávané a nezbytné funkce SIG je možné nakonfigurovat v rámci Microsoft Azure.

Prostřednictvím **chráněné** certifikace služeb Azure acsc má konkrétní doporučení pro účely připojení k Azure a při implementaci segmentace sítě mezi doménami zabezpečení, například mezi **chráněnými** a Internet. Mezi tato doporučení patří použití skupin zabezpečení sítě, bran firewall a virtuálních privátních sítí.  ACSC doporučuje použití zařízení virtuální brány. V Azure je k dispozici několik virtuálních zařízení, která mají fyzický ekvivalent na seznamu produktů s hodnocením ASD, nebo byly vyhodnoceny proti běžným profilům ochrany kritérií a jsou uvedeny na portálu Common kritéria. Tyto produkty vzájemně uznávají osoba ASD jako signatáři pro ujednání CCRA (Common kritéria Recognition).

Společnost Microsoft vytvořila pokyny k implementaci funkcí založených na Azure, které poskytují bezpečnostní funkce vyžadované k ochraně hranice mezi různými doménami zabezpečení, které jsou v kombinaci, tvořit ekvivalent k certifikovanému SIG. K dispozici je řada partnerů, kteří vám mohou pomoci s návrhem a implementací těchto funkcí a také s množstvím partnerských řešení, která jsou k dispozici.

## <a name="security-clearances-and-citizenship-of-microsoft-support-personnel"></a>Bezpečnostní prověrky a státní občanství pracovníků podpory Microsoftu

Společnost Microsoft pracuje s našimi službami globálně s využitím a vyškoleným personálem zabezpečení.  Zaměstnanci, kteří mají nedoprovázený fyzický přístup k zařízením v Sydney a Melbourne, mají za základnu základní bezpečnostní prověrky. Zaměstnanci v oblastech Austrálie Central a Austrálie – střední 2 mají minimální negativní dozvíte ČSFD 1 (NV1) volný prostor (podle potřeby pro **tajná** data). Tím zákazníkům zajistíte, že pracovníci v datových centrech s provozem Azure jsou vysoce důvěryhodné.

Microsoft má v rámci řízení přístupu na základě rolí nulovou zásadu přístupu s přístupem udělenou prostřednictvím systému, který je jen pro čtení, a stačí pouze v případě, že je dostatečná. V rozsáhlých případech můžou správci při odstraňování a údržbě služby vyžadovat přístup k zákaznickým datům ani oprávnění.  Vysoká úroveň automatizace a skriptování úloh pro vzdálené spuštění – negace nutnosti přímého přístupu k zákaznickým datům.

Oddělení právního programu pro obecné firmy potvrzuje, že zásady a postupy zabezpečení zaměstnanců společnosti Microsoft v rámci Azure jsou v souladu s záměrem přístupu PSPF k informacím v oblasti INFOSEC – 9.

## <a name="storing-international-traffic-of-arms-regulations-itar-or-export-administration-regulations-ear-data"></a>Ukládání mezinárodních přenosů v řádu zbraní (ITAR) nebo vývozních předpisů pro správní předpisy (ušní data)

Technické kontroly Azure, které pomáhají zákazníkům se splněním svých závazků pro exportovaná data, jsou v Azure stejné globálně. V důležitém případě není k dispozici žádné "ITAR/ušní pole", protože pro exportovaná data nejsou k dispozici žádná formální posouzení a certifikační architektura.

Pro Azure Government a státní správu USA pro Office 365 jsme měli k dispozici další smluvní a procesní opatření pro podporu zákazníků, kteří podléhají ovládacím prvkům pro export. Tyto další smluvní doložky a zaručená národní podpora a správa oblastí Azure pro Austrálii nejsou v platnosti.

To neznamená, že Azure v Austrálii nejde použít k ITARí nebo k dispozici, ale je potřeba jasně pochopit omezení, která vás zajímají prostřednictvím licence pro export, a před použitím Azure k uložení těchto povinností musíte implementovat další ochrany, abyste tyto závazky splnili. Tato data. Například může být nutné vytvořit státní příslušnost jako atribut do Azure Active Directory, použít Azure Information Protection k vymáhání pravidel šifrování nad daty a omezit je pouze na nás a jakékoli jiné státní příslušníky, které jsou součástí vývozní licence, před uložením do Azure Zašifrujte všechna data v místním prostředí, použijte klíč zákazníka nebo si podržíte vlastní klíč pro ITAR data a seznam se dostane do.....

Vzhledem k tomu, že ITAR není formální certifikace, je potřeba pochopit, jaká omezení a omezení jsou přidružená k licenci pro export, a pak s tím pracovat, jestli v Azure existují dostatečná řízení pro splnění těchto požadavků. V tomto případě jedním z problémů, které je potřeba pečlivě zvážit, je přístup našim inženýrům, kteří nemusí být státní příslušnost schválená v exportní licenci.

## <a name="next-steps"></a>Další postup

Přečtěte si článek o [službě azure VPN Gateway](vpn-gateway.md) pro konfiguraci a implementaci připojení VPN do Azure v Austrálii, která je kompatibilní se STANDARDem ISM.
