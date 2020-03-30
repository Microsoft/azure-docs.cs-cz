---
title: Vytvoření účtu Azure Media Services
description: Tento kurz vás provede kroky vytvoření účtu Azure Media Services.
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
ms.openlocfilehash: 2f1694825319ed8b8682c044e7e2282ed4c43dcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79478796"
---
# <a name="create-a-media-services-account"></a>Vytvoření účtu Media Services

Chcete-li v Azure začít šifrovat, kódovat, analyzovat, spravovat a streamovat mediální obsah, musíte si vytvořit účet Mediálních služeb. Účet Mediálních služeb musí být přidružen k jednomu nebo více účtům úložiště.

> [!NOTE]
> Účet Media Services a všechny přidružené účty úložiště musí být ve stejném předplatném Azure. Důrazně doporučujeme používat účty úložiště ve stejném umístění jako účet Media Services, abyste se vyhnuli další latenci a nákladům na výchozí přenos dat.

Tento článek popisuje kroky pro vytvoření nového účtu Azure Media Services. Vyberte si z následujících karet.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Portál Azure poskytuje způsob, jak rychle vytvořit účet Azure Media Services. Účet můžete použít pro přístup ke službě Media Services, která vám umožní ukládat, šifrovat, kódovat, spravovat a streamovat mediální obsah v Azure.

