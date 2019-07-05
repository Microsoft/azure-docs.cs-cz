---
title: Azure ve verzi preview Service Bus Role-Based řízení přístupu (RBAC) | Dokumentace Microsoftu
description: Řízení přístupu na základě role Azure Service Bus
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
ms.date: 09/19/2018
ms.author: aschhab
ms.openlocfilehash: 6f5390162ce56a0e77ef41740d7e88f3546c5530
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444735"
---
# <a name="active-directory-role-based-access-control-preview"></a>Aktivní Directory Role-Based řízení přístupu (preview)

Microsoft Azure poskytuje správu řízení integrované přístupu k prostředkům a aplikace založené na Azure Active Directory (Azure AD). S Azure AD, můžete buď spravovat uživatelské účty a aplikace konkrétně pro Azure na základě aplikací nebo může provést federaci stávající infrastrukturu služby Active Directory s Azure AD pro pořádaného microsoftem jednotného přihlašování, která také zahrnuje prostředky Azure a aplikace hostované v Azure. Aby bylo možné udělit přístup k prostředkům Azure pak můžete přiřadit tyto identity uživatelů a aplikací Azure AD pro globální a specifické pro služby role.

Pro Azure Service Bus, Správa oborů názvů a všech souvisejících prostředků na webu Azure portal a rozhraní API pro správu prostředků Azure už je chráněný pomocí *řízení přístupu na základě rolí* modelu (RBAC). RBAC pro modul runtime operace funkce je teď ve verzi public preview.

Aplikace, která používá Azure AD RBAC není potřeba zpracovat pravidla SAS a klíče nebo jiné přístupové tokeny specifické pro Service Bus. Klientská aplikace komunikuje se službou Azure AD k navázání kontext ověřování a získá přístupový token pro službu Service Bus. S uživatelskými účty domény, které vyžadují interaktivního přihlášení aplikace nikdy zpracovává všechny přihlašovací údaje přímo.

## <a name="service-bus-roles-and-permissions"></a>Role služby Service Bus a oprávnění

Azure poskytuje níže uvedené vestavěné role RBAC pro autorizaci přístupu k oboru názvů služby Service Bus:

* [Vlastník dat služby Azure Service Bus (Preview)](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner-preview): Umožňuje přístup k datům pro obor názvů služby Service Bus a jeho entit (fronty, témata, odběry a filtry)

>[!IMPORTANT]
> Dříve podporován spravovanou identitu pro přidání **"Vlastník"** nebo **"Přispěvatel"** role.
>
> Nicméně oprávnění pro přístup k datům **"Vlastník"** a **"Přispěvatel"** role se už zachované. Pokud jste používali **"Vlastník"** nebo **"Přispěvatel"** role a ty pak bude muset být přizpůsobena využívat **"Azure Service Bus vlastník dat (Preview)"** role.

## <a name="use-service-bus-with-an-azure-ad-domain-user-account"></a>Uživatelský účet domény Azure AD pomocí služby Service Bus

Následující část popisuje kroky potřebné k vytvoření a spuštění ukázkové aplikace, který zobrazí výzvu k zadání interaktivní Azure AD uživateli umožní přihlásit, jak udělit přístup k Service Bus s daným uživatelským účtem a tom, jak použít tuto identitu pro přístup k Event Hubs.

Tento úvod popisuje jednoduchou konzolovou aplikaci, [kód, pro který je na Githubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).

### <a name="create-an-active-directory-user-account"></a>Vytvoření účtu uživatele služby Active Directory

Tento první krok je volitelný. Každé předplatné služby Azure se automaticky páruje s oblastí tenanta služby Azure Active Directory a pokud máte přístup k předplatnému Azure, váš uživatelský účet je už zaregistrovaný. To znamená, že používáte na váš účet.

