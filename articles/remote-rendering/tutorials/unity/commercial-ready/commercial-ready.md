---
title: Vytvoření aplikace Azure Remote Rendering připravené pro komerční využití
description: Strategie a požadavky pro vytvoření komerční aplikace připravené pomocí vzdáleného vykreslování Azure
author: FlorianBorn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.openlocfilehash: 7258b37c9d92f25227eac678dde77963e644e64b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483313"
---
# <a name="tutorial-creating-a-commercial-ready-azure-remote-rendering-application"></a>Kurz: Vytvoření aplikace pro vzdálené vykreslování Azure připravené pro komerční zpracování

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Správa relací pro komerční aplikace
> * Sledování relací pro fakturaci
> * Optimalizace uživatelského prostředí kolem času načítání relace
> * Okolnosti týkající se latence sítě

## <a name="prerequisites"></a>Předpoklady

* Tento kurz sestaví [kurz: zabezpečení vzdáleného vykreslování Azure a úložiště modelu](../security/security.md).

## <a name="introduction-to-commercial-readiness"></a>Úvod do komerčního připravenosti

Vzdálené vykreslování Azure rozšiřuje to, co je možné se smíšenou realitou. Jakmile jsou základy integrované do vašeho řešení, existuje mnoho dalších informací, které vám pomůžou zajistit, aby vaše řešení bylo zabezpečené, škálovatelné a připravené k poskytování hodnoty.

Tento modul vás seznámí s dalšími možnostmi, které možná budete muset vzít v úvahu pro komerční aplikaci.

Obecný přehled osvědčených postupů architektury pro celou architekturu najdete v těchto postupech:

* [Azure Architecture Center](/azure/architecture/)
* [Příručka Začínáme pro vývojáře v Azure](../../../../guides/developer/azure-developer-guide.md)

## <a name="analytics"></a>Analýzy

Integrace analytických nástrojů vám může pomoci spravovat, sledovat a zdokonalovat vaše řešení.

Úplný seznam dostupných prostředků analýzy najdete na adrese:

