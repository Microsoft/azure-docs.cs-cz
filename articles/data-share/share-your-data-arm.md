---
title: Sdílení mimo vaši organizaci (šablona ARM) – rychlý Start Azure Data Share
description: Naučte se sdílet data se zákazníky a partnery pomocí sdílené složky Azure a šablony Azure Resource Manager (šablony ARM) v tomto rychlém startu.
author: mumian
ms.author: jgao
ms.service: data-share
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/19/2020
ms.openlocfilehash: 5abe92120c8b822ac86ced90658869a0858d4ff4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92487683"
---
# <a name="quickstart-share-data-using-azure-data-share-and-arm-template"></a>Rychlý Start: sdílení dat pomocí šablony Azure Data Share a ARM

Přečtěte si, jak nastavit novou sdílenou složku Azure z účtu služby Azure Storage pomocí šablony Azure Resource Manager (šablona ARM). A začněte sdílet data se zákazníky a partnery mimo vaši organizaci Azure. Seznam podporovaných úložišť dat najdete [v tématu podporovaná úložiště dat v Azure Data Share](./supported-data-stores.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-data-share-share-storage-account/).

:::code language="json" source="~/quickstart-templates/101-data-share-share-storage-account/azuredeploy.json":::

V šabloně jsou definované následující prostředky:

* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts):
* [Microsoft. Storage/storageAccounts/blobServices/Containers](/azure/templates/microsoft.storage/storageaccounts/blobservices/containers)
* [Microsoft. datashare/Accounts](/azure/templates/microsoft.datashare/accounts)
* [Microsoft. datashare/Accounts/shares](/azure/templates/microsoft.datashare/accounts/shares)
* [Microsoft. Storage/storageAccounts/Providers/roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [Microsoft. datashare/Accounts/Shares/DataSets](/azure/templates/microsoft.datashare/accounts/shares/datasets)
* [Microsoft. datashare/Accounts/Shares/pozvánky](/azure/templates/microsoft.datashare/accounts/shares/invitations)
* [Microsoft. datashare/Accounts/Shares/synchronizationSettings](/azure/templates/microsoft.datashare/accounts/shares/synchronizationsettings)

Šablona provádí následující úlohy:

* Vytvořte účet úložiště a kontejner, který se používá jako zdroj dat sdílení dat.
* Vytvořte účet pro sdílení dat a sdílenou složku.
* Vytvořte přiřazení role pro udělení role čtečky dat objektů BLOB úložiště do prostředku zdrojového sdílení dat. Viz [role a požadavky pro Azure Data Share](./concepts-roles-permissions.md).
* Přidejte datovou sadu do sdílené složky data.
* Přidejte příjemce do sdílené složky dat.
* Povolte plán snímků pro sdílenou složku dat.

Tato šablona se vytvoří pro účely učení. V praxi obvykle máte nějaká data v existujícím účtu úložiště. Před spuštěním šablony nebo skriptu pro vytvoření datové sady byste museli vytvořit přiřazení role. V některých případech se při nasazování šablony může zobrazit následující chybová zpráva:

```plaintext
"Missing permissions for DataShareAcccount on resource 'subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>' (Code: 5006)"
```

Je to proto, že se nasazení pokouší vytvořit datovou sadu, aby bylo možné dokončit přiřazení role Azure. Navzdory chybové zprávě může být nasazení úspěšné. Pořád budete moct projít [kontrolou nasazených prostředků](#review-deployed-resources).

## <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující obrázek pro přihlášení do Azure a otevřete šablonu.

    [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)
1. Vyberte nebo zadejte následující hodnoty:

    * **Předplatné**: vyberte předplatné Azure, které se používá k vytvoření sdílené složky dat a dalších prostředků.
    * **Skupina prostředků**: vyberte **vytvořit novou** a vytvořte novou skupinu prostředků nebo vyberte existující skupinu prostředků.
    * **Umístění:**: Vyberte umístění pro skupinu prostředků.
    * **Název projektu**: zadejte název projektu.  Název projektu se používá pro generování názvů prostředků.  Viz definice proměnných v předchozí šabloně.
    * **umístění**: vyberte umístění pro prostředky.  Pro skupinu prostředků můžete použít stejné umístění.
    * **E-mail pozvánky**: zadejte přihlašovací e-mailovou adresu příjemce pro sdílení dat.  E-mailový alias nefunguje.

    Pro zbývající nastavení použijte výchozí hodnotu.
1. vyberte Souhlasím **s podmínkami a ujednáními uvedenými nahoře** a s vybranou volbou **koupit**.

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Otevřete účet pro sdílení dat, který jste vytvořili.
1. V nabídce vlevo vyberte **Odeslat sdílené složky**.  V seznamu se zobrazí účet úložiště.
1. Vyberte účet úložiště.  V části **Podrobnosti** se zobrazí nastavení synchronizace, jak jste nakonfigurovali v šabloně.

    ![Nastavení synchronizace účtu úložiště Azure Data Share](./media/share-your-data-arm/azure-data-share-storage-account-synchronization-settings.png)
1. V horní části vyberte **pozvánky** . Zobrazí se vám e-mailová adresa, kterou jste zadali při nasazení šablony. **Stav** bude čeká na **vyřízení**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, která odstraní prostředky ve skupině prostředků.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu se naučíte, jak vytvořit sdílenou složku Azure a pozvat příjemce. Další informace o tom, jak může příjemce dat přijmout a přijmout sdílená data, najdete v kurzu [přijímání a přijímání dat](subscribe-to-data-share.md) .
