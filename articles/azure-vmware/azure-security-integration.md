---
title: Chraňte své virtuální počítače s řešeními VMware Azure pomocí Integrace Azure Security Center
description: Seznamte se s postupem ochrany virtuálních počítačů řešení Azure VMware pomocí nativních nástrojů zabezpečení Azure z jednoho řídicího panelu v Azure Security Center.
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 66270c5abef48f9d9f30ed1b262a6b4f2f8144c8
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99061460"
---
# <a name="protect-your-azure-vmware-solution-vms-with-azure-security-center-integration"></a>Chraňte své virtuální počítače s řešeními VMware Azure pomocí Integrace Azure Security Center

Nástroje Azure Native Security poskytují zabezpečenou infrastrukturu pro hybridní prostředí Azure, řešení Azure VMware a místní virtuální počítače (VM). V tomto článku se dozvíte, jak nastavit nástroje Azure pro zabezpečení hybridního prostředí. Budete používat různé nástroje k identifikaci a řešení různých typů hrozeb.

## <a name="azure-native-services"></a>Nativní služby Azure

Tady je stručný přehled každé nativní služby Azure:

- **Log Analytics pracovní prostor:** Log Analytics pracovní prostor je jedinečné prostředí pro ukládání dat protokolu. Každé pracovní prostředí má vlastní úložiště dat a konfiguraci. Zdroje dat a řešení jsou nakonfigurovány tak, aby ukládaly data do konkrétního pracovního prostoru.
- **Azure Security Center:** Azure Security Center je jednotný systém pro správu zabezpečení infrastruktury. Posílí stav zabezpečení datových center a poskytuje rozšířenou ochranu před hrozbami napříč hybridními úlohami v cloudu nebo místně.
- **Sentinel Azure:** Azure Sentinel je cloudová řešení pro správu událostí zabezpečení (SIEM) a automatizované reakce na orchestraci zabezpečení (společnosti). Poskytuje inteligentní analýzu zabezpečení a analýzu hrozeb v rámci prostředí. Je to jediné řešení pro detekci výstrah, viditelnost hrozeb, proaktivní lov a reakci na hrozby.

## <a name="topology"></a>Topologie

![Diagram znázorňující architekturu integrovaného zabezpečení Azure.](media/azure-security-integration/azure-integrated-security-architecture.png)

Agent Log Analytics umožňuje shromažďování dat protokolu z Azure, řešení Azure VMware a místních virtuálních počítačů. Data protokolu se odesílají do Azure Monitor protokolů a ukládají se do pracovního prostoru Log Analytics. Agenta Log Analytics můžete nasadit pomocí [rozšíření virtuálního počítače](../azure-arc/servers/manage-vm-extensions.md) servery s podporou ARC pro nové a stávající virtuální počítače. 

Po shromáždění protokolů v pracovním prostoru Log Analytics můžete nakonfigurovat pracovní prostor Log Analytics pomocí Azure Security Center. Azure Security Center vyhodnotí stav ohrožení zabezpečení virtuálních počítačů řešení Azure VMware a vyvolá upozornění na případné kritické ohrožení zabezpečení. Například vyhodnocuje chybějící opravy operačního systému, chybnou konfiguraci zabezpečení a službu [Endpoint Protection](../security-center/security-center-services.md).

V pracovním prostoru Log Analytics můžete nakonfigurovat Azure Sentinel pro detekci výstrah, viditelnost hrozeb, proaktivní lov a reakci na hrozby. V předchozím diagramu je Azure Security Center ke službě Azure Sentinel připojen pomocí konektoru Azure Security Center. Azure Security Center přepošle ohrožení zabezpečení prostředí do Azure Sentinel a vytvoří incident a namapuje se na další hrozby. Můžete také vytvořit dotaz na plánovaná pravidla, který zjistí nežádoucí aktivitu a převede ji na incidenty.

## <a name="benefits"></a>Výhody

