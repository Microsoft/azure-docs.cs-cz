---
title: Vývoj a správu úloh seznamu ve službě BizTalk Services | Dokumentace Microsoftu
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
ms.openlocfilehash: e762141e089b11dd0fb129f3bf758874d4ad4da8
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51227633"
---
# <a name="administration-and-development-task-list-in-biztalk-services"></a>Správu a vývoj seznamu úkolů ve službě BizTalk Services

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="getting-started"></a>Začínáme
Při práci s Microsoft Azure BizTalk Services, je několik místní a cloudové komponenty vzít v úvahu. Abyste mohli začít, vezměte v úvahu následující proces:  

| Krok | Kdo zodpovídá | Úkol | Související odkazy |
| --- | --- | --- | --- |
| 1. |Správce |Vytvořit odběr služby Microsoft Azure pomocí účtu Microsoft nebo účtu organizace |[Azure Portal](https://portal.azure.com) |
| 2. |Správce |Vytvořit nebo zřídit službu BizTalk. |[Vytvoření služby BizTalk](https://msdn.microsoft.com/library/azure/dn232347.aspx) |
| 3. |Správce |Registrovat vy nebo vaše společnost nasazení služby BizTalk Services |[Registrace a aktualizace nasazení služby BizTalk na portálu BizTalk Services](https://msdn.microsoft.com/library/azure/hh689837.aspx) |
| 4. |Správce |Platí v případě, že aplikace používá služba BizTalk Adapter Service k připojení k systému – obchodní (LOB) v místním nebo používá fronty nebo tématu cíl.  Vytvoření Namespace Azure Service Bus. Zadejte tento obor názvů, název vystavitele služby Service Bus a hodnoty klíče vystavitele služby Service Bus pro vývojáře. |[Postupy: vytvoření nebo úpravě Namespace služby Service Bus služby](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) a [hodnoty získat název vystavitele a klíč vystavitele](biztalk-issuer-name-issuer-key.md) |
| 5. |Developer |Instalace sady SDK a vytvořte projekt služby BizTalk v sadě Visual Studio. |[Nainstalujte sadu SDK Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689760.aspx) a [vytvořit bohaté možnosti zasílání zpráv koncových bodů v Azure](https://msdn.microsoft.com/library/azure/hh689766.aspx) |
| 6. |Developer |Nasazení vaší služby BizTalk projekt tak, aby vaše služba BizTalk hostované v Azure. |[Nasazení a aktualizaci projektu BizTalk Services](https://msdn.microsoft.com/library/azure/hh689881.aspx) |
| 7. |Správce |Platí v případě, že používáte EDI.  Můžete přidat partnery a dohody vytvořit na portálu Microsoft Azure BizTalk Services. Když vytvoříte smlouvu, můžete přidat most a/nebo transformace, které jsou vytvořené pro vývojáře k nastavení smlouvy. |[Konfigurace EDI, AS2 a EDIFACT v portálu BizTalk Services](https://msdn.microsoft.com/library/azure/hh689853.aspx) |
| 8. |Správce |Pomocí [REST](https://msdn.microsoft.com/library/azure/dn232347.aspx), monitorování stavu vaší služby BizTalk, včetně metrik výkonu. |[BizTalk Services: Karty Řídicí panel, Sledování a Škálování](https://go.microsoft.com/fwlink/p/?LinkID=302281) |
| 9. |Správce |Pomocí portálu Microsoft Azure BizTalk Services, spravujte artefakty, které používají služby BizTalk Services a sledování zpráv jako jsou zpracovány soubory most. |[Použití portálu BizTalk Services](https://msdn.microsoft.com/library/azure/dn874043.aspx) |
| 10. |Správce |Vytvoření plánu zálohování k zálohování služby BizTalk. |[Kontinuita podnikových procesů a zotavení po havárii ve službě BizTalk Services](https://msdn.microsoft.com/library/azure/dn509557.aspx) |

## <a name="next-steps"></a>Další kroky
[Kurzy a ukázky](https://msdn.microsoft.com/library/azure/hh689895.aspx)

[Vytvoření projektu v sadě Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)

[Nainstalujte sadu SDK Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689760.aspx)

## <a name="concepts"></a>Koncepty
[Vytvoření projektu v sadě Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)  
[EDI, AS2 a EDIFACT zasílání zpráv (B2b)](https://msdn.microsoft.com/library/azure/hh689898.aspx)  

## <a name="other-resources"></a>Další prostředky
[Přidat zdroj, cíl a most koncových bodů pro zasílání zpráv](https://msdn.microsoft.com/library/azure/hh689877.aspx)  
[Další informace a k vytvoření mapy zpráv a transformace](https://msdn.microsoft.com/library/azure/hh689905.aspx)  
[Pomocí služby adaptér BizTalk (BAS)](https://msdn.microsoft.com/library/azure/hh689889.aspx)  
[Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=303664)

