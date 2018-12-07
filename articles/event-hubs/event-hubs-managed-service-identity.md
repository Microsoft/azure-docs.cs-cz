---
title: Spravované identity pro prostředky Azure s verzí preview služby Azure Event Hubs | Dokumentace Microsoftu
description: Použití spravované identity pro prostředky Azure pomocí Azure Event Hubs
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: d7ed4f0276e6220e6df528075e83cb9fabc6cc84
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53011466"
---
# <a name="managed-identities-for-azure-resources-with-event-hubs"></a>Spravované identity pro prostředky Azure pomocí služby Event Hubs

[Spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) je funkce, mezi Azure, která umožňuje vytvořit zabezpečené identitu přidruženou k nasazení, pod kterým běží kód vaší aplikace. Potom můžete tuto identitu přidružit role řízení přístupu, které vlastní oprávnění pro přístup ke konkrétním prostředkům Azure, které vaše aplikace potřebuje. 

Pomocí spravované identity spravuje Platforma Azure tuto identitu modulu runtime. Není potřeba ukládat a chránit přístupových klíčů v kódu aplikace nebo konfigurace pro identitu, samotné nebo pro prostředky, které potřebujete získat přístup. Klienta aplikace služby Event Hubs v aplikaci Azure App Service nebo na virtuálním počítači s spravovaných identit pro prostředky Azure je povolená podpora nemusí zpracovávat pravidla SAS a klíče nebo jiné přístupové tokeny. Klientská aplikace potřebuje pouze adresu koncového bodu z oboru názvů Event Hubs. Když se aplikace připojí, vytvoří vazbu služby Event Hubs kontext spravované identity klienta v operaci, která se zobrazí v příklad dále v tomto článku.

Jakmile je přidružen k spravovanou identitu, klient služby Event Hubs můžete provést všechny schválené operace. Povolení uděleno tím, že přidružíte spravovanou identitu s rolí služby Event Hubs. 

## <a name="event-hubs-roles-and-permissions"></a>Event Hubs role a oprávnění

Spravovaná identita lze přidat pouze do "Vlastník" nebo "Přispěvatel" rolí obor názvů služby Event Hubs, která udělují oprávnění Úplné řízení identit na všechny entity v oboru názvů. Ale správy, které operace, které změní topologie obor názvů jsou zpočátku podporované pouze když Azure Resource Manageru. Není nativní správu rozhraní REST centra událostí. Tato podpora také znamená, že nelze použít klienta rozhraní .NET Framework [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) objekt v rámci spravovanou identitu. 
 
## <a name="use-event-hubs-with-managed-identities-for-azure-resources"></a>Event Hubs pomocí spravované identity pro prostředky Azure

Následující část popisuje následující kroky:

1. Vytvořte a nasaďte ukázkovou aplikaci, na kterém běží pod spravovanou identitu.
2. Udělte přístup této identity na obor názvů služby Event Hubs.
3. Jak aplikace komunikuje s event hubs pomocí tuto identitu.

Tento úvod popisuje webové aplikaci hostované v [služby Azure App Service](https://azure.microsoft.com/services/app-service/). Kroky potřebné pro aplikace hostované na virtuálním počítači jsou podobné.

### <a name="create-an-app-service-web-application"></a>Vytvořit webovou aplikaci služby App Service

Prvním krokem je vytvoření aplikace App Service ASP.NET. Pokud nejste obeznámeni s jak to udělat v Azure, postupujte podle [Tato příručka](../app-service/app-service-web-get-started-dotnet-framework.md). Místo vytváření aplikace MVC, jak je znázorněno v tomto kurzu, vytvořte aplikaci webových formulářů.

### <a name="set-up-the-managed-identity"></a>Nastavit spravované identity

Jakmile vytvoříte aplikaci, přejděte na nově vytvořenou webovou aplikaci na webu Azure Portal (také je znázorněno postupy) a pak přejděte na **identita spravované služby** stránce a povolení této funkce: 

![Stránka spravované Identity služby](./media/event-hubs-managed-service-identity/msi1.png)
 
Po povolení funkce, je novou identitu služby vytvořené ve službě Azure Active Directory a nakonfigurováno v hostiteli služby App Service.

### <a name="create-a-new-event-hubs-namespace"></a>Vytvoření nového oboru názvů služby Event Hubs

Dále [vytvořte obor názvů služby Event Hubs](event-hubs-create.md) v jedné oblasti Azure, které nabízí podporu verze preview pro spravované identity pro prostředky Azure: **oblasti USA – východ**, **USA – východ 2**, nebo  **Západní Evropa**. 

Přejděte do oboru názvů **řízení přístupu (IAM)** stránky na portálu a potom klikněte na tlačítko **přidat přiřazení role** přidáte spravovanou identitu do **vlastníka** role. Uděláte to tak, vyhledejte název webové aplikace v **přidat oprávnění** panel **vyberte** pole a potom klikněte na příslušnou položku. Potom klikněte na **Uložit**. Spravovaná identita pro webové aplikace teď má přístup k oboru názvů Event Hubs a do centra událostí, kterou jste vytvořili dřív. 

### <a name="run-the-app"></a>Spuštění aplikace

Nyní můžete upravte výchozí stránku, kterou jste vytvořili aplikaci ASP.NET. Můžete také použít kódu webové aplikace z [úložiště GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/MSI/EventHubsMSIDemoWebApp). 

Po spuštění aplikace, přejděte v prohlížeči EventHubsMSIDemo.aspx. Můžete ho také nastavit jako úvodní stránku. Kód můžete najít v souboru EventHubsMSIDemo.aspx.cs. Výsledkem je minimální webové aplikace s několika polí pro zadávání a **odeslat** a **přijímat** tlačítka, která připojení do služby Event Hubs k odesílání nebo příjem událostí. 

Poznámka: Jak [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) objekt je inicializován. Namísto použití zprostředkovatele tokenu sdíleného přístupového tokenu (SAS), kód vytvoří poskytovatel tokenů pro spravovanou identitu s `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)` volání. V důsledku toho nejsou žádné tajné kódy ukládat a používat. Tok kontextu spravovaných identit do služby Event Hubs a povolení handshake jsou automaticky zpracovány od poskytovatele tokenu, tedy modelem jednodušší než při použití SAS.

Po provedení těchto změn, publikování a spuštění aplikace. Stažením a následným importováním profil publikování v sadě Visual Studio můžete získat správná data publikování:

![Importovat profil publikování](./media/event-hubs-managed-service-identity/msi3.png)
 
Odesílat nebo přijímat zprávy, zadejte název oboru názvů a názvem entity, které jste vytvořili a pak klikněte na možnost **odeslat** nebo **přijímat**. 
 
Spravovaná identita funguje jenom v prostředí Azure a pouze v nasazení služby App Service, ve kterém jste je nakonfigurovali. Spravované identity s sloty nasazení služby App Service v tuto chvíli nefungují.

## <a name="next-steps"></a>Další postup

Další informace o službě Event Hubs naleznete pod těmito odkazy:

* Úvodní [Kurz služby Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
* [Podrobnosti o cenách služby Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Ukázkové aplikace, které používají službu Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)