- Nativní služby Azure je možné použít pro zabezpečení hybridního prostředí v Azure, řešení Azure VMware a místních služeb.
- Pomocí Log Analytics pracovního prostoru můžete shromažďovat data nebo protokoly do jednoho bodu a prezentovat stejná data pro různé nativní služby Azure.
- Azure Security Center poskytuje funkce zabezpečení, jako je monitorování integrity souborů, detekce útoků bez souborů, vyhodnocení opravy operačního systému, vyhodnocení chyb zabezpečení a hodnocení služby Endpoint Protection.
- Sentinel Azure umožňuje:
    - Shromažďovat data v cloudovém měřítku napříč všemi uživateli, zařízeními, aplikacemi a infrastrukturou, a to místně i v několika cloudech.
    - Zjistí dříve nezjištěné hrozby.
    - Prozkoumejte hrozby s využitím umělých funkcí a zjišťováním pro podezřelé aktivity se škálováním.
    - Můžete rychle reagovat na incidenty díky integrované orchestraci a automatizaci běžných úloh.

## <a name="create-a-log-analytics-workspace"></a>Vytvoření pracovního prostoru služby Log Analytics

Abyste mohli shromažďovat data z různých zdrojů, budete potřebovat Log Analytics pracovní prostor. Další informace najdete v tématu [Vytvoření pracovního prostoru Log Analytics z Azure Portal](../azure-monitor/learn/quick-create-workspace.md). 

## <a name="deploy-security-center-and-configure-azure-vmware-solution-vms"></a>Nasazení Security Center a konfigurace virtuálních počítačů s řešeními Azure VMware

Azure Security Center je předem nakonfigurovaný nástroj a nevyžaduje nasazení. V Azure Portal vyhledejte **Security Center** a vyberte ho.

### <a name="enable-azure-defender"></a>Povolení Azure Defenderu

Azure Defender rozšiřuje pokročilou ochranu před internetovými útoky v Azure Security Center napříč vašimi hybridními úlohami v místním prostředí i v cloudu. Proto je nutné povolit Azure Defender a chránit tak virtuální počítače s řešením Azure VMware. 

1. V Security Center vyberte **Začínáme**.

2. Vyberte kartu **upgrade** a pak vyberte své předplatné nebo pracovní prostor. 

3. Pokud chcete povolit Azure Defender, vyberte **upgradovat** .

## <a name="add-azure-vmware-solution-vms-to-security-center"></a>Přidání virtuálních počítačů řešení Azure VMware do Security Center

1. V Azure Portal vyhledejte **Azure ARC** a vyberte ho.

2. V části prostředky vyberte **servery** a potom **+ Přidat**.

    :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="Snímek obrazovky zobrazující stránku serverů Azure ARC pro přidání virtuálního počítače Azure VMware do Azure":::

3. Vyberte **vygenerovat skript**.
 
    :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="Snímek obrazovky se stránkou ARC Azure znázorňující možnost přidání serveru pomocí interaktivního skriptu"::: 
 
4. Na kartě **předpoklady** vyberte **Další**.

5. Na kartě **Podrobnosti o prostředku** vyplňte následující podrobnosti: 
    - Předplatné
    - Skupina prostředků
    - Region (Oblast) 
    - Operační systém
    - Podrobnosti proxy serveru
    
    Pak vyberte **Další: značky**.

6. Na kartě **značky** vyberte **Další**.

7. Na kartě **Stáhnout a spustit skript** vyberte **Stáhnout**.

8. Zadejte svůj operační systém a spusťte skript na VIRTUÁLNÍm počítači řešení Azure VMware.

## <a name="view-recommendations-and-passed-assessments"></a>Zobrazit doporučení a úspěšná posouzení

1. V Azure Security Center v levém podokně vyberte **inventář** .

