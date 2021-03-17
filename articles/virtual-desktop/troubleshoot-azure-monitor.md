---
title: Řešení potíží s monitorováním ve verzi Preview virtuálních počítačů s Windows – Azure
description: Řešení potíží s Azure Monitor pro virtuální počítače s Windows
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c335c1cf7e5319b812345714dbdc6b87ddc4e81b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709168"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop-preview"></a>Řešení potíží s Azure Monitor pro virtuální počítače s Windows (Preview)

>[!IMPORTANT]
>Azure Monitor pro virtuální počítač s Windows je momentálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučujeme ji používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek obsahuje známé problémy a řešení pro běžné problémy v Azure Monitor pro virtuální počítače s Windows (Preview).

## <a name="issues-with-configuration-and-setup"></a>Problémy s konfigurací a nastavením

Pokud sešit konfigurace nepracuje správně a automatizuje instalaci, můžete tyto prostředky použít k ručnímu nastavení prostředí:

- Pokud chcete ručně povolit diagnostiku nebo získat přístup k pracovnímu prostoru Log Analytics, přečtěte si téma [odeslání diagnostiky virtuálních počítačů Windows do Log Analytics](diagnostics-log-analytics.md).
- Ruční instalace rozšíření Log Analytics na hostitele najdete v tématu [Log Analytics rozšíření virtuálních počítačů pro Windows](../virtual-machines/extensions/oms-windows.md).
- Pokud chcete nastavit nový pracovní prostor Log Analytics, přečtěte si téma [Vytvoření pracovního prostoru Log Analytics v Azure Portal](../azure-monitor/logs/quick-create-workspace.md).
- Chcete-li přidat nebo odebrat čítače výkonu, přečtěte si téma [Konfigurace čítačů výkonu](../azure-monitor/agents/data-sources-performance-counters.md).
- Chcete-li konfigurovat události pro pracovní prostor Log Analytics, přečtěte si téma [shromáždění zdrojů dat protokolu událostí systému Windows pomocí agenta Log Analytics](../azure-monitor/agents/data-sources-windows-events.md).

## <a name="my-data-isnt-displaying-properly"></a>Moje data se nezobrazují správně

Pokud se vaše data nezobrazují správně, ověřte svou konfiguraci, oprávnění a ověřte, že jsou odblokovány požadované IP adresy. 

- Nejdřív se ujistěte, že jste vyplnili všechna pole v konfiguračním sešitě, jak je popsáno v tématu [použití Azure monitor pro virtuální plochu Windows k monitorování vašeho nasazení](azure-monitor.md). Pokud nějaké čítače nebo události chybí, nezobrazí se v Azure Portal žádná data, která jsou k nim přidružená.

- Ověřte přístupová oprávnění & obraťte se na majitele prostředků, aby požádala o chybějící oprávnění; Virtuální plocha pro monitorování všech uživatelů systému Windows vyžaduje následující oprávnění:

    - Přístup pro čtení předplatných Azure, která uchovávají vaše prostředky virtuálních klientů Windows
    - Přístup pro čtení do skupin prostředků předplatného, které obsahují hostitele relace virtuálních klientů Windows 
    - Přístup pro čtení k pracovnímu prostoru Log Analytics

- Je možné, že budete muset v bráně firewall serveru otevřít odchozí porty, aby bylo možné Azure Monitor odesílat data na portál, viz téma [Odchozí porty](../azure-monitor/app/ip-addresses.md). 

- Nezobrazuje se data z nedávné aktivity? Možná budete chtít počkat na 15 minut a aktualizovat informační kanál. Azure Monitor je doba latence 15 minut pro naplnění dat protokolu. Další informace najdete v tématu [čas příjmu dat protokolu v Azure monitor](../azure-monitor/logs/data-ingestion-time.md).

Pokud nechybí žádné informace, ale vaše data stále nejsou správně zobrazená, může se jednat o problém v dotazu nebo zdroji dat. Projděte si naše známé problémy a omezení. 

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Chci přizpůsobit Azure Monitor pro virtuální počítače s Windows