V současné době můžete na [webu Azure Portal:](https://portal.azure.com/)

* správa mediálních služeb v3 [živé události](live-events-outputs-concept.md), 
* zobrazení (nespravuje) v3 [Aktiva](assets-concept.md), 
* [získat informace o přístupu k api](access-api-portal.md). 

Pro všechny ostatní úlohy správy (například [Transformace a úlohy](transforms-jobs-concept.md) a [ochrana obsahu](content-protection-overview.md)) použijte rozhraní [REST API](https://aka.ms/ams-v3-rest-ref), ROZHRANÍ [CLI](https://aka.ms/ams-v3-cli-ref)nebo jednu z podporovaných [sad SDK](media-services-apis-overview.md#sdks).

Tento článek ukazuje, jak vytvořit účet Mediální služby pomocí portálu Azure.

### <a name="create-a-media-services-account"></a>Vytvoření účtu Media Services

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Klepněte na tlačítko **+Vytvořit prostředek** > **Media** > **Media Services**.
1. V části **Vytvořit účet mediální ch mediálních služeb** zadejte požadované hodnoty.
    
    | Name (Název) | Popis |
    | ---|---|
    |**Název účtu**|Zadejte název nového účtu Služby Media Services. Název účtu Media Services musí obsahovat jenom malá písmena a číslice, nesmí obsahovat mezery a musí mít délku 3 až 24 znaků.|
    |**Předplatné**|Pokud máte více než jedno předplatné, vyberte jedno ze seznamu předplatných Azure, ke kterým máte přístup.|
    |**Skupina prostředků**|Vyberte nový nebo existující prostředek. Skupina prostředků je kolekce prostředků, které sdílejí životní cyklus, oprávnění a zásady. Více se dozvíte [zde](../../azure-resource-manager/management/overview.md#resource-groups).|
    |**Umístění**|Vyberte zeměpisnou oblast, která bude použita k uložení záznamů médií a metadat pro váš účet Mediální služby. Tato oblast se bude používat ke zpracování a streamování vašeho média. V rozevíracím seznamu se vám zobrazí pouze ty oblasti Media Services, které jsou dostupné. |
    |**Účet úložiště**|Vyberte účet úložiště, který zajistí ukládání mediálního obsahu objektem blob z účtu Mediálních služeb. Můžete vybrat existující účet úložiště ve stejné zeměpisné oblasti jako váš účet Media Services, nebo můžete vytvořit nový účet úložiště. Nový účet úložiště bude vytvořen ve stejné oblasti. Pro názvy účtů úložiště platí stejná pravidla jako pro názvy účtů Media Services.<br/><br/>Musíte mít jeden účet **primárního** úložiště a můžete mít libovolný počet účtů **sekundárního** úložiště přidružených k účtu Mediálních služeb. Na webu Azure Portal můžete přidat účty sekundárního úložiště. Další informace najdete v tématu [Účty úložiště Azure s účty Azure Media Services](storage-account-concept.md).<br/><br/>Účet Media Services a všechny přidružené účty úložiště musí být ve stejném předplatném Azure. Důrazně doporučujeme používat účty úložiště ve stejném umístění jako účet Media Services, abyste se vyhnuli další latenci a nákladům na výchozí přenos dat.|
    
1. Zaškrtněte **Připnout na řídicí panel**, abyste viděli průběh nasazení účtu.
1. Klikněte na tlačítko **Vytvořit** dole na formuláři.

    Po vytvoření účtu Media Services se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Chcete-li spustit streamování obsahu a využít [výhod dynamického balení](dynamic-packaging-overview.md) a [dynamického šifrování](content-protection-overview.md), musí být koncový bod streamování, ze kterého chcete streamovat obsah, ve stavu **Spuštěno.** 

## <a name="use-the-azure-cli"></a>Použití Azure CLI

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="set-the-azure-subscription"></a>Nastavení předplatného Azure

V následujícím příkazu uveďte ID předplatného Azure, které chcete pro účet Media Services použít. Když přejdete na [Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) zobrazí se seznam předplatných, ke kterým máte přístup.

```azurecli
az account set --subscription mySubscriptionId
```

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí následujícího příkazu. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky, jako například účty Azure Media Services a přidružené účty Storage.

Můžete nahradit `amsResourceGroup` svou hodnotu.

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Při vytváření účtu Media Services je nutné zadat název prostředku účtu Azure Storage. Zadaný účet úložiště se připojí k vašemu účtu Media Services. Další informace o tom, jak se ve službě Media Services používají účty úložiště, najdete v tématu [Účty Storage](storage-account-concept.md).

Ke svému účtu Media Services musíte mít přidružený jeden **primární** účet úložiště a můžete mít libovolný počet **sekundárních** účtů úložiště. Služba Media Services podporuje účty **General-purpose v2** (GPv2) nebo **General-purpose v1** (GPv1). Účty jen pro objekty blob nejsou povolené jako **primární**. Další informace o účtech úložiště najdete v článku [Možnosti účtů Azure Storage](../../storage/common/storage-account-options.md). 

V tomto příkladu vytvoříme účet General Purpose v2, Standard LRS. Pokud chcete experimentovat s účty `--sku Standard_LRS`úložiště, použijte . Však při výběru skladové položky `--sku Standard_RAGRS`pro produkční prostředí byste měli zvážit, , který poskytuje geografické replikace pro kontinuitu provozu. Další informace naleznete v [tématu účty úložiště](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest).
 
Následující příkaz vytvoří účet Storage, který se přidruží k účtu Media Services. V níže uvedeném skriptu můžete nahradit `storageaccountforams` vaší hodnotou. `amsResourceGroup`musí odpovídat hodnotě, kterou jste zadali pro skupinu prostředků v předchozím kroku. Název účtu úložiště musí mít délku menší než 24.

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Vytvoření účtu Media Services

Následující příkaz Azure CLI vytvoří nový účet Media Services. Můžete nahradit následující hodnoty: `amsaccount` `storageaccountforams` (musí odpovídat hodnotě, kterou `amsResourceGroup` jste zadali pro váš účet úložiště) a (musí odpovídat hodnotě, kterou jste zadali pro skupinu prostředků).  

```azurecli
az ams account create --name amsaccount \
   -g amsResourceGroup --storage-account storageaccountforams \
   -l westus2 
```

### <a name="see-also"></a>Viz také

* [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [Připojení sekundárního úložiště k účtu Mediálních služeb](https://docs.microsoft.com/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

---

## <a name="next-steps"></a>Další kroky

[Streamování souboru](stream-files-dotnet-quickstart.md)
