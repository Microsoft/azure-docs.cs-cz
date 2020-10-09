---
title: zahrnout soubor
description: zahrnout soubor
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 11/06/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: d32beb2d799a60cb9c5be061c39e4ec834da8dcf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "66814716"
---
Tato funkce je ve verzi Preview. Pokud ho chcete použít, musíte nainstalovat rozšíření nebo modul verze Preview.

### <a name="install-extension-for-azure-cli"></a>Nainstalovat rozšíření pro Azure CLI

V případě Azure CLI potřebujete [rozšíření Event Grid](/cli/azure/azure-cli-extensions-list).

V [cloudshellu](/azure/cloud-shell/quickstart):

* Pokud jste dříve nainstalovali rozšíření, aktualizujte ho. `az extension update -n eventgrid`
* Pokud jste rozšíření dříve neinstalovali, nainstalujte ho. `az extension add -n eventgrid`

Pro místní instalaci:

1. [Nainstalujte rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). Ujistěte se, že máte nejnovější verzi, pomocí kontroly `az --version` .
1. Odinstalace předchozích verzí rozšíření `az extension remove -n eventgrid`
1. Nainstalujte `eventgrid` rozšíření pomocí nástroje. `az extension add -n eventgrid`

### <a name="install-module-for-powershell"></a>Nainstalovat modul pro PowerShell

Pro PowerShell potřebujete [modul AzureRM. EventGrid](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview).

V [cloudshellu](/azure/cloud-shell/quickstart-powershell):

* Nainstalovat modul `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Pro místní instalaci:

1. Otevřete konzolu PowerShellu jako správce.
1. Nainstalovat modul `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Pokud `-AllowPrerelease` parametr není k dispozici, použijte následující postup:

1. Spuštěním příkazu `Install-Module PowerShellGet -Force`
1. Spuštěním příkazu `Update-Module PowerShellGet`
1. Zavření konzoly PowerShellu
1. Restartování PowerShellu jako správce
1. Nainstalovat modul `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`