* [Analytické služby Azure](https://azure.microsoft.com/product-categories/analytics/)

### <a name="tracking-usage-for-billing"></a>Sledování využití pro fakturaci

Sledování spotřeby vzdáleného vykreslování Azure pomocí několika interních týmů nebo externích klientů se stane důležitým aspektem, zejména v situacích s více klienty.

Za tímto účelem Azure nabízí službu s názvem označování prostředků, která přidruží spotřebu služby vzdálené vykreslování Azure ke každému klientovi.

Další informace o pojmenování a označování prostředků je dobrým místem, kde začínáte:

* [Rozhodovací příručka pro pojmenování a označování prostředků](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

### <a name="diagnostics"></a>Diagnostika

Výkonné nástroje, jako je trasování událostí pro Windows (ETW) a protokolování trasování událostí (ETL), usnadňují generování událostí trasování v rámci aplikace a můžou pomáhat diagnostikovat síť, příjem obsahu, relaci, aplikace a další problémy, které mohou nastat v případě nasazení komerčního řešení.

Další informace najdete na adrese:

* [Vytvoření trasování výkonu na straně klienta](../../../how-tos/performance-tracing.md)
* [Jak shromažďovat data trasování událostí pro Windows (ETW)](/visualstudio/profiling/how-to-collect-event-tracing-for-windows-etw-data)
* [Použití portálu zařízení Windows: protokolování](/windows/mixed-reality/using-the-windows-device-portal)

### <a name="usage-analysis"></a>Analýza využití

Azure Application Insights pomáhá pochopit, jak uživatelé používají vaši aplikaci pro vzdálené vykreslování Azure. Pokaždé, když aktualizujete aplikaci, můžete posoudit, jak dobře funguje pro uživatele, a podle toho zlepšit vaše řešení. V tomto článku se seznámíte s tím, jak můžete provádět rozhodování na základě dat o dalších cyklech vývoje.

Další informace najdete na adrese:

* [Analýza využití pomocí Application Insights](../../../../azure-monitor/app/usage-overview.md)

## <a name="fast-startup-time-strategies"></a>Strategie rychlého spuštění

Váš případ použití může vyžadovat rychlé spuštění od spuštění aplikace až po 3D model zobrazení. Například během důležité schůzky, kde je důležité mít vše v chodu. Dalším příkladem je 3D model kontrola CAD, kde je pro zajištění efektivita rychlá iterace návrhu mezi aplikací CAD a smíšenou realitou.

Vzdálené vykreslování Azure vyžaduje předzpracované 3D modely a Azure v současné době trvá několik minut, než vytvoří relaci a nahraje model pro vykreslování. Díky tomu, aby tento proces byl co nejrychlejší a co nejrychleji, je potřeba připravit 3D model dat a relace ARR předem.

Zde sdílené návrhy nejsou aktuálně součástí standardního vzdáleného vykreslování Azure, ale můžete je implementovat sami pro rychlejší spuštění.

### <a name="initiate-early"></a>Zahájit včas

Aby se zkrátil čas spuštění, nejjednodušší řešení je přesunout vytváření a inicializaci relace co nejdříve v pracovním postupu uživatele. Jednou z strategií je inicializovat relaci, jakmile bude známo, že bude potřeba relace ARR. Tato akce bude často pokaždé, když uživatel začne nahrávat 3D model do Azure Blob Storage pro použití se vzdáleným vykreslováním Azure. V takovém případě je možné vytvořit a inicializovat vytvoření relace ve stejnou dobu jako 3D model nahrávání tak, aby oba pracovní proudy běžely paralelně.

Tento proces se dá zjednodušit tím, že zajistíte, aby zvolené vstupní a výstupní kontejnery Azure Blob Storage ve stejném oblastním datovém centru jako relace vzdáleného vykreslování Azure.

### <a name="scheduling"></a>Plánování

Pokud víte, že máte budoucí potřebu vzdáleného vykreslování Azure, můžete naplánovat konkrétní datum a čas, abyste spustili relaci vzdáleného vykreslování Azure.

Tato možnost může být nabízena prostřednictvím webového portálu, kde mohou uživatelé nahrát 3D model a naplánovat čas jejich zobrazení v budoucnu. To by bylo také dobrým místem pro dotazování na jiné předvolby, jako je například [*Standard*](../../../reference/vm-sizes.md) nebo [*Premium*](../../../reference/vm-sizes.md) Render. Vykreslování na úrovni *Premium* může být vhodné, pokud si přejete, aby se zobrazila směs assetů, kde je ideální velikost, která je nevhodná pro automatické určení nebo nutnost zajistit, aby v oblasti Azure byly dostupné virtuální počítače v zadaném čase.

### <a name="session-pooling"></a>Sdružování relací

V nejnáročnějších situacích je další možností sdružování relací, kdy se jedna nebo víc relací vytvoří a inicializuje v tuto dobu. Tím se vytvoří fond relací pro okamžité použití žádajícím uživatelem. Nevýhodou tohoto přístupu znamená, že jakmile se virtuální počítač inicializuje, spustí se fakturace za službu. Je možné, že nebudete nákladově efektivní udržovat fond relací neustále spuštěný, ale na základě analýz je možné předpovědět zátěže ve špičce nebo je můžete v kombinaci se strategií plánování uvedenou výše odhadnout, až budou relace potřeba, a odpovídajícím způsobem zvýšit a snížit zatížení fondu relací.

Tato strategie také pomáhá s optimalizací volby mezi relacemi *Standard* a *Premium* , protože by bylo mnohem rychlejší přepínat mezi těmito dvěma typy v rámci jedné relace uživatele, jako je například případ, kdy se jako první zobrazuje model složitosti *Premium* , následovaný ten, který může fungovat v rámci *standardu Standard*. Pokud jsou tyto uživatelské relace poměrně dlouhé, může dojít k výraznému snížení nákladů.

Další informace o relacích vzdáleného vykreslování Azure najdete v těchto případech:

* [Relace Remote Renderingu](../../../concepts/sessions.md)

## <a name="standard-vs-premium-server-size-routing-strategies"></a>Strategie směrování pro velikost Standard vs. Premium Server

Pokud potřebujete vybrat, jestli se má vytvořit server *úrovně Standard* nebo *Premium* , zobrazí se výzva při navrhování uživatelského prostředí a komplexního systému. I když je možnost používat jenom relace *Premium* , *standardní* relace využívají mnohem méně výpočetních prostředků Azure a jsou levnější než *Premium*. To poskytuje silnou motivaci k používání *standardních* relací, kdykoli je to možné, a v případě potřeby používejte jenom *Premium* .

Tady sdílíme několik možností, od nejmenších po nejucelenější, aby bylo možné spravovat možnosti relace.

### <a name="use-only-standard-or-premium"></a>Použít pouze Standard nebo Premium

Pokud jste si jistí, že vaše požadavky *vždycky* spadají pod prahovou hodnotu mezi *standardem* a *Premium*, bude toto rozhodnutí výrazně jednodušší. Stačí použít *standardní*. Mějte na paměti, že dopad na činnost koncového uživatele je významný, pokud je celková složitost načtených assetů pro *standardní* relaci odmítnutá jako příliš složitá.

Podobně platí, že pokud očekáváte, že velká část použití překročí prahovou hodnotu mezi *standardem* a *Premium*, nebo když náklady není klíčovým faktorem v případu použití, pak vždy zvolte možnost *Premium* je také možností, jak ji udržet jednoduché.

### <a name="ask-the-user"></a>Požádat uživatele

Pokud chcete podporovat obě *úrovně Standard* i *Premium*, nejjednodušší způsob, jak určit typ relace, která se má vytvořit, je požádat uživatele, aby si vybral 3D prostředky, které se mají zobrazit. Výzvou k tomuto přístupu je to, že vyžaduje, aby uživatel rozuměl složitosti 3D prostředků nebo dokonce i více prostředků, které se budou zobrazovat. Obvykle to z tohoto důvodu nedoporučujeme. Pokud uživatel vybere možnost nesprávného a zvolíte možnost *Standard*, výsledné uživatelské prostředí by mohlo být v nevhodném okamžiku ohroženo.

### <a name="analyze-the-3d-model"></a>Analyzovat 3D model

Dalším poměrně jednoduchým přístupem je analýza složitosti vybraných 3D prostředků. Je-li složitost modelu pod prahovou hodnotou pro *Standard*, iniciuje *standardní* relaci, jinak inicializuje relaci *Premium* . V tomto případě se jedná o to, že jedna relace může být nakonec použita k zobrazení více modelů, které mohou překročit prahovou hodnotu složitosti *standardní* relace. Výsledkem je nemožnost bezproblémového použití stejné relace pro posloupnost různých 3D prostředků.

### <a name="automatic-switching"></a>Automatické přepínání

Automatické přepínání mezi relacemi *Standard* a *Premium* může mít spoustu smyslů v rámci návrhu systému, který zahrnuje i sdružování relací. Tato strategie umožňuje další optimalizaci využití prostředků. Když uživatel načte modely pro zobrazení, určí se složitost a ve službě sdružování relací se vyžádá správná velikost relace.

## <a name="working-with-networks"></a>Práce se sítěmi

### <a name="diagnostics"></a>Diagnostika

Vzdálené vykreslování Azure vyžaduje rychlé připojení k Internetu s nízkou latencí. Kvalita sítě uživatele může mít významný dopad na kvalitu prostředí. Vzhledem k tom, že klienti mají pravděpodobně různé konfigurace sítě a občas nízkou latenci sítě, jsou diagnostické nástroje klíčem.  

Aby bylo zajištěno, že budete moci zajistit konzistentní vysoce kvalitní prostředí, doporučujeme do svých aplikací pro vzdálené vykreslování Azure integrovat serverové nástroje a analytické nástroje na straně klienta. Provedete si tak informace, které budete potřebovat k diagnostice a zmírnění potíží se sítí, ke kterým může dojít u klientů.

### <a name="client-network-configurations"></a>Konfigurace sítě klienta

Jedním z největších problémů při vývoji robustních řešení pro spolupráci, která jsou nasazená do široké škály podnikových prostředí, se připravuje na rozdíl od síťové topologie a konfigurace podnikových bran firewall, které můžou klienti používat.

Řada podniků blokuje všechny přenosy peer-to-peer v síti LAN. Díky tomu je obtížné využít výhod jednoduchého a zjednodušeného uživatelského prostředí pro automatické zjišťování LAN a vytvořit místní sdílenou relaci mezi všemi zjištěnými instancemi aplikace hybridní reality.

Dalším možným bodem selhání jsou směrovače nakonfigurované tak, aby záměrně omezily šířku pásma a brány firewall, které blokují většinu portů TCP/IP.

Kdykoli plánujete použití vzdáleného vykreslování Azure v neznámé síti, doporučujeme následující:

* Poskytněte kontrolní seznam před schůzkou pro vyhodnocení připravenosti na síť.
* Ujistěte se, že příslušné oblastní datové centrum může požadavek obsluhovat.
* Umožněte vám dostatek času na diagnostiku všech problémů.
* Přeneste mobilní hotspot s datovým tarifem s vysokou šířkou pásma jako zálohu.

### <a name="end-to-end-bandwidth"></a>Koncová šířka pásma

Je důležité vyhodnotit možnosti šířky pásma pro každou nožku sítě, která může existovat mezi virtuálním počítačem vzdáleného vykreslování Azure a koncovým klientem. Mějte na paměti, že segment sítě z datového centra Azure do poskytovatele internetových služeb může být větší než omezení od poskytovatele internetových služeb k klientovi. Test rychlosti stahování objektů BLOB lze použít k diagnostice těchto problémů.

### <a name="bandwidth-competition"></a>Konkurence šířky pásma

Při návrhu aplikace hybridní reality Pamatujte na to, že různé funkce aplikace můžou soutěžit se vzdáleným vygenerováním Azure pro šířku pásma. Nejpravděpodobnějším neočekávaným příkladem je, že řada účastníků v jedné místnosti současně očekává, že se k zobrazení 3D prostředků používá šipka. Každé nožky toku dat sítě bude muset mít kapacitu k přenosu celkového součtu všech datových proudů ARR v kombinaci.

Mezi další příklady patří streamované video, současná nahraná na pozadí a hlasová konverzace, zejména v případě, že existuje mnoho účastníků a systém používá distribuovaný přístup peer-to-peer, a to na rozdíl od serveru pro směšování zvukového zařízení v prostředním přístupu.

Další informace o analýze sítě najdete v těchto informacích:

* [Azure Storage Blob test rychlosti stahování](https://www.azurespeed.com/Azure/Download)
* [Statistika latence odezvy sítě Azure](../../../../networking/azure-network-latency.md)
* [Trasování výkonu na straně serveru](../../../overview/features/performance-queries.md)
* [Trasování výkonu na straně klienta](../../../how-tos/performance-tracing.md)

## <a name="collaboration-considerations"></a>Požadavky na spolupráci

Některé z nejcennějších způsobů použití vzdáleného vykreslování Azure zahrnují spolupráci mezi několika účastníky, kteří si současně prohlížejí stejné prostorové prostředí. V těchto sdílených relacích je důležité pochopit, že každý účastník bude potřebovat jedinečnou relaci vzdáleného vykreslování Azure, bez ohledu na to, jestli se nachází na stejném místě ve stejné síti, nebo ne.

To je pravdivé, protože každý účastník ve skutečnosti vidí stejné prostředí z různých Vantage bodů, což vyžaduje, aby byly stejné 3D prostředky vykresleny ze všech těchto perspektiv současně.

### <a name="multiple-azure-remote-rendering-sessions"></a>Několik relací vzdáleného vykreslování Azure

Pokud máte v úmyslu podporovat sdílená prostředí se vzdáleným zobrazením Azure, je potřeba, aby byly systémy, které jste umístili k vytváření a správě relací ARR, připravené k zahájení více relací. Tyto relace může být potřeba inicializovat v různých datových centrech Azure, pokud jsou účastníci geograficky rozptýlené.

Systém musí také spravovat možnost, že jeden nebo více účastníků může být v geografické oblasti, kterou aktuálně nepodporuje vzdálené vykreslování Azure, nebo v současné době nemá k dispozici žádné instance virtuálních počítačů vzdáleného vykreslování Azure.

Při kombinaci s sdružováním relací a dalšími strategiemi popsanými v tomto dokumentu se dá tato Správa víc souběžných relací dále zjednodušit.

### <a name="azure-blob-storage-considerations"></a>Předpoklady pro Azure Blob Storage

Všechny souběžné relace s technologií ARR můžou odkazovat na stejný identifikátor URI SAS pro převedený model, který se má zobrazit. Díky tomu je možné nahrávat a převádět požadované 3D prostředky a pak je sdílet mezi všemi relacemi. To platí zejména v případě, že se účastníci nacházejí společně a používají stejné datové centrum, ve kterém neexistují žádné problémy související se službou Azure Blob Storage nacházejí v jiném datovém centru, než je server vzdáleného vykreslování Azure a uživatel.

Pokud se obvykle odesílají 3D prostředky pro jednu relaci zobrazení a pak se zahodí, například v relaci revize návrhu, geografická oblast Azure Blob Storage relativní vzhledem k serveru vzdáleného vykreslování Azure je také méně kritická.

U 3D prostředků, které se opakovaně použijí, například v případě školicího případu, doporučujeme, abyste si v každém oblastním datovém centru, kde plánujete používat vzdálené vykreslování Azure, zachovali 3D prostředky připravené k použití v úložišti objektů BLOB. To je možné automatizovat pomocí Azure Storage redundance. SÍŤ CDN se často používá k tomuto účelu, ale ta ještě není možností pro vzdálené vykreslování Azure.

Další informace najdete tady:

* [Sdílená prostředí ve smíšené realitě](/windows/mixed-reality/shared-experiences-in-mixed-reality)
* [Azure Storage redundance](../../../../storage/common/storage-redundancy.md)

## <a name="managing-model-access"></a>Správa přístupu k modelu

Využití vzdáleného vykreslování Azure plně vyžaduje pečlivou pozornost komplexní infrastruktury pro správu 3D modelů.

Výhodou použití vzdáleného vykreslování Azure je, že před zobrazením nemusíte před zobrazením přenést velké 3D prostředky přímo do zařízení se smíšeným realitou.  Po nahrání a převodu 3D assetu pro použití se vzdáleným vykreslováním Azure může navíc libovolný počet uživatelů sdílet tuto jednu instanci 3D model.

### <a name="considerations-for-3d-model-access"></a>Pokyny pro přístup k 3D model

Tady je několik klíčových doporučení pro rozhodování o strategii přístupu k modelu.

Na základě očekávaného případu použití Určete nejvhodnější místo nebo kombinaci míst, aby uživatel mohl vybrat 3D prostředky k zobrazení. Mezi běžné možnosti patří:

* Přímo v rámci hybridního prostředí realit
* Prostřednictvím doprovodného webového portálu
* V doprovodném počítači nebo mobilní aplikaci

Pokud má váš případ použití vzory použití, ve kterých se stejný 3D prostředek dá nahrát víckrát, pak back-endu bude sledovat, které modely už jsou převedené pro použití s ARR, takže model je předem zpracovaný jenom jednou pro několik budoucích výběrů. Příkladem přezkoumání návrhu je, kde má tým přístup k běžnému původnímu prostředku 3D. U každého člena týmu se očekává, že v určitém okamžiku ve svém pracovním proudu bude model kontrolovat pomocí ARR. Pouze první zobrazení by pak aktivovalo krok před zpracováním. Následující zobrazení budou vyhledávat přidružený soubor po zpracování v kontejneru výstupu SAS.

V závislosti na případu použití pravděpodobně budete chtít určit a potenciálně zachovat správnou velikost serveru Azure Remote Render ( *Standard* nebo *Premium*) pro každý 3D prostředek nebo skupinu assetů, které se ve stejné relaci budou zobrazovat společně.  

### <a name="on-device-model-selection-list"></a>Seznam pro výběr modelu na zařízení

V mnoha případech použití, jako je například školení, pokyny k úlohám nebo marketingová aplikace, může být sada 3D prostředků, která se běžně zobrazuje ve vzdáleném vykreslování Azure, poměrně statická. V těchto situacích může být podmnožina 3D prostředků předem převedena a zpřístupněna prostřednictvím databáze, která obsahuje nezbytné informace k naplnění seznamu vybraných prostředků. Tato data pak můžete načíst z aplikace hybridní reality a naplnit tak nabídku výběr.

Tento krok je možné provést ještě podrobněji, protože nabízí způsob nahrávání soukromých 3D prostředků, které jsou jedinečné pro jednotlivé uživatele nebo skupiny. Tento seznam privátních prostředků se pak může zkombinovat se seznamem běžných, spravovaných prostředků v uživatelském prostředí pro vybírání 3D prostředků, které se mají zobrazit.

### <a name="on-device-onedrive-access"></a>Přístup k OneDrivu na zařízení

Vzhledem k tomu, že nástroj pro výběr souborů na OneDrivu je nativně sestavený na zařízení se smíšeným realitou Microsoftu, je vhodné vybrat 3D prostředky z OneDrivu, zejména v případech použití, kde je běžné načíst různé nebo upravené 3D modely. V tomto scénáři vybere uživatel jeden nebo více 3D prostředků prostřednictvím výběru souborů OneDrive v rámci aplikace hybridní reality. 3D prostředky by pak byly migrovány do vstupního kontejneru SAS, převedeny na kontejner výstupu SAS a připojeny k relaci ARR. V ideálním případě by aplikace hybridní reality vyvolala cloudový proces, který tyto kroky provede, a to na rozdíl od přesunu všech bitů z OneDrivu na zařízení a zpět do Azure Blob Storage.

Tento přístup se dá provést ještě jednou, protože ponechá přidružení mezi 3D prostředky, které se dřív prohlížely tak, že když se stejný model vybere znovu z OneDrivu, může aplikace obejít proces převodu a přímo načíst přidružený převedený 3D prostředek přes jeho identifikátor URI SAS.

Další informace najdete tady:

* [Šablona Microsoft Power automatizuje pro OneDrive pro Azure Storage replikaci](https://flow.microsoft.com/galleries/public/templates/2f90b5d3-029b-4e2e-ad37-1c0fe6d187fe/when-a-file-is-uploaded-to-onedrive-copy-it-to-azure-storage-container/)
* [Přehled rozhraní API pro File Storage OneDrivu](/graph/onedrive-concept-overview)

### <a name="direct-cad-access"></a>Přímý přístup do CAD

Jedním z působivého případu použití pro hybridní realitu je návrh kontrol probíhajících prací ve formátu CAD. V tomto scénáři je klíčovým časem nejrychlejší načítání z desktopu na hybridní realitu. Ideální řešení může zahrnovat vývoj modulů plug-in pro konkrétní aplikace CAD. Tyto moduly plug-in přímo spravují všechny aspekty procesu zatížení, převedení a zobrazení:

* Poskytněte uživatelské prostředí pro:
  * Spáruje aplikaci CAD s konkrétním zařízením hybridní reality (jednou).
  * Požádejte o zobrazení vybrané geometrie na zařízení se smíšenými realitami.
* Pokud ještě neběží, aktivujte relaci vzdáleného vykreslování Azure, aby při nahrávání a převodu souboru CAD mohla zpracovávat paralelně.
* Normalizovat data geometrie v CAD do jednoho z formátů, které podporuje vzdálené vykreslování Azure
* Přenos normalizovaných dat přímo do vstupního kontejneru Azure Blob Storage
* Iniciace procesu převodu modelu
* Propojit výstupní identifikátor URI SAS kontejneru výstupu modelu s relací vzdáleného vykreslování Azure
* Upozorněte aplikaci spárovaného hybridního realitu, že model je dostupný a připravený k zobrazení, a poskytne identifikátor URI pro výstup kontejneru, aby ho aplikace mohl připojit k relaci.

Mnohem jednodušší, ale mírně méně zjednodušený přístup by mohl automatizovat proces ukládání 3D model na místní pevný disk a pak zahájit proces přenosu tohoto uloženého souboru do vstupního kontejneru SAS.

### <a name="azure-marketplace"></a>Azure Marketplace

Mnoho podnikových klientů vyžaduje, aby vaše Azure Stack mohla být nasazena v rámci svých vlastních účtů Azure a přihlašovacích údajů z bezpečnostních důvodů. Aby to bylo možné, měli byste zvážit balení spravované aplikace Azure tak, aby bylo možné je publikovat na Azure Marketplace jako nabídka aplikace Azure.

Další informace najdete tady:

* [Azure Marketplace](https://azure.microsoft.com/marketplace/)
* [Kurz: publikování spravovaných aplikací Azure na webu Marketplace](../../../../marketplace/create-new-azure-apps-offer.md)

### <a name="security"></a>Zabezpečení

Je důležité sestavit komplexní řešení vzdáleného vykreslování Azure od základů od začátku do konce. Existuje mnoho aspektů zabezpečení, které je potřeba vzít v úvahu v návrhu komplexního řešení, včetně:

* Strategie ověřování
* Správa přístupu – skupiny, zásady a oprávnění
* Architektura s více tenanty
* Ukládání a přenos dat do úložiště
* Dočasné tokeny použití
* Distribuované útoky s cílem odepření služeb (DDoS)
* Detekce hrozeb
* Sítě VPN a zabezpečené sítě
* brány firewall,
* Správa certifikátů a tajných klíčů
* Ohrožení zabezpečení a zneužití aplikace

Pro ověřování se můžete co nejvíce přesunout na webovou službu Azure jako velkou část ověřování a správu relací. Výsledkem bude lepší spravované a bezpečnější řešení.

Další informace najdete tady:

* [Ověřování služby Azure AD](../../../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-service-authentication)
* [Posílení stav zabezpečení s využitím Azure](https://azure.microsoft.com/overview/security/)
* [Zabezpečení cloudu](https://azure.microsoft.com/product-categories/security/)