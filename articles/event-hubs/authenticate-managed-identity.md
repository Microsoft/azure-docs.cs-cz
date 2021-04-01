---
title: Ověřování spravované identity pomocí Azure Active Directory
description: Tento článek poskytuje informace o ověřování spravované identity pomocí Azure Active Directory pro přístup k prostředkům Azure Event Hubs.
ms.topic: conceptual
ms.date: 01/25/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 2070cfd94b39a08afb86ffd3579f1116faac72d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98805301"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Ověření spravované identity pomocí Azure Active Directory pro přístup k prostředkům Event Hubs
Azure Event Hubs podporuje ověřování Azure Active Directory (Azure AD) se [spravovanými identitami pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md). Spravované identity pro prostředky Azure můžou autorizovat přístup k Event Hubs prostředkům pomocí přihlašovacích údajů Azure AD z aplikací běžících ve službě Azure Virtual Machines (VM), aplikací Function App, Virtual Machine Scale Sets a dalších služeb. Pomocí spravovaných identit pro prostředky Azure spolu s ověřováním Azure AD se můžete vyhnout ukládání přihlašovacích údajů k vašim aplikacím, které běží v cloudu.

Tento článek popisuje, jak autorizovat přístup k centru událostí pomocí spravované identity z virtuálního počítače Azure.

## <a name="enable-managed-identities-on-a-vm"></a>Povolení spravovaných identit na virtuálním počítači
Než budete moct použít spravované identity pro prostředky Azure k autorizaci Event Hubs prostředků z virtuálního počítače, musíte nejdřív na VIRTUÁLNÍm počítači povolit spravované identity pro prostředky Azure. Informace o tom, jak povolit spravované identity pro prostředky Azure, najdete v jednom z těchto článků:

