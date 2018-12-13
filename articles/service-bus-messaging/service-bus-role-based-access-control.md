---
title: Azure ve verzi preview Service Bus Role-Based řízení přístupu (RBAC) | Dokumentace Microsoftu
description: Řízení přístupu na základě role Azure Service Bus
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: spelluru
ms.openlocfilehash: c2a0fdd9fc006a9c849a71d6064959759886d920
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53309463"
---
# <a name="active-directory-role-based-access-control-preview"></a>Aktivní Directory Role-Based řízení přístupu (preview)

Microsoft Azure poskytuje správu řízení integrované přístupu k prostředkům a aplikace založené na Azure Active Directory (Azure AD). S Azure AD, můžete buď spravovat uživatelské účty a aplikace konkrétně pro Azure na základě aplikací nebo může provést federaci stávající infrastrukturu služby Active Directory s Azure AD pro pořádaného microsoftem jednotného přihlašování, která také zahrnuje prostředky Azure a aplikace hostované v Azure. Aby bylo možné udělit přístup k prostředkům Azure pak můžete přiřadit tyto identity uživatelů a aplikací Azure AD pro globální a specifické pro služby role.

Pro Azure Service Bus, Správa oborů názvů a všech souvisejících prostředků na webu Azure portal a rozhraní API pro správu prostředků Azure už je chráněný pomocí *řízení přístupu na základě rolí* modelu (RBAC). RBAC pro modul runtime operace funkce je teď ve verzi public preview.

Aplikace, která používá Azure AD RBAC není potřeba zpracovat pravidla SAS a klíče nebo jiné přístupové tokeny specifické pro Service Bus. Klientská aplikace komunikuje se službou Azure AD k navázání kontext ověřování a získá přístupový token pro službu Service Bus. S uživatelskými účty domény, které vyžadují interaktivního přihlášení aplikace nikdy zpracovává všechny přihlašovací údaje přímo.

## <a name="service-bus-roles-and-permissions"></a>Role služby Service Bus a oprávnění

Počáteční verze public Preview můžete pouze přidat účty služby Azure AD a instanční objekty "Vlastník" nebo "Přispěvatel" rolí v oboru názvů služby Service Bus Messaging. Tato operace udělují oprávnění Úplné řízení všech entit v oboru názvů identity. Operace správy, které se mění topologii obor názvů jsou zpočátku pouze podporované i když Azure resource management a ne přes rozhraní nativní Správa služby Service Bus REST. Tato podpora také znamená, že rozhraní .NET Framework client [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) objekt nelze použít s účtem služby Azure AD.

## <a name="use-service-bus-with-an-azure-ad-domain-user-account"></a>Uživatelský účet domény Azure AD pomocí služby Service Bus

Následující část popisuje kroky potřebné k vytvoření a spuštění ukázkové aplikace, který zobrazí výzvu k zadání interaktivní Azure AD přihlášení uživatele, jak udělit přístup k Service Bus s daným uživatelským účtem a tom, jak použít tuto identitu pro přístup k Event Hubs.

Tento úvod popisuje jednoduchou konzolovou aplikaci, [kód, pro který je na Githubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).

### <a name="create-an-active-directory-user-account"></a>Vytvoření účtu uživatele služby Active Directory

Tento první krok je volitelný. Každé předplatné služby Azure se automaticky páruje s oblastí tenanta služby Azure Active Directory a pokud máte přístup k předplatnému Azure, váš uživatelský účet je už zaregistrovaný. To znamená, že používáte na váš účet.

Pokud chcete vytvořit zvláštní účet pro tento scénář [postupujte podle těchto kroků](../automation/automation-create-aduser-account.md). Musíte mít oprávnění k vytváření účtů v tenantovi Azure Active Directory, která nemusí být v případě větší podnikové scénáře.

### <a name="create-a-service-bus-namespace"></a>Vytvoření oboru názvů Service Bus

Dále [vytvoření oboru názvů služby Service Bus Messaging](service-bus-create-namespace-portal.md) v jedné oblasti Azure, které mají podporu náhledu pro RBAC: **USA – východ**, **USA – východ 2**, nebo **západní Evropa**.

Jakmile se vytvoří obor názvů, přejděte do jeho **řízení přístupu (IAM)** stránky na portálu a potom klikněte na **přidat přiřazení role** přidání do role vlastníka účtu uživatele Azure AD. Pokud používáte vlastní uživatelský účet a vytvořili obor názvů, jste již v roli vlastník. Pokud chcete přidat jiný účet k roli, vyhledejte název webové aplikace v **přidat oprávnění** panel **vyberte** pole a potom klikněte na položku. Potom klikněte na **Uložit**.

Uživatelský účet má teď přístup k oboru názvů služby Service Bus a do fronty jste předtím vytvořili.

### <a name="register-the-application"></a>Zaregistrovat aplikaci

Předtím, než spustíte ukázkovou aplikaci zaregistrovat ve službě Azure AD a schválit výzva k povolení spuštění, který umožňuje aplikaci přístup k Azure Service Bus svým jménem.

Protože ukázková aplikace je konzolová aplikace, musí registrovat nativní aplikaci a přidejte oprávnění k rozhraní API pro **Microsoft.ServiceBus** do sady "požadováno oprávnění". Nativní aplikace také potřebují **redirect-URI** ve službě Azure AD, která slouží jako identifikátor URI není potřeba se cílové sítě. Použití `http://servicebus.microsoft.com` v tomto příkladu vzhledem k tomu, že ukázkový kód již používá tento identifikátor URI.

Podrobné registrační kroky jsou vysvětlené v [v tomto kurzu](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Postupujte podle pokynů pro registraci **nativní** aplikace a pak postupujte podle pokynů aktualizace a přidejte **Microsoft.ServiceBus** rozhraní API požadovaná oprávnění. Jak budete postupovat podle kroků, poznamenejte si **TenantId** a **ApplicationId**, protože ji budete potřebovat tyto hodnoty ke spuštění aplikace.

### <a name="run-the-app"></a>Spuštění aplikace

Před spuštěním ukázky, upravte soubor App.config a v závislosti na scénáři, nastavte následující hodnoty:

- `tenantId`: Nastavte na **TenantId** hodnotu.
- `clientId`: Nastavte na **ApplicationId** hodnotu.
- `clientSecret`: Pokud se chcete přihlásit pomocí tajného klíče klienta, vytvořte ho ve službě Azure AD. Také můžete použijte webovou aplikaci nebo API namísto nativní aplikaci. Přidejte také aplikaci v rámci **řízení přístupu (IAM)** v oboru názvů, kterou jste vytvořili dřív.
- `serviceBusNamespaceFQDN`: Nastavte úplný název DNS váš nově vytvořený obor názvů služby Service Bus; například `example.servicebus.windows.net`.
- `queueName`: Nastavte název fronty, kterou jste vytvořili.
- Identifikátor URI pro přesměrování, který jste zadali ve vaší aplikaci v předchozích krocích.

Když spustíte aplikaci konzoly, zobrazí se výzva k výběru scénáře; Klikněte na tlačítko **interaktivní přihlášení uživatele** zadáním příslušného čísla a stisknutím klávesy ENTER. Aplikace zobrazí okno přihlášení, vyzve k zadání vašeho svolení pro přístup k Service Bus a pak používá službu spustit prostřednictvím scénář posílání a přijímání pomocí identity přihlášení.

## <a name="next-steps"></a>Další postup

Pokud se o přenosu zpráv přes Service Bus chcete dozvědět víc, pročtěte si následující témata.

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
