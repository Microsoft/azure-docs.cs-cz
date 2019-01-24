---
title: Spravované identity pro prostředky Azure s verzí preview služby Azure Service Bus | Dokumentace Microsoftu
description: Použití spravované identity pro prostředky Azure pomocí Azure Service Bus
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/01/2018
ms.author: aschhab
ms.openlocfilehash: 5edeebd9698384785082e5a441c24e136ed22481
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856875"
---
# <a name="managed-identities-for-azure-resources-with-service-bus"></a>Spravované identity pro prostředky Azure pomocí služby Service Bus 

[Spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) je funkce, mezi Azure, která umožňuje vytvořit zabezpečené identitu přidruženou k nasazení, pod kterým běží kód vaší aplikace. Potom můžete tuto identitu přidružit role řízení přístupu, které vlastní oprávnění pro přístup ke konkrétním prostředkům Azure, které vaše aplikace potřebuje.

Pomocí spravované identity spravuje Platforma Azure tuto identitu modulu runtime. Není potřeba ukládat a chránit přístupových klíčů v kódu aplikace nebo konfigurace pro identitu, samotné nebo pro prostředky, které potřebujete získat přístup. Service Bus klientskou aplikaci pro spuštění povoleno v aplikaci Azure App Service nebo na virtuálním počítači s spravovaných entit pro prostředky Azure, není potřeba podporu zpracování pravidel SAS a klíče nebo jiné přístupové tokeny. Klientská aplikace potřebuje pouze adresu koncového bodu z oboru názvů služby Service Bus Messaging. Když se aplikace připojí, vytvoří vazbu služby Service Bus spravované entity kontext klienta v operaci, která se zobrazí v příklad dále v tomto článku. Jakmile je přidružen k spravovanou identitu, váš klient služby Service Bus můžete provést všechny schválené operace. Povolení uděleno tím, že přidružíte spravované entity s rolí služby Service Bus. 

## <a name="service-bus-roles-and-permissions"></a>Role služby Service Bus a oprávnění

Spravovaná identita lze přidat pouze do role "Vlastník" nebo "Přispěvatel" oboru názvů služby Service Bus. Zajišťuje úplné řízení identit na všechny entity v oboru názvů. Ale správy, které operace, které změní topologie obor názvů jsou zpočátku podporované pouze když Azure Resource Manageru. Není nativní správu rozhraní REST pro Service Bus. Tato podpora také znamená, že nelze použít klienta rozhraní .NET Framework [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) objekt v rámci spravovanou identitu.

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Service Bus pomocí spravované identity pro prostředky Azure

Následující část popisuje kroky potřebné k vytvoření a nasazení ukázkové aplikace, na kterém běží pod spravovanou identitu, jak udělit této identity přístup k oboru názvů zasílání zpráv Service Bus a jak aplikace komunikuje s entitami služby Service Bus pomocí tuto identitu.

Tento úvod popisuje webové aplikaci hostované v [služby Azure App Service](https://azure.microsoft.com/services/app-service/). Kroky potřebné pro aplikace hostované na virtuálním počítači jsou podobné.

### <a name="create-an-app-service-web-application"></a>Vytvořit webovou aplikaci služby App Service

Prvním krokem je vytvoření aplikace App Service ASP.NET. Pokud nejste obeznámeni s jak postupovat v Azure, postupujte podle [Tato příručka](../app-service/app-service-web-get-started-dotnet-framework.md). Místo vytváření aplikace MVC, jak je znázorněno v tomto kurzu, vytvořte aplikaci webových formulářů.

### <a name="set-up-the-managed-identity"></a>Nastavit spravované identity

Jakmile vytvoříte aplikaci, přejděte na nově vytvořenou webovou aplikaci na webu Azure Portal (také je znázorněno postupy) a pak přejděte na **identita spravované služby** stránce a povolení této funkce: 

![](./media/service-bus-managed-service-identity/msi1.png)

Po povolení funkce, je novou identitu služby vytvořené ve službě Azure Active Directory a nakonfigurováno v hostiteli služby App Service.

### <a name="create-a-new-service-bus-messaging-namespace"></a>Vytvořit nový obor názvů zasílání zpráv Service Bus

Dále [vytvoření oboru názvů služby Service Bus Messaging](service-bus-create-namespace-portal.md) v jedné oblasti Azure, které mají podporu náhledu pro RBAC: **USA – východ**, **USA – východ 2**, nebo **západní Evropa**. 

Přejděte do oboru názvů **řízení přístupu (IAM)** stránky na portálu a potom klikněte na tlačítko **přidat přiřazení role** přidáte spravovanou identitu do **vlastníka** role. Uděláte to tak, vyhledejte název webové aplikace v **přidat oprávnění** panel **vyberte** pole a potom klikněte na příslušnou položku. Potom klikněte na **Uložit**.

Webová aplikace spravovaná identita má nyní přístup k oboru názvů služby Service Bus a do fronty jste předtím vytvořili. 

### <a name="run-the-app"></a>Spuštění aplikace

Nyní upravte výchozí stránku aplikace technologie ASP.NET, kterou jste vytvořili. Můžete použít kód webové aplikace z [úložiště GitHub](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet).  

Stránku Default.aspx je cílovou stránkou. Kód můžete najít v souboru Default.aspx.cs. Výsledkem je minimální webové aplikace s několika polí pro zadávání a **odeslat** a **přijímat** tlačítka, která připojení k Service Bus k odesílání nebo příjmu zpráv.

Poznámka: Jak [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) objekt je inicializován. Namísto použití zprostředkovatele tokenu sdíleného přístupového tokenu (SAS), kód vytvoří poskytovatel tokenů pro spravovanou identitu s `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.ServiceBusAudience)` volání. V důsledku toho nejsou žádné tajných kódů a zachovat. Tok kontextu spravovanou identitu služby Service Bus a ověření metody handshake jsou automaticky zpracovány poskytovatelem tokenu. Je jednodušší model než pomocí SAS.

Po provedení těchto změn, publikování a spuštění aplikace. Můžete-li získat správný publikování dat snadno, stahování a import profilu publikování v sadě Visual Studio:

![](./media/service-bus-managed-service-identity/msi3.png)
 
Odesílat nebo přijímat zprávy, zadejte název oboru názvů a název sady entit, které jste vytvořili. Potom klikněte na možnost **odeslat** nebo **přijímat**.


> [!NOTE]
> - Spravovaná identita funguje jenom v prostředí Azure, v App Service, virtuální počítače Azure a škálovací sady. Pro aplikace .NET knihovnu Microsoft.Azure.Services.appauthentication přistupovat, který používá balíček NuGet služby Service Bus poskytuje abstrakci přes tento protokol a podporuje místní vývojové prostředí. Tato knihovna také umožňuje testovat kód místně na svém vývojovém počítači, pomocí uživatelského účtu Visual Studio, Azure CLI 2.0 nebo integrované ověřování Active Directory. Další informace o místním vývojovém možnosti k této knihovně najdete v tématu [ověřování služba služba do služby Azure Key Vault pomocí rozhraní .NET](../key-vault/service-to-service-authentication.md).  
> 
> - V současné době spravované identity nebude fungovat s sloty nasazení služby App Service.

## <a name="next-steps"></a>Další postup

Další informace o zasílání zpráv Service Bus, najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)