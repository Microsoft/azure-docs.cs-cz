---
title: Vytvoření účtu Azure Media Services
description: Tento kurz vás provede jednotlivými kroky při vytváření účtu Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 49cac230363750e481e165712bf4f619e5cba7ae
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92017841"
---
# <a name="create-a-media-services-account"></a>Vytvoření účtu Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Pokud chcete začít šifrovat, kódovat, analyzovat, spravovat a streamovat mediální obsah v Azure, musíte vytvořit účet Media Services. Účet Media Services musí být přidružený k jednomu nebo více účtům úložiště. Tento článek popisuje kroky pro vytvoření nového účtu Azure Media Services.

> [!NOTE]
> Účet Media Services a všechny přidružené účty úložiště musí být ve stejném předplatném Azure. Důrazně doporučujeme používat účty úložiště ve stejném umístění jako účet Media Services, abyste se vyhnuli další latenci a nákladům na výchozí přenos dat.

 K vytvoření účtu Media Services můžete použít buď Azure Portal, nebo rozhraní příkazového řádku. Vyberte kartu pro metodu, kterou chcete použít.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="portal"></a>[Azure Portal](#tab/portal/)

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

<!-- Move this section. This section should be moved to conceptual.  It doesn't belong in task based -->
Azure Portal poskytuje způsob, jak rychle vytvořit Azure Media Services účet. Účet můžete použít pro přístup ke službě Media Services, která vám umožní ukládat, šifrovat, kódovat, spravovat a streamovat mediální obsah v Azure.

V současné době můžete použít [Azure Portal](https://portal.azure.com/) k těmto akcím:

* Správa událostí Media Services V3 [Live](live-events-outputs-concept.md) 
* Zobrazit (Nespravovat) 3 [prostředky](assets-concept.md)V3 
* [Získejte informace o přístupu k rozhraním API](./access-api-howto.md). 

Pro všechny ostatní úlohy správy (například [transformace a úlohy](transforms-jobs-concept.md) a [Ochrana obsahu](content-protection-overview.md)) použijte [REST API](/rest/api/media/accountfilters), [CLI](/cli/azure/ams)nebo jednu z podporovaných [sad SDK](media-services-apis-overview.md#sdks).
<!-- Move this section. This section should be moved to conceptual.  It doesn't belong in task based -->

### <a name="use-the-azure-portal-to-create-a-media-services-account"></a>Vytvoření účtu Media Services pomocí Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Klikněte na **+ vytvořit prostředek**  >  **média**  >  **Media Services**.
1. V části **Vytvoření účtu Media Services** zadejte požadované hodnoty.

    | Název | Popis |
    | ---|---|
    |**Název účtu**|Zadejte název nového účtu Media Services. Název účtu Media Services musí obsahovat jenom malá písmena a číslice, nesmí obsahovat mezery a musí mít délku 3 až 24 znaků.|
    |**Předplatné**|Pokud máte více než jedno předplatné, vyberte ho ze seznamu předplatných Azure, ke kterým máte přístup.|
    |**Skupina prostředků**|Vyberte nový nebo existující prostředek. Skupina prostředků je kolekce prostředků, které sdílejí životní cyklus, oprávnění a zásady. Další informace najdete [tady](../../azure-resource-manager/management/overview.md#resource-groups).|
    |**Umístění**|Vyberte zeměpisnou oblast, která se bude používat k ukládání médií a záznamů metadat pro váš Media Services účet. Tato oblast se bude používat ke zpracování a streamování vašeho média. V rozevíracím seznamu se vám zobrazí pouze ty oblasti Media Services, které jsou dostupné. |
    |**Účet úložiště**|Vyberte účet úložiště, který bude poskytovat blobové úložiště mediálního obsahu z vašeho účtu Media Services. Můžete vybrat existující účet úložiště ve stejné zeměpisné oblasti jako váš účet Media Services, nebo můžete vytvořit nový účet úložiště. Nový účet úložiště bude vytvořen ve stejné oblasti. Pro názvy účtů úložiště platí stejná pravidla jako pro názvy účtů Media Services.<br/><br/>Musíte mít jeden **primární** účet úložiště a můžete mít k vašemu Media Servicesmu účtu přiřazený libovolný počet **sekundárních** účtů úložiště. Pomocí Azure Portal můžete přidat sekundární účty úložiště. Další informace najdete v tématu [účty Azure Storage s účty Azure Media Services](storage-account-concept.md).<br/><br/>Účet Media Services a všechny přidružené účty úložiště musí být ve stejném předplatném Azure. Důrazně doporučujeme používat účty úložiště ve stejném umístění jako účet Media Services, abyste se vyhnuli další latenci a nákladům na výchozí přenos dat.|

1. Zaškrtněte **Připnout na řídicí panel**, abyste viděli průběh nasazení účtu.
1. Klikněte na tlačítko **Vytvořit** dole na formuláři.

    Po vytvoření účtu Media Services se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod [dynamického balení](dynamic-packaging-overview.md) a [dynamického šifrování](content-protection-overview.md), musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **spuštěno** . 

## <a name="cli"></a>[Rozhraní příkazového řádku](#tab/cli/)

## <a name="use-the-azure-cli"></a>Použití Azure CLI

<!-- NOTE: The following are in the includes file and are reused in other How To articles. All task based content should be in the includes folder with the task- prefix prepended to the file name. -->

### <a name="set-the-azure-subscription"></a>Nastavení předplatného Azure

[!INCLUDE [Set the Azure subscription with CLI](./includes/task-set-azure-subscription-cli.md)]

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [Create a resource group with CLI](./includes/task-create-resource-group-cli.md)]

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

[!INCLUDE [Create a storage account with CLI](./includes/task-create-storage-account-cli.md)]

### <a name="create-a-media-services-account"></a>Vytvoření účtu Media Services

[!INCLUDE [Create a Media Services account with CLI](./includes/task-create-media-services-account-cli.md)]

### <a name="see-also"></a>Viz také

* [Azure CLI](/cli/azure/ams?view=azure-cli-latest)
* [Připojit sekundární úložiště k účtu Media Services](/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

---

## <a name="next-steps"></a>Další kroky

[Streamování souboru](stream-files-dotnet-quickstart.md)