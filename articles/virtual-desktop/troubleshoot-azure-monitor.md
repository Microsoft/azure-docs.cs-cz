---
title: Řešení potíží s monitorováním ve verzi Preview virtuálních počítačů s Windows – Azure
description: Řešení potíží s Azure Monitor pro virtuální počítače s Windows
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 91cf6729911cdb674c5451f172e76a2e9d5943e4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466786"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop-preview"></a>Řešení potíží s Azure Monitor pro virtuální počítače s Windows (Preview)

>[!IMPORTANT]
>Azure Monitor pro virtuální počítač s Windows je momentálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučujeme ji používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek obsahuje známé problémy a řešení pro běžné problémy v Azure Monitor pro virtuální počítače s Windows (Preview).

## <a name="the-configuration-workbook-isnt-working-properly"></a>Sešit konfigurace nefunguje správně.

Pokud sešit konfigurace Azure Monitor nefunguje, můžete tyto prostředky použít k ručnímu nastavení jednotlivých částí:

- Pokud chcete ručně povolit diagnostiku nebo získat přístup k pracovnímu prostoru Log Analytics, přečtěte si téma [odeslání diagnostiky virtuálních počítačů Windows do Log Analytics](diagnostics-log-analytics.md).
- Ruční instalace rozšíření Log Analytics na hostitele najdete v tématu [Log Analytics rozšíření virtuálních počítačů pro Windows](../virtual-machines/extensions/oms-windows.md).
- Pokud chcete nastavit nový pracovní prostor Log Analytics, přečtěte si téma [Vytvoření pracovního prostoru Log Analytics v Azure Portal](../azure-monitor/learn/quick-create-workspace.md).
- Chcete-li přidat nebo odebrat čítače výkonu, přečtěte si téma [Konfigurace čítačů výkonu](../azure-monitor/platform/data-sources-performance-counters.md).
- Chcete-li konfigurovat události pro pracovní prostor Log Analytics, přečtěte si téma [shromáždění zdrojů dat protokolu událostí systému Windows pomocí agenta Log Analytics](../azure-monitor/platform/data-sources-windows-events.md).

Případně může problém způsobovat buď nedostatek prostředků, nebo nemá požadovaná oprávnění.

Pokud předplatné nemá žádné prostředky virtuálního stolního počítače s Windows, nezobrazí se v parametru *předplatného* .

Pokud nemáte přístup pro čtení ke správným předplatným, nezobrazí se v parametru *předplatného* a jejich data se na řídicím panelu nezobrazí. Pokud chcete tento problém vyřešit, obraťte se na vlastníka předplatného a požádejte ho o přístup pro čtení.

## <a name="my-data-isnt-displaying-properly"></a>Moje data se nezobrazují správně

Pokud se vaše data nezobrazují správně, může během procesu konfigurace Azure Monitor dojít k nějakému problému. Nejdřív se ujistěte, že jste vyplnili všechna pole v konfiguračním sešitě, jak je popsáno v tématu [použití Azure monitor pro virtuální plochu Windows k monitorování vašeho nasazení](azure-monitor.md). Nastavení pro nová i existující prostředí můžete kdykoli změnit. Pokud nějaké čítače nebo události chybí, nezobrazí se v Azure Portal žádná data, která jsou k nim přidružená.

Pokud nechybí žádné informace, ale vaše data stále nejsou správně zobrazená, může se jednat o problém v dotazu nebo zdroji dat. 

Pokud se nezobrazí žádné chyby instalačního programu a stále nevidíte očekávaná data, můžete chtít počkat 15 minut a aktualizovat informační kanál. Azure Monitor pro naplnění dat protokolu vyvolala období latence 15 minut. Další informace najdete v tématu [čas příjmu dat protokolu v Azure monitor](../azure-monitor/platform/data-ingestion-time.md).

Nakonec, pokud nechybí žádné informace, ale vaše data stále nejsou zobrazená, může se jednat o problém v dotazu nebo zdroji dat. Pokud je to tento případ, možná budete muset kontaktovat podporu a vyřešit problém.

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Chci přizpůsobit Azure Monitor pro virtuální počítače s Windows

Azure Monitor pro virtuální počítače s Windows používá Azure Monitor sešity. Sešity vám umožní uložit kopii šablony sešitu virtuálních počítačů s Windows a vytvořit vlastní vlastní nastavení.

Přizpůsobené šablony se neaktualizují, pokud skupina produktů aktualizuje původní šablonu. Toto je návrh v nástroji sešity, takže budete muset uložit kopii aktualizované šablony a znovu sestavit vlastní nastavení, abyste mohli přijímat aktualizace. Další informace najdete v tématu [řešení potíží s přehledem na základě sešitu](../azure-monitor/insights/troubleshoot-workbooks.md) a v části [sešity](../azure-monitor/platform/workbooks-overview.md).

## <a name="i-cant-interpret-the-data"></a>Nejde interpretovat data

Přečtěte si další informace o datových termínech v [glosáři Azure monitor pro virtuální počítače s Windows](azure-monitor-glossary.md).

## <a name="the-data-i-need-isnt-available"></a>Potřebná data nejsou k dispozici.

Nemůžete najít datový bod, který by mohl pomáhat diagnostikovat problém? Pošlete nám svůj názor!

- Informace o tom, jak ponechávat zpětnou vazbu, najdete v tématech [Přehled řešení potíží, zpětná vazba a podpora pro virtuální počítače s Windows](troubleshoot-set-up-overview.md).
- Zpětnou vazbu k virtuálnímu počítači s Windows můžete také odeslat na webu [Centrum zpětné vazby na virtuálním počítači s Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) nebo na [našem fóru UserVoice](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

## <a name="known-issues"></a>Známé problémy

Jedná se o problémy, které v současné době znáte a pracujeme na opravě:

- V současné době můžete pro monitorování vybrat jenom jedno předplatné, skupinu prostředků a fond hostitelů. Z tohoto důvodu můžete při použití stránky sestavy uživatelů pochopit uživatelské prostředí, abyste ověřili, že máte správný fond hostitelů, který uživatel používal, nebo že jeho data nebudou naplňovat vizuály.

- V současné době není možné uložit oblíbená nastavení do Azure Monitor, pokud neuložíte vlastní šablonu sešitu. To znamená, že správci IT budou muset zadat svůj název předplatného, názvy skupin prostředků a předvolby fondu hostitelů při každém otevření Azure Monitor pro virtuální počítač s Windows.

- V tuto chvíli není možné exportovat data z Azure Monitor pro virtuální plochu Windows do Excelu.

- Všechna závažnost 1 Azure Monitor výstrahy pro všechny produkty v rámci vybraného předplatného se zobrazí na stránce Přehled. To je záměrné, protože výstrahy z jiných produktů v rámci předplatného můžou mít vliv na virtuální plochu Windows. V současné době je dotaz omezen na výstrahy o závažnosti 1 s výjimkou závažných upozornění s vysokou prioritou 0 na stránce Přehled.

- Některé chybové zprávy nejsou fráze uživatelsky přívětivé, a ne všechny chybové zprávy jsou popsány v dokumentaci.

## <a name="next-steps"></a>Další kroky

Pokud si nejste jistí, jak interpretovat data nebo se chcete dozvědět víc o běžných termínech, podívejte se na [glosář Azure monitor pro virtuální počítače s Windows](azure-monitor-glossary.md). Pokud se chcete dozvědět, jak nastavit a používat Azure Monitor pro virtuální počítače s Windows, přečtěte si téma [použití Azure monitor pro virtuální plochu Windows k monitorování vašeho nasazení](azure-monitor.md).