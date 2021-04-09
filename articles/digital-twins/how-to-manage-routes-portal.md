---
title: Správa koncových bodů a tras (portál)
titleSuffix: Azure Digital Twins
description: Přečtěte si, jak nastavit a spravovat koncové body a trasy událostí pro data digitálních vláken Azure pomocí Azure Portal.
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 31f960b39e771e7bfbf67c6e52c5da8e1fc6e0ec
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932450"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-portal"></a>Správa koncových bodů a tras v Azure Digital provlákna (portál)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

V digitálních prostředcích Azure můžete směrovat [oznámení událostí](how-to-interpret-event-data.md) na služby pro příjem dat nebo připojené výpočetní prostředky. K tomu je potřeba nejprve nastavit **koncové body**, které mohou události přijímat. Pak můžete vytvořit [**trasy událostí**](concepts-route-events.md) , které určují, které události generované pomocí digitálních vláken Azure jsou doručeny do těch koncových bodů.

Tento článek vás provede procesem vytvoření koncových bodů a tras pomocí [Azure Portal](https://portal.azure.com).

Alternativně můžete také spravovat koncové body a trasy pomocí [rozhraní API pro směrování událostí](/rest/api/digital-twins/dataplane/eventroutes), sady [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis)nebo rozhraní příkazového [řádku Azure Digital revlákens CLI](how-to-use-cli.md). Verzi tohoto článku, která používá tyto mechanismy místo portálu, najdete v tématu [*How to: Manage Endpoints and Routes (API and CLI)*](how-to-manage-routes-apis-cli.md).

## <a name="prerequisites"></a>Požadavky

* Budete potřebovat **účet Azure** (můžete [si ho nastavit zdarma).](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* V předplatném Azure budete potřebovat **instanci digitálního vlákna Azure** . Pokud instanci již nemáte, můžete ji vytvořit pomocí kroků v tématu [*Postupy: nastavení instance a ověřování*](how-to-set-up-instance-portal.md). Použijte následující hodnoty z instalačního programu užitečné pro pozdější použití v tomto článku:
    - Název instance
    - Skupina prostředků

Tyto podrobnosti můžete najít v [Azure Portal](https://portal.azure.com) po nastavení instance. Přihlaste se na portál a na panelu hledání na portálu vyhledejte název vaší instance.
 
:::image type="content" source="media/how-to-manage-routes-portal/search-field-portal.png" alt-text="Snímek obrazovky s panelem hledání Azure Portal." lightbox="media/how-to-manage-routes-portal/search-field-portal.png":::

Výběrem vaší instance z výsledků zobrazíte tyto podrobnosti v přehledu vaší instance:

:::image type="content" source="media/how-to-manage-routes-portal/instance-details.png" alt-text="Snímek obrazovky se stránkou s přehledem instance digitálních vláken Azure Název a skupina prostředků jsou zvýrazněné.":::

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Vytvoření koncového bodu pro digitální vlákna Azure

Toto jsou podporované typy koncových bodů, které můžete vytvořit pro instanci:
* [Event Grid](../event-grid/overview.md) 
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Další informace o různých typech koncových bodů najdete v tématu [*Výběr mezi službou zasílání zpráv Azure*](../event-grid/compare-messaging-services.md).

V této části se dozvíte, jak vytvořit jeden z těchto koncových bodů v [Azure Portal](https://portal.azure.com).

[!INCLUDE [digital-twins-endpoint-resources.md](../../includes/digital-twins-endpoint-resources.md)]

### <a name="create-the-endpoint"></a>Vytvoření koncového bodu 

Jakmile vytvoříte prostředky koncového bodu, můžete je použít pro koncový bod digitálních vláken Azure. Pokud chcete vytvořit nový koncový bod, na stránce vaší instance v [Azure Portalu](https://portal.azure.com) přejít na stránku vaší instance (můžete najít instanci zadáním jejího názvu do panelu hledání na portálu).

1. V nabídce instance vyberte _koncové body_. Pak na stránce *koncové body* , které následuje, vyberte *+ vytvořit koncový bod*. Tím se otevře stránka *vytvořit koncový bod* , kde budete vyplňovat pole v následujících krocích.

    :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-grid.png" alt-text="Snímek obrazovky s vytvářením koncového bodu typu Event Grid." lightbox="media/how-to-manage-routes-portal/create-endpoint-event-grid.png":::

1. Zadejte **název** koncového bodu a vyberte **Typ koncového bodu**.

1. Dokončete další podrobnosti, které jsou požadovány pro váš typ koncového bodu, včetně vašeho předplatného a [výše](#prerequisite-create-endpoint-resources)popsaných prostředků koncového bodu.
    1. V případě centra událostí a Service Bus jenom koncových bodů, musíte vybrat **typ ověřování**. Ověřování pomocí klíčů můžete použít s předem vytvořeným autorizačním pravidlem nebo ověřováním na základě identity, pokud budete používat koncový bod se [spravovanou identitou](concepts-security.md#managed-identity-for-accessing-other-resources-preview) pro instanci digitálních vláken Azure. 

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png" alt-text="Snímek obrazovky s vytvářením koncového bodu typu centrum událostí" lightbox="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

1. Vytvoření koncového bodu dokončíte tak, že vyberete _Uložit_.

>[!IMPORTANT]
> Aby bylo možné úspěšně použít ověřování na základě identity pro váš koncový bod, bude nutné vytvořit spravovanou identitu pro vaši instanci pomocí kroků v tématu [*Postupy: povolení spravované identity pro události směrování (Preview)*](./how-to-enable-managed-identities-portal.md).

Po vytvoření koncového bodu můžete ověřit, že byl koncový bod úspěšně vytvořen, zaškrtnutím ikony oznámení na horním panelu Azure Portal: 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-notifications.png" alt-text="Snímek obrazovky s oznámením, že se má ověřit vytvoření koncového bodu Na horním panelu portálu se vybere ikona ve tvaru zvonku a zobrazí se oznámení o tom, že koncový bod ADT-eh-Endpoint byl úspěšně vytvořen.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Pokud se vytvoření koncového bodu nepovede, Sledujte chybovou zprávu a zkuste to znovu za několik minut.

Koncový bod, který byl vytvořen zpátky, můžete také zobrazit na stránce *koncových bodů* pro instanci digitálních vláken Azure.

Teď je služba Event Grid, centrum událostí nebo Service Bus k dispozici jako koncový bod uvnitř digitálních vláken Azure pod názvem, který jste zvolili pro koncový bod. Tento název obvykle použijete jako cíl **trasy události**, kterou vytvoříte [později v tomto článku](#create-an-event-route).

### <a name="create-an-endpoint-with-dead-lettering"></a>Vytvoření koncového bodu s nedoručenými písmeny

Když koncový bod nemůže doručovat událost v určitém časovém období nebo po pokusu o doručení události v určitém počtu opakování, může odeslat nedoručenou událost do účtu úložiště. Tento proces se označuje jako **nedoručené**.

Aby bylo možné vytvořit koncový bod s povolenou výjimkou, je nutné použít příkazy rozhraní [příkazového řádku](how-to-use-cli.md) nebo [rozhraní API plochy ovládacího prvku](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) pro vytvoření koncového bodu, nikoli Azure Portal.

Pokyny k tomu, jak to provést pomocí těchto nástrojů, najdete v tématu [*rozhraní API a verze CLI*](how-to-manage-routes-apis-cli.md#create-an-endpoint-with-dead-lettering) tohoto článku.

## <a name="create-an-event-route"></a>Vytvoření trasy události

Aby bylo možné ve skutečnosti odesílat data z digitálních vláken Azure do koncového bodu, bude nutné definovat **trasu události**. Tyto trasy umožňují vývojářům nasměrovat tok událostí v celém systému a na navazující služby. Přečtěte si další informace o trasách událostí v tématu [*Koncepty: směrování událostí digitálních vláken Azure*](concepts-route-events.md).

**Předpoklad**: před přechodem na vytvoření trasy je nutné vytvořit koncové body, jak je popsáno výše v tomto článku. Po dokončení nastavení koncových bodů můžete pokračovat v vytváření směrování událostí.

>[!NOTE]
>Pokud jste své koncové body v poslední době nasadili, ověřte, že se jejich nasazení dokončilo, **než** se pokusíte použít pro novou trasu události. Pokud nemůžete nastavit trasu, protože koncové body nejsou připravené, počkejte pár minut a zkuste to znovu.

### <a name="creation-steps-with-the-azure-portal"></a>Postup vytvoření pomocí Azure Portal

Definice trasy události obsahuje tyto prvky:
* Název trasy, kterou chcete použít
* Název koncového bodu, který chcete použít
* Filtr definující události posílané do koncového bodu
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

:::image type="content" source="media/how-to-manage-routes-portal/create-event-route-no-filter.png" alt-text="Snímek obrazovky s vytvářením trasy události pro vaši instanci." lightbox="media/how-to-manage-routes-portal/create-event-route-no-filter.png":::

Po dokončení stiskněte tlačítko _Uložit_ a vytvořte tak trasu události.

## <a name="filter-events"></a>Události filtru

Jak je popsáno výše, trasy mají pole **filtru** . Pokud je hodnota filtru v trase `false` , nebudou do koncového bodu odesílány žádné události. 

Po povolení minimálního filtru `true` koncových bodů dostane z digitálních vláken Azure nejrůznější události:
* Telemetrie aktivovaná pomocí [digitálních vláken](concepts-twins-graph.md) pomocí rozhraní API služby Azure Digital Service
* Dvojitá oznámení o změně vlastností, která se aktivují při změnách vlastností u všech vláken v instanci digitálních vláken Azure
* Události životního cyklu aktivované při vytváření nebo odstraňování vazeb

Můžete omezit typy odesílaných událostí tak, že definujete konkrétnější filtr.

Pokud chcete přidat filtr událostí při vytváření trasy události, použijte část _Přidání filtru tras událostí_ na stránce *vytvořit trasu* události. 

Můžete buď vybrat některé z běžných možností filtru Basic, nebo použít rozšířené možnosti filtru k psaní vlastních filtrů.

>[!NOTE]
> Filtry rozlišují **Velká** a malá písmena a musí se shodovat s případem datové části. 
>
> Pro filtry telemetrie to znamená, že velká a malá písmena musí odpovídat velikosti písmen v telemetrii odesílané zařízením, a nemusí nutně znamenat velikost písmen definované v modelu vlákna.

#### <a name="use-the-basic-filters"></a>Použít základní filtry

Chcete-li použít základní filtry, rozbalte možnost _typy událostí_ a zaškrtněte políčka odpovídající událostem, které chcete odeslat do koncového bodu. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-1.png" alt-text="Snímek obrazovky s vytvořením trasy události se základním filtrem Výběr zaškrtávacích políček událostí.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Tím se automaticky vyplní textové pole filtru textem vybraného filtru:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-2.png" alt-text="Snímek obrazovky s vytvořením trasy události se základním filtrem Zobrazuje se automaticky vyplněný text filtru po výběru událostí.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="use-the-advanced-filters"></a>Použití rozšířených filtrů

Alternativně můžete použít možnost pokročilého filtru a napsat vlastní filtry.

Chcete-li vytvořit trasu události s možnostmi pokročilého filtru, přepněte přepínač pro _Rozšířený editor_ , aby jej bylo možné povolit. Do pole *filtru* pak můžete napsat vlastní filtry událostí:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-advanced.png" alt-text="Snímek obrazovky s vytvořením trasy události s pokročilým filtrem":::
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