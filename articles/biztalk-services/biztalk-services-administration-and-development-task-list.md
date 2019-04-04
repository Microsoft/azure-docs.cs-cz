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
ms.openlocfilehash: 6b8e0fea73dc24f7e680482be1f06fba6d702804
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916236"
---
# <a name="administration-and-development-task-list-in-biztalk-services"></a>Správu a vývoj seznamu úkolů ve službě BizTalk Services

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]
> 
> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="getting-started"></a>Začínáme
Při práci s Microsoft Azure BizTalk Services, je několik místní a cloudové komponenty vzít v úvahu. Abyste mohli začít, vezměte v úvahu následující proces:  

| Krok | Kdo zodpovídá | Úkol | Související odkazy |
| --- | --- | --- | --- |
| 1. |Správce |Vytvořit odběr služby Microsoft Azure pomocí účtu Microsoft nebo účtu organizace |[portál Azure](https://portal.azure.com) |
| 2. |Správce |Vytvořit nebo zřídit službu BizTalk. |[Vytvoření služby BizTalk](/previous-versions/azure/reference/dn232347(v=azure.100)) |
| 3. |Správce |Registrovat vy nebo vaše společnost nasazení služby BizTalk Services |[Registrace a aktualizace nasazení služby BizTalk na portálu BizTalk Services](/previous-versions/azure/hh689837(v=azure.100)) |
| 4. |Správce |Platí v případě, že aplikace používá služba BizTalk Adapter Service k připojení k systému – obchodní (LOB) v místním nebo používá fronty nebo tématu cíl.  Vytvoření Namespace Azure Service Bus. Zadejte tento obor názvů, název vystavitele služby Service Bus a hodnoty klíče vystavitele služby Service Bus pro vývojáře. |[Postup: Vytvořte nebo upravte Namespace služby Service Bus služby](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) a [hodnoty získat název vystavitele a klíč vystavitele](biztalk-issuer-name-issuer-key.md) |
| 5. |Developer |Instalace sady SDK a vytvořte projekt služby BizTalk v sadě Visual Studio. |[Nainstalujte sadu SDK Azure BizTalk Services](/previous-versions/azure/hh689760(v=azure.100)) a [vytvořit bohaté možnosti zasílání zpráv koncových bodů v Azure](/previous-versions/azure/hh689766(v=azure.100)) |
| 6. |Developer |Nasazení vaší služby BizTalk projekt tak, aby vaše služba BizTalk hostované v Azure. |[Nasazení a aktualizaci projektu BizTalk Services](/previous-versions/azure/hh689881(v=azure.100)) |
| 7. |Správce |Platí v případě, že používáte EDI.  Můžete přidat partnery a dohody vytvořit na portálu Microsoft Azure BizTalk Services. Když vytvoříte smlouvu, můžete přidat most a/nebo transformace, které jsou vytvořené pro vývojáře k nastavení smlouvy. |[Konfigurace EDI, AS2 a EDIFACT v portálu BizTalk Services](/previous-versions/azure/hh689853(v=azure.100)) |
| 8. |Správce |Pomocí [REST](/previous-versions/azure/reference/dn232347(v=azure.100)), monitorování stavu vaší služby BizTalk, včetně metrik výkonu. |[BizTalk Services: Karty řídicí panel, sledování a škálování](https://go.microsoft.com/fwlink/p/?LinkID=302281) |
| 9. |Správce |Pomocí portálu Microsoft Azure BizTalk Services, spravujte artefakty, které používají služby BizTalk Services a sledování zpráv jako jsou zpracovány soubory most. |[Použití portálu BizTalk Services](/previous-versions/azure/dn874043(v=azure.100)) |
| 10. |Správce |Vytvoření plánu zálohování k zálohování služby BizTalk. |[Kontinuita podnikových procesů a zotavení po havárii ve službě BizTalk Services](/previous-versions/azure/dn509557(v=azure.100)) |

## <a name="next-steps"></a>Další kroky
[Kurzy a ukázky](/previous-versions/azure/hh689895(v=azure.100))

[Vytvoření projektu v sadě Visual Studio](/previous-versions/azure/hh689811(v=azure.100))

[Nainstalujte sadu SDK Azure BizTalk Services](/previous-versions/azure/hh689760(v=azure.100))

## <a name="concepts"></a>Koncepty
[Vytvoření projektu v sadě Visual Studio](/previous-versions/azure/hh689811(v=azure.100))  
[EDI, AS2 a EDIFACT zasílání zpráv (B2b)](/previous-versions/azure/hh689898(v=azure.100))  

## <a name="other-resources"></a>Další prostředky
[Přidat zdroj, cíl a most koncových bodů pro zasílání zpráv](/previous-versions/azure/hh689877(v=azure.100))  
[Další informace a k vytvoření mapy zpráv a transformace](/previous-versions/azure/hh689905(v=azure.100))  
[Pomocí služby adaptér BizTalk (BAS)](/previous-versions/azure/hh689889(v=azure.100))  
[Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=303664)

