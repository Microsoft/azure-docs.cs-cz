---
title: Řešení potíží s služby BizTalk Services pomocí protokoly operací | Microsoft Docs
description: Řešení potíží s služby BizTalk Services pomocí protokoly operací. MABS, WABS
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
ms.openlocfilehash: 7d3a357e1a3929153288a9d99e21f2379bcac891
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2017
ms.locfileid: "24102014"
---
# <a name="biztalk-services-troubleshoot-using-operation-logs"></a>BizTalk Services: Řešení problémů pomocí protokolů operací

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="what-are-the-operation-logs"></a>Jaké jsou protokoly operací
Protokoly operací je funkce služby správy, která vám umožní zobrazit historické protokoly operací týkajících se služeb Azure, včetně služby BizTalk Services. To umožňuje zobrazit historická data týkající se správy operací na své předplatné služby BizTalk až 180 dní.

> [!NOTE]
> Tato funkce zaznamená pouze protokoly pro operace správy v BizTalk Services, například při spuštění služby, založenou na aktivní, a tak dále. Tyto operace jsou sledovány pomocí [rozhraní API REST služby BizTalk](http://msdn.microsoft.com/library/azure/dn232347.aspx). Úplný seznam operací, které jsou sledovány pomocí služby správy najdete v tématu [Operations sledovaných pomocí služeb Azure Management](#bizops).<br/><br/>
> Není to zachytit v protokolech činnosti týkající se služby BizTalk runtime (například zprávy zpracovává mostů atd.). Pokud chcete zobrazit tyto protokoly, použijte zobrazení sledování z portálu služby BizTalk Services. Další informace najdete v tématu [sledování zpráv](http://msdn.microsoft.com/library/azure/hh949805.aspx).
> 
> 

## <a name="view-biztalk-services-operation-logs"></a>Zobrazení, protokoly operací služby BizTalk Services
1. Na portálu, vyberte **Management Services**a pak vyberte **protokoly operací** kartě.
2. Můžete filtrovat na základě různých parametrů jako předplatné, rozsah kalendářních dat, typu služby (např. služby BizTalk), název služby nebo stav operace (úspěšné, neúspěšné) protokolů.
3. Vyberte na značku zaškrtnutí zobrazíte filtrované seznamu. Následující obrázek znázorňuje aktivity související s testbiztalkservice: ![zobrazit protokoly operací][ViewLogs] 
4. Chcete-li zobrazit více o konkrétní operaci, vyberte řádek a klikněte na **podrobnosti** na hlavním panelu v dolní části.

## <a name="bizops"></a>Operace sledovat pomocí služby Azure pro
Následující tabulka uvádí operace, které jsou sledovány pomocí služby pro Azure:

| Název operace | Úkol |
| --- | --- |
| CreateBizTalkService |Operace vytvoření nové služby BizTalk |
| DeleteBizTalkService |Operace odstranění služby BizTalk |
| RestartBizTalkService |Operace restartování služby BizTalk |
| StartBizTalkService |Operaci pro spuštění služby BizTalk |
| StopBizTalkService |Operace zastavení služby BizTalk |
| DisableBizTalkService |Operace zakázání služby BizTalk |
| EnableBizTalkService |Operaci povolení služby BizTalk |
| BackupBizTalkService |Operace zálohování služby BizTalk |
| RestoreBizTalkService |Operace obnovení služby BizTalk ze zadané zálohy |
| SuspendBizTalkService |Operace pozastavení služby BizTalk |
| ResumeBizTalkService |Operace obnovení služby BizTalk |
| ScaleBizTalkService |Operace škálování služby BizTalk nahoru nebo dolů |
| ConfigUpdateBizTalkService |Operace se aktualizovat konfiguraci služby BizTalk |
| ServiceUpdateBizTalkService |Operace k aktualizaci nebo starší verzi služby BizTalk na jinou verzi |
| PurgeBackupBizTalkService |Operace k vyprázdnění zálohování služby BizTalk mimo dobu uchování |

## <a name="see-also"></a>Viz také
* [Zálohování služby BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
* [Služba BizTalk obnovit ze zálohy](http://go.microsoft.com/fwlink/p/?LinkID=325582)
* [BizTalk Services: Developer, Basic, Standard a Premium tabulka edic](http://go.microsoft.com/fwlink/p/?LinkID=302279)
* [BizTalk Services: zřízení](http://go.microsoft.com/fwlink/p/?LinkID=302280)
* [BizTalk Services: Tabulka stavů zřízení](http://go.microsoft.com/fwlink/p/?LinkID=329870)
* [BizTalk Services: Karty Řídicí panel, Sledování a Škálování](http://go.microsoft.com/fwlink/p/?LinkID=302281)
* [BizTalk Services: Omezování](http://go.microsoft.com/fwlink/p/?LinkID=302282)
* [BizTalk Services: Název a klíč vystavitele](http://go.microsoft.com/fwlink/p/?LinkID=303941)
* [Jak začít používat sadu SDK Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png

