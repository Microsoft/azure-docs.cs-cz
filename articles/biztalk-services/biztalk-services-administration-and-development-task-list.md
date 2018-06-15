---
title: Vývoj a správu úloh seznamu ve službě BizTalk Services | Microsoft Docs
description: Plánování a úlohy podpory pro nasazení služby Azure BizTalk Services.
services: biztalk-services
documentationcenter: ''
author: msftman
manager: erikre
editor: ''
ms.assetid: 0ab70b5b-1a88-4ba5-b329-ec51b785010e
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
ms.openlocfilehash: 9edd7261ca62f505ffb4854e3132fae916768f67
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2017
ms.locfileid: "26626584"
---
# <a name="administration-and-development-task-list-in-biztalk-services"></a>Správu a vývoj seznamu úloh ve službě BizTalk Services

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="getting-started"></a>Začínáme
Při práci se službou Microsoft Azure BizTalk Services, je několik místní a cloudové součásti vzít v úvahu. Abyste mohli začít, zvažte následující postup proces:  

| Krok | Kdo odpovídá | Úkol | Související odkazy |
| --- | --- | --- | --- |
| 1. |Správce |Vytvořit odběr služby Microsoft Azure pomocí účtu Microsoft nebo účtu organizace |[Azure Portal](https://portal.azure.com) |
| 2. |Správce |Vytvořit nebo zřizování služby BizTalk. |[Vytvoření služby BizTalk](https://msdn.microsoft.com/library/azure/dn232347.aspx) |
| 3. |Správce |Registrace vám nebo vaší společnosti nasazení služby BizTalk Services |[Registrace a aktualizace nasazení služby BizTalk na portálu služby BizTalk](https://msdn.microsoft.com/library/azure/hh689837.aspx) |
| 4. |Správce |Platí, pokud aplikace používá služba BizTalk Adapter Service se připojit k-obchodní (LOB) v místním systému nebo používá fronta nebo téma cílový.  Vytvořte Namespace sběrnice služby Azure. Tento obor názvů, název vystavitele sběrnice služby a klíč vystavitele sběrnice služby hodnoty předáte vývojářům. |[Postupy: vytvoření nebo úprava Namespace služby Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) a [hodnoty získat název vystavitele a klíč vystavitele](biztalk-issuer-name-issuer-key.md) |
| 5. |Developer |Instalace sady SDK a vytvoření projektu služby BizTalk v sadě Visual Studio. |[Instalace služby Azure BizTalk Services SDK](https://msdn.microsoft.com/library/azure/hh689760.aspx) a [vytvoření bohaté zasílání zpráv koncových bodů v Azure](https://msdn.microsoft.com/library/azure/hh689766.aspx) |
| 6. |Developer |Nasazení služby BizTalk projektu pro svoji službu BizTalk hostované v Azure. |[Nasazení a aktualizaci projektu služby BizTalk](https://msdn.microsoft.com/library/azure/hh689881.aspx) |
| 7. |Správce |Pokud používáte EDI se vztahuje.  Můžete přidat partnery a vytvořte smlouvy na portálu Microsoft Azure BizTalk Services. Když vytvoříte smlouvu, můžete přidat most nebo transformací vytvořené vývojáři nastavení smlouvy. |[Konfigurace EDI a AS2, EDIFACT na portálu služby BizTalk](https://msdn.microsoft.com/library/azure/hh689853.aspx) |
| 8. |Správce |Pomocí [REST](https://msdn.microsoft.com/library/azure/dn232347.aspx), monitorování stavu služby BizTalk, včetně metrik výkonu. |[BizTalk Services: Karty Řídicí panel, Sledování a Škálování](http://go.microsoft.com/fwlink/p/?LinkID=302281) |
| 9. |Správce |Pomocí portálu Microsoft Azure BizTalk Services, spravujte artefakty používané služby BizTalk Services a sledování zpráv, jako jsou zpracovány pomocí most soubory. |[Pomocí portálu služby BizTalk](https://msdn.microsoft.com/library/azure/dn874043.aspx) |
| 10. |Správce |Vytvoření plánu zálohování k zálohování službu BizTalk. |[Kontinuita podnikových procesů a zotavení po havárii ve službě BizTalk Services](https://msdn.microsoft.com/library/azure/dn509557.aspx) |

## <a name="next-steps"></a>Další kroky
[Výukové programy a ukázky](https://msdn.microsoft.com/library/azure/hh689895.aspx)

[Vytvoření projektu v sadě Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)

[Instalace služby Azure BizTalk Services SDK](https://msdn.microsoft.com/library/azure/hh689760.aspx)

## <a name="concepts"></a>Koncepty
[Vytvoření projektu v sadě Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)  
[EDI, AS2 a zasílání zpráv EDIFACT (Business-Business)](https://msdn.microsoft.com/library/azure/hh689898.aspx)  

## <a name="other-resources"></a>Další prostředky
[Přidání zdroje, cílové a most koncové body pro zasílání zpráv](https://msdn.microsoft.com/library/azure/hh689877.aspx)  
[Další informace a vytvoření mapy zpráv a transformace](https://msdn.microsoft.com/library/azure/hh689905.aspx)  
[Pomocí služby BizTalk Adapter Service (BAS)](https://msdn.microsoft.com/library/azure/hh689889.aspx)  
[Služby Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303664)

