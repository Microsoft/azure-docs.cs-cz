---
title: Rychlý start – Vytvoření účtu Azure Media Services pomocí CLI 2.0 | Microsoft Docs
description: Postup tohoto rychlého startu použijte k vytvoření účtu služby Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/27/2018
ms.author: juliako
ms.openlocfilehash: f7e5cb28f90466e9366c0a32e2a333e6823b9396
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33779714"
---
# <a name="quickstart-create-an-azure-media-services-account"></a>Rychlý start: Vytvoření účtu Azure Media Services

> [!NOTE]
> Nejnovější verze služby Azure Media Services (2018-03-30) je ve verzi Preview. Tato verze je také označována jako v3. 

Ať už jste vývojář nebo tvůrce mediálního obsahu, musíte si k ukládání, šifrování, kódování, spravování a streamování mediálního obsahu v Azure vytvořit účet Media Services. Při vytváření účtu Media Services je nutné zadat ID prostředku účtu Azure Storage. Zadaný účet úložiště se připojí k vašemu účtu Media Services. Prostředek účtu úložiště musí být umístěný ve stejné zeměpisné oblasti jako účet Media Services.  

Tento rychlý start popisuje kroky pro vytvoření nového účtu Azure Media Services pomocí CLI 2.0.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](http://portal.azure.com) a spusťte **CloudShell**, abyste mohli provést příkazy CLI, jak můžete vidět v dalších krocích.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

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

Ke svému účtu Media Services musíte mít přidružený jeden **primární** účet úložiště a můžete mít libovolný počet **sekundárních** účtů úložiště. Služba Media Services podporuje účty **General-purpose v2** (GPv2) nebo **General-purpose v1** (GPv1). Účty jen pro objekty blob nejsou povolené jako **primární**. Další informace o účtech úložiště najdete v článku [Možnosti účtů Azure Storage](../../storage/common/storage-account-options.md). 

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
