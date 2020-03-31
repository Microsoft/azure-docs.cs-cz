---
title: Ochrana před hrozbami v Azure Security Center
description: Toto téma popisuje prostředky chráněné funkcemi ochrany před hrozbami centra Zabezpečení Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: e348180eb94c1703ceecf2f2b00ab942ba5ff0ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536322"
---
# <a name="threat-protection-in-azure-security-center"></a>Ochrana před hrozbami v Azure Security Center

Pokud Centrum zabezpečení zjistí hrozbu v jakékoli oblasti vašeho prostředí, vygeneruje výstrahu. Tyto výstrahy popisují podrobnosti o ovlivněných prostředcích, navrhované nápravné kroky a v některých případech možnost aktivovat aplikaci logiky v reakci.

Ochrana před hrozbami Azure Security Center poskytuje komplexní obranu pro vaše prostředí:

* **Ochrana před hrozbami pro výpočetní prostředky Azure:** počítače s Windows, počítače s Linuxem, Azure App Service a kontejnery Azure

* **Ochrana před hrozbami pro datové prostředky Azure:** SQL Database a SQL Data Warehouse, Azure Storage a Azure Cosmos DB

* **Ochrana před hrozbami pro vrstvy služeb Azure:** Síťová vrstva Azure, vrstva správy Azure (Azure Resource Manager) (Preview) a Azure Key Vault (Preview)

Bez ohledu na to, zda je výstraha generována centrem zabezpečení nebo přijata centrem zabezpečení z jiného produktu zabezpečení, můžete ji exportovat. Chcete-li exportovat výstrahy do Azure Sentinelu (nebo siem třetí strany) nebo jiného externího nástroje, postupujte podle pokynů v [části Export výstrah do systému SIEM](continuous-export.md). 




## <a name="threat-protection-for-windows-machines"></a>Ochrana před hrozbami pro počítače se systémem Windows<a name="windows-machines"></a>

Azure Security Center se integruje se službami Azure a monitoruje a chrání vaše počítače s Windows. Centrum zabezpečení představuje výstrahy a návrhy nápravných zařízení ze všech těchto služeb ve snadno použitelném formátu.

