---
title: Ověření spravované identity pomocí Služby Azure Active Directory
description: Tento článek obsahuje informace o ověřování spravované identity pomocí Služby Azure Active Directory pro přístup k prostředkům Centra událostí Azure
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: dfc60fbc03021e72dccc0f60a7ac34d204ef6df9
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025182"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Ověření spravované identity pomocí Služby Azure Active Directory pro přístup k prostředkům centra událostí
Azure Event Hubs podporuje ověřování Azure Active Directory (Azure AD) se [spravovanými identitami pro prostředky Azure.](../active-directory/managed-identities-azure-resources/overview.md) Spravované identity pro prostředky Azure můžou autorizovat přístup k prostředkům Centra událostí pomocí přihlašovacích údajů Azure AD z aplikací spuštěných ve virtuálních počítačích Azure, aplikacích funkcí, škálovacích sadách virtuálních počítačů a dalších službách. Pomocí spravovaných identit pro prostředky Azure společně s ověřováním Azure AD se můžete vyhnout ukládání přihlašovacích údajů s vašimi aplikacemi, které běží v cloudu.

Tento článek ukazuje, jak autorizovat přístup k centru událostí pomocí spravované identity z virtuálního počítače Azure.

## <a name="enable-managed-identities-on-a-vm"></a>Povolení spravovaných identit na virtuálním počítači
Než budete moct použít spravované identity pro prostředky Azure k autorizaci prostředků Centra událostí z vašeho virtuálního počítače, musíte nejdřív povolit spravované identity pro prostředky Azure na virtuálním počítači. Informace o povolení spravovaných identit pro prostředky Azure najdete v jednom z těchto článků:

