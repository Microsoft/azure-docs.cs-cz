---
title: Vytvoření účtu Azure Media Services
description: Tento kurz vás provede jednotlivými kroky při vytváření účtu Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/15/2020
ms.author: juliako
ms.custom: devx-track-azurecli
ms.openlocfilehash: b48aa215b621ab617ef3ff99ce66d972059a4ffc
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498751"
---
# <a name="create-a-media-services-account"></a>Vytvoření účtu Media Services

Pokud chcete začít šifrovat, kódovat, analyzovat, spravovat a streamovat mediální obsah v Azure, musíte vytvořit účet Media Services. Účet Media Services musí být přidružený k jednomu nebo více účtům úložiště.

> [!NOTE]
> Účet Media Services a všechny přidružené účty úložiště musí být ve stejném předplatném Azure. Důrazně doporučujeme používat účty úložiště ve stejném umístění jako účet Media Services, abyste se vyhnuli další latenci a nákladům na výchozí přenos dat.

Tento článek popisuje kroky pro vytvoření nového účtu Azure Media Services. Vyberte z následujících karet.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Azure Portal poskytuje způsob, jak rychle vytvořit Azure Media Services účet. Účet můžete použít pro přístup ke službě Media Services, která vám umožní ukládat, šifrovat, kódovat, spravovat a streamovat mediální obsah v Azure.

V současné době můžete použít [Azure Portal](https://portal.azure.com/) k těmto akcím:

* Správa událostí Media Services V3 [Live](live-events-outputs-concept.md) 
* Zobrazit (Nespravovat) 3 [prostředky](assets-concept.md)V3 
* [Získejte informace o přístupu k rozhraním API](./access-api-howto.md). 

Pro všechny ostatní úlohy správy (například [transformace a úlohy](transforms-jobs-concept.md) a [Ochrana obsahu](content-protection-overview.md)) použijte [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref)nebo jednu z podporovaných [sad SDK](media-services-apis-overview.md#sdks).

V tomto článku se dozvíte, jak vytvořit účet Media Services pomocí Azure Portal.

### <a name="create-a-media-services-account"></a>Vytvoření účtu Media Services

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

## <a name="use-the-azure-cli"></a>Použití Azure CLI

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="set-the-azure-subscription"></a>Nastavení předplatného Azure

V následujícím příkazu uveďte ID předplatného Azure, které chcete pro účet Media Services použít. Když přejdete na [Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) zobrazí se seznam předplatných, ke kterým máte přístup.

```azurecli
az account set --subscription mySubscriptionId
```

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí následujícího příkazu. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky, jako například účty Azure Media Services a přidružené účty Storage.

Můžete nahradit `amsResourceGroup` svou hodnotou.

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Při vytváření účtu Media Services je nutné zadat název prostředku účtu Azure Storage. Zadaný účet úložiště se připojí k vašemu účtu Media Services. Další informace o tom, jak se ve službě Media Services používají účty úložiště, najdete v tématu [Účty Storage](storage-account-concept.md).

Ke svému účtu Media Services musíte mít přidružený jeden **primární** účet úložiště a můžete mít libovolný počet **sekundárních** účtů úložiště. Služba Media Services podporuje účty **General-purpose v2** (GPv2) nebo **General-purpose v1** (GPv1). Účty jen pro objekty blob nejsou povolené jako **primární**. Další informace o účtech úložiště najdete v článku [Možnosti účtů Azure Storage](../../storage/common/storage-account-overview.md). 

V tomto příkladu vytvoříme účet Pro obecné účely v2 (Standard LRS). Pokud chcete experimentovat s účty úložiště, použijte `--sku Standard_LRS` . Když však vybíráte SKU pro produkci, měli byste zvážit, `--sku Standard_RAGRS` , což zajišťuje geografickou replikaci pro kontinuitu podnikových prostředí. Další informace najdete v tématu [účty úložiště](/cli/azure/storage/account?view=azure-cli-latest).
 
Následující příkaz vytvoří účet Storage, který se přidruží k účtu Media Services. V níže uvedeném skriptu můžete nahradit `storageaccountforams` vaší hodnotou. `amsResourceGroup`musí odpovídat hodnotě, kterou jste zadali pro skupinu prostředků v předchozím kroku. Název účtu úložiště musí mít délku menší než 24.

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Vytvoření účtu Media Services

Následující příkaz Azure CLI vytvoří nový účet Media Services. Můžete nahradit následující hodnoty: `amsaccount` `storageaccountforams` (musí odpovídat hodnotě, kterou jste zadali pro účet úložiště), a `amsResourceGroup` (musí se shodovat s hodnotou, kterou jste zadali pro skupinu prostředků).  

```azurecli
az ams account create --name amsaccount \
   -g amsResourceGroup --storage-account storageaccountforams \
   -l westus2 
```

### <a name="see-also"></a>Viz také

* [Azure CLI](/cli/azure/ams?view=azure-cli-latest)
* [Připojit sekundární úložiště k účtu Media Services](/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

---

## <a name="next-steps"></a>Další kroky

[Streamování souboru](stream-files-dotnet-quickstart.md)
