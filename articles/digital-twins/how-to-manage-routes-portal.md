---
title: Správa koncových bodů a tras (portál)
titleSuffix: Azure Digital Twins
description: Přečtěte si, jak nastavit a spravovat koncové body a trasy událostí pro data digitálních vláken Azure pomocí Azure Portal.
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 91c767fb031633900434b3aa07ccfae7cf7458cb
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92332089"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-portal"></a>Správa koncových bodů a tras v Azure Digital provlákna (portál)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

V digitálních prostředcích Azure můžete směrovat [oznámení událostí](how-to-interpret-event-data.md) na služby pro příjem dat nebo připojené výpočetní prostředky. To je prováděno napřed nastavením **koncových bodů** , které mohou přijímat události. Pak můžete vytvořit [**trasy událostí**](concepts-route-events.md) , které určují, které události generované pomocí digitálních vláken Azure jsou doručeny do těch koncových bodů.

Tento článek vás provede procesem vytvoření koncových bodů a tras pomocí [Azure Portal](https://portal.azure.com).

Můžete také spravovat koncové body a trasy pomocí [rozhraní API EventRoutes](how-to-use-apis-sdks.md), [sady .NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)nebo rozhraní příkazového [řádku Azure Digital revlákens](how-to-use-cli.md). Verzi tohoto článku, která používá tyto mechanismy místo portálu, najdete v tématu [*How to: Manage Endpoints and Routes (API and CLI)*](how-to-manage-routes-apis-cli.md).

## <a name="prerequisites"></a>Předpoklady

* Budete potřebovat **účet Azure** (můžete [si ho nastavit zdarma).](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* V předplatném Azure budete potřebovat **instanci digitálního vlákna Azure** . Pokud instanci již nemáte, můžete ji vytvořit pomocí kroků v tématu [*Postupy: nastavení instance a ověřování*](how-to-set-up-instance-portal.md). Použijte následující hodnoty z instalačního programu užitečné pro pozdější použití v tomto článku:
    - Název instance
    - Skupina prostředků

Tyto podrobnosti můžete najít v [Azure Portal](https://portal.azure.com) po nastavení instance. Přihlaste se na portál a na panelu hledání na portálu vyhledejte název vaší instance.
 
:::image type="content" source="media/how-to-manage-routes-portal/search-field-portal.png" alt-text="Snímek obrazovky s panelem hledání Azure Portal.":::

Výběrem vaší instance z výsledků zobrazíte stránku podrobností pro vaši instanci:

:::image type="content" source="media/how-to-manage-routes-portal/instance-details.png" alt-text="Snímek obrazovky s panelem hledání Azure Portal." border="false":::

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Vytvoření koncového bodu pro digitální vlákna Azure

Toto jsou podporované typy koncových bodů, které můžete vytvořit pro instanci:
* [Event Grid](../event-grid/overview.md) 
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Další informace o různých typech koncových bodů najdete v tématu [*Výběr mezi službou zasílání zpráv Azure*](../event-grid/compare-messaging-services.md).

Aby bylo možné propojit koncový bod s digitálními podsítěmi Azure, musí již existovat téma Event gridu, centrum událostí nebo Service Bus, které používáte pro koncový bod. 

### <a name="create-an-event-grid-endpoint"></a>Vytvoření koncového bodu Event Grid

**Předpoklad**: Vytvořte téma Event Grid podle kroků uvedených v [části *Vytvoření vlastního tématu* ](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) v rychlém startu Event Grid *vlastních událostí* .

Jakmile téma vytvoříte, můžete ho propojit s digitálními výsledky Azure na stránce instance digitálních vláken Azure v [Azure Portal](https://portal.azure.com) (instanci můžete najít zadáním jejího názvu do panelu hledání na portálu).

V nabídce instance vyberte _koncové body_. Pak na stránce *koncové body* , které následuje, vyberte *+ vytvořit koncový bod*. 

Na stránce *vytvořit koncový bod* , která se otevře, můžete vytvořit koncový bod typu _Event Grid_ tak, že vyberete odpovídající přepínač. Dokončete ostatní podrobnosti: do pole _název_ zadejte název vašeho koncového bodu, v rozevíracím seznamu vyberte své _předplatné_ a z třetího rozevíracího seznamu vyberte předem vytvořené  _Event Grid téma_ .

Pak vytvořte svůj koncový bod tím, že kliknete na _Uložit_.

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-grid.png" alt-text="Snímek obrazovky s panelem hledání Azure Portal.":::

Pomocí ikony oznámení na horním panelu Azure Portal můžete ověřit, že je koncový bod úspěšně vytvořen: 

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-notifications.png" alt-text="Snímek obrazovky s panelem hledání Azure Portal." border="false":::

Koncový bod, který byl vytvořen zpátky, můžete také zobrazit na stránce *koncových bodů* pro instanci digitálních vláken Azure.

Pokud se vytvoření koncového bodu nepovede, Sledujte chybovou zprávu a zkuste to znovu za několik minut.

Teď je téma Event gridu dostupné jako koncový bod uvnitř digitálních vláken Azure pod názvem zadaným v poli _název_ . Tento název se obvykle používá jako cíl **trasy události**, kterou vytvoříte [později v tomto článku](#create-an-event-route).

### <a name="create-an-event-hubs-endpoint"></a>Vytvoření koncového bodu Event Hubs

**Požadavky**: 
* Budete potřebovat _Event Hubs obor názvů_ a _centrum událostí_. Oba z těchto možností vytvořte pomocí postupu v Event Hubs [*vytvoření centra událostí*](../event-hubs/event-hubs-create.md) rychlý Start.
* Budete potřebovat _autorizační pravidlo_. Pokud to chcete vytvořit, přečtěte si článek Event Hubs [*autorizaci přístupu k prostředkům Event Hubs pomocí článku sdílené přístupové podpisy*](../event-hubs/authorize-access-shared-access-signature.md) .

Přejít na stránku podrobností pro instanci digitálních vláken Azure v [Azure Portal](https://portal.azure.com) (můžete ji najít zadáním jejího názvu do panelu hledání na portálu).

V nabídce instance vyberte _koncové body_. Pak na stránce *koncové body* , které následuje, vyberte *+ vytvořit koncový bod*. 

Na stránce *vytvořit koncový bod* , která se otevře, můžete vytvořit koncový bod typu _centrum událostí_ , a to tak, že vyberete odpovídající přepínač. Do pole _název_ zadejte název koncového bodu. Pak vyberte své _předplatné_a předem vytvořený _obor názvů centra událostí_, _centrum událostí_a _autorizační pravidlo_ z příslušných rozevíracích seznamů.

Pak vytvořte svůj koncový bod tím, že kliknete na _Uložit_.

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub.png" alt-text="Snímek obrazovky s panelem hledání Azure Portal.":::

To, jestli se koncový bod úspěšně vytvoří, můžete ověřit tak, že zkontrolujete ikonu oznámení na horním Azure Portalovém panelu. 

Pokud se vytvoření koncového bodu nepovede, Sledujte chybovou zprávu a zkuste to znovu za několik minut.

Centrum událostí je teď k dispozici jako koncový bod uvnitř digitálních vláken Azure pod názvem zadaným v poli _název_ . Tento název se obvykle používá jako cíl **trasy události**, kterou vytvoříte [později v tomto článku](#create-an-event-route).

### <a name="create-a-service-bus-endpoint"></a>Vytvoření koncového bodu Service Bus

**Požadavky**: 
* Budete potřebovat _Service Bus obor názvů_ a _Service Bus téma_. Oba z těchto možností vytvořte pomocí postupu v [*tématu Service Bus vytváření témat a odběrů*](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md) rychlý Start. Nemusíte dokončit [*vytváření odběrů v části tématu*](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md#create-subscriptions-to-the-topic) .
* Budete potřebovat _autorizační pravidlo_. Pokud to chcete vytvořit, přečtěte si článek Service Bus [*ověřování a autorizaci*](../service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature) .

Přejít na stránku podrobností pro instanci digitálních vláken Azure v [Azure Portal](https://portal.azure.com) (můžete ji najít zadáním jejího názvu do panelu hledání na portálu).

V nabídce instance vyberte _koncové body_. Pak na stránce *koncové body* , které následuje, vyberte *+ vytvořit koncový bod*. 

Na stránce *vytvořit koncový bod* , která se otevře, můžete vytvořit koncový bod typu _Service Bus_ tak, že vyberete odpovídající přepínač. Do pole _název_ zadejte název koncového bodu. Pak vyberte své _předplatné_a předem vytvořený _Service Bus obor názvů_, _Service Bus téma_a _autorizační pravidlo_ z příslušných rozevíracích seznamů.

Pak vytvořte svůj koncový bod tím, že kliknete na _Uložit_.

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-service-bus.png" alt-text="Snímek obrazovky s panelem hledání Azure Portal.":::

To, jestli se koncový bod úspěšně vytvoří, můžete ověřit tak, že zkontrolujete ikonu oznámení na horním Azure Portalovém panelu. 

Pokud se vytvoření koncového bodu nepovede, Sledujte chybovou zprávu a zkuste to znovu za několik minut.

Nyní je téma Service Bus k dispozici jako koncový bod uvnitř digitálních vláken Azure pod názvem zadaným v poli _název_ . Tento název se obvykle používá jako cíl **trasy události**, kterou vytvoříte [později v tomto článku](#create-an-event-route).

### <a name="create-an-endpoint-with-dead-lettering"></a>Vytvoření koncového bodu s nedoručenými písmeny

Když koncový bod nemůže doručovat událost v určitém časovém období nebo po pokusu o doručení události v určitém počtu opakování, může odeslat nedoručenou událost do účtu úložiště. Tento proces se označuje jako **nedoručené**.

Aby bylo možné vytvořit koncový bod s povolenou výjimkou, je nutné použít [rozhraní API ARM](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) k vytvoření koncového bodu, nikoli Azure Portal.

Pokyny k tomu, jak to udělat pomocí rozhraní API, najdete v tématu [*rozhraní API a verze CLI*](how-to-manage-routes-apis-cli.md#create-an-endpoint-with-dead-lettering) tohoto článku.

## <a name="create-an-event-route"></a>Vytvoření trasy události

Aby bylo možné ve skutečnosti odesílat data z digitálních vláken Azure do koncového bodu, bude nutné definovat **trasu události**. Tyto trasy umožňují vývojářům nasměrovat tok událostí v celém systému a na navazující služby. Přečtěte si další informace o trasách událostí v tématu [*Koncepty: směrování událostí digitálních vláken Azure*](concepts-route-events.md).

**Předpoklad**: před přechodem na vytvoření trasy je nutné vytvořit koncové body, jak je popsáno výše v tomto článku. Po dokončení nastavení koncových bodů můžete pokračovat v vytváření směrování událostí.

>[!NOTE]
>Pokud jste své koncové body v poslední době nasadili, ověřte, že se jejich nasazení dokončilo, **než** se pokusíte použít pro novou trasu události. Pokud nemůžete nastavit trasu, protože koncové body nejsou připravené, počkejte pár minut a zkuste to znovu.

### <a name="creation-steps-with-the-azure-portal"></a>Postup vytvoření pomocí Azure Portal

Definice trasy události obsahuje tyto prvky:
* Název trasy, kterou chcete použít
* Název koncového bodu, který chcete použít
* Filtr definující události odesílané do koncového bodu
    - Pokud chcete zakázat trasu, aby se neodesílaly žádné události, použijte hodnotu filtru. `false`
    - Pokud chcete povolit trasu, která nemá žádné konkrétní filtrování, použijte hodnotu filtru. `true`
    - Podrobnosti o jakémkoli jiném typu filtru najdete v části [*události filtru*](#filter-events) níže.

Jedna trasa může umožňovat výběr více oznámení a typů událostí.

Pokud chcete vytvořit trasu události, na stránce s podrobnostmi pro instanci digitálních vláken Azure v [Azure Portal](https://portal.azure.com) (můžete najít instanci zadáním jejího názvu do panelu hledání na portálu).

V nabídce instance vyberte možnost _trasy událostí_. Pak na stránce *trasy událostí* , která následuje, vyberte *+ vytvořit trasu události*. 

Na stránce *vytvořit trasu události* , která se otevře, vyberte minimálně:
* Název trasy v poli _název_
* _Koncový bod_ , který chcete použít k vytvoření trasy 

Aby bylo možné trasu povolit, je nutné také **Přidat filtr trasy události** alespoň `true` . (Výchozí hodnota pole `false` vytvoří trasu, ale do ní nebudou odesílány žádné události.) Provedete to tak, že přepnete přepínač pro _Rozšířený editor_ a povolíte jeho zápis `true` do pole *filtru* .

:::image type="content" source="media/how-to-manage-routes-portal/create-event-route-no-filter.png" alt-text="Snímek obrazovky s panelem hledání Azure Portal." lightbox="media/how-to-manage-routes-portal/create-event-route-no-filter.png":::

Po dokončení stiskněte tlačítko _Uložit_ a vytvořte tak trasu události.

## <a name="filter-events"></a>Události filtru

Jak je popsáno výše, trasy mají pole **filtru** . Pokud je hodnota filtru v trase `false` , nebudou do koncového bodu odesílány žádné události. 

Po povolení minimálního filtru `true` koncových bodů dostane z digitálních vláken Azure nejrůznější události:
* Telemetrie aktivovaná pomocí [digitálních vláken](concepts-twins-graph.md) pomocí rozhraní API služby Azure Digital Service
* Dvojitá oznámení o změně vlastností, která se aktivují při změnách vlastností u všech vláken v instanci digitálních vláken Azure
* Události životního cyklu aktivované při vytváření nebo odstraňování vazeb
* Přidané nebo odstraněné události změny modelu aktivované při přidání nebo odstranění [modelů](concepts-models.md) nakonfigurovaných v instanci digitálních vláken Azure

Můžete omezit typy odesílaných událostí tak, že definujete konkrétnější filtr.

Pokud chcete přidat filtr událostí při vytváření trasy události, použijte část _Přidání filtru tras událostí_ na stránce *vytvořit trasu* události. 

Můžete buď vybrat některé z běžných možností filtru Basic, nebo použít rozšířené možnosti filtru k psaní vlastních filtrů.

#### <a name="use-the-basic-filters"></a>Použít základní filtry

Chcete-li použít základní filtry, rozbalte možnost _typy událostí_ a zaškrtněte políčka odpovídající událostem, které chcete odeslat do koncového bodu. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-1.png" alt-text="Snímek obrazovky s panelem hledání Azure Portal.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Tím se automaticky vyplní textové pole filtru textem vybraného filtru:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-2.png" alt-text="Snímek obrazovky s panelem hledání Azure Portal.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="use-the-advanced-filters"></a>Použití rozšířených filtrů

Alternativně můžete použít možnost pokročilého filtru a napsat vlastní filtry.

Chcete-li vytvořit trasu události s možnostmi pokročilého filtru, přepněte přepínač pro _Rozšířený editor_ , aby jej bylo možné povolit. Do pole *filtru* pak můžete napsat vlastní filtry událostí:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-advanced.png" alt-text="Snímek obrazovky s panelem hledání Azure Portal.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Tady jsou podporované filtry tras. Podrobnosti ve sloupci *schéma textu schématu* jsou text, který lze zadat do pole filtru.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Další kroky

Přečtěte si o různých typech zpráv o událostech, které můžete získat:
* [*Postupy: interpretace dat událostí*](how-to-interpret-event-data.md)
