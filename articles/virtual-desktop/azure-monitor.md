---
title: Použití monitorování virtuálních klientských počítačů s Windows – Azure
description: Jak používat Azure Monitor pro virtuální počítače s Windows
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 7da35f77dd232e5f523e2bdc3f125c68015ab871
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448181"
---
# <a name="use-azure-monitor-for-windows-virtual-desktop-to-monitor-your-deployment"></a>Monitorování nasazení pomocí Azure Monitor pro virtuální počítač s Windows

Azure Monitor pro virtuální počítače s Windows je řídicí panel založený na Azure Monitorch sešitech, které pomáhají profesionálům v oblasti IT pochopit prostředí virtuálních počítačů s Windows. V tomto tématu se dozvíte, jak nastavit Azure Monitor pro virtuální plochu Windows pro monitorování prostředí virtuálních počítačů s Windows.

## <a name="requirements"></a>Požadavky

Než začnete používat Azure Monitor pro virtuální počítače s Windows, musíte nastavit následující věci:

- Všechna prostředí virtuálních počítačů s Windows, která sledujete, musí být založená na nejnovější verzi Virtual desktopu Windows, která je kompatibilní s Azure Resource Manager.
- Nejméně jeden nakonfigurovaný pracovní prostor Log Analytics. Pro hostitele relací virtuálních počítačů s Windows použijte určený pracovní prostor Log Analytics, abyste zajistili, že se čítače výkonu a události shromažďují jenom z hostitelů relací v nasazení virtuálních počítačů s Windows.
- V pracovním prostoru Log Analytics povolte shromažďování dat pro následující věci:
    - Diagnostika z prostředí virtuálních počítačů s Windows
    - Doporučené čítače výkonu z hostitelů relace virtuálních počítačů s Windows
    - Doporučené protokoly událostí Windows z hostitelů relace virtuálních počítačů s Windows

 Jediný postup, který je popsaný v tomto článku, je ten, který budete potřebovat k monitorování virtuálního klienta Windows. Můžete zakázat všechny ostatní položky odesílající data do svého pracovního prostoru Log Analytics a ušetřit tak náklady.

Azure Monitor monitorování všech uživatelů pro vaše prostředí bude potřebovat taky následující oprávnění ke čtení:

- Přístup pro čtení předplatných Azure, která uchovávají vaše prostředky virtuálních klientů Windows
- Přístup pro čtení do skupin prostředků předplatného, které obsahují hostitele relace virtuálních klientů Windows
- Přístup pro čtení k pracovnímu prostoru Log Analytics nebo pracovním prostorům

>[!NOTE]
> Oprávnění jen pro čtení umožňuje správcům zobrazit data. Budou potřebovat různá oprávnění ke správě prostředků na portálu virtuální plochy Windows.

## <a name="open-azure-monitor-for-windows-virtual-desktop"></a>Otevřít Azure Monitor pro virtuální počítač s Windows

Azure Monitor pro virtuální plochu Windows můžete otevřít jedním z následujících způsobů:

