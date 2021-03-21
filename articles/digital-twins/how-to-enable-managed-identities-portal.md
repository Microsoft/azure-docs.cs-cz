---
title: Povolení spravované identity pro události směrování (Preview) – portál
titleSuffix: Azure Digital Twins
description: Podívejte se, jak povolit identitu přiřazenou systémem pro digitální vlákna Azure a použít ji k přeposílání událostí pomocí Azure Portal.
author: baanders
ms.author: baanders
ms.date: 1/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1211cd306f10fb349bd42568697443ff103a171c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101703081"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview-azure-portal"></a>Povolit spravovanou identitu pro směrování událostí digitálních vláken Azure (Preview): Azure Portal

[!INCLUDE [digital-twins-managed-service-identity-selector.md](../../includes/digital-twins-managed-service-identity-selector.md)]

Tento článek popisuje, jak povolit [identitu přiřazenou systémem pro instanci digitálních vláken Azure](concepts-security.md#managed-identity-for-accessing-other-resources-preview) (aktuálně ve verzi Preview) a používat identitu při předávání událostí do podporovaných cílů, jako je [centrum událostí](../event-hubs/event-hubs-about.md), [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)   umístění a [kontejner Azure Storage](../storage/blobs/storage-blobs-introduction.md).

Tento článek vás provede procesem použití [**Azure Portal**](https://portal.azure.com).

Tady je postup, který je popsaný v tomto článku: 

1. Vytvořte instanci digitálních vláken Azure s identitou systému nebo povolte identitu přiřazenou systémem v existující instanci digitálních vláken Azure. 
1. Přidejte k identitě příslušnou roli nebo role. Například přiřaďte roli **odesilatele dat centra událostí Azure** k identitě, pokud je koncový bod centrem událostí, nebo **Azure Service Bus role odesílatel dat** , pokud je koncový bod Service Bus.
1. Vytvořte koncový bod v digitálních událostech Azure, které můžou k ověřování používat identity přiřazené systémem.

## <a name="enable-system-managed-identities-for-an-instance"></a>Povolení identit spravovaných systémem pro instanci 

Když v instanci digitálního vlákna Azure povolíte identitu přiřazenou systémem, Azure pro ni automaticky vytvoří identitu ve službě [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md). Tuto identitu pak můžete použít k ověření koncových bodů digitálních vláken Azure pro předávání událostí.

V **rámci počátečního nastavení instance** můžete povolit identitu spravované systémem pro instanci digitálního vlákna Azure, nebo **ji později povolit v instanci, která již existuje**.

Kterákoli z těchto metod vytváření poskytne stejné možnosti konfigurace a stejný konečný výsledek pro vaši instanci. Tato část popisuje, jak provést obojí.

### <a name="add-a-system-managed-identity-during-instance-creation"></a>Přidání identity spravované systémem během vytváření instance

V této části se dozvíte, jak povolit systémově spravovanou identitu na instanci digitálních vláken Azure, která se právě vytváří. Tato část se zaměřuje na krok spravované identity procesu vytváření; Úplný návod k vytvoření nové instance digitálního vlákna Azure najdete v tématu [*Postup: nastavení instance a ověřování*](how-to-set-up-instance-portal.md).

Možnost systémově spravovaná identita je umístěna na kartě **Upřesnit** v nastavení instance.

Na této kartě zapněte tuto funkci výběrem možnosti **on** pro **identitu spravovanou systémem** .

:::image type="content" source="media/how-to-enable-managed-identities/create-instance-advanced.png" alt-text="Snímek obrazovky Azure Portal zobrazující kartu Upřesnit v dialogovém okně vytvořit prostředek pro digitální vlákna Azure Existuje zvýraznění kolem názvu karty, možnosti on pro spravovanou identitu systému a navigačních tlačítek (recenze + vytvořit, předchozí, další: Upřesnit).":::

Pak můžete pomocí dolních navigačních tlačítek pokračovat v práci se zbytkem nastavení instance.

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>Přidání identity spravované systémem do existující instance

V této části přidáte identitu spravovanou systémem do instance digitálního vlákna Azure, která už existuje.

1. Nejprve v prohlížeči přejděte na [Azure Portal](https://portal.azure.com) .

1. V panelu hledání na portálu vyhledejte název vaší instance a vyberte ji, abyste zobrazili její podrobnosti.

1. V nabídce na levé straně vyberte **identita (Preview)** .

1. Na této stránce vyberte možnost **zapnout** , pokud chcete tuto funkci zapnout.

1. Stiskněte tlačítko **Uložit** a potvrďte  akci.

    :::image type="content" source="media/how-to-enable-managed-identities/identity-digital-twins.png" alt-text="Snímek obrazovky Azure Portal zobrazující stránku identity (Preview) pro instanci digitálních vláken Azure V nabídce instance digitálního vlákna Azure se zvýrazní název stránky, možnost Zapnuto pro stav, tlačítko Uložit a tlačítko pro potvrzení Ano.":::

Po uložení změny se na této stránce zobrazí další pole pro **ID objektu** a **oprávnění** nové identity.

V případě potřeby můžete zkopírovat **ID objektu** z tohoto místa a použít tlačítko **oprávnění** k zobrazení rolí Azure, které jsou přiřazeny k identitě. Chcete-li nastavit některé role, pokračujte k další části.

## <a name="assign-azure-roles-to-the-identity"></a>Přiřazení rolí Azure k identitě 

Jakmile se pro instanci digitálních vláken Azure vytvoří identita přiřazená systémem, bude nutné přiřadit příslušné role k ověřování pomocí různých typů [koncových bodů](concepts-route-events.md) pro předávání událostí do podporovaných cílů. Tato část popisuje možnosti role a jejich přiřazení k identitě přiřazené systémem.

>[!NOTE]
> Toto je důležitý krok – bez něj nebude mít identita přístup k vašim koncovým bodům a události nebudou doručeny.

### <a name="supported-destinations-and-azure-roles"></a>Podporované cíle a role Azure 

Tady jsou minimální role, které identita potřebuje pro přístup ke koncovému bodu v závislosti na typu cíle. Budou fungovat i role s vyššími oprávněními (jako jsou role vlastníka dat).

| Cíl | Role Azure |
| --- | --- |
| Azure Event Hubs | Odesilatel dat Event Hubs Azure |
| Azure Service Bus | Azure Service Bus odesílatel dat |
| Kontejner úložiště Azure | Přispěvatel dat v objektech blob služby Storage |

Další informace o koncových bodech, trasách a typech cílů podporovaných pro směrování v rámci digitálních vláken Azure najdete v tématu [*Koncepty: trasy událostí*](concepts-route-events.md).

### <a name="assign-the-role"></a>Přiřazení role

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

K přiřazení role k identitě Začněte otevřením [Azure Portal](https://portal.azure.com).

1. Přejděte na prostředek koncového bodu (centrum událostí, Service Bus téma nebo kontejner úložiště), a to tak, že vyhledáte jeho název na panelu hledání na portálu. 
1. V nabídce na levé straně vyberte **řízení přístupu (IAM)** .
1. Kliknutím na tlačítko **+ Přidat** přidejte nové přiřazení role.

    :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-1.png" alt-text="Snímek obrazovky Azure Portal znázorňující stránku řízení přístupu (IAM) pro centrum událostí. Zvýrazní se tlačítko + Přidat." lightbox="media/how-to-enable-managed-identities/add-role-assignment-1.png":::

1. Na následující stránce **Přidat přiřazení role** vyplňte hodnoty:
    * **Role**: vyberte požadovanou roli z rozevírací nabídky.
    * **Přiřadit přístup k**: v části **spravovaná identita přiřazená systémem** vyberte **digitální vlákna**.
    * **Předplatné**: Vyberte předplatné. V rámci vybraného předplatného se zobrazí všechny spravované identity digitálních vláken Azure.
    * **Vyberte**: tady vyberete spravovanou identitu instance digitálního vlákna Azure, ke které se přiřadí role. Název spravované identity se shoduje s názvem instance, proto si vyberte název instance digitálního vlákna Azure. Když vyberete tuto možnost, identita pro instanci se zobrazí v části **Vybrané členy** v dolní části podokna.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-2.png" alt-text="Naplnění uvedených polí do dialogového okna Přidat přiřazení role":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

Až skončíte s zadáváním podrobností, vyberte **Uložit**.

## <a name="create-an-endpoint-with-identity-based-authentication"></a>Vytvoření koncového bodu s ověřováním na základě identity

Po nastavení systému spravované identity pro instanci digitálních vláken Azure a přiřazení odpovídajících rolí můžete vytvořit [koncové body](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) digitálních vláken Azure, které umožňují použití identity pro ověřování. Tato možnost je k dispozici pouze pro centra událostí a koncové body typu Service Bus (není podporováno pro Event Grid).

>[!NOTE]
> Nelze upravit koncový bod, který již byl vytvořen s identitou založenou na klíči, aby bylo možné změnit ověřování na základě identity. Typ ověřování je nutné zvolit při prvním vytvoření koncového bodu.

Postupujte podle [pokynů pro vytvoření koncového bodu digitálních vláken Azure](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins).

Až se dostanete na krok dokončení podrobností požadovaných pro váš typ koncového bodu, nezapomeňte vybrat typ ověřování **založený na identitě** .

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png" alt-text="Snímek obrazovky s vytvářením koncového bodu typu centrum událostí" lightbox="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Dokončete nastavení koncového bodu a vyberte **Uložit**.

## <a name="considerations-for-disabling-system-managed-identities"></a>Předpoklady pro zakázání identit spravovaných systémem

Vzhledem k tomu, že identita je spravovaná odděleně od koncových bodů, které ji používají, je důležité vzít v úvahu důsledky, které mohou mít změny identity nebo její role na koncových bodech v instanci digitálních vláken Azure. Pokud je identita zakázaná nebo je z ní odebrána potřebná role koncového bodu, koncový bod může být nepřístupný a tok událostí bude přerušen.

Pokud chcete pokračovat v používání koncového bodu, který byl nastavený se spravovanou identitou, která je teď zakázaná, musíte koncový bod odstranit a [znovu ho vytvořit](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) s jiným typem ověřování. Po této změně může trvat až hodinu, než se události obnoví v doručení do koncového bodu.

## <a name="next-steps"></a>Další kroky

Další informace o spravovaných identitách v Azure AD: 
* [*Spravované identity pro prostředky Azure*](../active-directory/managed-identities-azure-resources/overview.md)