- [portál Azure](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Šablona Azure Resource Manageru](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Klientské knihovny Azure Resource Manageru](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Udělení oprávnění spravované identitě ve službě Azure AD
Chcete-li autorizovat požadavek na službu Event Hubs ze spravované identity ve vaší aplikaci, nejprve nakonfigurujte nastavení řízení přístupu na základě rolí (RBAC) pro tuto spravovanou identitu. Azure Event Hubs definuje role RBAC, které zahrnují oprávnění pro odesílání a čtení z event hubů. Když je role RBAC přiřazena spravované identitě, spravované identitě je udělen přístup k datům centra událostí v příslušném oboru.

Další informace o přiřazování rolí RBAC najdete [v tématu Ověření pomocí služby Azure Active Directory, kde najdete přístup k prostředkům centra událostí](authorize-access-azure-active-directory.md).

## <a name="use-event-hubs-with-managed-identities"></a>Použití služby Event Hubs pomocí spravovaných identit
Chcete-li používat centra událostí se spravovanými identitami, musíte přiřadit identitu roli a příslušný obor. Postup v této části používá jednoduchou aplikaci, která běží pod spravovanou identitou a přistupuje k prostředkům Event Hubs.

Tady používáme ukázkovou webovou aplikaci hostovohouji ve [službě Azure App Service](https://azure.microsoft.com/services/app-service/). Podrobné pokyny k vytvoření webové aplikace najdete v [tématu Vytvoření webové aplikace ASP.NET Core v Azure.](../app-service/app-service-web-get-started-dotnet.md)

Po vytvoření aplikace postupujte takto: 

1. Přejděte do **nastavení** a vyberte **Identita**. 
1. Vyberte **stav,** na který chcete být **zapnuti**. 
1. Vyberte **Uložit** a nastavení se uloží. 

    ![Spravovaná identita webové aplikace](./media/authenticate-managed-identity/identity-web-app.png)

Po povolení tohoto nastavení se ve vaší službě Azure Active Directory (Azure AD) vytvoří nová identita služby a nakonfiguruje se do hostitele služby App Service.

Nyní přiřaďte tuto identitu služby roli v požadovaném oboru ve vašich prostředcích Centra událostí.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Přiřazení rolí RBAC pomocí portálu Azure
Pokud chcete přiřadit roli prostředkům Centra událostí, přejděte na tento prostředek na webu Azure Portal. Zobrazte nastavení řízení přístupu (IAM) pro prostředek a podle následujících pokynů spravujte přiřazení rolí:

> [!NOTE]
> Následující kroky přiřadí roli identity služby do oborů názvů Event Hubs. Stejným postupem můžete přiřadit roli vymezenou jakémukoli prostředku centra událostí. 

1. Na webu Azure Portal přejděte do oboru názvů Centra událostí a zobrazte **přehled** oboru názvů. 
1. V levém menu vyberte **Řízení přístupu (IAM),** chcete-li zobrazit nastavení řízení přístupu pro centrum událostí.
1.  Výběrem karty **Přiřazení rolí** zobrazíte seznam přiřazení rolí.
3.  Výběrem **možnosti Přidat** přidáte novou roli.
4.  Na stránce **Přidat přiřazení role** vyberte role Centra událostí, které chcete přiřadit. Potom vyhledejte identitu služby, kterou jste zaregistrovali, abyste roli přiřadili.
    
    ![Stránka přiřazení role Přidat](./media/authenticate-managed-identity/add-role-assignment-page.png)
5.  Vyberte **Uložit**. Identita, které jste roli přiřadili, se zobrazí v seznamu v této roli. Například následující obrázek ukazuje, že identita služby má vlastníka dat centra událostí.
    
    ![Identita přiřazená k roli](./media/authenticate-managed-identity/role-assigned.png)

Po přiřazení role bude mít webová aplikace přístup k prostředkům Centra událostí v rámci definovaného oboru. 

### <a name="test-the-web-application"></a>Test webové aplikace
1. Vytvořte obor názvů Event Hubs a centrum událostí. 
2. Nasaďte webovou aplikaci do Azure. Odkazy na webovou aplikaci na GitHubu najdete v následující části s kartami. 
3. Ujistěte se, že SendReceive.aspx je nastaven jako výchozí dokument pro webovou aplikaci. 
3. Povolte **identitu** webové aplikace. 
4. Přiřaďte tuto identitu roli **Vlastníka dat centra událostí** na úrovni oboru názvů nebo na úrovni centra událostí. 
5. Spusťte webovou aplikaci, zadejte název oboru názvů a název centra událostí, zprávu a vyberte **Odeslat**. Chcete-li událost přijmout, vyberte **možnost Přijmout**. 

#### <a name="azuremessagingeventhubs-latest"></a>[Azure.Messaging.EventHubs (nejnovější)](#tab/latest)
Nyní můžete spustit webovou aplikaci a nasměrovat prohlížeč na ukázkovou stránku aspx. Ukázkovou webovou aplikaci, která odesílá a přijímá data z prostředků Event Hubs, najdete v [úložišti GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp).

Nainstalujte nejnovější balíček z [NuGet](https://www.nuget.org/packages/Azure.Messaging.EventHubs/)a začněte odesílat události do center událostí pomocí **eventhubproducerclienta** a přijímat události pomocí **EventHubConsumerClient**. 

> [!NOTE]
> Ukázka jazyka Java, která používá spravovanou identitu k publikování událostí do centra událostí, najdete v tématu [Publikování událostí s ukázkou identity Azure na GitHubu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs).

```csharp
protected async void btnSend_Click(object sender, EventArgs e)
{
    await using (EventHubProducerClient producerClient = new EventHubProducerClient(txtNamespace.Text, txtEventHub.Text, new DefaultAzureCredential()))
    {
        // create a batch
        using (EventDataBatch eventBatch = await producerClient.CreateBatchAsync())
        {

            // add events to the batch. only one in this case. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes(txtData.Text)));

            // send the batch to the event hub
            await producerClient.SendAsync(eventBatch);
        }

        txtOutput.Text = $"{DateTime.Now} - SENT{Environment.NewLine}{txtOutput.Text}";
    }
}
protected async void btnReceive_Click(object sender, EventArgs e)
{
    await using (var consumerClient = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, $"{txtNamespace.Text}.servicebus.windows.net", txtEventHub.Text, new DefaultAzureCredential()))
    {
        int eventsRead = 0;
        try
        {
            using CancellationTokenSource cancellationSource = new CancellationTokenSource();
            cancellationSource.CancelAfter(TimeSpan.FromSeconds(5));

            await foreach (PartitionEvent partitionEvent in consumerClient.ReadEventsAsync(cancellationSource.Token))
            {
                txtOutput.Text = $"Event Read: { Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray()) }{ Environment.NewLine}" + txtOutput.Text;
                eventsRead++;
            }
        }
        catch (TaskCanceledException ex)
        {
            txtOutput.Text = $"Number of events read: {eventsRead}{ Environment.NewLine}" + txtOutput.Text;
        }
    }
}
```

#### <a name="microsoftazureeventhubs-legacy"></a>[Microsoft.Azure.EventHubs (starší verze)](#tab/old)
Nyní můžete spustit webovou aplikaci a nasměrovat prohlížeč na ukázkovou stránku aspx. Ukázkovou webovou aplikaci, která odesílá a přijímá data z prostředků Event Hubs, najdete v [úložišti GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp).

Nainstalujte nejnovější balíček z [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)a začněte odesílat a přijímat data z center událostí pomocí klienta EventHubClient, jak je znázorněno v následujícím kódu: 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```
---

## <a name="event-hubs-for-kafka"></a>Event Hubs pro Kafka
Aplikace Apache Kafka můžete použít k odesílání zpráv do a přijímání zpráv z Azure Event Hubs pomocí spravované identity OAuth. Podívejte se na následující ukázku na [GitHubu: Event Hubs for Kafka - odesílání a přijímání zpráv pomocí spravované identity OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity).

## <a name="samples"></a>ukázky
- **Ukázky Azure.Messaging.EventHubs**
    - [.NET](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Ukázky Microsoft.Azure.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Tyto ukázky používají starou knihovnu **Microsoft.Azure.EventHubs,** ale můžete ji snadno aktualizovat na použití nejnovější **knihovny Azure.Messaging.EventHubs.** Pokud chcete vzorek přesunout z používání staré knihovny do nové, přečtěte si [příručku pro migraci z Microsoft.Azure.EventHubs na Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
    Tato ukázka byla aktualizována tak, aby používala nejnovější knihovnu **Azure.Messaging.EventHubs.**
- [Event Hubs pro Kafka - odesílání a přijímání zpráv pomocí spravované identity OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)


## <a name="next-steps"></a>Další kroky
- V následujícím článku se dozvíte o spravovaných identitách pro prostředky Azure: [Co je spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md)
- Podívejte se na následující související články:
    - [Ověřování požadavků na Centra událostí Azure z aplikace pomocí Služby Azure Active Directory](authenticate-application.md)
    - [Ověřování požadavků na Centra událostí Azure pomocí sdílených přístupových podpisů](authenticate-shared-access-signature.md)
    - [Autorizace přístupu k prostředkům Centra událostí pomocí Služby Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Autorizace přístupu k prostředkům Centra událostí pomocí sdílených přístupových podpisů](authorize-access-shared-access-signature.md)