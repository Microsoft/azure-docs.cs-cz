---
title: Reakce na události s využitím upozornění Azure Log Analytics | Microsoft Docs
description: V tomto kurzu se naučíte používat upozornění služby Log Analytics ke zjišťování důležitých informací o vašem pracovním prostoru nebo k preventivnímu upozorňování na problémy a volání akcí, které se je pokusí opravit.
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 10/05/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 996696587f53b1ecd861dc71925a730f8a6efd9b
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52975647"
---
# <a name="respond-to-events-with-azure-monitor-alerts"></a>Reakce na události s upozorněními služby Azure Monitor
Upozornění služby Azure Monitor zjišťují důležité informace v úložišti Log Analytics. Vytvářejí se na základě pravidel upozornění, která automaticky v pravidelných intervalech spouští prohledávání protokolů. Pokud výsledky prohledávání protokolů odpovídají určitým kritériím, vytvoří se záznam upozornění, který je možné nakonfigurovat, aby reagoval automaticky.  Tento kurz je pokračováním kurzu [Vytváření a sdílení řídicích panelů s daty Log Analytics](tutorial-logs-dashboards.md).   

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření pravidla upozornění
> * Konfigurace skupiny akcí na posílání e-mailového oznámení

K dokončení příkladu v tomto kurzu potřebujete existující virtuální počítač [připojený k pracovnímu prostoru Log Analytics](../../log-analytics/log-analytics-quick-collect-azurevm.md).  

## <a name="sign-in-to-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-alerts"></a>Vytváření upozornění
Upozornění vytvářejí pravidla upozornění služby Azure Monitor. Pravidla mohou v pravidelných intervalech automaticky spouštět uložené dotazy nebo vlastní prohledávání protokolů.  Můžete vytvářet upozornění na základě konkrétních metrik výkonu, vytvoření určitých událostí, chybějící události nebo počtu událostí vytvořených v konkrétním časovém intervalu.  Upozornění můžete například použít, když chcete oznámit, že průměrné využití procesoru překročilo určitou prahovou hodnotu, že byla zjištěna chybějící aktualizace nebo ke generování události, protože bylo zjištěno, že neběží určitá služba Windows nebo linuxový démon (proces).  Pokud výsledky prohledávání protokolu odpovídají určitým kritériím, vytvoří se upozornění. Pravidlo pak může automaticky spustit jednu nebo více akcí. Může vás třeba upozornit nebo volat jiný proces. 

V následujícím příkladu vytvoříte pravidlo upozornění na naměřenou hodnotu, které vychází z dotazu na *využití procesoru virtuálních počítačů Azure* uloženého v [kurzu o vizualizaci dat](tutorial-logs-dashboards.md).  Upozornění se vytvoří pro každý virtuální počítač, který překročí prahovou hodnotu 90 %.  

1. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.
2. V levém podokně vyberte **Upozornění** a potom nahoře na stránce klikněte na **Nové pravidlo upozornění** a vytvořte nové upozornění.<br><br> ![Vytvoření nového pravidla upozornění](./media/tutorial-response/alert-rule-02.png)<br>
3. V prvním kroku vyberte v části **Vytvořit upozornění** jako zdroj pracovní prostor Log Analytics, protože jde o výstražný signál založený na protokolu.  Vyfiltrujte výsledky. Pokud máte více předplatných, vyberte v rozevíracím seznamu určité **předplatné**, které obsahuje dříve vytvořený virtuální počítač a pracovní prostor Log Analytics.  Vyfiltrujte **typ prostředku** tím, že v rozevíracím seznamu vyberete **Log Analytics**.  Nakonec vyberte v poli **Prostředek** položku **DefaultLAWorkspace** a pak klikněte na **Hotovo**.<br><br> ![Vytvoření upozornění – 1. krok](./media/tutorial-response/alert-rule-03.png)<br>
4. V části **Kritéria výstrah** klikněte na **Přidat kritéria**, vyberte uložený dotaz a zadejte logiku, která je pro pravidlo upozornění závazná.  V podokně **Konfigurovat logiku signálů** vyberte ze seznamu *Azure VMs - Processor Utilization* (Virtuální počítače Azure – využití procesoru).  Podokno se aktualizuje, aby zobrazovalo nastavení konfigurace upozornění.  Nahoře se zobrazují výsledky za posledních 30 minut vybraného signálu a také samotný vyhledávací dotaz.  
5. Nakonfigurujte upozornění podle následujících informací:  
   a. V rozevíracím seznamu **Na základě** vyberte **Měření metriky**.  Měření metriky vytvoří pro každý objekt dotazu upozornění s hodnotou, která překračuje zadanou prahovou hodnotu.  
   b. V poli **Podmínka** vyberte **Větší než** a jako **prahovou hodnotu** zadejte **90**.  
   c. V části Aktivovat upozornění na základě vyberte **Po sobě jdoucí porušení**, v rozevíracím seznamu vyberte **Větší než** a zadejte hodnotu 3.  
   d. V části Hodnocení na základě upravte hodnotu **Období** na **30** minut. Pravidlo se spustí každých pět minut a vrátí záznamy vytvořené za posledních třicet minut od aktuálního času.  Nastavení delšího období zvyšuje potenciál latence dat a zajišťuje, aby dotaz vrátil data a aby se zabránilo falešně negativním hodnotám, kdy se výstraha nespustí.  
