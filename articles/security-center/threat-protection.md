---
title: Ochrana před hrozbami v Azure Security Center
description: Toto téma popisuje prostředky chráněné funkcí ochrany před internetovými útoky v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: e2f72cc0ea6851caaf5d0db2f17f8e16473d420e
ms.sourcegitcommit: 0194a29a960e3615f96a2d9d8a7e681cf3e8f9ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89667543"
---
# <a name="threat-protection-in-azure-security-center"></a>Ochrana před hrozbami v Azure Security Center

Pokud Security Center zjistí hrozbu v jakékoli oblasti vašeho prostředí, vygeneruje výstrahu. Tyto výstrahy popisují podrobnosti o ovlivněných prostředcích, navrhovaných krocích pro nápravu a v některých případech možnost aktivovat aplikaci logiky v reakci.

Ochrana před hrozbami Azure Security Center poskytuje komplexní ochranu vašeho prostředí:

* **Ochrana před hrozbami pro výpočetní prostředky Azure**: počítače s Windows, počítače se systémem Linux, Azure App Service a kontejnery Azure

* **Ochrana před hrozbami pro datové prostředky Azure**: SQL Database a Azure synapse Analytics (dřív SQL Data Warehouse), Azure Storage a Azure Cosmos DB

* **Ochrana před hrozbami pro vrstvy služeb Azure**: síťová vrstva Azure, vrstva správy azure (Azure Resource Manager) (Preview) a Azure Key Vault (Preview)

Bez ohledu na to, jestli je výstraha vygenerovaná Security Center nebo přijatá Security Center z jiného bezpečnostního produktu, můžete ji exportovat. Pokud chcete exportovat výstrahy do Azure Sentinel, všech SIEM třetích stran nebo jakýchkoli jiných externích nástrojů, postupujte podle pokynů v tématu [Export výstrah do Siem](continuous-export.md). 

> [!NOTE]
> Zobrazení výstrah z různých zdrojů může trvat různě dlouho. Například výstrahy, které vyžadují analýzu síťového provozu, mohou trvat déle než výstrahy související s podezřelými procesy běžícími na virtuálních počítačích.

> [!TIP]
> Pokud chcete povolit možnosti ochrany před hrozbami Security Center, musíte použít cenovou úroveň Standard u předplatného, které obsahuje příslušné úlohy.
>
> Ochranu před hrozbami pro **účty Azure Storage** můžete povolit na úrovni předplatného nebo na úrovni prostředků.
> Ochranu před hrozbami pro **Azure SQL Database SQL servery** můžete povolit buď na úrovni předplatného, nebo na úrovni prostředků.
> Ochranu před hrozbami pro **Azure Database for MariaDB/MySQL/PostgreSQL** můžete povolit jenom na úrovni prostředků.



## <a name="threat-protection-for-windows-machines"></a>Ochrana před hrozbami pro počítače s Windows <a name="windows-machines"></a>

Azure Security Center se integruje se službami Azure za účelem monitorování a ochrany počítačů se systémem Windows. Security Center prezentuje výstrahy a návrhy oprav ze všech těchto služeb ve snadno použitelném formátu.

* **Microsoft Defender Advanced Threat Protection (ATP)** <a name="windows-atp"></a> – Security Center rozšiřuje své cloudové platformy na ochranu díky integraci s pokročilou ochranou před internetovými útoky v programu Microsoft Defender (ATP). Společně poskytují komplexní možnosti detekce a odezvy koncových bodů (EDR).

    > [!IMPORTANT]
    > Senzor ATP v programu Microsoft Defender je automaticky povolen na serverech se systémem Windows, které používají Security Center.

    Když ATP v programu Microsoft Defender zjistí hrozbu, aktivuje výstrahu. Výstraha se zobrazí na řídicím panelu Security Center. Z řídicího panelu se můžete překlopit do konzoly ATP v programu Microsoft Defender a provést podrobné šetření, které odhalí rozsah útoku. Další informace o ochraně ATP v programu Microsoft Defender najdete v tématu připojení [serverů ke službě Microsoft Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

* Detekce útoků typu **soubor** <a name="windows-fileless"></a> – Bezsouborová útoky vkládají do paměti škodlivá datová data, aby se zabránilo detekci pomocí technik prohledávání na základě disku. Datová část útočníka pak přetrvává v paměti napadených procesů a provádí široké spektrum škodlivých aktivit.

    Díky detekci útoků bez souborů můžou automatizované techniky forenzní paměti identifikovat sady nástrojů, techniky a chování pro útoky bez souborů. Toto řešení pravidelně kontroluje počítač za běhu a extrahuje přehledy přímo z paměti procesů. Konkrétní přehledy pro Linux zahrnují identifikaci: 

    - Dobře známé sady nástrojů a software pro dolování kryptografických softwaru 
    - Skrytý spustitelný kód, což je malý kus kódu, který se obvykle používá jako datová část při využívání ohrožení zabezpečení softwaru.
    - Vložený škodlivý spustitelný soubor v paměti procesu

    Detekce útoků bez souborů generuje podrobné výstrahy zabezpečení obsahující popisy s dalšími metadaty procesu, například síťovou aktivitou. Tím se zrychluje jejich třídění, korelace a doba odezvy za příjem dat. Tento přístup doplňuje řešení EDR založené na událostech a poskytuje zvýšené pokrytí detekce.

    Podrobnosti o výstrahách detekce útoků bez souborů najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-windows).

