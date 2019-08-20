---
title: Vysvětlení zabezpečení pro Azure (Austrálie)
description: Informace o australských oblastech a splnění konkrétních požadavků na zásady, předpisy a právní předpisy australské vlády.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 79f5773cf6f4906f93a5b95ce5c042da09c789fc
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575457"
---
# <a name="azure-australia-security-explained"></a>Vysvětlení zabezpečení pro Azure (Austrálie)

Tento článek popisuje některé běžné otázky a oblasti zájmu pro australské vlády pro státní správu, které se prošetří s využitím, návrhu a nasazením Microsoft Azure Austrálie.

## <a name="irap-and-certified-cloud-services-list-documents"></a>IRAP a certifikovaný seznam dokumentů Cloud Services

Australský Internet Security Center (ACSC) poskytuje certifikát pro certifikaci, zprávu o certifikaci a zákaznickou příručku pro službu, která se přidá do seznamu certifikovaných Cloud Services (CCSL).

Microsoft je aktuálně uvedený na CCSL pro Azure, Office 365 a Dynamics 365 CRM.

Společnost Microsoft zpřístupňuje naše dokumenty pro audit, posouzení a ACSC k dispozici zákazníkům a partnerům na straně [portálu Microsoft Service Trust](https://aka.ms/au-irap)na konkrétní Austrálii.

## <a name="dissemination-limiting-markers-and-protected-certification"></a>Rozšiřování značek omezujících a CHRÁNĚNých certifikátů

Proces používání systémů, včetně cloudových služeb schválených pro použití v rámci státních organizací, je definovaný v [příručce Information Security Manual (ISM)](https://acsc.gov.au/infosec/ism/) , která je vytvořená a publikovaná pomocí acsc. ACSC je subjektem v rámci australských signálů (ASD), který je zodpovědný za vydávání certifikátů a certifikace v cloudu.

Proces schvalování má dva kroky:

1. **Posouzení zabezpečení (IRAP)** : Proces, ve kterém registrovaní profesionálové vyhodnocují systémy, služby a řešení v rámci technických kontrol v rámci ISM a vyhodnocují, zda byly ovládací prvky efektivně implementovány. Posouzení také identifikuje všechna konkrétní rizika pro schvalovací autoritu, aby bylo možné je před vydáním schválení provozu (ATO) zvážit.

1. **Schválení k provozu**: Proces, ve kterém se vedoucí instituce státní správy formálně uznává a přijímá reziduální riziko systému pro informace, které zpracovává, ukládá a komunikuje. Vstupem tohoto procesu je posouzení zabezpečení.

Posouzení služeb Azure na chráněné úrovni označuje, že implementace kontrolních mechanismů zabezpečení vyžadovaných pro ukládání a zpracování CHRÁNĚNých a nižších dat byla potvrzena a efektivně funguje.

## <a name="australian-data-classification-changes"></a>Změny klasifikace australskéch dat

Od 1. října 2018 oznámilo oddělení obecného zabezpečení obecnou změnu v rozhraní PSPF (Security Policy Policy Framework), konkrétně v novém [citlivých a klasifikovaných informačních systémech](https://www.protectivesecurity.gov.au/information/sensitive-classified-information/Pages/default.aspx).

![Revidované klasifikace PSPF](media/pspf-classifications.png)

Všechny australské úřady a organizace, které provozují PSPF, jsou těmito změnami ovlivněny. Primární změna, která má vliv na naše aktuální certifikace IRAP/CCSL, je, že se vyřadí stávající příznačení DLMs (s omezením na rozšiřování). OFICIÁLNÍ: Citlivé označení nahrazuje různé DLMs používané pro ochranu citlivých informací. Tato změna také zavedla tři značky správy informací, které lze použít na všechny oficiální informace na všech úrovních citlivosti a klasifikace. CHRÁNĚNá klasifikace zůstane beze změny.

Výraz "unklasifikovaná" je z nového systému odebraný a termín "neoficiální" se použije pro informace, které nejsou státními úřady, i když nevyžaduje formální označení.

## <a name="choose-an-azure-region-for-official-sensitive-and-protected-workloads"></a>Vyberte oblast Azure pro OFICIÁLNÍho: Citlivá a CHRÁNĚNá zatížení

Oficiální pracovník Azure: Citlivé a chráněné certifikované služby se nasazují do všech čtyř australských oblastí datových Center: Austrálie – východ, Austrálie – jih, Austrálie – střed a Austrálie – střed 2 Zpráva o certifikaci vydaná ACSC doporučuje, aby se CHRÁNĚNá data nasadila do oblastí Azure Government v Canberra, pokud je zde k dispozici služba. Další informace o CHRÁNĚNých certifikovaných službách Azure najdete na [stránce Austrálie na portálu Trust Service](https://aka.ms/au-irap).

>[!NOTE]
>Microsoft doporučuje, aby se data státní správy všech Sensitivities a klasifikací nasadily do oblastí Austrálie Central a Austrálie – střední 2, protože jsou navržené a provozované speciálně pro potřeby státní správy.

Další informace o zvláštní povaze australských oblastí Azure najdete v tématu Azure ve středových oblastech [Austrálie](https://azure.microsoft.com/global-infrastructure/australia/).

## <a name="how-microsoft-separates-classified-and-official-data"></a>Jak společnost Microsoft odděluje klasifikovaná a oficiální data

Microsoft provozuje Azure a Office 365 v Austrálii jako v případě, že jsou všechna data citlivá nebo klasifikovaná, což vyvolává naše ovládací prvky zabezpečení s tímto vysokou čárou.

Infrastruktura, která podporuje Azure, potenciálně zachovává data více klasifikací. Vzhledem k tomu, že předpokládáme, že jsou zákaznická data klasifikována, jsou zavedeny příslušné ovládací prvky. Microsoft přijal základní stav zabezpečení pro naše služby, které jsou v souladu s požadavky PSPF na ukládání a zpracování CHRÁNĚNých utajovaných informací.

Abychom zákazníkům zajistili, že jeden tenant v Azure nehrozí v nebezpečí od ostatních tenantů, Microsoft implementuje komplexní podrobné kontroly.

Nad rámec funkcí implementovaných v rámci Microsoft Azure platformy můžou další uživatelsky konfigurovatelné ovládací prvky, jako je šifrování pomocí klíčů spravovaných zákazníkem, vnořená virtualizace a přístup pro správu za běhu, dál snížit riziko. V rámci Azure Government oblasti Austrálie v Canberra se jedná o proces formálního seznamu povolených pouze Austrálie a státní samosprávy na Novém Zélandu a národní důležité organizace pro infrastrukturu. Tento Cloud komunity poskytuje další záruky pro organizace, které jsou citlivé na rizika spolupracujících tenantů.

Zpráva o certifikaci Microsoft Azure PROTECTed potvrzuje, že tyto ovládací prvky platí pro ukládání a zpracování CHRÁNĚNých klasifikovaných dat a jejich izolaci.

## <a name="relevance-of-irapccsl-to-state-government-and-critical-infrastructure-providers"></a>Relevance IRAP/CCSL pro státní státní správu a kritické poskytovatele infrastruktury

Mnoho státních státních institucí a důležitých poskytovatelů infrastruktury zakládá požadavky federální správy do svých zásad zabezpečení a bezpečnostního rozhraní. Tyto organizace také řídí úřední, oficiální: Citlivá a určitá množství CHRÁNĚNých utajovaných dat, ať už od jejich interakce s federální vládou nebo vlastními právy.

Australské vlády se stále stále zaměřuje na zásady a právní předpisy na ochraně neoficiálních dat, která mají zásadní vliv na bezpečnost a ekonomickou prosperitu Austrálie. V takovém případě jsou oblasti Austrálie Azure a certifikace CCSL relevantní pro všechny tyto obory.

![Důležité sektory infrastruktury](media/nci-sectors.png)

Certifikace Microsoftu ukazují, že služby Azure byly na pracovišti důkladné, přísné a formální vyhodnocení ochrany zabezpečení a že byly schváleny pro zpracování takových vysoce citlivých dat.

## <a name="location-and-control-of-microsoft-data-centres"></a>Umístění a kontrola datových center Microsoftu

Je povinným požadavkem na vládu a důležitou infrastrukturu, aby explicitně znala umístění datových center a vlastnictví pro cloudové služby zpracovávající svá data. Microsoft je jedinečný jako poskytovatel cloudu s měřítkem a poskytuje rozsáhlé informace o těchto umístěních a vlastnictví.

Oblasti Austrálie Microsoftu (Austrálie – střed a Austrálie – střed 2) fungují v rámci zařízení pro datacentra CDC. Vlastnictví DataCentre technologie CDC je australské řízení s 48% vlastnictvím ze služby Superannuation Corporation, 48% vlastnictvím z Infratil (Nový Zéland, duální Austrálie a na Novém Zélandu na burze v rámci dlouhodobé infrastruktury fond assetů) a 4% australský Management. 

Správa datacentra CDC má smluvní záruky, které se dovedou k australské vládě, která omezuje budoucí přenos vlastnictví a řízení. Tato průhlednost dodavatelských řetězců a vlastnictví prostřednictvím partnerství Microsoftu s datacentremi CDC je v souladu se zásadami [strategie hostování celé vlády](https://www.dta.gov.au/our-projects/whole-government-hosting-strategy) a definicí certifikovaného datacentra.

## <a name="azure-services-that-are-included-in-the-current-ccsl-certification"></a>Služby Azure, které jsou zahrnuté v aktuální certifikaci CCSL

V červnu 2017 ACSC Certified 41 služby Azure pro ukládání a zpracování dat bez klasifikace: Úroveň DLM. V dubnu 2018 byly pro CHRÁNĚNá klasifikovaná data certifikovány 24 těchto služeb.

Dostupnost služeb Azure s certifikací ACSC v rámci našich oblastí Azure v Austrálii je následující: (služby zobrazené tučně jsou certifikovány na chráněné úrovni).

|Azure ve středových oblastech Austrálie|Jiné než regionální služby a jiné oblasti|
|---|---|
|API Management, App Gateway, Aplikační služby, **Automation**, **Azure Portal**, **Backup**, **Batch**, **Cloud Services**, Cosmos DB, Event Hubs, **ExpressRoute**, HDInsight, **Key Vault**, Load Balancer, protokol Analýzy, **vícefaktorové ověřování**, Redis Cache, **Správce prostředků**, **Service Bus**, **Service Fabric**, **Site Recovery**, **SQL Database**, **úložiště**, Traffic Manager, **Virtual Počítače**, **Virtual Network** **VPN Gateway**|**Azure Active Directory**, CDN, Data Catalog, **Import exportu**, **Information Protection**, **centrum IoT**, Machine Learning, Media Services, **Notification Hubs**, Power BI, **Scheduler**, **Security Center**, Hledat, Stream Analytics|
|

Společnost Microsoft zveřejňuje [přehled Microsoft Azure dodržování předpisů](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/44/Microsoft%20Azure%20Compliance%20Offerings.pdf) , které uvádí všechny služby v oboru pro všechny procesy dodržování předpisů a hodnocení, které jsou v Azure, a to včetně IRAP/CCSL.

## <a name="azure-service-not-listed-or-assessed-at-a-lower-level-than-needed"></a>Služba Azure není uvedená nebo zhodnocena na nižší úrovni, než je potřeba.

Služby, které nejsou certifikované nebo které byly certifikovány v OFICIÁLNÍm umístění: Citlivá, ale ne CHRÁNĚNá úroveň, se dá použít společně nebo jako součást řešení hostujícího CHRÁNĚNá data, která poskytují tyto služby:

- Neukládají ani nezpracovávají CHRÁNĚNá data bez šifrování nebo
- Dokončili jste posouzení rizik a schválili jste službu pro ukládání CHRÁNĚNých dat sami.

K ukládání a zpracování OFICIÁLNÍch dat můžete použít službu, která není součástí CCSL. certifikát ISM ale vyžaduje, abyste před zahájením nebo obnovením smlouvy s poskytovatelem cloudových služeb oznámili ACSC v psaní, které provádíte.

Každá služba, kterou používá agentura pro chráněné úlohy, musí být vyhodnocená a schválená v souladu s procesy popsanými v rámci úřadu ISM a v procesu posouzení IRAP spravovaného agenturou v [strategii zabezpečeného cloudu odložených](https://www.dta.gov.au/files/cloud-strategy/secure-cloud-strategy.pdf)služeb.

![Proces certifikace strategie zabezpečení neaktivující cloudovou strategii](media/certification.png)

Společnost Microsoft průběžně vyhodnocuje naše služby, aby bylo zajištěno, že je platforma zabezpečená a slouží pro účely australské vlády z Austrálie. Pokud potřebujete pomoc se službou, která aktuálně není v CCSL na chráněné úrovni, obraťte se na společnost Microsoft.

Vzhledem k tomu, že společnost Microsoft obsahuje řadu služeb certifikovaných na CCSL jak v neklasifikované DLM, tak i v CHRÁNĚNých klasifikacích, společnost ISM vyžaduje, abychom v každé dva roky vyplnili IRAP posouzení našich služeb. Microsoft se zavazuje každoroční posouzení, což je také příležitost zahrnout další služby k posouzení.

## <a name="certified-protected-gateway-in-azure"></a>Certifikovaná CHRÁNĚNá brána v Azure

Microsoft neprovozuje zabezpečenou internetovou bránu (SIG) certifikovaný státní správou z důvodu omezení počtu SIGs přípustných v rámci programu pro konsolidaci brány. Ale očekávané a nezbytné funkce SIG je možné nakonfigurovat v rámci Microsoft Azure.

Díky chráněné certifikaci služeb Azure má ACSC specifická doporučení pro účely připojení k Azure a při implementaci segmentace sítě mezi doménami zabezpečení, například mezi CHRÁNĚNým a internetem. Mezi tato doporučení patří použití skupin zabezpečení sítě, bran firewall a virtuálních privátních sítí. ACSC doporučuje použití zařízení virtuální brány. V Azure je k dispozici několik virtuálních zařízení, která mají fyzický ekvivalent na seznamu produktů s hodnocením ASD, nebo byly vyhodnoceny proti běžným profilům ochrany kritérií a jsou uvedeny na portálu Common kritéria. Tyto produkty vzájemně uznávají osoba ASD jako signatáři pro obecné ujednání o rozpoznávání kritérií.

Společnost Microsoft vytvořila pokyny k implementaci funkcí založených na Azure, které poskytují bezpečnostní funkce vyžadované k ochraně hranice mezi různými doménami zabezpečení, které v kombinaci tvoří ekvivalent certifikovaného SIG. Několik partnerů může pomáhat s návrhem a implementací těchto schopností a k dispozici je řada partnerských řešení, která jsou k dispozici.

## <a name="security-clearances-and-citizenship-of-microsoft-support-personnel"></a>Bezpečnostní prověrky a státní občanství pracovníků podpory Microsoftu

Společnost Microsoft pracuje s našimi službami globálně s využitím a vyškoleným personálem zabezpečení. Zaměstnanci, kteří mají nedoprovázený fyzický přístup k zařízením v Sydney a Melbourne, mají za základnu základní bezpečnostní prověrky. Zaměstnanci v oblastech Austrálie Central a Austrálie – střední 2 mají minimální negativní dozvíte ČSFD 1 (NV1) volný prostor (podle potřeby pro TAJNá data). Tyto požadavky na volný prostor poskytují zákazníkům, že personál v datových centrech s operačním systémem Azure je vysoce důvěryhodný.

Společnost Microsoft má k dispozici nulový přístup k zásadám, které jsou uděleny prostřednictvím systému pouze v čase, a stačí jenom spravovat na základě řízení přístupu na základě rolí. V rozsáhlých případech můžou správci při odstraňování a údržbě služby vyžadovat přístup k zákaznickým datům ani oprávnění. Vysoká úroveň automatizace a skriptování úloh pro vzdálené spuštění – negace nutnosti přímého přístupu k zákaznickým datům.

Oddělení obecného právního zástupce potvrzuje, že zásady a postupy zabezpečení zaměstnanců společnosti Microsoft v rámci Azure jsou v souladu s záměrem přístupu PSPF k informačním ustanovením v INFOSEC-9.

## <a name="store-international-traffic-of-arms-regulations-itar-or-export-administration-regulations-ear-data"></a>Ukládat mezinárodní provoz na ITAR (zbraně) nebo exportovat nařízení pro správu (ušní) data

Technické kontroly Azure, které pomáhají zákazníkům se splněním svých závazků pro exportovaná data, jsou v Azure stejné globálně. Důležité je, že pro exportovaná data nejsou k dispozici žádná formální hodnocení a certifikační architektura.

Pro Azure Government a Office 365 USA pro obranu jsme na pracovišti zavedli další smluvní a procesní míry pro podporu zákazníků, kteří podléhají ovládacím prvkům pro export. Tyto další smluvní doložky a státní podpora státních podpor a správy oblastí Azure nejsou pro Austrálii k disruku.

To neznamená, že Azure v Austrálii nejde použít k ITARí nebo k dispozici, ale je potřeba jasně pochopit omezení, která vás zajímají prostřednictvím licence pro export. Před použitím Azure k uložení těchto dat musíte taky implementovat další ochrany, abyste splnili tyto závazky. Například může být nutné:

- Státní příslušnost sestavení jako atribut Azure Active Directory.
- Pomocí Azure Information Protection můžete vynutilit pravidla šifrování pro data a omezit je jenom na USA a na vývozní licenci se zahrnou i jakékoli jiné státní příslušníky.
- Všechna místní data před uložením do Azure Zašifrujte pomocí klíče zákazníka nebo si pro ITAR data podržíte vlastní klíč.

Vzhledem k tomu, že ITAR není formální certifikace, je potřeba pochopit, jaká omezení a omezení jsou spojená s licencí pro export. Pak můžete bez ohledu na to, jestli je v Azure dostatek ovládacích prvků pro splnění těchto požadavků. V takovém případě je jedním z problémů, které je vhodné pečlivě zvážit, přístup našim inženýrům, kteří nemusí být státní příslušnost schválena ve vývozní licenci.

## <a name="next-steps"></a>Další postup

 Konfigurace a implementace připojení k síti VPN kompatibilní s protokolem ISM pro Azure v Austrálii najdete v tématu [Azure VPN Gateway](vpn-gateway.md).