2. Jako typ prostředku vyberte **servery – Azure ARC**.
 
     :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="Snímek obrazovky se stránkou inventáře Azure Security Center zobrazující servery – ARC Azure vybraný v části typ prostředku.":::

3. Vyberte název prostředku. Otevře se stránka se zobrazenými podrobnostmi o stavu zabezpečení vašeho prostředku.

4. V části **seznam doporučení** vyberte karty **doporučení**, **úspěšné posouzení** a **nedostupné hodnocení** , abyste zobrazili tyto podrobnosti.

    :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="Snímek obrazovky Azure Security Center zobrazující doporučení a posouzení zabezpečení":::

## <a name="deploy-an-azure-sentinel-workspace"></a>Nasazení pracovního prostoru služby Azure Sentinel

Azure Sentinel je postavená na Log Analytics pracovním prostoru. Prvním krokem při připojování služby Azure Sentinel je výběr pracovního prostoru Log Analytics, který chcete pro tento účel použít.

1. V Azure Portal vyhledejte službu **Azure Sentinel** a vyberte ji.

2. Na stránce pracovní prostory Azure Sentinel vyberte **+ Přidat**.

3. Vyberte pracovní prostor Log Analytics a vyberte **Přidat**.

## <a name="enable-data-collector-for-security-events-on-azure-vmware-solution-vms"></a>Povolení shromažďování dat pro události zabezpečení u virtuálních počítačů řešení Azure VMware

Nyní jste připraveni připojit Azure Sentinel k vašim zdrojům dat, v tomto případě události zabezpečení.

1. Na stránce pracovní prostory Azure Sentinel vyberte nakonfigurovaný pracovní prostor.

2. V části konfigurace vyberte **datové konektory**.

3. Ve sloupci název konektoru v seznamu vyberte možnost **události zabezpečení** a pak vyberte možnost **otevřít stránku konektoru**.

4. Na stránce konektor vyberte události, které chcete streamovat, a pak vyberte **použít změny**.

    :::image type="content" source="media/azure-security-integration/select-events-you-want-to-stream.png" alt-text="Snímek obrazovky se stránkou událostí zabezpečení v Azure Sentinel, kde můžete vybrat události ke streamování.":::

## <a name="connect-azure-sentinel-with-azure-security-center"></a>Připojení Azure Sentinel pomocí Azure Security Center  

1. Na stránce pracovní prostor Azure Sentinel vyberte nakonfigurovaný pracovní prostor.

2. V části konfigurace vyberte **datové konektory**.

3. V seznamu vyberte **Azure Security Center** a pak vyberte **otevřít stránku konektoru**.

    :::image type="content" source="media/azure-security-integration/connect-security-center-with-azure-sentinel.png" alt-text="Snímek obrazovky se stránkou datových konektorů ve službě Azure Sentinel ukazující výběr pro připojení Azure Security Center s využitím Azure Sentinel.":::

4. Vyberte **připojit** , pokud chcete Azure Security Center připojit k Azure Sentinel.

5. Povolit **vytvoření incidentu** pro vygenerování incidentu pro Azure Security Center.

## <a name="create-rules-to-identify-security-threats"></a>Vytvoření pravidel pro identifikaci bezpečnostních hrozeb

Po připojení zdrojů dat ke službě Azure Sentinel můžete vytvořit pravidla, která generují výstrahy na základě zjištěných hrozeb. V následujícím příkladu vytvoříme pravidlo pro identifikaci pokusů o přihlášení k Windows serveru s nesprávným heslem.

1. Na stránce Přehled služby Azure Sentinel v části konfigurace vyberte **Analytics**.

2. V části konfigurace vyberte **Analytics**.

3. Vyberte **+ vytvořit** a v rozevíracím seznamu vyberte **naplánované pravidlo dotazu**.

4. Na kartě **Obecné** zadejte požadované informace.

    - Název
    - Description
    - Taktika
    - Závažnost
    - Status

    Vyberte **Další: nastavit logiku pravidla >**.

