---
title: Architektura řešení bez agentů
description: Přečtěte si o Azure Defenderu pro architekturu a tok informací bez agentů IoT.
ms.topic: overview
ms.date: 1/25/2021
ms.author: shhazam
ms.openlocfilehash: 99ccd0597fddaa86a2452160fc2cbfdadab87a1b
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784862"
---
# <a name="azure-defender-for-iot-architecture"></a>Azure Defender pro architekturu IoT

Tento článek popisuje funkční systémovou architekturu programu Defender pro řešení bez agentů IoT. Azure Defender pro IoT nabízí dvě sady možností, které vyhovují potřebám vašeho prostředí, řešení bez agentů pro organizace a řešení založené na agentech pro sestavovatele zařízení.

## <a name="agentless-solution-for-organizations"></a>Řešení bez agentů pro organizace
### <a name="defender-for-iot-components"></a>Defender pro součásti IoT

Defender pro IoT se připojuje ke cloudu Azure i k místním součástem. Řešení je navržené pro škálovatelnost ve velkých i geograficky distribuovaných prostředích s více vzdálenými umístěními. Toto řešení umožňuje vícevrstvou distribuovanou architekturu podle zemí, oblastí, organizační jednotky nebo zóny. 

Azure Defender pro IoT zahrnuje tyto komponenty: 

**Nasazení připojená ke cloudu**

- Azure Defender pro virtuální počítač nebo zařízení se senzorem IoT
- Azure Portal pro správu cloudu a integraci do Azure Sentinel
- Místní Konzola pro správu pro správu místních lokalit
- Vložený Agent zabezpečení (volitelné)

**Nasazení air-gapped (offline)**

- Azure Defender pro virtuální počítač nebo zařízení se senzorem IoT
- Místní Konzola pro správu pro správu místních lokalit

:::image type="content" source="./media/architecture/defender-iot-security-architecture-v3.png" alt-text="Architektura pro program Defender pro IoT.":::

### <a name="azure-defender-for-iot-sensors"></a>Azure Defender pro IoT snímače

Defender pro IoT snímače zjistí a nepřetržitě monitoruje síťová zařízení. Senzory shromažďují síťový provoz ICS pomocí pasivního monitorování (bez agentů) na zařízeních IoT a OT. 
 
Technologie bez agentů, které jsou sestavené pro IoT a síť, přináší v rámci připojení k síti hloubkovou viditelnost v oblasti IoT a nebezpečí. Má žádný vliv na výkon sítě a síťových zařízení z důvodu neinvazivního přístupu k NTA (Network provoz Analysis). 
 
Použití analytických a vysoce používaných analýz chování a hloubkové kontroly paketů vrstvy 7 na úrovni, která umožňuje provádět analýzu nad rámec tradičních řešení založených na podpisech a okamžitě zjišťovat pokročilé IoT a hrozby (například malware bez souborů) na základě neobvyklé nebo neoprávněné aktivity. 
  
Defender pro senzory IoT se připojí k portu SPAN nebo k síti KLEPNE a okamžitě zahájí provádění DPI na síťovém provozu IoT a OT. 
 
Shromažďování dat, zpracování, analýzy a upozorňování probíhá přímo na senzoru. Tento proces je ideální pro umístění s nízkou šířkou pásma nebo vysokou latencí, protože do konzoly pro správu se přenáší jenom metadata.

Senzor zahrnuje pět vyhledávačů analýz. Moduly aktivují výstrahy na základě analýzy v reálném čase i v předem zaznamenaném provozu. K dispozici jsou následující stroje: 

#### <a name="protocol-violation-detection-engine"></a>Modul pro detekci porušení protokolu
Modul pro detekci narušení protokolu identifikuje použití struktur paketů a hodnot polí porušujících specifikace protokolu ICS, například: Modbus Exception a spuštění zastaralých výstrah kódu funkce.

#### <a name="policy-violation-detection-engine"></a>Modul pro detekci porušení zásad
Pomocí strojového učení modul pro detekci porušení zásad upozorní uživatele na jakékoli odchylky od chování podle směrného plánu, jako je například neoprávněné použití konkrétních kódů funkcí, přístup ke konkrétním objektům nebo změny konfigurace zařízení. Příklad: DeltaV verze softwaru se změnila a neautorizované výstrahy programování pro PLC. Konkrétně modul porušení zásad modeluje sítě ICS jako deterministické sekvence stavů a přechodů, a to s využitím patentované techniky nazvané modelování průmyslových konečných stavů (IFSM). Modul pro detekci porušení zásad vytváří základní hodnoty sítí ICS, takže platforma vyžaduje kratší období učení, aby bylo možné sestavit základní hodnotu sítě, než jsou obecné matematické přístupy nebo analýzy, které byly původně vyvinuty spíše než v síti.

