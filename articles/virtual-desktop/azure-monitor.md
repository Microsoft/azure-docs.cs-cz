---
title: Použití monitorování virtuálních klientských počítačů s Windows ve verzi Preview – Azure
description: Jak používat Azure Monitor pro virtuální počítače s Windows
author: Heidilohr
ms.topic: how-to
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e9da1071686dafa003a5a49d0864b77644493344
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594463"
---
# <a name="use-azure-monitor-for-windows-virtual-desktop-to-monitor-your-deployment-preview"></a>Monitorování nasazení pomocí Azure Monitor pro virtuální počítač s Windows (Preview)

>[!IMPORTANT]
>Azure Monitor pro virtuální počítač s Windows je momentálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučujeme ji používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Monitor pro virtuální počítače s Windows (Preview) je řídicí panel založený na Azure Monitorch sešitech, které pomáhají profesionálům v oblasti IT pochopit prostředí virtuálních počítačů s Windows. V tomto tématu se dozvíte, jak nastavit Azure Monitor pro virtuální plochu Windows pro monitorování prostředí virtuálních počítačů s Windows.

## <a name="requirements"></a>Požadavky

Než začnete používat Azure Monitor pro virtuální počítače s Windows, musíte nastavit následující věci:

- Všechna prostředí virtuálních počítačů s Windows, která sledujete, musí být založená na nejnovější verzi Virtual desktopu Windows, která je kompatibilní s Azure Resource Manager.

- Nejméně jeden nakonfigurovaný pracovní prostor Log Analytics.

- V pracovním prostoru Log Analytics povolte shromažďování dat pro následující věci:
    - Všechny požadované čítače výkonu
    - Všechny čítače výkonu nebo události používané v Azure Monitor pro virtuální počítače s Windows
    - Data z diagnostického nástroje pro všechny objekty v prostředí, které budete monitorovat.
    - Virtuální počítače v prostředí, které budete monitorovat.

Každý monitor Azure Monitor pro virtuální plochu Windows pro vaše prostředí bude taky potřebovat následující oprávnění ke čtení:

- Přístup pro čtení ke skupině prostředků, ve které se nacházejí prostředky prostředí.

- Přístup pro čtení skupin prostředků, ve kterých se nachází hostitelé relace prostředí

>[!NOTE]
> Oprávnění jen pro čtení umožňuje správcům zobrazit data. Budou potřebovat různá oprávnění ke správě prostředků na portálu virtuální plochy Windows.

## <a name="open-azure-monitor-for-windows-virtual-desktop"></a>Otevřít Azure Monitor pro virtuální počítač s Windows

Azure Monitor pro virtuální plochu Windows můžete otevřít jedním z následujících způsobů:

- Přejít na [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks).

- Vyhledejte a vyberte **virtuální počítač s Windows** z Azure Portal a pak vyberte **přehledy**.

- Vyhledejte a vyberte **Azure monitor** z Azure Portal. V části **přehledy** vyberte **centrum Insights** a v části **Další** vyberte **virtuální plochu Windows** otevřete řídicí panel na stránce Azure monitor.

Po otevření Azure Monitor pro virtuální počítač s Windows zaškrtněte jedno nebo více políček s označením **předplatné**, **Skupina prostředků**, **fond hostitelů** a **časový rozsah** na základě toho, které prostředí chcete monitorovat.

>[!NOTE]
>Virtuální klient Windows v současné době podporuje jenom monitorování jednoho předplatného, skupiny prostředků a fondu hostitelů. Pokud nemůžete najít prostředí, které chcete monitorovat, přečtěte si [dokumentaci k řešení problémů](troubleshoot-azure-monitor.md) se známými požadavky na funkce a problémy.

## <a name="set-up-with-the-configuration-workbook"></a>Nastavení pomocí sešitu konfigurace

Pokud Azure Monitor pro virtuální počítač s Windows poprvé otevíráte, budete muset nakonfigurovat Azure Monitor prostředků virtuálních počítačů s Windows. Konfigurace prostředků:

1. Otevřete sešit v Azure Portal.
2. Vyberte **otevřít sešit konfigurace**.

Sešit konfigurace nastaví prostředí pro monitorování a umožní vám kontrolu konfigurace po dokončení procesu instalace. Je důležité si ověřit konfiguraci v případě, že se položky na řídicím panelu nezobrazuje správně nebo když skupina produktů publikuje aktualizace, které vyžadují další datové body.

## <a name="host-pool-diagnostic-settings"></a>Nastavení diagnostiky fondu hostitelů

Nastavení diagnostiky Azure Monitor budete muset povolit u všech objektů v prostředí virtuálních počítačů s Windows, které tuto funkci podporují.

1. Otevřete Azure Monitor pro virtuální počítač s Windows na [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)a pak vyberte **sešit konfigurace**.

2. Vyberte prostředí, které chcete monitorovat v rámci **předplatného**, **skupiny prostředků** a **fondu hostitelů**.

