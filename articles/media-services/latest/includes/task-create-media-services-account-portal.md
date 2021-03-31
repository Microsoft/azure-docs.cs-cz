---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/04/2020
ms.author: inhenkel
ms.custom: portal
ms.openlocfilehash: 72857564a6b195353e7bf2a27c8369004d52d3f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95971316"
---
<!-- Use the portal to create a media services account. -->

## <a name="create-a-media-services-account"></a>Vytvoření účtu Media Services

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Klikněte na **+ vytvořit prostředek**  >  **média**  >  **Media Services**.
1. V části **Vytvoření účtu Media Services** zadejte požadované hodnoty.

    | Název | Description |
    | ---|---|
    |**Název účtu**|Zadejte název nového účtu Media Services. Název účtu Media Services musí obsahovat jenom malá písmena a číslice, nesmí obsahovat mezery a musí mít délku 3 až 24 znaků.|
    |**Předplatné**|Pokud máte více než jedno předplatné, vyberte ho ze seznamu předplatných Azure, ke kterým máte přístup.|
    |**Skupina prostředků**|Vyberte nový nebo existující prostředek. Skupina prostředků je kolekce prostředků, které sdílejí životní cyklus, oprávnění a zásady. Další informace najdete [tady](../../../azure-resource-manager/management/overview.md#resource-groups).|
    |**Umístění**|Vyberte zeměpisnou oblast, která se bude používat k ukládání médií a záznamů metadat pro váš Media Services účet. Tato oblast se bude používat ke zpracování a streamování vašeho média. V rozevíracím seznamu se vám zobrazí pouze ty oblasti Media Services, které jsou dostupné. |
    |**Účet úložiště**|Vyberte účet úložiště, který bude poskytovat blobové úložiště mediálního obsahu z vašeho účtu Media Services. Můžete vybrat existující účet úložiště ve stejné zeměpisné oblasti jako váš účet Media Services, nebo můžete vytvořit nový účet úložiště. Nový účet úložiště bude vytvořen ve stejné oblasti. Pro názvy účtů úložiště platí stejná pravidla jako pro názvy účtů Media Services.<br/><br/>Musíte mít jeden **primární** účet úložiště a můžete mít k vašemu Media Servicesmu účtu přiřazený libovolný počet **sekundárních** účtů úložiště. Pomocí Azure Portal můžete přidat sekundární účty úložiště. Další informace najdete v tématu [účty Azure Storage s účty Azure Media Services](../storage-account-concept.md).<br/><br/>Účet Media Services a všechny přidružené účty úložiště musí být ve stejném předplatném Azure. Důrazně doporučujeme používat účty úložiště ve stejném umístění jako účet Media Services, abyste se vyhnuli další latenci a nákladům na výchozí přenos dat.|
    |**Rozšířená nastavení**| Můžete vytvořit účet pomocí identity spravované systémem výběrem přepínače **spravovaného systémem** .  Díky tomuto výběru budete moct používat spravované klíče zákazníka nebo Přineste vlastní klíč (BYOK) a Media Services, abyste mohli povolit důvěryhodné úložiště.  Další informace o klíčích spravovaných zákazníkem najdete v tématu [Přineste si vlastní klíč (klíče spravované zákazníky) pomocí Media Services](../concept-use-customer-managed-keys-byok.md). Navíc budou povolené i [spravované identity](../concept-managed-identities.md) .

1. Zaškrtněte **Připnout na řídicí panel**, abyste viděli průběh nasazení účtu.
1. Klikněte na tlačítko **Vytvořit** dole na formuláři.
