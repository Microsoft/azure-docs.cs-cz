---
title: Identita spravované služby s verzí preview služby Azure Service Bus | Dokumentace Microsoftu
description: Identity spravovaných služeb pomocí Azure Service Bus
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2018
ms.author: sethm
ms.openlocfilehash: 30df312e349bd6f6ebd1f38141075382be2522a2
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397980"
---
# <a name="managed-service-identity-preview"></a>Identita spravované služby (Preview)

Identity spravované služby (MSI) je funkce napříč Azure, která umožňuje vytvořit zabezpečené identitu přidruženou k nasazení, pod kterým běží kód vaší aplikace. Potom můžete tuto identitu přidružit role řízení přístupu, které vlastní oprávnění pro přístup ke konkrétním prostředkům Azure, které vaše aplikace potřebuje.

Na platformě Azure pomocí MSI, spravuje tuto identitu modulu runtime. Není potřeba ukládat a chránit přístupových klíčů v kódu aplikace nebo konfigurace pro identitu, samotné nebo pro prostředky, které potřebujete získat přístup. Klientskou aplikaci služby Service Bus s povolená podpora MSI v aplikaci Azure App Service nebo na virtuálním počítači není potřeba zpracovat pravidla SAS a klíče nebo jiné přístupové tokeny. Klientská aplikace potřebuje pouze adresu koncového bodu z oboru názvů služby Service Bus Messaging. Když se aplikace připojí, Service Bus váže kontextu MSI do klienta v operaci, která se zobrazí v příklad dále v tomto článku. 

Jakmile je přidružen k identita spravované služby, klient služby Service Bus můžete provádět všem oprávněným operace. Povolení uděleno tím, že přidružíte MSI s rolí služby Service Bus. 

## <a name="service-bus-roles-and-permissions"></a>Role služby Service Bus a oprávnění

Počáteční verze public preview verzi pouze přidáte identitu spravované služby "Vlastník" nebo "Přispěvatel" rolí oboru názvů služby Service Bus, které udělují oprávnění Úplné řízení identit na všechny entity v oboru názvů. Však nepodporuje správu operací, které se mění topologii obor názvů jsou zpočátku pouze když Azure Resource Manageru a ne přes rozhraní nativní Správa služby Service Bus REST. Tato podpora také znamená, že nelze použít klienta rozhraní .NET Framework [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) objekt v rámci identity spravované služby.

## <a name="use-service-bus-with-a-managed-service-identity"></a>Použití služby Service Bus s využitím Identity spravované služby

Následující část popisuje kroky potřebné k vytvoření a nasazení ukázkové aplikace, na kterém běží v části Identita spravované služby, jak udělit této identity přístup k oboru názvů zasílání zpráv Service Bus a jak aplikace komunikuje se službou Service Bus entity pomocí tuto identitu.

Tento úvod popisuje webové aplikaci hostované v [služby Azure App Service](https://azure.microsoft.com/services/app-service/). Kroky potřebné pro aplikace hostované na virtuálním počítači jsou podobné.

### <a name="create-an-app-service-web-application"></a>Vytvořit webovou aplikaci služby App Service

Prvním krokem je vytvoření aplikace App Service ASP.NET. Pokud nejste obeznámeni s jak to udělat v Azure, postupujte podle [Tato příručka](../app-service/app-service-web-get-started-dotnet-framework.md). Místo vytváření aplikace MVC, jak je znázorněno v tomto kurzu, vytvořte aplikaci webových formulářů.

### <a name="set-up-the-managed-service-identity"></a>Nastavení identity spravované služby

Jakmile vytvoříte aplikaci, přejděte na nově vytvořenou webovou aplikaci na webu Azure Portal (také je znázorněno postupy) a pak přejděte na **identita spravované služby** stránce a povolení této funkce: 

![](./media/service-bus-managed-service-identity/msi1.png)

Po povolení funkce, je novou identitu služby vytvořené ve službě Azure Active Directory a nakonfigurováno v hostiteli služby App Service.

### <a name="create-a-new-service-bus-messaging-namespace"></a>Vytvořit nový obor názvů zasílání zpráv Service Bus

Dále [vytvoření oboru názvů služby Service Bus Messaging](service-bus-create-namespace-portal.md) v jedné oblasti Azure, které mají podporu náhledu pro RBAC: **oblasti USA – východ**, **USA – východ 2**, nebo **západní Evropa** . 

Přejděte do oboru názvů **řízení přístupu (IAM)** stránky na portálu a potom klikněte na tlačítko **přidat** přidáte identitu spravované služby pro **vlastníka** role. Uděláte to tak, vyhledejte název webové aplikace v **přidat oprávnění** panel **vyberte** pole a potom klikněte na příslušnou položku. Potom klikněte na **Uložit**.

![](./media/service-bus-managed-service-identity/msi2.png)
 
Identita spravované služby webové aplikace teď má přístup k oboru názvů služby Service Bus a do fronty jste předtím vytvořili. 

### <a name="run-the-app"></a>Spuštění aplikace

Nyní upravte výchozí stránku aplikace technologie ASP.NET, kterou jste vytvořili. Můžete použít kód webové aplikace z [úložiště GitHub](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet).  

Stránku Default.aspx je cílovou stránkou. Kód můžete najít v souboru Default.aspx.cs. Výsledkem je minimální webové aplikace s několika polí pro zadávání a **odeslat** a **přijímat** tlačítka, která připojení k Service Bus k odesílání nebo příjmu zpráv.

Poznámka: Jak [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) objekt je inicializován. Namísto použití zprostředkovatele tokenu sdíleného přístupového tokenu (SAS), kód vytvoří poskytovatel tokenů pro identity spravované služby u `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.ServiceBusAudience)` volání. V důsledku toho nejsou žádné tajných kódů a zachovat. Tok kontext identity spravované služby Service Bus a ověření metody handshake jsou automaticky zpracovány od poskytovatele tokenu, tedy modelem jednodušší než při použití SAS.

Po provedení těchto změn, publikování a spuštění aplikace. Ke stažení a pak importovat profil publikování v sadě Visual Studio je snadný způsob, jak získat správný publikování dat:

![](./media/service-bus-managed-service-identity/msi3.png)
 
Odesílat nebo přijímat zprávy, zadejte název oboru názvů a názvem entity, které jste vytvořili a pak klikněte na možnost **odeslat** nebo **přijímat**.


> [!NOTE]
> - Identita spravované služby funguje jenom v prostředí Azure, v App Service, virtuální počítače Azure a škálovací sady. Pro aplikace .NET knihovnu Microsoft.Azure.Services.appauthentication přistupovat, který používá balíček NuGet služby Service Bus poskytuje abstrakci přes tento protokol a podporuje místní vývojové prostředí. Tato knihovna také umožňuje testovat kód místně na svém vývojovém počítači, pomocí uživatelského účtu Visual Studio, Azure CLI 2.0 nebo integrované ověřování Active Directory. Další informace o místním vývojovém možnosti k této knihovně najdete v tématu [ověřování služba služba do služby Azure Key Vault pomocí rozhraní .NET](../key-vault/service-to-service-authentication.md).  
> 
> - Identity spravovaných služeb se v současné době nefungují s sloty nasazení služby App Service.

## <a name="next-steps"></a>Další postup

Pokud se o přenosu zpráv přes Service Bus chcete dozvědět víc, pročtěte si následující témata.

* [Základy služby Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)