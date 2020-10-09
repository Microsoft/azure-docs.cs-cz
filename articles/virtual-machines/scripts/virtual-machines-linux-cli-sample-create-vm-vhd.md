---
title: Ukázkový skript Azure CLI – vytvoření virtuálního počítače s virtuálním pevným diskem
description: Ukázkový skript Azure CLI – Vytvoření virtuálního počítače s využitím virtuálního pevného disku
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/09/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 6f5e30b0d6a072f9a40ae57ebe325461cdfe5bb1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87479605"
---
# <a name="create-a-vm-with-a-virtual-hard-disk"></a>Vytvoření virtuálního počítače s využitím virtuálního pevného disku

Tento příklad vytvoří virtuální počítač s využitím virtuálního pevného disku.
Vytvoří skupinu prostředků, účet úložiště a kontejner a pak vytvoří virtuální počítač nahráním virtuálního pevného disku do kontejneru.
Nahradí veřejný klíč SSH vaším veřejným klíčem, abyste získali přístup k virtuálnímu počítači.

Budete potřebovat spouštěcí virtuální pevný disk. Skript hledá soubor `~/sample.vhd`.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-vhd/create-vm-vhd.sh "Create VM using a VHD")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```azurecli-interactive 
az group delete -n az-cli-vhd
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, virtuálního počítače, skupiny dostupnosti, nástroje pro vyrovnávání zatížení a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az storage account list](/cli/azure/storage/account) | Vypíše účty úložiště. |
| [az storage account check-name](/cli/azure/storage/account) | Zkontroluje platnost názvu účtu úložiště a jestli už neexistuje. |
| [az storage account keys list](/cli/azure/storage/account/keys) | Vypíše klíče pro účty úložiště. |
| [az storage blob exists](/cli/azure/storage/blob) | Zkontroluje, jestli objekt blob existuje. |
| [az storage container create](/cli/azure/storage/container) | Vytvoří kontejner v účtu úložiště. |
| [az storage blob upload](/cli/azure/storage/blob) | Vytvoří v kontejneru objekt blob nahráním virtuálního pevného disku. |
| [az vm list](/cli/azure/vm) | Používá se s parametrem `--query` ke kontrole, jestli už se název virtuálního počítače nepoužívá. | 
| [az vm create](/cli/azure/vm/availability-set) | Vytvoří virtuální počítače. |
| [az vm list-ip-addresses](/cli/azure/vm#az-vm-list-ip-addresses) | Získá IP adresu vytvořeného virtuálního počítače. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
