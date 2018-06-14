---
title: Azure preview Service Bus Role-Based řízení přístupu (RBAC) | Microsoft Docs
description: Řízení přístupu na základě role Azure Service Bus
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
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 729d6db6b2fc6495ffb0f4fbe4d545d7ad953cef
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2017
ms.locfileid: "26783412"
---
# <a name="active-directory-role-based-access-control-preview"></a>Aktivní Directory Role-Based řízení přístupu (preview)

Microsoft Azure poskytuje správu řízení integrované přístupu k prostředkům a aplikacím, které jsou založené na Azure Active Directory (Azure AD). S Azure AD, můžete buď spravovat uživatelské účty a aplikace speciálně pro vaši Azure na základě aplikací, nebo můžete vytvořit federaci stávající infrastruktury služby Active Directory s Azure AD pro společnosti jednotného přihlašování, která také zahrnuje prostředků Azure a Azure hostované aplikace. Aby bylo možné udělit přístup k prostředkům Azure pak můžete přiřadit tyto identity uživatelů a aplikací Azure AD pro globální a specifickou pro službu role.

Pro Azure Service Bus, správu oborů názvů a všechny související prostředky prostřednictvím portálu Azure a rozhraní API pro správu prostředků Azure už je chráněný pomocí *řízení přístupu na základě role* modelu (RBAC). Funkce RBAC pro modul runtime operace je nyní ve verzi public preview. 

Aplikace, která používá Azure AD RBAC nemusí zpracování pravidel SAS a klíče nebo jiných přístupových tokenů specifické pro Service Bus. Klientská aplikace komunikuje se službou Azure AD k navázání kontext ověřování a získá přístupový token pro Service Bus. S uživatelskými účty domény, které vyžadují interaktivní přihlášení aplikace nikdy zpracovává všechny přihlašovací údaje přímo.

## <a name="service-bus-roles-and-permissions"></a>Service Bus rolí a oprávnění

Pro počáteční verzi public preview můžete pouze přidat účty Azure AD a objekty služby rolí "Vlastník" nebo "Přispěvatel" v oboru názvů zasílání zpráv Service Bus. Tato operace udělují oprávnění Úplné řízení všech entit v oboru názvů identity. Operace správy, které mění topologii obor názvů se původně podporované správy prostředků, když Azure a ne prostřednictvím rozhraní pro správu nativní REST pro Service Bus. Tato podpora také znamená, že klient pro rozhraní .NET Framework [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) objekt nelze použít s účtem Azure AD.  

## <a name="use-service-bus-with-an-azure-ad-domain-user-account"></a>Použití služby Service Bus pomocí účtu domény uživatele Azure AD

Následující část popisuje kroky potřebné k vytvoření a spuštění ukázkové aplikace, která zobrazí výzvu pro interaktivní Azure AD přihlášení uživatele, jak k udělení přístupu Service Bus s daným uživatelským účtem a jak používat tuto identitu pro přístup k Event Hubs. 

Tento úvod popisuje jednoduché konzolové aplikace [kód, pro který je na Githubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).

### <a name="create-an-active-directory-user-account"></a>Vytvořit uživatelský účet služby Active Directory

Tento první krok je volitelný. Každé předplatné služby Azure automaticky spárují se klienta služby Azure Active Directory a zda máte přístup k předplatnému Azure, váš uživatelský účet je již zaregistrován. To znamená, že používáte na váš účet. 

Pokud chcete vytvořit účet specifické pro tento scénář [postupujte podle těchto kroků](../automation/automation-create-aduser-account.md). Musíte mít oprávnění k vytváření účtů v klientovi služby Azure Active Directory, která nemusí být případ větší podnikové scénáře.

### <a name="create-a-service-bus-namespace"></a>Vytvoření oboru názvů Service Bus

Dále [vytvoření oboru názvů zasílání zpráv Service Bus](service-bus-create-namespace-portal.md) v jedné oblasti Azure, které mají preview podporu pro RBAC: **USA – východ**, **USA – východ 2**, nebo **západní Evropa** . 

Po vytvoření oboru názvů, přejděte do jeho **řízení přístupu (IAM)** na portálu a pak klikněte na tlačítko **přidat** k Azure AD uživatelský účet přidal k roli vlastníka. Pokud používáte vlastní uživatelský účet a vytvoříte obor názvů, jste již v roli vlastníka. Pokud chcete přidat jiný účet k roli, vyhledejte název webové aplikace **přidat oprávnění** panel **vyberte** pole a pak klikněte na položku. Potom klikněte na **Uložit**.

![](./media/service-bus-role-based-access-control/rbac1.PNG)

Uživatelský účet má teď přístup k oboru názvů Service Bus a do fronty jste předtím vytvořili.
 
### <a name="register-the-application"></a>Zaregistrovat aplikaci

Před spuštěním ukázkovou aplikaci zaregistrovat ve službě Azure AD a schválit řádku souhlasu, která umožňuje aplikaci přístup k Azure Service Bus jeho jménem. 

Protože vzorová aplikace je konzolovou aplikaci, musíte registrovat nativní aplikaci a přidejte oprávnění rozhraní API pro **Microsoft.ServiceBus** do sady "požadovaná oprávnění". Nativní aplikace je také nutné **redirect-URI** ve službě Azure AD, která slouží jako identifikátor URI není nutné do cílového umístění v síti. Použití `http://servicebus.microsoft.com` v tomto příkladu protože ukázku kódu již používá tento identifikátor URI.

Registrace podrobný postup je podrobně [v tomto kurzu](../active-directory/develop/active-directory-integrating-applications.md). Postupujte podle kroků k registraci **nativní** aplikace a pak postupujte podle pokynů aktualizace a přidejte **Microsoft.ServiceBus** rozhraní API požadovaná oprávnění. Jak budete postupovat podle kroků, poznamenejte si **TenantId** a **ApplicationId**, jak je budete potřebovat tyto hodnoty ke spuštění aplikace.

### <a name="run-the-app"></a>Spuštění aplikace

Před spuštěním ukázky, upravte soubor App.config a v závislosti na scénáři, nastavte následující hodnoty:

- `tenantId`: Nastavte na **TenantId** hodnotu.
- `clientId`: Nastavte na **ApplicationId** hodnotu. 
- `clientSecret`: Pokud se chcete přihlásit pomocí tajný klíč klienta, vytvořte ho ve službě Azure AD. Navíc místo nativní aplikace použijte na webovou aplikaci nebo rozhraní API. Navíc přidat aplikaci v části **řízení přístupu (IAM)** v oboru názvů, které jste předtím vytvořili.
- `serviceBusNamespaceFQDN`: Nastavte na úplný název DNS vašeho nově vytvořeného oboru názvů Service Bus; například `example.servicebus.windows.net`.
- `queueName`: Nastavte na název fronty, kterou jste vytvořili.
- Identifikátor URI přesměrování, který jste zadali ve vaší aplikaci v předchozích krocích.
 
Při spuštění konzolové aplikace, budete vyzváni k výběru scénáři; Klikněte na tlačítko **interaktivní přihlášení uživatele** zadáním jeho číslo a stisknutím klávesy ENTER. Aplikace zobrazí okno přihlášení, požádá o váš souhlas pro přístup k Service Bus a pak je spustit prostřednictvím scénáři odesílání a přijímání pomocí identity přihlášení pomocí služby.

## <a name="next-steps"></a>Další kroky

Pokud se o přenosu zpráv přes Service Bus chcete dozvědět víc, pročtěte si následující témata.

* [Základy služby Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)