Pokud chcete vytvořit zvláštní účet pro tento scénář [postupujte podle těchto kroků](../automation/automation-create-aduser-account.md). Musíte mít oprávnění k vytváření účtů v tenantovi Azure Active Directory, která nemusí být v případě větší podnikové scénáře.

### <a name="create-a-service-bus-namespace"></a>Vytvoření oboru názvů Service Bus

Dále [vytvoření oboru názvů služby Service Bus Messaging](service-bus-create-namespace-portal.md).

Jakmile se vytvoří obor názvů, přejděte do jeho **řízení přístupu (IAM)** stránky na portálu a potom klikněte na **přidat přiřazení role** přidání do role vlastníka účtu uživatele Azure AD. Pokud používáte vlastní uživatelský účet a vytvořili obor názvů, jste již v roli vlastník. Pokud chcete přidat jiný účet k roli, vyhledejte název webové aplikace v **přidat oprávnění** panel **vyberte** pole a potom klikněte na položku. Potom klikněte na **Uložit**.

Uživatelský účet má teď přístup k oboru názvů služby Service Bus a do fronty jste předtím vytvořili.

### <a name="register-the-application"></a>Zaregistrovat aplikaci

Předtím, než spustíte ukázkovou aplikaci zaregistrovat ve službě Azure AD a schválit výzva k povolení spuštění, který umožňuje aplikaci přístup k Azure Service Bus svým jménem.

Protože ukázková aplikace je konzolová aplikace, musí registrovat nativní aplikaci a přidejte oprávnění k rozhraní API pro **Microsoft.ServiceBus** do sady "požadováno oprávnění". Nativní aplikace také potřebují **redirect-URI** ve službě Azure AD, která slouží jako identifikátor URI není potřeba se cílové sítě. Použití `https://servicebus.microsoft.com` v tomto příkladu vzhledem k tomu, že ukázkový kód již používá tento identifikátor URI.

Podrobné registrační kroky jsou vysvětlené v [v tomto kurzu](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Postupujte podle pokynů pro registraci **nativní** aplikace a pak postupujte podle pokynů aktualizace a přidejte **Microsoft.ServiceBus** rozhraní API požadovaná oprávnění. Jak budete postupovat podle kroků, poznamenejte si **TenantId** a **ApplicationId**, protože ji budete potřebovat tyto hodnoty ke spuštění aplikace.

### <a name="run-the-app"></a>Spuštění aplikace

Před spuštěním ukázky, upravte soubor App.config a v závislosti na scénáři, nastavte následující hodnoty:

- `tenantId`: Nastavte na **TenantId** hodnotu.
- `clientId`: Nastavte na **ApplicationId** hodnotu.
- `clientSecret`: Pokud chcete k přihlášení pomocí tajného klíče klienta, vytvořte ho ve službě Azure AD. Také můžete použijte webovou aplikaci nebo API namísto nativní aplikaci. Přidejte také aplikaci v rámci **řízení přístupu (IAM)** v oboru názvů, kterou jste vytvořili dřív.
- `serviceBusNamespaceFQDN`: Nastavte úplný název DNS váš nově vytvořený obor názvů služby Service Bus; například `example.servicebus.windows.net`.
- `queueName`: Nastavte název fronty, kterou jste vytvořili.
- Identifikátor URI pro přesměrování, který jste zadali ve vaší aplikaci v předchozích krocích.

Když spustíte aplikaci konzoly, zobrazí se výzva k výběru scénáře; Klikněte na tlačítko **interaktivní přihlášení uživatele** zadáním příslušného čísla a stisknutím klávesy ENTER. Aplikace zobrazí okno přihlášení, vyzve k zadání vašeho svolení pro přístup k Service Bus a pak používá službu spustit prostřednictvím scénář posílání a přijímání pomocí identity přihlášení.

## <a name="next-steps"></a>Další postup

Pokud se o přenosu zpráv přes Service Bus chcete dozvědět víc, pročtěte si následující témata.

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
