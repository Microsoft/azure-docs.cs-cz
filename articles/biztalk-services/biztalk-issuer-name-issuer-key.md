---
title: Název vystavitele a klíč vystavitele ve službě BizTalk Services | Microsoft Docs
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
ms.openlocfilehash: 18eac72d75680ab12c4a0bea9dfc5ac8a5fce566
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2017
ms.locfileid: "24103425"
---
# <a name="biztalk-services-issuer-name-and-issuer-key"></a>BizTalk Services: Název a klíč vystavitele

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Služba Azure BizTalk Services používá název vystavitele Service Bus a Issuer Key a název vystavitele řízení přístupu a klíč vystavitele. Zejména:

| Úkol | Které název vystavitele a klíč vystavitele |
| --- | --- |
| Nasazení aplikace z Visual Studia |Název vystavitele řízení přístupu a klíč vystavitele |
| Konfigurace portálu Azure BizTalk Services |Název vystavitele řízení přístupu a klíč vystavitele |
| Vytvoření předávací LOB pomocí adaptéru služby BizTalk v sadě Visual Studio |Název vystavitele sběrnice služby a klíč vystavitele |

Toto téma obsahuje kroky k načtení název vystavitele a klíč vystavitele. 

## <a name="access-control-issuer-name-and-issuer-key"></a>Název vystavitele řízení přístupu a klíč vystavitele
Název vystavitele řízení přístupu a klíč vystavitele se používají následující:

* Aplikace služby BizTalk Azure vytvořit v sadě Visual Studio: Pokud chcete úspěšně nasadit aplikace služby BizTalk v sadě Visual Studio do Azure, zadejte název vystavitele řízení přístupu a klíč vystavitele. 
* Portál služby Azure BizTalk: Při vytváření služby BizTalk a otevřete portál služby BizTalk, název vystavitele řízení přístupu a klíč vystavitele se automaticky registruje pro vaše nasazení s stejné hodnoty řízení přístupu.

### <a name="get-the-access-control-issuer-name-and-issuer-key"></a>Získat název vystavitele řízení přístupu a klíč vystavitele

Pokud chcete používat pro ověřování služby ACS a získat hodnoty název vystavitele a klíč vystavitele, celkový krokům patří:

1. Nainstalujte [rutin prostředí Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Přidání účtu Azure:`Add-AzureAccount`
3. Vrátí název předplatného:`get-azuresubscription`
4. Vyberte předplatné:`select-azuresubscription <name of your subscription>` 
5. Vytvořte nový obor názvů:`new-azuresbnamespace <name for the service bus> "Location" -CreateACSNamespace $true -NamespaceType Messaging`

    Příklad:`new-azuresbnamespace biztalksbnamespace "South Central US" -CreateACSNamespace $true -NamespaceType Messaging`
      
5. Když je vytvořen nový obor názvů služby ACS, (který může trvat několik minut), jsou uvedené hodnoty název vystavitele a klíč vystavitele v připojovacím řetězci: 

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
Klíče vystavitele = SharedSecretKey

Více na [New-AzureSBNamespace](https://msdn.microsoft.com/library/dn495165.aspx) rutiny. 

## <a name="service-bus-issuer-name-and-issuer-key"></a>Název vystavitele sběrnice služby a klíč vystavitele
Název vystavitele sběrnice služby a klíč vystavitele jsou používány adaptér služby BizTalk. V projektu služby BizTalk v sadě Visual Studio používáte adaptér služby BizTalk se připojit k-obchodní (LOB) v místním systému. Pokud chcete připojit, vytvořit obchodní předávání a zadejte podrobnosti o systémové vaše obchodní. Při této činnosti, můžete taky zadat název vystavitele sběrnice služby a klíč vystavitele.

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>Načíst název vystavitele sběrnice služby a klíč vystavitele
1. Přihlaste se k webu [Azure Portal](http://portal.azure.com).
2. Vyhledejte **Service Bus**a zvolte svůj obor názvů. 
3. Otevřete **zásady sdíleného přístupu** vlastnosti, vyberte zásadu a zobrazit **připojovací řetězec** pro název a hodnoty klíče.  

## <a name="next"></a>Další
Další témata týkající se služby Azure BizTalk Services:

* [Instalace služby Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Kurzy: Služby Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Jak začít používat sadu SDK Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Služby Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Viz také
* [Postupy: Konfigurace identity služby pomocí služby správy služby ACS](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
* [BizTalk Services: Developer, Basic, Standard a Premium tabulka edic](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk Services: zřízení](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [BizTalk Services: Tabulka stavů zřízení](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [BizTalk Services: Karty Řídicí panel, Sledování a Škálování](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk Services: Zálohování a obnovení](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk Services: Omezování](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>