- [Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Šablona Azure Resource Manageru](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Klientské knihovny Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Udělení oprávnění spravované identitě v Azure AD
Pokud chcete autorizovat požadavek na Event Hubs služby ze spravované identity ve vaší aplikaci, nejdřív nakonfigurujte nastavení řízení přístupu na základě role Azure (Azure RBAC) pro tuto spravovanou identitu. Azure Event Hubs definuje role Azure, které zahrnují oprávnění k posílání a čtení z Event Hubs. Když je role Azure přiřazená ke spravované identitě, udělí se spravované identitě přístup k Event Hubs datům v příslušném oboru.

Další informace o přiřazování rolí Azure najdete v tématu [ověřování pomocí Azure Active Directory pro přístup k prostředkům Event Hubs](authorize-access-azure-active-directory.md).

## <a name="use-event-hubs-with-managed-identities"></a>Použití služby Event Hubs pomocí spravovaných identit
Chcete-li použít Event Hubs se spravovanými identitami, je nutné přiřadit roli a příslušnému oboru k identitě. Postup v této části používá jednoduchou aplikaci, která běží pod spravovanou identitou a přistupuje k prostředkům Event Hubs.

Tady používáme ukázkovou webovou aplikaci hostovanou v [Azure App Service](https://azure.microsoft.com/services/app-service/). Podrobné pokyny pro vytvoření webové aplikace najdete v tématu [Vytvoření webové aplikace v ASP.NET Core v Azure](../app-service/quickstart-dotnetcore.md) .

Po vytvoření aplikace proveďte tyto kroky: 

1. Přejít na **Nastavení** a vybrat **identitu**. 
1. Vyberte **stav** , který má být **zapnut**. 
1. Vyberte **Uložit** a nastavení se uloží. 

    :::image type="content" source="./media/authenticate-managed-identity/identity-web-app.png" alt-text="Spravovaná identita pro webovou aplikaci":::
4. Pro informační zprávu vyberte **Ano** . 

    Po povolení tohoto nastavení se v Azure Active Directory (Azure AD) vytvoří nová identita služby a nakonfiguruje se na hostitele App Service.

    Teď tuto identitu služby přiřaďte roli v požadovaném oboru ve vašich Event Hubsch prostředcích.

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>Přiřazení rolí Azure pomocí Azure Portal
Chcete-li přiřadit roli k Event Hubs prostředkům, přejděte k tomuto prostředku v Azure Portal. Zobrazit nastavení Access Control (IAM) prostředku a podle těchto pokynů můžete spravovat přiřazení rolí:

> [!NOTE]
> Následující kroky přiřadí roli identity služby vašim oborům názvů Event Hubs. Stejný postup můžete provést, chcete-li přiřadit role s rozsahem Event Hubs prostředku. 

1. V Azure Portal přejděte na svůj obor názvů Event Hubs a zobrazte **Přehled** oboru názvů. 
1. V nabídce vlevo vyberte **Access Control (IAM)** a zobrazte nastavení řízení přístupu pro centrum událostí.
1.  Vyberte kartu **přiřazení rolí** a zobrazte seznam přiřazení rolí.
3.  Vyberte **Přidat** a pak vybrat * * přidat přiřazení role * * *.
4.  Na stránce **Přidat přiřazení role** použijte následující postup:
    1. Jako **role** vyberte roli Event Hubs, kterou chcete přiřadit. V tomto příkladu se jedná o **vlastníka dat Event Hubs Azure**.
    1. V poli **přiřadit přístup k** vyberte **App Service** v části **spravovaná identita přiřazená systémem**. 
    1. Vyberte **předplatné** , ve kterém se vytvořila spravovaná identita pro webovou aplikaci.
    1. Vyberte **spravovanou identitu** pro webovou aplikaci, kterou jste vytvořili. Výchozí název identity je stejný jako název webové aplikace. 
    1. Pak vyberte **Uložit**. 
    
        ![Přidat stránku přiřazení role](./media/authenticate-managed-identity/add-role-assignment-page.png)

    Po přiřazení role bude mít webová aplikace přístup k prostředkům Event Hubs v rámci definovaného oboru. 

    > [!NOTE]
    > Seznam služeb, které podporují spravované identity, najdete v tématu [služby, které podporují spravované identity prostředků Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

### <a name="test-the-web-application"></a>Test webové aplikace
1. Vytvoří obor názvů Event Hubs a centrum událostí. 
2. Nasaďte webovou aplikaci do Azure. Odkazy na webovou aplikaci na GitHubu najdete v následující části s kartami. 
3. Ujistěte se, že SendReceive. aspx je nastaven jako výchozí dokument pro webovou aplikaci. 
3. Povolte **identitu** webové aplikace. 
4. Přiřaďte tuto identitu k roli **Event Hubs data Owner** na úrovni oboru názvů nebo centra událostí. 
5. Spusťte webovou aplikaci, zadejte název oboru názvů a název centra událostí, zprávu a vyberte **Odeslat**. Pokud chcete událost přijmout, vyberte **přijmout**. 

#### <a name="azuremessagingeventhubs-latest"></a>[Azure. Messaging. EventHubs (nejnovější)](#tab/latest)
Nyní můžete spustit webovou aplikaci a nasměrovat prohlížeč na vzorovou stránku ASPX. Můžete najít ukázkovou webovou aplikaci, která odesílá a přijímá data z Event Hubsch prostředků v [úložišti GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp).

Nainstalujte si nejnovější balíček ze sady [NuGet](https://www.nuget.org/packages/Azure.Messaging.EventHubs/)a začněte odesílat události pro Event Hubs pomocí **EventHubProducerClient** a příjem událostí pomocí **EventHubConsumerClient**. 

> [!NOTE]
> Vzorek Java, který používá spravovanou identitu k publikování událostí do centra událostí, najdete v tématu věnovaném [publikování událostí s Azure identity Sample na GitHubu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs).

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
Nyní můžete spustit webovou aplikaci a nasměrovat prohlížeč na vzorovou stránku ASPX. Můžete najít ukázkovou webovou aplikaci, která odesílá a přijímá data z Event Hubsch prostředků v [úložišti GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp).

Nainstalujte si nejnovější balíček z [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)a začněte odesílat a přijímat data z Center událostí pomocí EventHubClient, jak je znázorněno v následujícím kódu: 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```
---

## <a name="event-hubs-for-kafka"></a>Event Hubs pro Kafka
Pomocí Apache Kafka aplikací můžete odesílat a přijímat zprávy z Azure Event Hubs pomocí spravované identity OAuth. Podívejte se na následující ukázku na GitHubu: [Event Hubs pro posílání a přijímání zpráv pomocí spravované identity OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity).

## <a name="samples"></a>ukázky
- Ukázky pro **Azure. Messaging. EventHubs**
    - [.NET](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Ukázky Microsoft. Azure. EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) 
    
    Tyto ukázky používají starou knihovnu **Microsoft. Azure. EventHubs** , ale můžete ji snadno aktualizovat tak, aby používala nejnovější knihovnu **Azure. Messaging. EventHubs** . Postup přesunutí ukázky z použití staré knihovny do nové verze najdete v [Průvodci migrací z Microsoft. Azure. EventHubs do Azure. Messaging. EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
    Tato ukázka se aktualizovala tak, aby používala nejnovější knihovnu **Azure. Messaging. EventHubs** .
- [Event Hubs pro Kafka – posílání a přijímání zpráv pomocí spravované identity OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)


## <a name="next-steps"></a>Další kroky
- V následujícím článku se dozvíte o spravovaných identitách prostředků Azure: [co jsou spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md)
- Podívejte se na následující související články:
    - [Ověřování požadavků do Azure Event Hubs z aplikace pomocí Azure Active Directory](authenticate-application.md)
    - [Ověřování požadavků na Azure Event Hubs pomocí sdílených přístupových podpisů](authenticate-shared-access-signature.md)
    - [Autorizace přístupu k prostředkům Event Hubs pomocí Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Autorizace přístupu k prostředkům Event Hubs pomocí sdílených přístupových podpisů](authorize-access-shared-access-signature.md)