* **Ochrana ATP** <a name="windows-atp"></a> v programu Microsoft Defender – Security Center rozšiřuje své platformy ochrany cloudových úloh o integraci s programem Microsoft Defender Advanced Threat Protection (ATP). Společně poskytují komplexní funkce detekce koncových bodů a odezvy (EDR).

    > [!IMPORTANT]
    > Snímač ochrany ATP programu Microsoft Defender je automaticky povolen na serverech se systémem Windows, které používají centrum zabezpečení.

    Když ochrana ATP v programu Microsoft Defender zjistí hrozbu, spustí výstrahu. Výstraha se zobrazí na řídicím panelu Centra zabezpečení. Z řídicího panelu můžete přejít ke konzoli ochrany ATP v programu Microsoft Defender a provést podrobné šetření, abyste odhalili rozsah útoku. Další informace o programu Microsoft Defender ATP naleznete [v tématu Onboard servers to the Microsoft Defender ATP service](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

* **Analýza výpisu stavu systému** <a name="windows-dump"></a> - při selhání softwaru výpis stavu systému zachytí část paměti v době selhání.

    Selhání mohlo být způsobeno malwarem nebo obsahovat malware. Aby se zabránilo detekci bezpečnostními produkty, používají různé formy malwaru útok bez souborů, který zabraňuje zápisu na disk nebo šifrování softwarových součástí zapsaných na disk. Tento typ útoku je obtížné zjistit pomocí tradičních přístupů založených na disku.

    Však pomocí analýzy paměti, můžete zjistit tento druh útoku. Analýzou paměti v výpisu stavu systému zabezpečení centrum může zjistit techniky útoku používá. Útok se například může pokoušet zneužít chyby zabezpečení v softwaru, získat přístup k důvěrným datům a tajně přetrvávat v ohroženém počítači. Security Center tuto práci s minimálním dopadem na výkon pro hostitele.

    Podrobnosti o výstrahách analýzy výpisu stavu systému stavu systému naleznete v [referenční tabulce výstrah](alerts-reference.md#alerts-windows).

* **Detekce** <a name="windows-fileless"></a> útoků bez souborů – útoky bez souborů zaměřené na vaše koncové body jsou běžné. Aby se zabránilo detekci, útoky bez souborů injektují škodlivé datové části do paměti. Datové části útočníka přetrvávají v paměti ohrožených procesů a provádějí širokou škálu škodlivých aktivit.

    Díky detekci útoků bez souborů identifikují automatizované forenzní techniky paměti sady nástrojů, techniky a chování bez souborů. Toto řešení pravidelně prohledává váš počítač za běhu a extrahuje poznatky přímo z paměti procesů kritických pro zabezpečení.

    Najde důkazy o zneužití, vkládání kódu a provádění škodlivých datových částí. Detekce útoků bez souborů generuje podrobná výstrahy zabezpečení pro urychlení třídění výstrah, korelace a následné doby odezvy. Tento přístup doplňuje řešení EDR založená na událostech a poskytuje větší detekční pokrytí.

    Podrobnosti o výstrahách detekce útoků bez souborů naleznete v [referenční tabulce výstrah](alerts-reference.md#alerts-windows).

> [!TIP]
> Výstrahy systému Windows můžete simulovat stažením [playbooku Centra zabezpečení Azure: Výstrahy zabezpečení](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046).






## <a name="threat-protection-for-linux-machines"></a>Ochrana před hrozbami pro počítače s Linuxem<a name="linux-machines"></a>

Security Center shromažďuje záznamy auditu z počítačů s Linuxem pomocí **auditovaných**, což je jeden z nejběžnějších architektur pro auditování Linuxu. kontrolované hody v hlavním jádru. 

* **Linux auditované výstrahy a Microsoft Monitoring Agent (MMA) integrace** <a name="linux-auditd"></a> - Auditovaný systém se skládá z podsystému na úrovni jádra, který je zodpovědný za sledování volání systému. Filtruje je podle zadané sady pravidel a zapisuje zprávy pro ně do soketu. Security Center integruje funkce z auditovaného balíčku v rámci Microsoft Monitoring Agent (MMA). Tato integrace umožňuje shromažďování auditovaných událostí ve všech podporovaných distribucích Linuxu bez jakýchkoli vyložených.  

    auditované záznamy jsou shromažďovány, obohaceny a agregovány do událostí pomocí agenta Linux MMA. Security Center neustále přidává nové analýzy, které používají signály Linuxu k detekci škodlivého chování na cloudových a místních počítačích s Linuxem. Podobně jako funkce systému Windows se tyto analýzy rozprostírají napříč podezřelými procesy, pochybnými pokusy o přihlášení, načítáním modulu jádra a dalšími aktivitami. Tyto aktivity mohou naznačovat, že stroj je buď napaden, nebo byl porušen.  

    Seznam výstrah linuxových záznamů naleznete v [referenční tabulce výstrah](alerts-reference.md#alerts-linux).

> [!TIP]
> Výstrahy Linuxu můžete simulovat stažením [playbooku Azure Security Center: Linux Detections](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).





## <a name="threat-protection-for-azure-app-service"></a>Ochrana před hrozbami pro službu Azure App Service<a name="app-services"></a>

> [!NOTE]
> Tato služba není momentálně dostupná ve vládních a suverénních cloudových oblastech Azure.

Security Center používá rozsah cloudu k identifikaci útoků zaměřených na aplikace spuštěné přes App Service. Útočníci sondují webové aplikace, aby našli a zneužili slabiny. Před směrováním do konkrétních prostředí procházejí požadavky na aplikace spuštěné v Azure několika branami, kde jsou kontrolovány a protokolovány. Tato data se pak používají k identifikaci zneužití a útočníků a k naučení nových vzorů, které budou použity později.

Pomocí viditelnosti, kterou Azure má jako poskytovatele cloudu, Security Center analyzuje interní protokoly služby App Service k identifikaci metodiky útoku na více cílů. Metodika například zahrnuje rozsáhlé skenování a distribuované útoky. Tento typ útoku obvykle pochází z malé podmnožiny IP adresy a zobrazuje vzorky procházení na podobné koncové body na více hostitelích. Útoky hledají zranitelnou stránku nebo plugin a nelze je identifikovat z hlediska jednoho hostitele.

Pokud používáte plán služby App Service založené na Windows, centrum zabezpečení má také přístup k základním karanténám zabezpečení a virtuálním mům. Spolu s výše uvedenými údaji protokolu může infrastruktura vyprávět příběh, od nového útoku, který koluje ve volné přírodě, až po kompromisy v zákaznických strojích. Proto i v případě, že Security Center je nasazen a po webová aplikace byla zneužita, může být schopen detekovat probíhající útoky.

Seznam výstrah služby Azure App Service najdete v [tabulce referenčních výstrah](alerts-reference.md#alerts-azureappserv).

Další informace o plánech služby App Service najdete v tématu [Plány služby App Service](https://azure.microsoft.com/pricing/details/app-service/plans/).





## <a name="threat-protection-for-azure-containers"></a>Ochrana před hrozbami pro kontejnery Azure<a name="azure-containers"></a>

> [!NOTE]
> Tato služba není momentálně dostupná ve vládních a suverénních cloudových oblastech Azure.

Security Center poskytuje ochranu před hrozbami v reálném čase pro kontejnerizovaná prostředí a generuje výstrahy pro podezřelé aktivity. Pomocí těchto informací můžete rychle opravit problémy se zabezpečením a vylepšit zabezpečení kontejnerů.

Security Center poskytuje ochranu před hrozbami na různých úrovních: 

* **Úroveň hostitele** – agent Centra zabezpečení (k dispozici na úrovni Standard, [podrobnosti](security-center-pricing.md) o cenách) monitoruje Linux kvůli podezřelým aktivitám. Agent aktivuje výstrahy pro podezřelé aktivity pocházející z uzlu nebo kontejneru spuštěného na něm. Příklady takových aktivit zahrnují detekci webového prostředí a spojení se známými podezřelými IP adresami.

    Pro hlubší přehled o zabezpečení vašeho kontejnerizovaného prostředí agent monitoruje analýzy specifické pro kontejnery. Spustí výstrahy pro události, jako je vytváření privilegovaných kontejnerů, podezřelý přístup k serverům rozhraní API a servery Secure Shell (SSH) spuštěné uvnitř kontejneru Dockeru.

    >[!IMPORTANT]
    > Pokud se rozhodnete agenty neinstalovat do hostitelů, obdržíte pouze podmnožinu výhod ochrany před hrozbami a výstrah zabezpečení. Stále budete dostávat upozornění týkající se analýzy sítě a komunikace se škodlivými servery.

    Seznam výstrah na úrovni hostitele naleznete v [tabulce referenčních výstrah](alerts-reference.md#alerts-containerhost).


* Na **úrovni clusteru AKS**je ochrana před hrozbami založena na analýze protokolů auditu Společnosti Kubernetes. Chcete-li povolit toto monitorování **bez agentů,** přidejte do předplatného možnost Kubernetes ze stránky **Nastavení nastavení cenové &** (viz [ceny).](security-center-pricing.md) Chcete-li generovat výstrahy na této úrovni, Security Center monitoruje služby spravované AKS pomocí protokolů načtených AKS. Příklady událostí na této úrovni zahrnují vystavené řídicí panely Kubernetes, vytváření vysokých privilegovaných rolí a vytváření citlivých připojení.

    >[!NOTE]
    > Security Center generuje výstrahy zabezpečení pro akce služby Azure Kubernetes a nasazení, ke kterým dochází po povolení možnosti Kubernetes v nastavení předplatného. 

    Seznam výstrah na úrovni clusteru AKS naleznete v [referenční tabulce výstrah](alerts-reference.md#alerts-akscluster).

Také náš globální tým bezpečnostních výzkumníků neustále sleduje prostředí hrozeb. Přidávají výstrahy specifické pro kontejnery a chyby zabezpečení, jakmile jsou zjištěny.

> [!TIP]
> Upozornění na kontejnery můžete simulovat podle pokynů v [tomto příspěvku blogu](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>Ochrana před hrozbami pro databázi SQL a datový sklad SQL<a name="data-sql"></a>

Pokročilá ochrana před internetovými technologiemi pro Azure SQL Database detekuje neobvyklé aktivity označující neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

Zobrazí se výstrahy, pokud existují podezřelé databázové aktivity, potenciální chyby zabezpečení nebo útoky injektáží SQL a neobvyklé vzory přístupu k databázi a dotazů.

Pokročilá ochrana před internetovými hrozbami pro Azure SQL Database a SQL je součástí jednotného balíčku [Advanced Data Security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) pro pokročilé možnosti zabezpečení SQL, který zahrnuje Azure SQL Databases, spravované instance Azure SQL Database, databáze Azure SQL Data Warehouse a sql servery na virtuálních počítačích Azure.

Další informace naleznete v tématu:

* [Jak povolit pokročilou ochranu před internetovými technologiemi pro Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Jak povolit pokročilou ochranu před internetovými hrozbami pro servery SQL ve virtuálních počítačích Azure](security-center-iaas-advanced-data.md)
* [Seznam výstrah ochrany před hrozbami pro databázi SQL a datový sklad SQL](alerts-reference.md#alerts-sql-db-and-warehouse)




## <a name="threat-protection-for-azure-storage"></a>Ochrana před hrozbami pro Azure Storage<a name="azure-storage"></a>

> [!NOTE]
> Tato služba je dostupná v amerických vládních cloudech, ale žádné jiné suverénní nebo azure vládní cloudové oblasti.

Pokročilá ochrana před internetovými hrozbami pro úložiště (která je v současné době k dispozici jenom pro úložiště objektů Blob) detekuje neobvyklé a potenciálně škodlivé pokusy o přístup k účtům úložiště nebo o jejich zneužití. Tato úroveň ochrany umožňuje řešit hrozby, aniž byste museli být odborníkem na zabezpečení, a pomáhá vám spravovat systémy monitorování zabezpečení.

Další informace naleznete v tématu:

* [Jak povolit pokročilou ochranu před internetovými hrozbami pro azure storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Seznam výstrah ochrany před hrozbami pro Azure Storage](alerts-reference.md#alerts-azurestorage)


> [!TIP]
> Výstrahy Azure Storage můžete simulovat podle pokynů v [tomto příspěvku blogu](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131).




## <a name="threat-protection-for-azure-cosmos-db"></a>Ochrana před hrozbami pro Azure Cosmos DB<a name="cosmos-db"></a>

Výstrahy Azure Cosmos DB jsou generovány neobvyklé a potenciálně škodlivé pokusy o přístup nebo zneužití účtů Azure Cosmos DB.

Další informace naleznete v tématu:

* [Pokročilá ochrana před hrozbami pro Azure Cosmos DB (preview)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Seznam výstrah ochrany před hrozbami pro Azure Cosmos DB (Preview)](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Ochrana před hrozbami pro síťovou vrstvu Azure<a name="network-layer"></a>

Analýza síťové vrstvy Security Center je založena na ukázkových [datech IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), což jsou hlavičky paketů shromážděné základními směrovači Azure. Na základě tohoto zdroje dat používá Centrum zabezpečení modely strojového učení k identifikaci a označení škodlivých dopravních aktivit. Security Center také používá databázi Microsoft Threat Intelligence k obohacení IP adres.

Některé konfigurace sítě mohou středisku zabezpečení zabránit ve generování výstrah týkajících se podezřelé síťové aktivity. Centrum zabezpečení pro generování síťových výstrah zajistěte, aby:

- Váš virtuální počítač má veřejnou IP adresu (nebo je na nástroj pro vyrovnávání zatížení s veřejnou IP adresou).

- Přenosy odchozích přenosů v síti virtuálního počítače nejsou blokovány externím řešením IDS.

- Virtuálnímu počítači byla přiřazena stejná IP adresa za celou hodinu, během které došlo k podezřelé komunikaci. To platí také pro virtuální společnosti vytvořené jako součást spravované služby (například AKS, Databricks).

Seznam výstrah síťové vrstvy Azure najdete v [tabulce reference výstrah](alerts-reference.md#alerts-azurenetlayer).

Podrobnosti o tom, jak může centrum zabezpečení používat signály související se sítí k použití ochrany před [hrozbami, naleznete v tématu Heuristické detekce DNS v Centru zabezpečení](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).



## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Ochrana před hrozbami pro vrstvu správy Azure (Azure Resource Manager) (preview)<a name ="management-layer"></a>

Vrstva ochrany Centra zabezpečení založená na Azure Resource Manageru je momentálně ve verzi Preview.

Security Center nabízí další vrstvu ochrany pomocí událostí Azure Resource Manager, který se považuje za rovinu řízení pro Azure. Analýzou záznamů Azure Resource Manager, Security Center zjistí neobvyklé nebo potenciálně škodlivé operace v prostředí předplatného Azure.

Seznam výstrah Správce prostředků Azure (preview) najdete v [tabulce referenčních výstrah](alerts-reference.md#alerts-azureresourceman).



>[!NOTE]
> Některé z předchozích analýz jsou poháněny Microsoft Cloud App Security. Chcete-li těžit z těchto analýz, musíte aktivovat licenci Cloud App Security. Pokud máte licenci Cloud App Security, pak jsou tyto výstrahy ve výchozím nastavení povolené. Zakázání výstrah:
>
> 1. V okně **Centrum zabezpečení** vyberte **zásady zabezpečení**. U předplatného, které chcete změnit, vyberte **Upravit nastavení**.
> 2. Vyberte **detekci ohrožení**.
> 3. V části **Povolit integrace**zrušte zaškrtnutí **políčka Povolit zabezpečení Microsoft Cloud App Security přístup k mým datům**a vyberte **Uložit**.

>[!NOTE]
>Security Center ukládá zákaznická data související se zabezpečením ve stejné zeměpisné oblasti jako její prostředek. Pokud společnost Microsoft ještě nenasadila Centrum zabezpečení v geografické mno žaznosti prostředku, uloží data do Spojených států. Pokud je povoleno cloudové zabezpečení aplikací, tyto informace se ukládají v souladu s pravidly geografické polohy cloudového zabezpečení aplikací. Další informace naleznete [v tématu Ukládání dat pro neregionální služby](https://azuredatacentermap.azurewebsites.net/).








## <a name="threat-protection-for-azure-key-vault-preview"></a>Ochrana před hrozbami pro trezor klíčů Azure (preview)<a name="azure-keyvault"></a>

> [!NOTE]
> Tato služba není momentálně dostupná ve vládních a suverénních cloudových oblastech Azure.

Azure Key Vault je cloudová služba, která chrání šifrovací klíče a tajné klíče, jako jsou certifikáty, připojovací řetězce a hesla. 

Azure Security Center zahrnuje Azure nativní, pokročilou ochranu před hrozbami pro Azure Key Vault, která poskytuje další vrstvu inteligence zabezpečení. Security Center detekuje neobvyklé a potenciálně škodlivé pokusy o přístup nebo zneužití účtů trezoru klíčů. Tato vrstva ochrany umožňuje řešit hrozby bez odborníka na zabezpečení a bez nutnosti spravovat systémy monitorování zabezpečení jiných výrobců.  

Když dojde k neobvyklým aktivitám, Centrum zabezpečení zobrazí výstrahy a volitelně je odešle e-mailem správcům předplatného. Tyto výstrahy zahrnují podrobnosti o podezřelé aktivitě a doporučení, jak vyšetřovat a narušovat hrozby. 

Seznam výstrah azure key vault uhlečuje v [referenční tabulce výstrah](alerts-reference.md#alerts-azurekv).





## <a name="threat-protection-for-other-microsoft-services"></a>Ochrana před hrozbami pro jiné služby společnosti Microsoft<a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Ochrana před hrozbami pro Azure WAF<a name="azure-waf"></a>

Azure Application Gateway nabízí Firewall webových aplikací (WAF) poskytující centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožením zabezpečení.

Webové aplikace jsou stále více terčem škodlivých útoků, které zneužívají běžně známé chyby zabezpečení. Waf aplikační brány je založen na základní sadě pravidel 3.0 nebo 2.2.9 z projektu zabezpečení otevřených webových aplikací. WAF se aktualizuje automaticky, aby byl chráněn před novými chybami zabezpečení. 

Pokud máte licenci pro Azure WAF, vaše waf výstrahy jsou vyponožené do Security Center bez další konfigurace potřebné. Další informace o výstrahách generovaných systémem WAF naleznete v [tématu Skupiny pravidel a pravidla brány firewall webových aplikací .](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31)


### <a name="threat-protection-for-azure-ddos-protection"></a>Ochrana před hrozbami pro ochranu Azure DDoS<a name="azure-ddos"></a>

Distribuované útoky odmítnutí služby (DDoS) je známo, že je snadné spustit. Staly se velkým bezpečnostním problémem, zejména pokud přesouváte aplikace do cloudu. 

Útok DDoS se pokusí vyčerpat prostředky aplikace, takže aplikace není k dispozici oprávněným uživatelům. Útoky DDoS mohou cílit na libovolný koncový bod, který lze dosáhnout prostřednictvím internetu.

Chcete-li se bránit proti útokům DDoS, kupte si licenci pro Azure DDoS Protection a ujistěte se, že dodržujete osvědčené postupy návrhu aplikací. DDoS Protection poskytuje různé úrovně služeb. Další informace naleznete v [tématu Přehled ochrany DDoS Azure](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Seznam výstrah Azure DDoS Protection naleznete v [referenční tabulce výstrah](alerts-reference.md#alerts-azureddos).


## <a name="next-steps"></a>Další kroky
Další informace o výstrahách zabezpečení z těchto funkcí ochrany před hrozbami naleznete v následujících článcích:

* [Referenční tabulka pro všechny výstrahy Azure Security Center](alerts-reference.md)
* [Výstrahy zabezpečení ve službě Azure Security Center](security-center-alerts-overview.md)
* [Správa a reakce na upozornění zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Export výstrah a doporučení zabezpečení (Preview)](continuous-export.md)