---
title: Řešení potíží s monitorováním virtuálního počítače s Windows – Azure
description: Řešení potíží s Azure Monitor pro virtuální počítače s Windows
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/29/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: ef1a7f92fd31e8ae87a0ead6b5071d8240aa993e
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286398"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop"></a>Řešení potíží s Azure Monitor pro virtuální počítače s Windows

Tento článek obsahuje známé problémy a řešení pro běžné problémy v Azure Monitor pro virtuální počítače s Windows.

## <a name="issues-with-configuration-and-setup"></a>Problémy s konfigurací a nastavením

Pokud sešit konfigurace nepracuje správně a automatizuje instalaci, můžete tyto prostředky použít k ručnímu nastavení prostředí:

- Pokud chcete ručně povolit diagnostiku nebo získat přístup k pracovnímu prostoru Log Analytics, přečtěte si téma [odeslání diagnostiky virtuálních počítačů Windows do Log Analytics](diagnostics-log-analytics.md).
- Ruční instalace rozšíření Log Analytics na hostitele relace najdete v tématu [Log Analytics rozšíření virtuálních počítačů pro Windows](../virtual-machines/extensions/oms-windows.md).
- Pokud chcete nastavit nový pracovní prostor Log Analytics, přečtěte si téma [Vytvoření pracovního prostoru Log Analytics v Azure Portal](../azure-monitor/logs/quick-create-workspace.md).
- Pokud chcete přidat, odebrat nebo upravit čítače výkonu, přečtěte si téma [Konfigurace čítačů výkonu](../azure-monitor/agents/data-sources-performance-counters.md).
- Chcete-li nakonfigurovat protokoly událostí systému Windows pro pracovní prostor Log Analytics, přečtěte si téma [shromáždění zdrojů dat protokolu událostí systému Windows pomocí agenta Log Analytics](../azure-monitor/agents/data-sources-windows-events.md).

## <a name="my-data-isnt-displaying-properly"></a>Moje data se nezobrazují správně

Pokud se vaše data nezobrazují správně, podívejte se na následující běžná řešení:

- Nejdřív se ujistěte, že jste správně nastavili konfigurační sešit, jak je popsáno v tématu [použití Azure monitor pro virtuální plochu Windows k monitorování vašeho nasazení](azure-monitor.md). Pokud nějaké čítače nebo události chybí, nezobrazí se v Azure Portal žádná data, která jsou k nim přidružená.
- Ověřte přístupová oprávnění & obraťte se na majitele prostředků, aby požádala o chybějící oprávnění; Virtuální plocha pro monitorování všech uživatelů systému Windows vyžaduje následující oprávnění:
    - Přístup pro čtení předplatných Azure, která uchovávají vaše prostředky virtuálních klientů Windows
    - Přístup pro čtení do skupin prostředků předplatného, které obsahují hostitele relace virtuálních klientů Windows 
    - Přístup pro čtení z libovolných Log Analytics pracovních prostorů, které používáte
- Je možné, že budete muset v bráně firewall serveru otevřít odchozí porty, aby bylo možné Azure Monitor a Log Analytics odesílat data na portál. Další informace o tom, jak to provést, najdete v následujících článcích:
      - [Azure Monitor Odchozí porty](../azure-monitor/app/ip-addresses.md)
      - [Log Analytics požadavky brány firewall](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements). 
- Nezobrazuje se data z nedávné aktivity? Možná budete chtít počkat na 15 minut a aktualizovat informační kanál. Azure Monitor je doba latence 15 minut pro naplnění dat protokolu. Další informace najdete v tématu [čas příjmu dat protokolu v Azure monitor](../azure-monitor/logs/data-ingestion-time.md).

