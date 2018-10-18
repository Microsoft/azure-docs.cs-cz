---
title: Rychlý start – Vytvoření účtu Azure Media Services pomocí Azure CLI | Microsoft Docs
description: Postup tohoto rychlého startu použijte k vytvoření účtu služby Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: de54571308b737b9160a39ee4ba5d4b2d9f15775
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376529"
---
# <a name="quickstart-create-an-azure-media-services-account"></a>Rychlý start: Vytvoření účtu Azure Media Services

Ať už jste vývojář nebo tvůrce mediálního obsahu, musíte si k ukládání, šifrování, kódování, spravování a streamování mediálního obsahu v Azure vytvořit účet Media Services. Při vytváření účtu Media Services je nutné zadat ID prostředku účtu Azure Storage. Zadaný účet úložiště se připojí k vašemu účtu Media Services. Prostředek účtu úložiště musí být umístěný ve stejné zeměpisné oblasti jako účet Media Services.  

Tento rychlý start popisuje kroky pro vytvoření nového účtu Azure Media Services pomocí Azure CLI.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](http://portal.azure.com) a spusťte **CloudShell**, abyste mohli provést příkazy CLI, jak můžete vidět v dalších krocích.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="set-the-azure-subscription"></a>Nastavení předplatného Azure

V následujícím příkazu uveďte ID předplatného Azure, které chcete pro účet Media Services použít. Když přejdete na [Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) zobrazí se seznam předplatných, ke kterým máte přístup.

```azurecli-interactive
az account set --subscription <mySubscriptionId>
```

## <a name="create-an-azure-resource-group"></a>Vytvoření skupiny prostředků Azure

Následující příkaz vytvoří skupinu prostředků, ve které chcete mít účet Storage a Media Services. Zástupný text *myresourcegroup* nahraďte názvem, který chcete pro skupinu prostředků použít.

```azurecli-interactive
az group create -n <myresourcegroup> -l westus2
```

## <a name="create-an-azure-storage-account"></a>Vytvoření účtu služby Azure Storage

Při vytváření účtu Media Services je nutné zadat ID prostředku účtu Azure Storage. Zadaný účet úložiště se připojí k vašemu účtu Media Services. 

Ke svému účtu Media Services musíte mít přidružený jeden **primární** účet úložiště a libovolný počet **sekundárních** účtů úložiště. Služba Media Services podporuje **účty pro obecné účely verze 2** a **účty pro obecné účely verze 1**. Účty Blob Storage nejsou povolené jako **primární**. Další informace o účtech úložiště najdete v [přehledu účtu Azure Storage](../../storage/common/storage-account-overview.md). 

Následující příkaz vytvoří účet Storage, který se přidruží k účtu Media Services (primárnímu). V níže uvedeném skriptu nahraďte zástupný text *storageaccountforams*. Název účtu musí mít méně než 24 znaků.

```azurecli-interactive
az storage account create -n <storageaccountforams> -g <myresourcegroup>
```

## <a name="create-an-azure-media-services-account"></a>Vytvoření účtu Azure Media Services

Níže najdete příkazy Azure CLI, které vytvoří nový účet Media Services. Musíte jen nahradit následující zvýrazněné hodnoty:

* *myamsaccountname*
* *myresourcegroup*
* *storageaccountforams*

```azurecli-interactive
az ams create -n <myamsaccountname> -g <myresourcegroup> --storage-account <storageaccountforams>
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud ze skupiny prostředků dále nepotřebujete žádné prostředky, včetně účtu služby Media Services, který jste vytvořili v rámci tohoto rychlého startu, odstraňte tuto skupinu prostředků.

V nástroji **CloudShell** spusťte následující příkaz:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Streamování souboru](stream-files-dotnet-quickstart.md)
