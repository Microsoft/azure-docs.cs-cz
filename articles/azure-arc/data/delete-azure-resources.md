---
title: Odstranění prostředků z Azure
description: Odstranění prostředků z Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ce46b7afe7344fabde03805dc2a0977411be5811
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107716074"
---
# <a name="delete-resources-from-azure"></a>Odstranění prostředků z Azure

Tento článek popisuje, jak odstranit prostředky z Azure.

> [!WARNING]
> Při odstraňování prostředků, jak je popsáno v tomto článku, jsou tyto akce nevratné.

V režimu nepřímých připojení se odstranění instance z Kubernetes neodebere z Azure a odstranění instance z Azure ji neodebere z Kubernetes. V případě režimu nepřímých připojení je odstranění prostředku proces dvou kroků a v budoucnu bude vylepšený. Kubernetes bude zdrojem pravdy a portál bude aktualizován tak, aby odrážel.

V některých případech může být nutné ručně odstranit prostředky datových služeb s podporou ARC Azure v Azure.  Tyto prostředky můžete odstranit pomocí kterékoli z následujících možností.

- [Odstranění prostředků z Azure](#delete-resources-from-azure)
  - [Odstranit celou skupinu prostředků](#delete-an-entire-resource-group)
  - [Odstranění konkrétních prostředků ve skupině prostředků](#delete-specific-resources-in-the-resource-group)
  - [Odstranění prostředků pomocí Azure CLI](#delete-resources-using-the-azure-cli)
    - [Odstranění prostředků spravované instance SQL pomocí rozhraní příkazového řádku Azure](#delete-sql-managed-instance-resources-using-the-azure-cli)
    - [Odstranění prostředků skupiny serverů PostgreSQL s škálovatelným škálováním pomocí Azure CLI](#delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli)
    - [Odstranění prostředků řadiče dat ARC Azure pomocí rozhraní příkazového řádku Azure](#delete-azure-arc-data-controller-resources-using-the-azure-cli)
    - [Odstranění skupiny prostředků pomocí Azure CLI](#delete-a-resource-group-using-the-azure-cli)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-an-entire-resource-group"></a>Odstranit celou skupinu prostředků

Pokud jste používali určitou a vyhrazenou skupinu prostředků pro datové služby s podporou ARC Azure a chcete odstranit *všechno* , co se nachází uvnitř skupiny prostředků, můžete odstranit skupinu prostředků, která odstraní vše, co je v ní obsažená.  

Skupinu prostředků v Azure Portal můžete odstranit následujícím způsobem:

- Přejděte do skupiny prostředků v Azure Portal, kde se vytvořily prostředky datových služeb s podporou ARC Azure.
- Klikněte na tlačítko **Odstranit skupinu prostředků** .
- Potvrďte odstranění zadáním názvu skupiny prostředků a kliknutím na **Odstranit**.

## <a name="delete-specific-resources-in-the-resource-group"></a>Odstranění konkrétních prostředků ve skupině prostředků

Konkrétní prostředky datových služeb s podporou ARC Azure můžete odstranit ve skupině prostředků v Azure Portal následujícím způsobem:

- Přejděte do skupiny prostředků v Azure Portal, kde se vytvořily prostředky datových služeb s podporou ARC Azure.
- Vyberte všechny prostředky, které chcete odstranit.
- Klikněte na tlačítko Odstranit.
- Potvrďte odstranění zadáním Yes a kliknutím na **Odstranit**.

## <a name="delete-resources-using-the-azure-cli"></a>Odstranění prostředků pomocí Azure CLI

Pomocí Azure CLI můžete odstranit konkrétní prostředky datových služeb s podporou ARC Azure.

### <a name="delete-sql-managed-instance-resources-using-the-azure-cli"></a>Odstranění prostředků spravované instance SQL pomocí rozhraní příkazového řádku Azure

Pokud chcete z Azure odstranit prostředky spravované instance SQL pomocí rozhraní příkazového řádku Azure, nahraďte zástupné hodnoty v níže uvedeném příkazu a spusťte ji.

```azurecli
az resource delete --name <sql instance name> --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group <resource group name>

#Example
#az resource delete --name sql1 --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group rg1
```

### <a name="delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli"></a>Odstranění prostředků skupiny serverů PostgreSQL s škálovatelným škálováním pomocí Azure CLI

Pokud chcete z Azure odstranit prostředek skupiny serverů PostgreSQL s použitím rozhraní příkazového řádku Azure CLI, nahraďte zástupné hodnoty v níže uvedeném příkazu a spusťte ho.

```azurecli
az resource delete --name <postgresql instance name> --resource-type Microsoft.AzureArcData/postgresInstances --resource-group <resource group name>

#Example
#az resource delete --name pg1 --resource-type Microsoft.AzureArcData/postgresInstances --resource-group rg1
```

### <a name="delete-azure-arc-data-controller-resources-using-the-azure-cli"></a>Odstranění prostředků řadiče dat ARC Azure pomocí rozhraní příkazového řádku Azure

> [!NOTE]
> Před odstraněním kontroleru dat ARC Azure byste měli odstranit všechny prostředky instance databáze, které spravuje.

Pokud chcete odstranit řadič dat ARC Azure z Azure pomocí rozhraní příkazového řádku Azure, nahraďte zástupné hodnoty v níže uvedeném příkazu a spusťte ho.

```azurecli
az resource delete --name <data controller name> --resource-type Microsoft.AzureArcData/dataControllers --resource-group <resource group name>

#Example
#az resource delete --name dc1 --resource-type Microsoft.AzureArcData/dataControllers --resource-group rg1
```

### <a name="delete-a-resource-group-using-the-azure-cli"></a>Odstranění skupiny prostředků pomocí Azure CLI

Pomocí Azure CLI můžete také [Odstranit skupinu prostředků](../../azure-resource-manager/management/delete-resource-group.md).