Azure Monitor pro virtuální počítače s Windows používá Azure Monitor sešity. Sešity vám umožní uložit kopii šablony sešitu virtuálních počítačů s Windows a vytvořit vlastní vlastní nastavení.

Podle návrhu nebudou vlastní šablony sešitu automaticky přijímat aktualizace ze skupiny Products. Další informace najdete v tématu [řešení potíží s přehledem na základě sešitu](../azure-monitor/insights/troubleshoot-workbooks.md) a v části [sešity](../azure-monitor/visualize/workbooks-overview.md).

## <a name="i-cant-interpret-the-data"></a>Nejde interpretovat data

Přečtěte si další informace o datových termínech v [glosáři Azure monitor pro virtuální počítače s Windows](azure-monitor-glossary.md).

## <a name="the-data-i-need-isnt-available"></a>Potřebná data nejsou k dispozici.

Pokud chcete monitorovat další čítače výkonu nebo události, můžete jim povolit, aby se odesílaly do vašeho pracovního prostoru Log Analytics a mohli je monitorovat v diagnostice hostitele: prohlížeč hostitele. 

- Postup přidání čítačů výkonu najdete v tématu [Konfigurace čítačů výkonu](../azure-monitor/agents/data-sources-performance-counters.md#configuring-performance-counters) .
- Chcete-li přidat události systému Windows, přečtěte si téma [Konfigurace protokolů událostí systému Windows](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)

Nemůžete najít datový bod, který by mohl pomáhat diagnostikovat problém? Pošlete nám svůj názor!

- Informace o tom, jak ponechávat zpětnou vazbu, najdete v tématech [Přehled řešení potíží, zpětná vazba a podpora pro virtuální počítače s Windows](troubleshoot-set-up-overview.md).
- Zpětnou vazbu k virtuálnímu počítači s Windows můžete také odeslat na webu [Centrum zpětné vazby na virtuálním počítači s Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) nebo na [našem fóru UserVoice](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

## <a name="known-issues-and-limitations"></a>Známé problémy a omezení

Jedná se o problémy a omezení, které v současnosti víme a pracujeme na opravě:

- Současně můžete monitorovat pouze jeden fond hostitelů. 

- Chcete-li uložit oblíbená nastavení, je nutné uložit vlastní šablonu sešitu. Vlastní šablony automaticky nepřijmou aktualizace z produktové skupiny.

- Některé chybové zprávy nejsou fráze uživatelsky přívětivé, a ne všechny chybové zprávy jsou popsány v dokumentaci.

- Čítač výkonu celkový počet relací může v průběhu několika relací na malém čísle a celkový počet relací, které se mohou vyskytnout, jít nad rámec maximálního počtu relací.

- Počet dostupných relací neodráží zásady škálování ve fondu hostitelů. 
    
- V některých případech může událost dokončení připojení chybět a může to mít vliv na některé vizuály, jako je připojení v průběhu času, a na stav připojení uživatele.  
    
- Sešit konfigurace podporuje pouze konfiguraci hostitelů ve stejné oblasti, jako je jejich skupina prostředků. 

- Čas potřebný k připojení zahrnuje dobu, kterou uživatel potřebuje k zadání přihlašovacích údajů. To je sladěné se zkušenostmi, ale v některých případech může zobrazit nepravdivé špičky. 
    

## <a name="next-steps"></a>Další kroky

Pokud si nejste jistí, jak interpretovat data nebo se chcete dozvědět víc o běžných termínech, podívejte se na [glosář Azure monitor pro virtuální počítače s Windows](azure-monitor-glossary.md). Pokud se chcete dozvědět, jak nastavit a používat Azure Monitor pro virtuální počítače s Windows, přečtěte si téma [použití Azure monitor pro virtuální plochu Windows k monitorování vašeho nasazení](azure-monitor.md).