- Přejít na [aka.MS/azmonwvdi](https://aka.ms/azmonwvdi).
- Vyhledejte a vyberte **virtuální počítač s Windows** z Azure Portal a pak vyberte **přehledy**.
- Vyhledejte a vyberte **Azure monitor** z Azure Portal. V části **přehledy** vyberte **centrum Insights** a pak vyberte **virtuální klient Windows**.
Po otevření stránky zadejte **předplatné**, **skupinu prostředků**, **fond hostitelů** a **časový rozsah** prostředí, které chcete monitorovat.

>[!NOTE]
>Virtuální klient Windows v současné době podporuje jenom monitorování jednoho předplatného, skupiny prostředků a fondu hostitelů. Pokud nemůžete najít prostředí, které chcete monitorovat, přečtěte si [dokumentaci k řešení problémů](troubleshoot-azure-monitor.md) se známými požadavky na funkce a problémy.

## <a name="log-analytics-settings"></a>Nastavení Log Analytics

Pokud chcete začít používat Azure Monitor pro virtuální počítače s Windows, budete potřebovat aspoň jeden pracovní prostor Log Analytics. Pro hostitele relací virtuálních počítačů s Windows použijte určený pracovní prostor Log Analytics, abyste zajistili, že čítače výkonu a události budou jenom hostitelé relace v nasazení virtuálních klientů Windows. Pokud už máte nastavený pracovní prostor, přeskočte dopředu a [nastavte ho pomocí konfiguračního sešitu](#set-up-using-the-configuration-workbook). Pokud ho chcete nastavit, přečtěte si téma [Vytvoření pracovního prostoru Log Analytics v Azure Portal](../azure-monitor/logs/quick-create-workspace.md).

>[!NOTE]
>Použijí se standardní poplatky za úložiště dat pro Log Analytics. Chcete-li začít, doporučujeme vám zvolit Model průběžných plateb a upravit při škálování nasazení a využít více dat. Další informace najdete v tématu [Azure monitor ceny](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="set-up-using-the-configuration-workbook"></a>Nastavení pomocí sešitu konfigurace

Pokud otevíráte Azure Monitor pro virtuální počítač s Windows poprvé, budete muset nastavit Azure Monitor pro prostředí virtuálních počítačů s Windows. Konfigurace prostředků:

1. Otevřete Azure Monitor pro virtuální počítač s Windows v Azure Portal na [aka.MS/azmonwvdi](https://aka.ms/azmonwvdi)a pak vyberte **sešit konfigurace**.
2. Vyberte prostředí, které chcete nakonfigurovat v části **předplatné**, **Skupina prostředků** a **fond hostitelů**.

Sešit konfigurace nastaví prostředí pro monitorování a umožní vám kontrolu konfigurace po dokončení procesu instalace. V případě, že se položky na řídicím panelu nezobrazuje správně nebo když skupina produktů zveřejňuje aktualizace, které vyžadují nové nastavení, je důležité ověřit konfiguraci.

### <a name="resource-diagnostic-settings"></a>Nastavení diagnostiky prostředků

Pokud chcete shromažďovat informace o infrastruktuře virtuálních počítačů s Windows, budete muset povolit několik diagnostických nastavení v fondech hostitelů virtuálních počítačů s Windows a v pracovních prostorech (Toto je váš pracovní prostor Windows VM, ne váš Log Analytics pracovní prostor). Další informace o fondech hostitelů, pracovních prostorech a dalších objektech prostředků virtuálního počítače s Windows najdete v naší [příručce k prostředí](environment-setup.md).

Další informace o diagnostice virtuálních počítačů s Windows a podporovaných tabulkách diagnostiky najdete v části [odeslání diagnostiky virtuálních klientů Windows do Log Analytics](diagnostics-log-analytics.md).

Nastavení diagnostiky prostředků v sešitě konfigurace: 

1. V sešitě konfigurace vyberte kartu **nastavení diagnostiky prostředků** . 
2. Vyberte **Log Analytics pracovní prostor** pro odeslání diagnostiky virtuálních klientů Windows. 

#### <a name="host-pool-diagnostic-settings"></a>Nastavení diagnostiky fondu hostitelů

Nastavení diagnostiky fondu hostitelů pomocí části nastavení diagnostiky prostředků v sešitě konfigurace:

1. V části **fond hostitelů** zkontrolujte, jestli je povolená Diagnostika virtuálních počítačů s Windows. Pokud nejsou, zobrazí se chybová zpráva s informacemi o tom, že se pro vybraný fond hostitelů nenašla žádná existující konfigurace diagnostiky. Budete muset povolit následující podporované diagnostické tabulky:

    - CheckPoint
    - Chyba
    - Správa
    - Připojení
    - HostRegistration
    - AgentHealthStatus
    
    >[!NOTE]
    > Pokud se nezobrazí chybová zpráva, nemusíte provádět kroky 2 až 4.

2. Vyberte **Konfigurovat fond hostitelů**.
3. Vyberte **Nasadit**.
4. Aktualizujte sešit konfigurace.

#### <a name="workspace-diagnostic-settings"></a>Nastavení diagnostiky pracovního prostoru 

Nastavení diagnostiky pracovního prostoru pomocí části nastavení diagnostiky prostředků v sešitě konfigurace:

 1. V části **pracovní prostor** zkontrolujte, jestli je v pracovním prostoru virtuální plocha Windows povolená Diagnostika virtuálních klientů Windows. Pokud nejsou, zobrazí se chybová zpráva s informacemi o tom, že pro vybraný pracovní prostor nebyla nalezena žádná existující konfigurace diagnostiky. Budete muset povolit následující podporované diagnostické tabulky:
 
    - CheckPoint
    - Chyba
    - Správa
    - Informační kanál
    
    >[!NOTE]
    > Pokud se nezobrazí chybová zpráva, nemusíte provádět kroky 2-4.

2. Vyberte **Konfigurovat pracovní prostor**.
3. Vyberte **Nasadit**.
4. Aktualizujte sešit konfigurace.

### <a name="session-host-data-settings"></a>Nastavení dat hostitele relace

Chcete-li shromažďovat informace o hostitelích relace virtuálních počítačů s Windows, je nutné nainstalovat agenta Log Analytics ve všech hostitelích relace ve fondu hostitelů, zajistěte, aby hostitelé relace odesílali do pracovního prostoru Log Analytics a nakonfigurovali nastavení agenta Log Analytics pro shromažďování údajů o výkonu a protokolech událostí systému Windows.

Pracovní prostor Log Analytics, do kterého odesíláte data hostitele relace, nemusí být stejný jako ten, který odesílá diagnostická data do. Pokud máte hostitele relace Azure mimo prostředí virtuálních klientských počítačů s Windows, doporučujeme, abyste pro hostitele relace virtuálních počítačů s Windows určili určený Log Analytics pracovní prostor. 

Chcete-li nastavit pracovní prostor Log Analytics, ve kterém chcete shromažďovat data hostitele relace: 

1. Vyberte kartu **Nastavení dat hostitele relace** v sešitě konfigurace. 
2. Vyberte **pracovní prostor Log Analytics** , do kterého chcete data hostitele relace odeslat. 

#### <a name="session-hosts"></a>Hostitelé relace

Musíte nainstalovat agenta Log Analytics do všech hostitelů relací ve fondu hostitelů a odesílat data z těchto hostitelů do vybraného pracovního prostoru Log Analytics. Pokud není Log Analytics nakonfigurovaný pro všechny hostitele relací ve fondu hostitelů, zobrazí se v horní části **Nastavení dat hostitele** relace část **hostitelé relace** se zprávou "Někteří hostitelé ve fondu hostitelů neodesílají data do vybraného pracovního prostoru Log Analytics."

>[!NOTE]
> Pokud se nezobrazí část **hostitelé relace** nebo chybová zpráva, jsou správně nastaveny všichni hostitelé relace. Přeskočte dopředu k nastavení pokynů pro [čítače výkonu pracovního prostoru](#workspace-performance-counters).

Nastavení zbývajících hostitelů relací pomocí sešitu konfigurace:

1. Vyberte **Přidat hostitele do pracovního prostoru**. 
2. Aktualizujte sešit konfigurace.

>[!NOTE]
>Aby bylo možné nainstalovat rozšíření Log Analytics, musí být spuštěn hostitelský počítač. Pokud automatické nasazení nefunguje, můžete místo toho nainstalovat rozšíření na hostitele ručně. Informace o tom, jak rozšíření nainstalovat ručně, najdete v tématu [Log Analytics rozšíření virtuálních počítačů pro Windows](../virtual-machines/extensions/oms-windows.md).

#### <a name="workspace-performance-counters"></a>Čítače výkonu pracovního prostoru

Abyste mohli shromažďovat informace o výkonu z hostitelů relací a odesílat je do pracovního prostoru Log Analytics, budete muset povolit konkrétní čítače výkonu.

Pokud již máte povolené čítače výkonu a chcete je odebrat, postupujte podle pokynů v části [Konfigurace čítačů výkonu](../azure-monitor/agents/data-sources-performance-counters.md). Čítače výkonu můžete přidat a odebrat ve stejném umístění.

Postup nastavení čítačů výkonu pomocí konfiguračního sešitu: 

1. V části **čítače výkonu pracovního prostoru** v sešitě konfigurace zkontrolujte **nakonfigurované čítače** , aby se zobrazily čítače, které jste už povolili odeslání do Log Analytics pracovního prostoru. Zkontrolujte **chybějící čítače** a ujistěte se, že jste povolili všechny požadované čítače.
2. Pokud máte čítače chybějící, vyberte **Konfigurovat čítače výkonu**.
3. Vyberte **použít konfiguraci**.
4. Aktualizujte sešit konfigurace.
5. Zkontrolujte, zda jsou všechny požadované čítače povoleny, a to kontrolou seznamu **chybějících čítačů** . 

#### <a name="configure-windows-event-logs"></a>Konfigurace protokolů událostí systému Windows

Pro shromažďování chyb, varování a informací z hostitelů relací a jejich odesílání do pracovního prostoru Log Analytics budete taky muset povolit konkrétní protokoly událostí systému Windows.

Pokud jste již protokoly událostí systému Windows povolili a chcete je odebrat, postupujte podle pokynů v části [Konfigurace protokolů událostí systému Windows](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs).  Protokoly událostí systému Windows můžete přidávat a odebírat ve stejném umístění.

Postup nastavení protokolů událostí systému Windows pomocí konfiguračního sešitu:

1. V části **Konfigurace protokolů událostí systému Windows** zkontrolujte **konfigurované protokoly událostí** , abyste viděli protokoly událostí, které jste už povolili pro odeslání do pracovního prostoru Log Analytics. Zkontrolujte **chybějící protokoly událostí** a ujistěte se, že jste povolili všechny protokoly událostí systému Windows.
2. Pokud máte chybějící protokoly událostí systému Windows, vyberte **Konfigurovat události**.
3. Vyberte **Nasadit**.
4. Aktualizujte sešit konfigurace.
5. Zkontrolujte, zda jsou všechny požadované protokoly událostí systému Windows povoleny kontrolou seznamu **chybějících protokolů událostí** . 

>[!NOTE]
>Pokud se automatické nasazení události nepovede, v sešitě konfigurace vyberte **otevřít konfiguraci agenta** a ručně přidejte všechny chybějící protokoly událostí systému Windows. 

## <a name="optional-configure-alerts"></a>Volitelné: Konfigurace výstrah

Azure Monitor pro virtuální počítače s Windows umožňuje sledovat Azure Monitor výstrahy, ke kterým dochází v rámci vybraného předplatného v kontextu dat virtuálních klientů Windows. Výstrahy Azure Monitor jsou volitelnou funkcí v předplatných Azure a je třeba je nastavit odděleně od Azure Monitor pro virtuální počítače s Windows. Pomocí architektury Azure Monitor Alerts můžete nastavit vlastní výstrahy na událostech, diagnostikě a prostředcích virtuálních počítačů s Windows. Další informace o výstrahách Azure Monitor najdete v tématu [reakce na události s výstrahami Azure monitor](../azure-monitor/alerts/tutorial-response.md).

## <a name="diagnostic-and-usage-data"></a>Diagnostika a data o používání

Společnost Microsoft automaticky shromažďuje data o využití a výkonu prostřednictvím služby Azure Monitor. Společnost Microsoft používá tato data ke zlepšení kvality, zabezpečení a integrity služby.

Aby bylo možné poskytovat přesné a efektivní možnosti odstraňování potíží, zahrnují shromážděná data ID relace portálu, Azure Active Directory ID uživatele a název karty portálu, kde došlo k události. Microsoft neshromažďuje jména, adresy ani jiné kontaktní údaje.

Další informace o shromažďování a používání dat naleznete v tématu [prohlášení o zásadách ochrany osobních údajů služby Microsoft Online Services](https://privacy.microsoft.com/privacystatement).

>[!NOTE]
>Další informace o zobrazení nebo odstranění vašich osobních údajů shromažďovaných službou najdete v tématu [žádosti o služby Azure Data GDPR](/microsoft-365/compliance/gdpr-dsr-azure). Další informace o GDPR najdete v [části GDPR portálu pro důvěryhodnost služby](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).

## <a name="next-steps"></a>Další kroky

Teď, když jste nakonfigurovali Azure Monitor pro prostředí virtuálních počítačů s Windows, najdete tady některé materiály, které vám pomůžou začít monitorovat vaše prostředí:

- Podívejte se na náš [Glosář](azure-monitor-glossary.md) , kde najdete další informace o pojmech a konceptech souvisejících s Azure monitor pro virtuální počítače s Windows.
- Pokud chcete odhadnout, měřit a spravovat náklady na úložiště dat, přečtěte si téma [odhad nákladů na Azure monitor](azure-monitor-costs.md).
- Pokud narazíte na problém, podívejte se do [Průvodce odstraňováním potíží](troubleshoot-azure-monitor.md) , kde najdete nápovědu a známé problémy.