3. V části **nastavení diagnostiky fondu hostitelů** zkontrolujte, jestli je pro fond hostitelů povolená Diagnostika virtuálních počítačů s Windows. Pokud nejsou, zobrazí se chybová zpráva s informacemi o tom, že se pro vybraný fond hostitelů nenašla žádná existující konfigurace diagnostiky. 
   
   Je třeba povolit následující tabulky:

    - CheckPoint
    - Chyba
    - Správa
    - Připojení
    - HostRegistration

    >[!NOTE]
    > Pokud se chybová zpráva nezobrazí, nemusíte provádět krok 4.

4. Vyberte **Konfigurovat fond hostitelů**.

5. Vyberte **Nasadit**.

6. Aktualizujte sešit.

Přečtěte si další informace o tom, jak povolit diagnostiku na všech objektech v prostředí virtuálních počítačů s Windows nebo získat přístup k pracovnímu prostoru Log Analytics v tématu [odeslání diagnostiky virtuálních klientů Windows do Log Analytics](diagnostics-log-analytics.md).

## <a name="configure-log-analytics"></a>Konfigurace služby Log Analytics

Pokud chcete začít používat Azure Monitor pro virtuální počítače s Windows, budete také potřebovat aspoň jeden pracovní prostor Log Analytics, abyste mohli shromažďovat data z prostředí, které chcete monitorovat, a dodat ho do sešitu. Pokud už máte nastavené nastavení, přeskočte dopředu a [nastavte čítače výkonu](#set-up-performance-counters). Pokud chcete nastavit nový pracovní prostor pro Log Analytics pro předplatné Azure, které obsahuje prostředí pro virtuální počítače s Windows, přečtěte si téma [Vytvoření pracovního prostoru Log Analytics v Azure Portal](../azure-monitor/logs/quick-create-workspace.md).

>[!NOTE]
>Použijí se standardní poplatky za úložiště dat pro Log Analytics. Chcete-li začít, doporučujeme vám zvolit Model průběžných plateb a upravit při škálování nasazení a využít více dat. Další informace najdete v tématu [Azure monitor ceny](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="set-up-performance-counters"></a>Nastavení čítačů výkonu

Pro kolekci je nutné povolit specifické čítače výkonu v příslušném vzorkovacím intervalu v pracovním prostoru Log Analytics. Tyto čítače výkonu jsou jediné čítače, které budete potřebovat k monitorování virtuálního klienta Windows. Můžete zakázat všechny ostatní, aby ušetřily náklady.

Pokud již máte povolené čítače výkonu a chcete je odebrat, postupujte podle pokynů v části [Konfigurace čítačů výkonu](../azure-monitor/agents/data-sources-performance-counters.md) a překonfigurujte čítače výkonu. I když tento článek popisuje, jak přidat čítače, můžete je také odebrat ve stejném umístění.

Pokud jste ještě nenastavili čítače výkonu, tady je postup, jak je nakonfigurovat pro Azure Monitor pro virtuální počítače s Windows:

1. Přejít na [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)a pak vyberte **sešit konfigurace** spodní části okna.

2. V části **konfigurace Log Analytics** vyberte pracovní prostor, který jste nastavili pro vaše předplatné.

3. V části **čítače výkonu pracovního prostoru** se zobrazí seznam čítačů potřebných pro monitorování. Na pravé straně tohoto seznamu zaškrtněte položky v seznamu **chybějící čítače** a povolte tak čítače, které budete potřebovat ke spuštění monitorování pracovního prostoru.

4. Vyberte **Konfigurovat čítače výkonu**.

5. Vyberte **použít konfiguraci**.

6. Aktualizujte sešit konfigurace a pokračujte v nastavování prostředí.

Po počáteční konfiguraci můžete také přidat nové čítače výkonu, kdykoli se služba aktualizuje a vyžaduje nové nástroje pro monitorování. To, jestli jsou všechny požadované čítače povolené, můžete ověřit tak, že je vyberete v seznamu **chybějících čítačů** .

>[!NOTE]
>Čítače výkonu zpoždění vstupu jsou kompatibilní pouze s Windows 10 RS5 a novějším nebo Windows serverem 2019 a novějším.

Další informace o tom, jak ručně přidat čítače výkonu, které ještě nejsou povolené pro shromažďování, najdete v tématu [Konfigurace čítačů výkonu](../azure-monitor/agents/data-sources-performance-counters.md).

### <a name="set-up-windows-events"></a>Nastavení událostí Windows

V dalším kroku budete muset povolit konkrétní události Windows pro shromažďování v pracovním prostoru Log Analytics. Události popsané v této části jsou jenom Azure Monitor pro potřeby virtuálních počítačů s Windows. Můžete zakázat všechny ostatní, aby ušetřily náklady.

Nastavení událostí systému Windows:

1. Pokud už máte události systému Windows povolené a chcete je odebrat, odeberte události, které nepotřebujete, než použijete konfigurační sešit, aby bylo možné povolit sadu požadovanou pro monitorování.

2. V aka.ms/azmonwvdi klikněte na Azure Monitor pro virtuální plochu Windows na [](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)a potom v dolní části okna vyberte **sešit konfigurace** .

3. V **konfiguraci událostí systému Windows** je k dispozici seznam událostí systému Windows, které jsou požadovány pro monitorování. Na pravé straně tohoto seznamu se nachází seznam **chybějících událostí** , kde najdete požadované názvy událostí a typy událostí, které nejsou aktuálně povolené pro váš pracovní prostor. Každý z těchto názvů si poznamenejte pro pozdější účely.

4. Vyberte možnost **otevřít konfiguraci pracovních prostorů**.

5. Vyberte **data**.

6. Vyberte **protokoly událostí systému Windows**.

7. Přidejte chybějící názvy událostí z kroku 3 a požadovaný typ události pro každý z nich.

8. Aktualizujte sešit konfigurace a pokračujte v nastavování prostředí.

Po počáteční konfiguraci můžete přidat nové události systému Windows, pokud aktualizace nástroje pro monitorování vyžadují povolení nových událostí. Abyste se ujistili, že máte povolené všechny požadované události, přejděte zpátky do seznamu **chybějících událostí** a povolte všechny chybějící události, které vidíte.

## <a name="install-the-log-analytics-agent-on-all-hosts"></a>Nainstalovat agenta Log Analytics na všech hostitelích

Nakonec budete muset nainstalovat agenta Log Analytics na všech hostitelích ve fondu hostitelů, aby se odesílaly data z hostitelů do vybraného pracovního prostoru.

Instalace agenta Log Analytics:

1. V aka.ms/azmonwvdi klikněte na Azure Monitor pro virtuální plochu Windows na [](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)a potom v dolní části okna vyberte **sešit konfigurace** .

2. Pokud není Log Analytics nakonfigurovaný pro všechny hostitele ve fondu hostitelů, zobrazí se v dolní části oddílu konfigurace Log Analytics chyba se zprávou "Někteří hostitelé v hostitelském fondu neodesílají data do vybraného Log Analytics pracovního prostoru." Vyberte **Přidat hostitele do pracovního prostoru** a přidejte tak vybrané hostitele. Pokud se chybová zpráva nezobrazí, zastavte se sem.

3. Aktualizujte sešit konfigurace.

>[!NOTE]
>Aby bylo možné nainstalovat rozšíření Log Analytics, musí být spuštěn hostitelský počítač. Pokud se automatické nasazení na hostiteli nepovede, můžete rozšíření vždy nainstalovat na hostitele ručně. Informace o tom, jak rozšíření nainstalovat ručně, najdete v tématu [Log Analytics rozšíření virtuálních počítačů pro Windows](../virtual-machines/extensions/oms-windows.md).

## <a name="optional-configure-alerts"></a>Volitelné: Konfigurace výstrah

Můžete nakonfigurovat Azure Monitor pro virtuální počítače s Windows, aby vás upozornila na to, jestli se ve vybraném předplatném nevyskytují závažné Azure Monitor výstrahy. Pokud to chcete provést, postupujte podle pokynů v tématu [reakce na události s výstrahami Azure monitor](../azure-monitor/alerts/tutorial-response.md).

## <a name="diagnostic-and-usage-data"></a>Diagnostika a data o používání

Společnost Microsoft automaticky shromažďuje data o využití a výkonu prostřednictvím služby Azure Monitor. Společnost Microsoft používá tato data ke zlepšení kvality, zabezpečení a integrity služby.

Aby bylo možné poskytovat přesné a efektivní možnosti odstraňování potíží, zahrnují shromážděná data ID relace portálu, Azure Active Directory ID uživatele a název karty portálu, kde došlo k události. Microsoft neshromažďuje jména, adresy ani jiné kontaktní údaje.

Další informace o shromažďování a používání dat naleznete v tématu [prohlášení o zásadách ochrany osobních údajů služby Microsoft Online Services](https://privacy.microsoft.com/privacystatement).

>[!NOTE]
>Další informace o zobrazení nebo odstranění vašich osobních údajů shromažďovaných službou najdete v tématu [žádosti o služby Azure Data GDPR](/microsoft-365/compliance/gdpr-dsr-azure). Další informace o GDPR najdete v [části GDPR portálu pro důvěryhodnost služby](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).

## <a name="next-steps"></a>Další kroky

Teď, když jste nakonfigurovali Azure Portal virtuálních počítačů s Windows, najdete tady některé materiály, které vám můžou pomáhat:

- Podívejte se na náš [Glosář](azure-monitor-glossary.md) , kde najdete další informace o pojmech a konceptech souvisejících s Azure monitor pro virtuální počítače s Windows.
- Pokud narazíte na problém, Projděte si [Průvodce řešením potíží](troubleshoot-azure-monitor.md) , kde najdete nápovědu.