#### <a name="industrial-malware-detection-engine"></a>Průmyslový modul pro detekci malwaru
Průmyslový modul pro detekci malwaru identifikuje chování, které indikuje přítomnost známého malwaru, jako je například Conficker, černá energie, Havex, WannaCry, NotPetya a Triton. 

#### <a name="anomaly-detection-engine"></a>Modul detekce anomálií
Modul detekce anomálií detekuje neobvyklou komunikaci a chování počítače na počítač (M2M). Díky modelování sítí ICS jako deterministické sekvence stavů a přechodů vyžaduje platforma kratší období učení než obecné matematické přístupy nebo analýzy, které byly pro něj původně vyvinuty, spíše než z. Také detekuje anomálie rychleji, s minimálními falešně falešně pozitivními. Mezi výstrahy modulu pro detekci anomálií patří nadměrné pokusy o přihlášení k protokolu SMB a zjištěné výstrahy při kontrole PLC.

#### <a name="operational-incident-detection"></a>Zjišťování provozního incidentu
Detekce provozního incidentu detekuje provozní problémy, jako je přerušované připojení, které může označovat předčasné znaménko selhání zařízení. Například zařízení se považuje za odpojené (nereagují) a v příkazu Siemens S7 stop PLC byly odeslány výstrahy.

### <a name="management-consoles"></a>Konzoly pro správu
Správa Azure Defenderu pro IoT napříč hybridními prostředími se provádí pomocí dvou portálů pro správu: 
- Konzola senzorů
- Místní Konzola pro správu
- Azure Portal

### <a name="sensor-console"></a>Konzola senzorů
Detekce senzorů se zobrazují v konzole senzorů, kde je můžete zobrazit, prošetřit a analyzovat v mapě sítě, inventáři zařízení a v rozsáhlých sestavách, například v sestavách hodnocení rizik, dotazech dolování dat a vektorech útoků. Konzolu nástroje můžete také použít k zobrazení a zpracování hrozeb zjištěných moduly senzorů, předávajících informace partnerským systémům, správě uživatelů a dalších možností.

:::image type="content" source="./media/architecture/sensor-console-v2.png" alt-text="Defender pro konzolu IoT snímač":::

### <a name="on-premises-management-console"></a>Místní Konzola pro správu
Místní Konzola pro správu umožňuje operátorům SOC (Security Operations Center) spravovat a analyzovat výstrahy agregované z různých senzorů do jednoho jediného řídicího panelu a poskytuje celkový přehled o stavu sítí typu OT.

Tato architektura poskytuje komplexní jednotný pohled na síť na úrovni SOC, optimalizovaném zpracování výstrah a řízení zabezpečení provozní sítě. tím se zajistí, že řízení rozhodování a řízení rizik zůstane bezchybného.

Kromě víceklientské architektury, monitorování, analýzy dat a centralizovaného dálkových řízení senzorů poskytuje konzola pro správu dodatečné nástroje pro údržbu systému (například vyloučení výstrah) a plně přizpůsobené funkce vytváření sestav pro každé vzdálené zařízení. Tato architektura podporuje místní správu na úrovni webu, úrovni zóny a globální správu v rámci SOC.

Konzolu pro správu lze nasadit pro konfiguraci s vysokou dostupností, která zajišťuje konzolu zálohování, která pravidelně přijímá zálohy všech konfiguračních souborů vyžadovaných pro obnovení. Pokud primární konzola selže, zařízení pro správu místní lokality se automaticky převezmou při synchronizaci s konzolou zálohování, aby se zachovala dostupnost bez přerušení.

Integrovaná integrace s pracovními postupy SOC a spouštěním knih umožňuje snadnou prioritu aktivit zmírňování a korelace mezi různými pracovišti hrozeb.

- Holistický – zmenšete složitost s jednou sjednocenou platformou pro správu zařízení, rizika a správu ohrožení zabezpečení a s reakcí na incidenty.

- Agregace a korelace – zobrazí, agreguje a analyzuje data a výstrahy shromážděné ze všech lokalit.

- Řízení všech senzorů – nakonfigurujte a monitorujte všechny snímače z jednoho místa.

   :::image type="content" source="media/updates/alerts-and-site-management-v2.png" alt-text="Spravujte všechna vaše upozornění a informace.":::

### <a name="azure-portal"></a>portál Azure

Portál Defender pro IoT v Azure vám umožňuje:

- Koupit zařízení řešení

- Instalace a aktualizace softwaru

- Zprovoznění senzorů do Azure

- Aktualizace balíčků pro analýzu hrozeb

## <a name="see-also"></a>Viz také

[Defender pro IoT – Nejčastější dotazy](resources-frequently-asked-questions.md)

[Systémové požadavky](quickstart-system-prerequisites.md)
