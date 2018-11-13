---
title: Název vystavitele a klíč vystavitele v BizTalk Services | Dokumentace Microsoftu
description: Zjistěte, jak načíst název vystavitele a klíč vystavitele pro Service Bus nebo řízení přístupu (ACS) ve službě BizTalk Services. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 067fe356-d1aa-420f-b2f2-1a418686470a
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: eb5b4b3741b064a934833b3094c69db85e9ccabb
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238705"
---
# <a name="biztalk-services-issuer-name-and-issuer-key"></a>BizTalk Services: Název a klíč vystavitele

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Služba Azure BizTalk Services se používá název vystavitele služby Service Bus a klíč vystavitele a název vystavitele řízení přístupu a klíč vystavitele. Zejména:

| Úkol | Které název vystavitele a klíč vystavitele |
| --- | --- |
| Nasazení aplikace v sadě Visual Studio |Název vystavitele řízení přístupu a klíč vystavitele |
| Konfigurace portálu Azure BizTalk Services |Název vystavitele řízení přístupu a klíč vystavitele |
| Vytvoření objektu LOB Relay pomocí služby adaptér BizTalk v sadě Visual Studio |Název vystavitele služby Service Bus a klíč vystavitele |

Toto téma obsahuje také postup načtení názvu vystavitele a klíč vystavitele. 

## <a name="access-control-issuer-name-and-issuer-key"></a>Název vystavitele řízení přístupu a klíč vystavitele
Název vystavitele řízení přístupu a klíč vystavitele se používají následující:

* Aplikace služby BizTalk Azure vytvořené v sadě Visual Studio: Pokud chcete úspěšně nasadit aplikaci služby BizTalk v sadě Visual Studio do Azure, můžete zadat název vystavitele řízení přístupu a klíč vystavitele. 
* Azure portálu BizTalk Services: Při vytváření služby BizTalk a otevřete portálu BizTalk Services, název vystavitele řízení přístupu a klíč vystavitele jsou automaticky zaregistrováni pro nasazení pomocí stejné hodnoty řízení přístupu.

### <a name="get-the-access-control-issuer-name-and-issuer-key"></a>Získejte název vystavitele řízení přístupu a klíč vystavitele

Použití služby ACS pro ověřování a získat hodnoty název a klíč vystavitele, celkový postup je následující:

1. Nainstalujte [rutin prostředí Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Přidáte účet Azure: `Add-AzureAccount`
3. Vrátí název vašeho odběru: `get-azuresubscription`
4. Vyberte své předplatné: `select-azuresubscription <name of your subscription>` 
5. Vytvořte nový obor názvů: `new-azuresbnamespace <name for the service bus> "Location" -CreateACSNamespace $true -NamespaceType Messaging`

    Příklad:    `new-azuresbnamespace biztalksbnamespace "South Central US" -CreateACSNamespace $true -NamespaceType Messaging`
      
5. Když se vytvoří nový obor názvů služby ACS, (to může trvat několik minut), název vystavitele a klíč vystavitele hodnoty jsou uvedené v připojovacím řetězci: 

    ```
    Name                  : biztalksbnamespace
    Region                : South Central US
    DefaultKey            : abcdefghijklmnopqrstuvwxyz
    Status                : Active
    CreatedAt             : 10/18/2016 9:36:30 PM
    AcsManagementEndpoint : https://biztalksbnamespace-sb.accesscontrol.windows.net/
    ServiceBusEndpoint    : https://biztalksbnamespace.servicebus.windows.net/
    ConnectionString      : Endpoint=sb://biztalksbnamespace.servicebus.windows.net/;SharedSecretIssuer=owner;SharedSecretValue=abcdefghijklmnopqrstuvwxyz
    NamespaceType         : Messaging
    ```

Shrnutí:  
Název vystavitele = SharedSecretIssuer  
Klíč vystavitele = SharedSecretKey

Více o [New-AzureSBNamespace](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azuresbnamespace) rutiny. 

## <a name="service-bus-issuer-name-and-issuer-key"></a>Název vystavitele služby Service Bus a klíč vystavitele
Název vystavitele služby Service Bus a klíč vystavitele používají služby adaptér BizTalk. V projektu BizTalk Services v sadě Visual Studio pomocí služby adaptér BizTalk pro připojení místnímu systému obchodní (LOB). Pro připojení, vytvoření LOB Relay a zadejte podrobnosti o systému LOB. Při tomto postupu můžete také zadat název vystavitele služby Service Bus a klíč vystavitele.

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>Chcete-li načíst název vystavitele služby Service Bus a klíč vystavitele
1. Přihlaste se k webu [Azure Portal](http://portal.azure.com).
2. Vyhledejte **služby Service Bus**a vyberte váš obor názvů. 
3. Otevřít **zásady sdíleného přístupu** vlastnosti, vyberte zásady a zobrazit **připojovací řetězec** zadání jména a hodnoty klíče.  

## <a name="next"></a>Další
Další témata týkající se služby Azure BizTalk Services:

* [Instalace sady SDK pro Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Kurzy: Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Jak začít používat sadu SDK Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Viz také
* [Postupy: používání služby ACS Management ke konfiguraci identity služby](https://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
* [BizTalk Services: Developer, Basic, Standard a Premium tabulka edic](https://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk Services: zřízení](https://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [BizTalk Services: Tabulka stavů zřízení](https://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [BizTalk Services: Karty Řídicí panel, Sledování a Škálování](https://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk Services: Zálohování a obnovení](https://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk Services: Omezování](https://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>