6. Klikněte na **Hotovo** a dokončete pravidlo upozornění.<br><br> ![Konfigurace signálu upozornění](./media/tutorial-response/alert-signal-logic-02.png)<br> 
7. Přejděte ke druhému kroku, ve kterém do pole **Název pravidla upozornění** zadáte název upozornění, například **Využití CPU na více než 90 procent**.  Do pole **Popis** zadejte podrobné informace o upozornění a v poli **Závažnost** vyberte **Kritické (záv. 0)**.<br><br> ![Konfigurace podrobností upozornění](./media/tutorial-response/alert-signal-logic-04.png)<br>
8. Pokud chcete vytvořené pravidlo ihned aktivovat, potvrďte výchozí hodnotu přepínače **Po vytvoření povolit pravidlo**.
9. Ve třetím a posledním kroku zadejte **Skupinu akcí**, abyste zajistili, že se při každé aktivaci upozornění provedou stejné akce. Skupinu akcí můžete použít pro každé definované pravidlo.  Ke konfiguraci nové skupiny akcí použijte následující informace:  
   a. Vyberte **Nová skupina akcí**. Zobrazí se podokno **Přidat skupinu akcí**.  
   b. Do pole **Název skupiny akcí** zadejte název, třeba **Operace IT – oznámení** a do pole **Krátký název** zadejte třeba **itop-ozn**.  
   c. Zkontrolujte správnost výchozích hodnot v polích **Předplatné** a **Skupina prostředků**. Pokud nejsou správné, vyberte správné hodnoty v rozevíracím seznamu.   
   d. V oddílu Akce zadejte název akce, třeba **Poslat e-mail** a jako **Typ akce** vyberte v rozevíracím seznamu **E-mail/SMS/Vytažení/Hlasová**. Vpravo se otevře podokno vlastností **E-mail/SMS/Vytažení/Hlasová**, kde můžete zadat další informace.  
   e. V podokně **E-mail/SMS/Vytažení/Hlasová** aktivujte **E-mail** a zadejte platnou e-mailovou adresu SMTP pro zasílání zpráv.  
   f. Klikněte na tlačítko **OK** a uložte změny.<br><br> 

    ![Vytvoření nové skupiny akcí](./media/tutorial-response/action-group-properties-01.png)

10. Skupinu akcí dokončete kliknutím na **OK**. 
11. K dokončení pravidla upozornění klikněte na **Vytvořit pravidlo upozornění**. Pravidlo se okamžitě spustí.<br><br> ![Dokončení nového pravidla upozornění](./media/tutorial-response/alert-rule-01.png)<br> 

## <a name="view-your-alerts-in-azure-portal"></a>Zobrazení upozornění na webu Azure Portal
Vytvořená upozornění si v Azure můžete prohlédnout v jediném podokně, kde můžete také spravovat všechna pravidla upozornění ve všech svých předplatných Azure. V seznamu jsou všechna pravidla upozornění (povolená i zakázaná). Můžete je řadit podle cílových prostředků, skupin prostředků, názvu pravidla nebo stavu. Je tu i agregovaný souhrn všech aktivovaných upozornění a celkový počet nakonfigurovaných/povolených pravidel upozornění.<br><br> ![Stránka stavu služby Azure Alerts](./media/tutorial-response/azure-alerts-02.png)  

Když se výstraha aktivuje, zobrazí se stav v tabulce, včetně informace o počtu výskytů ve vybraném časovém intervalu (výchozí je posledních šest hodin).  V doručené poště byste měli mít příslušný e-mail, který bude vypadat podobně jako následující příklad. Informuje o problematických virtuálních počítačích a nejdůležitějších výsledcích, které odpovídají nastavenému vyhledávacímu dotazu.<br><br> ![Příklad akce upozornění e-mailem](./media/tutorial-response/azure-alert-email-notification-01.png)

## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak můžou pravidla upozornění proaktivně identifikovat problémy spouštěním prohledávání protokolů v naplánovaných intervalech a reagovat na problémy v případě, že výsledky prohledávání splní konkrétní kritéria.

Na tomto odkazu najdete předem připravené ukázky skriptů pro Log Analytics.  

> [!div class="nextstepaction"]
> [Ukázky skriptů pro Log Analytics](../../log-analytics/powershell-samples.md)