> [!TIP]
> Výstrahy Windows můžete simulovat stažením [Azure Security Center PlayBook: výstrahy zabezpečení](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046).






## <a name="threat-protection-for-linux-machines"></a>Ochrana před hrozbami pro počítače se systémem Linux <a name="linux-machines"></a>

Security Center shromažďuje záznamy auditu z počítačů se systémem Linux pomocí **auditu**, jeden z nejběžnějších rozhraní pro auditování Linux. auditované života v jádru hlavní. 

* **Auditované výstrahy systému Linux a integrace** <a name="linux-auditd"></a> agenta Log Analytics – Auditované systémy se skládají z podsystému na úrovni jádra, který je zodpovědný za monitorování systémových volání. Filtruje je podle zadané sady pravidel a zapisuje zprávy pro ně do soketu. Security Center integruje funkce z auditovaného balíčku v rámci agenta Log Analytics. Tato integrace umožňuje shromažďování auditovaných událostí ve všech podporovaných distribucích systému Linux bez jakýchkoli požadavků.

    auditované záznamy jsou shromažďovány, obohaceny a agregovány do událostí pomocí agenta Log Analytics pro Linux. Security Center průběžně přidává nové analýzy, které používají signály Linux k detekci škodlivého chování na cloudových a místních počítačích se systémem Linux. Podobně jako funkce Windows tyto analýzy přesahují podezřelé procesy, pokusy o přihlášení k podezřelých, načítání modulů jádra a další činnosti. Tyto aktivity mohou znamenat, že počítač je buď napadený, nebo byl napadený.  

    Seznam výstrah pro Linux najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-linux).

> [!TIP]
> Výstrahy pro Linux můžete simulovat stažením [Azure Security Center PlayBook: detekce Linux](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).





## <a name="threat-protection-for-azure-app-service"></a>Ochrana před hrozbami pro Azure App Service <a name="app-services"></a>

> [!NOTE]
> Tato služba není aktuálně dostupná v oblastech cloudu Azure pro státní správu a svrchované oblasti.

Security Center používá škálování cloudu k identifikaci útoků cílících na aplikace běžící přes App Service. Útočníci hledají webové aplikace a využívají slabých míst. Před směrováním do konkrétních prostředí požadavky na aplikace běžící v Azure procházejí několika branami, kde jsou zkontrolovány a protokolovány. Tato data se pak používají k identifikaci zneužití a útočníků a k získání nových vzorů, které se použijí později.

Díky viditelnosti, kterou Azure nabízí jako poskytovatel cloudu, Security Center analyzuje App Service interní protokoly k identifikaci metodologie útoku na více cílů. Například metodologie zahrnuje rozšířenou kontrolu a distribuované útoky. Tento typ útoku obvykle pochází z malé podmnožiny IP adres a ukazuje vzory procházení k podobným koncovým bodům na více hostitelích. Útoky hledají zranitelnou stránku nebo modul plug-in a nelze je identifikovat z hlediska jednoho hostitele.

Pokud používáte plán App Service založený na Windows, Security Center má také přístup k podkladovým izolovaným prostorům a virtuálním počítačům. Spolu s výše uvedenými daty protokolu může infrastruktura sdělit příběh z nového útoku, který je v případě ohrožení bezpečnosti v zákaznických počítačích v nestejné přírodě. Proto i v případě, že je Security Center nasazena po zneužití webové aplikace, může být schopna detekovat průběžné útoky.