Pokud nechybí žádné informace, ale vaše data stále nejsou správně zobrazená, může se jednat o problém v dotazu nebo zdroji dat. Přečtěte si o [známých problémech a omezeních](#known-issues-and-limitations). 

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Chci přizpůsobit Azure Monitor pro virtuální počítače s Windows

Azure Monitor pro virtuální počítače s Windows používá Azure Monitor sešity. Sešity vám umožní uložit kopii šablony sešitu virtuálních počítačů s Windows a vytvořit vlastní vlastní nastavení.

Podle návrhu nebudou vlastní šablony sešitu automaticky přijímat aktualizace ze skupiny Products. Další informace najdete v tématu [řešení potíží s přehledem na základě sešitu](../azure-monitor/insights/troubleshoot-workbooks.md) a v části [sešity](../azure-monitor/visualize/workbooks-overview.md).

## <a name="i-cant-interpret-the-data"></a>Nejde interpretovat data

Přečtěte si další informace o datových termínech v [glosáři Azure monitor pro virtuální počítače s Windows](azure-monitor-glossary.md).

## <a name="the-data-i-need-isnt-available"></a>Potřebná data nejsou k dispozici.

Pokud chcete monitorovat více čítačů výkonu nebo protokolů událostí systému Windows, můžete jim povolit odesílání diagnostických informací do vašeho pracovního prostoru Log Analytics a jejich monitorování v **diagnostice hostitele: prohlížeč hostitele**. 

- Postup přidání čítačů výkonu najdete v tématu [Konfigurace čítačů výkonu](../azure-monitor/agents/data-sources-performance-counters.md#configuring-performance-counters) .
- Chcete-li přidat události systému Windows, přečtěte si téma [Konfigurace protokolů událostí systému Windows](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)

Nemůžete najít datový bod, který by mohl pomáhat diagnostikovat problém? Pošlete nám svůj názor!

- Informace o tom, jak ponechávat zpětnou vazbu, najdete v tématech [Přehled řešení potíží, zpětná vazba a podpora pro virtuální počítače s Windows](troubleshoot-set-up-overview.md).
- Zpětnou vazbu k virtuálnímu počítači s Windows můžete také opustit v [centru pro zpětnou vazbu na virtuálním počítači s Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

## <a name="known-issues-and-limitations"></a>Známé problémy a omezení

Níže jsou uvedené problémy a omezení, o kterých jsme se dozvěděli, a pracujeme na jejich řešení:

- Současně můžete monitorovat pouze jeden fond hostitelů. 
- Chcete-li uložit oblíbená nastavení, je nutné uložit vlastní šablonu sešitu. Vlastní šablony automaticky nepřijmou aktualizace z produktové skupiny.
- V sešitu konfigurace se někdy při načítání vašich výběrů zobrazí chyby "dotaz se nezdařil". Aktualizujte dotaz, v případě potřeby znovu zadejte svůj výběr a chybu by se měla vyřešit. 
- Některé chybové zprávy nejsou uživatelsky přívětivé, a ne všechny chybové zprávy jsou popsány v dokumentaci.
- Čítač výkonu celkový počet relací může v průběhu několika relací na malém čísle a celkový počet relací, které se mohou vyskytnout, jít nad rámec maximálního počtu relací.
- Počet dostupných relací neodráží zásady škálování ve fondu hostitelů.   
- Vidíte v konfliktu nebo neočekávané době připojení? V některých případech může událost dokončení připojení chybět a může mít vliv na některé vizuály a metriky.
- Čas potřebný k připojení zahrnuje dobu, kterou uživatel potřebuje k zadání přihlašovacích údajů. To je sladěné se zkušenostmi, ale v některých případech může zobrazit nepravdivé špičky. 
    

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak začít, najdete v tématu [použití Azure monitor pro virtuální plochu Windows k monitorování vašeho nasazení](azure-monitor.md).
- Pokud chcete odhadnout, měřit a spravovat náklady na úložiště dat, přečtěte si téma [odhad nákladů na Azure monitor](azure-monitor-costs.md).
- Podívejte se na náš [Glosář](azure-monitor-glossary.md) , kde najdete další informace o pojmech a konceptech souvisejících s Azure monitor pro virtuální počítače s Windows.