5. Na kartě **nastavit logiku pravidla** zadejte požadované informace.

    - Dotaz na pravidlo (tady vidíte náš vzorový dotaz)
    
        ```
        SecurityEvent
        |where Activity startswith '4625'
        |summarize count () by IpAddress,Computer
        |where count_ > 3
        ```
        
    - Mapovat entity
    - Plánování dotazů
    - Prahová hodnota upozornění
    - Seskupování událostí
    - Potlačení

    Vyberte **Další**.

6. Na kartě **Nastavení incidentu** povolit možnost **vytvořit incidenty z výstrah aktivovaných pomocí tohoto pravidla analýzy** a vybrat **Další: Automatická odpověď >**.
 
    :::image type="content" source="media/azure-security-integration/create-new-analytic-rule-wizard.png" alt-text="Snímek obrazovky průvodce analytickým pravidlem pro vytvoření nového pravidla ve službě Azure Sentinel ukazující vytvoření incidentů z výstrah aktivovaných pomocí tohoto pravidla analýzy, které je povoleno.":::

7. Vyberte **Další: zkontrolujte >**.

8. Na kartě **Kontrola a vytvoření** zkontrolujte informace a vyberte **vytvořit**.

Po třetím neúspěšném pokusu o přihlášení k Windows serveru aktivuje vytvořené pravidlo incident pro všechny neúspěšné pokusy.

## <a name="view-generated-alerts"></a>Zobrazit vygenerované výstrahy

Vygenerované incidenty můžete zobrazit pomocí služby Azure Sentinel. Můžete také přiřadit incidenty a po jejich vyřešení je zavřít, a to vše z v rámci služby Azure Sentinel.

1. Přejít na stránku Přehled služby Azure Sentinel.

2. V části Správa hrozeb vyberte **incidenty**.

3. Vyberte incident. Pak můžete incident přiřadit týmu za účelem vyřešení.

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="Snímek stránky incidentů služby Azure Sentinel s vybraným incidentem a možnost přiřazení incidentu k řešení":::

    Po vyřešení problému ho můžete zavřít.

## <a name="hunt-security-threats-with-queries"></a>Prolovené bezpečnostní hrozby pomocí dotazů

Můžete vytvářet dotazy nebo používat dostupný předem definovaný dotaz v rámci Azure Sentinel k identifikaci hrozeb ve vašem prostředí. V následujících krocích spustíte předem definovaný dotaz.

1. Přejít na stránku Přehled služby Azure Sentinel.

2. V části Správa hrozeb vyberte možnost **lov**. Zobrazí se seznam předem definovaných dotazů.

3. Vyberte dotaz a pak vyberte **Spustit dotaz**.

4. Vyberte výsledky **zobrazení** a zkontrolujte výsledky.

### <a name="create-a-new-query"></a>Vytvoření nového dotazu

1.  V části Správa hrozeb vyberte možnost **lov** a potom **+ Nový dotaz**.

    :::image type="content" source="media/azure-security-integration/create-new-query.png" alt-text="Snímek stránky pro lov v rámci Azure Sentinel se zvýrazněnou možností + nový dotaz":::

2. Zadejte následující informace, chcete-li vytvořit vlastní dotaz.

    - Název
    - Description
    - Vlastní dotaz
    - Zadat mapování
    - Taktika
    
3. Vyberte **Vytvořit**. Pak můžete vybrat vytvořený dotaz, **Spustit dotaz** a **Zobrazit výsledky**.

## <a name="next-steps"></a>Další kroky

- Naučte se používat [řídicí panel Azure Defender](../security-center/azure-defender-dashboard.md).
- Prozkoumejte celou škálu ochrany, kterou nabízí [Azure Defender](../security-center/azure-defender.md).
- Seznamte [se s pokročilým zjišťováním útoků s více fázemi v Azure Sentinel](../azure-monitor/learn/quick-create-workspace.md).