Seznam výstrah Azure App Service najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-azureappserv).

Další informace o plánech App Service najdete v tématu [plány App Service](https://azure.microsoft.com/pricing/details/app-service/plans/).





## <a name="threat-protection-for-containers"></a>Ochrana před hrozbami pro kontejnery <a name="azure-containers"></a>

### <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Všeobecně dostupné|
|Stanov|Úroveň Standard|
|Požadované role a oprávnění:|**Správce zabezpečení** může zrušit výstrahy.<br>**Čtenář zabezpečení** může zobrazit zjištění.|
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![No](./media/icons/no-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov)|
|||

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]








## <a name="threat-protection-for-sql-database-and-azure-synapse-analytics-formerly-sql-data-warehouse"></a>Ochrana před hrozbami pro SQL Database a Azure synapse Analytics (dřív SQL Data Warehouse) <a name="data-sql"></a>

Rozšířená ochrana před internetovými útoky pro Azure SQL Database detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

Výstrahy se zobrazí, když dojde k podezřelým databázovým aktivitám, potenciálním ohrožením zabezpečení nebo útokům prostřednictvím injektáže SQL a k neobvyklé přístupu k databázi a ke vzorům dotazů.

Rozšířená ochrana před internetovými útoky pro Azure SQL Database a SQL je součástí sjednoceného balíčku [zabezpečení dat (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) pro pokročilé funkce zabezpečení SQL, zahrnující Azure SQL Database, spravované instance Azure SQL, databáze Azure synapse Analytics (dřív SQL Data Warehouse) a SQL servery na Virtual Machines Azure.

Další informace naleznete v tématu:

* [Jak povolit rozšířenou ochranu před internetovými útoky pro Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Jak povolit rozšířenou ochranu před internetovými útoky pro SQL servery na Azure Virtual Machines](security-center-iaas-advanced-data.md)
* [Seznam výstrah ochrany před hrozbami pro SQL Database a Azure synapse Analytics (dřív SQL Data Warehouse)](alerts-reference.md#alerts-sql-db-and-warehouse)



## <a name="threat-protection-for-azure-storage"></a>Ochrana před hrozbami pro Azure Storage <a name="azure-storage"></a>

### <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|[BLOB Storage](https://azure.microsoft.com/services/storage/blobs/) (Obecná dostupnost)<br>[Soubory Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) (Preview)<br>[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) (Preview)|
|Stanov|Úroveň Standard|
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) Čína gov, jiné gov|
|||


### <a name="whats-protected"></a>Co je chráněno?

Ochrana před hrozbami pro Azure Storage detekuje na vašich Azure Storage účtech potenciálně škodlivé aktivity. Data je možné chránit bez ohledu na to, jestli jsou uložená jako kontejnery objektů blob, sdílené složky nebo datová jezera.

Tato vrstva ochrany umožňuje řešit hrozby, *aniž* byste museli být odborníkem na zabezpečení a pomáhají spravovat systémy monitorování zabezpečení.

### <a name="what-kind-of-alerts-does-threat-protection-for-azure-storage-provide"></a>Jaký typ výstrah ochrana před hrozbami pro Azure Storage poskytnout?

Výstrahy zabezpečení se aktivují, když dojde k těmto akcím:

- **Podezřelá aktivita** – například účet úložiště byl úspěšně ZÍSKÁN z IP adresy, která se označuje jako aktivní uzel ukončení systému.
- **Neobvyklé chování** – například změny ve vzoru přístupu k účtu úložiště
- **Potenciální nahraný malware** – analýza reputace hodnoty hash indikuje, že nahraný soubor obsahuje malware.

Výstrahy obsahují podrobnosti o incidentu, který je aktivoval, a doporučení ohledně toho, jak tyto hrozby prozkoumat a opravit.

### <a name="what-is-hash-reputation-analysis-for-malware"></a>Co je analýza reputace hodnot hash pro malware?

Pokud chcete zjistit, jestli je nahraný soubor podezřelý, ochrana před hrozbami pro Azure Storage používá analýzu reputace hodnoty hash, kterou podporuje [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684). Nástroje ochrany před internetovými útoky neskenují nahrané soubory, ale prozkoumají protokoly úložiště a porovnávají hodnoty hash nově nahraných souborů o známých virech, trojských koním, spywaru a ransomwarem. 

Pokud je podezření, že se jedná o malware, Security Center zobrazí výstrahu a může volitelně poslat e-mail vlastníkovi úložiště k odstranění podezřelého souboru. Chcete-li nastavit automatické odebrání souborů, které analýza reputace hodnot hash indikuje jako malware, nasaďte [automatizaci pracovního postupu, která aktivuje výstrahy obsahující "potenciální malware nahraný do účtu úložiště"](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).



### <a name="next-steps"></a>Další kroky 

Podrobnosti o cenách, včetně bezplatné 30denní zkušební verze, najdete na stránce s [cenami Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

Další informace naleznete v tématu:

* [Jak povolit rozšířenou ochranu před internetovými útoky pro Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Seznam výstrah ochrany před hrozbami pro Azure Storage](alerts-reference.md#alerts-azurestorage)
* [Možnosti logiky hrozeb Microsoftu](https://go.microsoft.com/fwlink/?linkid=2128684)

> [!TIP]
> Výstrahy úložiště můžete simulovat podle pokynů v [tomto blogovém příspěvku](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131).







## <a name="threat-protection-for-azure-cosmos-db-preview"></a>Ochrana před hrozbami pro Azure Cosmos DB (Preview) <a name="cosmos-db"></a>

Výstrahy Azure Cosmos DB jsou generovány neobvyklými a potenciálně škodlivými pokusy o přístup k účtům Azure Cosmos DB nebo jejich zneužití.

Další informace naleznete v tématu:

* [Rozšířená ochrana před internetovými útoky pro Azure Cosmos DB (Preview)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Seznam výstrah ochrany před hrozbami pro Azure Cosmos DB (Preview)](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Ochrana před hrozbami pro síťovou vrstvu Azure <a name="network-layer"></a>

Analýza Security Centerch síťových vrstev vychází z ukázkových [dat IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), což jsou hlavičky paketů shromážděné směrovači Azure Core. Na základě tohoto datového kanálu používá Security Center k identifikaci a označení aktivit škodlivého provozu modely strojového učení. Security Center k rozšíření IP adres používá taky databázi Microsoft Threat Intelligence.

Některé konfigurace sítě mohou omezit Security Center generování výstrah na podezřelé síťové aktivity. Pokud Security Center chcete generovat výstrahy sítě, zajistěte, aby:

- Váš virtuální počítač má veřejnou IP adresu (nebo se nachází ve službě Vyrovnávání zatížení s veřejnou IP adresou).

- Externí řešení identifikátorů ID neblokuje provoz odchozího výstupu sítě vašeho virtuálního počítače.

- Vašemu virtuálnímu počítači se přiřadila stejná IP adresa pro celou hodinu, během které došlo k podezřelé komunikaci. To platí také pro virtuální počítače vytvořené jako součást spravované služby (například AKS, datacihly).

Seznam výstrah síťových vrstev Azure najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-azurenetlayer).




## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Ochrana před hrozbami pro vrstvu správy Azure (Azure Resource Manager) (Preview)<a name ="management-layer"></a>

Vrstva ochrany Security Center založená na Azure Resource Manager je momentálně ve verzi Preview.

Security Center nabízí další vrstvu ochrany pomocí Azure Resource Managerch událostí, které se považují za řídicí plochu pro Azure. Díky analýze záznamů Azure Resource Manager Security Center detekuje neobvyklé nebo potenciálně škodlivé operace v prostředí předplatného Azure.

Seznam výstrah Azure Resource Manager (Preview) najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-azureresourceman).



>[!NOTE]
> Některé z předchozích analýz jsou napájené z Microsoft Cloud App Security. Pokud chcete tyto analýzy využít, musíte aktivovat licenci Cloud App Security. Pokud máte licenci Cloud App Security, jsou tyto výstrahy ve výchozím nastavení povolené. Zakázání výstrah:
>
> 1. V nabídce Security Center vyberte **cenové & nastavení**.
> 1. Vyberte předplatné, které chcete změnit.
> 1. Vyberte **detekci hrozeb**.
> 1. Zrušte zaškrtnutí políčka **Allow Microsoft Cloud App Security pro přístup k datům**a vyberte **Uložit**.

>[!NOTE]
>Security Center ukládá zákaznická data týkající se zabezpečení ve stejné geografické podobě jako její prostředek. Pokud společnost Microsoft ještě nebyla nasazena Security Center v geografickém prostředku, uloží data do USA. Pokud je povolená Cloud App Security, ukládají se tyto informace v souladu s pravidly geografického umístění Cloud App Security. Další informace najdete v tématu [úložiště dat pro jiné než regionální služby](https://azuredatacentermap.azurewebsites.net/).

1. Nastavte pracovní prostor, do kterého instalujete agenta. Ujistěte se, že je pracovní prostor ve stejném předplatném, které používáte v Security Center a máte oprávnění ke čtení a zápisu v pracovním prostoru.

1. Nastavte cenovou úroveň Standard a vyberte **Uložit**.






## <a name="threat-protection-for-azure-key-vault-preview"></a>Ochrana před hrozbami pro Azure Key Vault (Preview)<a name="azure-keyvault"></a>

> [!NOTE]
> Tato služba není aktuálně dostupná v oblastech cloudu Azure pro státní správu a svrchované oblasti.

Azure Key Vault je cloudová služba, která chrání šifrovací klíče a tajné kódy, jako jsou certifikáty, připojovací řetězce a hesla. 

Azure Security Center zahrnuje rozšířenou ochranu před internetovými útoky Azure pro Azure Key Vault a poskytuje další vrstvu funkcí Security Intelligence. Security Center detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k účtům Key Vault nebo jejich zneužití. Tato vrstva ochrany umožňuje řešit hrozby bez nutnosti provádět odborníky na zabezpečení a bez nutnosti spravovat systémy monitorování zabezpečení třetích stran.  

Když dojde k neobvyklé aktivitám, Security Center zobrazuje výstrahy a volitelně je odesílá prostřednictvím e-mailu správcům předplatného. Tyto výstrahy obsahují podrobnosti o podezřelé aktivitě a doporučení ohledně toho, jak tyto hrozby prozkoumat a opravit. 

Seznam výstrah Azure Key Vault najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-azurekv).





## <a name="threat-protection-for-other-microsoft-services"></a>Ochrana před hrozbami pro další služby Microsoftu <a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Ochrana před hrozbami pro Azure WAF <a name="azure-waf"></a>

Azure Application Gateway nabízí Firewall webových aplikací (WAF) poskytující centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožením zabezpečení.

Webové aplikace jsou stále více zaměřené na škodlivé útoky, které využívají často známá ohrožení zabezpečení. Application Gateway WAF vychází ze základní sady pravidel 3,0 nebo 2.2.9 v otevřeném projektu zabezpečení webové aplikace. WAF se automaticky aktualizuje, aby se chránila před novými chybami zabezpečení. 

Pokud máte licenci pro Azure WAF, vaše výstrahy WAF se streamují Security Center bez nutnosti další konfigurace. Další informace o výstrahách vygenerovaných službou WAF najdete v tématu [skupiny pravidel a pravidla pro Firewall webových aplikací](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="threat-protection-for-azure-ddos-protection"></a>Ochrana před hrozbami pro Azure DDoS Protection <a name="azure-ddos"></a>

Je známo, že je možné snadno spustit útok s cílem odepření služeb (DDoS). Jsou to skvělé problémy se zabezpečením, zejména pokud přesouváte aplikace do cloudu. 

Útok DDoS se pokusí vyčerpat prostředky aplikace, takže aplikace nebude k dispozici oprávněným uživatelům. Útoky DDoS můžou cílit na libovolný koncový bod, ke kterému se dá získat přístup přes Internet.

Pokud chcete chránit před útoky DDoS, Zakupte si licenci pro Azure DDoS Protection a ujistěte se, že jste si jisti osvědčenými postupy pro návrh aplikace. DDoS Protection poskytuje různé úrovně služeb. Další informace najdete v tématu [přehled Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Seznam výstrah Azure DDoS Protection najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-azureddos).


## <a name="next-steps"></a>Další kroky
Další informace o výstrahách zabezpečení z těchto funkcí ochrany před hrozbami najdete v následujících článcích:

* [Referenční tabulka pro všechny výstrahy Azure Security Center](alerts-reference.md)
* [Výstrahy zabezpečení ve službě Azure Security Center](security-center-alerts-overview.md)
* [Správa a reakce na upozornění zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Exportovat výstrahy a doporučení zabezpečení (Preview)](continuous-export.md)