---
title: Řešení potíží s BizTalk Services pomocí protokolů operací | Dokumentace Microsoftu
description: Řešení potíží s BizTalk Services pomocí protokolů operací. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 1081a9c6-58cc-4a76-8ac8-11e5e7a6ea27
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: f69035202a3358af38ebaf8e94abdd3b030e633f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51256064"
---
# <a name="biztalk-services-troubleshoot-using-operation-logs"></a>BizTalk Services: Řešení potíží pomocí protokolů operací

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="what-are-the-operation-logs"></a>Co jsou protokoly operací
Protokoly operací je funkce, služby pro správu, která vám umožní zobrazit historické protokoly operací provedených na služby Azure, včetně BizTalk Services. To umožňuje zobrazit historická data týkající se operací správy v rámci předplatného služby BizTalk až 180 dní zpětně.

> [!NOTE]
> Tato funkce zachytí pouze protokoly pro operace správy ve službě BizTalk Services, jako je například při spuštění služby, vztahuje aktivní, a tak dále. Tyto operace jsou sledovány [REST API služby BizTalk Service](https://msdn.microsoft.com/library/azure/dn232347.aspx). Úplný seznam operací, které jsou sledovány v rámci služby správy, naleznete v tématu [operace sledované pomocí Azure Management Services](#bizops).<br/><br/>
> Není to zachycení v protokolech aktivit související se modul runtime služby BizTalk (například zpráva zpracována přemostění a tak dále). Chcete-li zobrazit tyto protokoly, použijte zobrazení sledování z portálu BizTalk Services. Další informace najdete v tématu [sledování zpráv](https://msdn.microsoft.com/library/azure/hh949805.aspx).
> 
> 

## <a name="view-biztalk-services-operation-logs"></a>Zobrazit protokoly operací služby BizTalk Services
1. Na portálu vyberte **Management Services**a pak vyberte **protokoly operací** kartu.
2. Můžete filtrovat protokoly podle různých parametrů, jako je předplatné, rozsah kalendářních dat, typ služby (např. služby BizTalk), název služby nebo stav operace (úspěšné, neúspěšné).
3. Vyberte značku zaškrtnutí zobrazíte filtrovaný seznam. Na následujícím obrázku zobrazuje aktivity související s testbiztalkservice: ![zobrazit protokoly operací][ViewLogs] 
4. Chcete-li zobrazit informace o konkrétní operaci, vyberte řádek a klikněte na tlačítko **podrobnosti** na hlavním panelu v dolní části.

## <a name="bizops"></a>Operace, které jsou sledovány v rámci služeb Azure pro správu
V následující tabulce jsou uvedeny operace, které jsou sledovány v rámci správy služeb Azure:

| Název operace | Úkol |
| --- | --- |
| CreateBizTalkService |Operace vytvořit novou službu BizTalk |
| DeleteBizTalkService |Operace odstranění služby BizTalk |
| RestartBizTalkService |Operace restartování služby BizTalk |
| StartBizTalkService |Operaci pro spuštění služby BizTalk |
| StopBizTalkService |Operace zastavit službu BizTalk |
| DisableBizTalkService |Operace zakázání služby BizTalk |
| EnableBizTalkService |Operaci povolení služby BizTalk |
| BackupBizTalkService |Operace zálohování služby BizTalk |
| RestoreBizTalkService |Operace obnovení služby BizTalk ze zadané zálohy |
| SuspendBizTalkService |Operaci pozastavení služby BizTalk |
| ResumeBizTalkService |Operace obnovení služby BizTalk |
| ScaleBizTalkService |Operace škálování služby BizTalk směrem nahoru nebo dolů |
| ConfigUpdateBizTalkService |Operace se aktualizovat konfiguraci služby BizTalk |
| ServiceUpdateBizTalkService |Operace upgradovat nebo downgradovat na jinou verzi služby BizTalk |
| PurgeBackupBizTalkService |Operace odstranění služby BizTalk mimo dobu uchování zálohy |

## <a name="see-also"></a>Viz také
* [Zálohování služby BizTalk](https://go.microsoft.com/fwlink/p/?LinkID=325584)
* [Obnovení ze zálohy služby BizTalk](https://go.microsoft.com/fwlink/p/?LinkID=325582)
* [BizTalk Services: Developer, Basic, Standard a Premium tabulka edic](https://go.microsoft.com/fwlink/p/?LinkID=302279)
* [BizTalk Services: zřízení](https://go.microsoft.com/fwlink/p/?LinkID=302280)
* [BizTalk Services: Tabulka stavů zřízení](https://go.microsoft.com/fwlink/p/?LinkID=329870)
* [BizTalk Services: Karty Řídicí panel, Sledování a Škálování](https://go.microsoft.com/fwlink/p/?LinkID=302281)
* [BizTalk Services: Omezování](https://go.microsoft.com/fwlink/p/?LinkID=302282)
* [BizTalk Services: Název a klíč vystavitele](https://go.microsoft.com/fwlink/p/?LinkID=303941)
* [Jak začít používat sadu SDK Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png

