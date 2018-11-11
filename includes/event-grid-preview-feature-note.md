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
ms.openlocfilehash: 85a1579e32b4c216f234f77c76316bedeaea77b0
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285811"
---
Tato funkce je ve verzi Preview. Jeho použití, je nutné nainstalovat rozšíření ve verzi preview nebo modulu.

### <a name="install-extension-for-azure-cli"></a>Instalace rozšíření pro rozhraní příkazového řádku Azure

Rozhraní příkazového řádku Azure, je nutné [rozšíření služby Event Grid](/cli/azure/azure-cli-extensions-list).

V [cloud Shell](/azure/cloud-shell/quickstart):

* Pokud jste dříve nainstalovali rozšíření, aktualizujte `az extension update -n eventgrid`
* Pokud jste nenainstalovali rozšíření dříve, nainstalujte `az extension add -n eventgrid`

Pro místní instalaci:

1. Odinstalujte Azure CLI místně.
1. Nainstalujte [nejnovější verzi](/cli/azure/install-azure-cli) příkazového řádku Azure.
1. Spusťte okno příkazového řádku.
1. Odinstalujte předchozí verze rozšíření `az extension remove -n eventgrid`
1. Instalace rozšíření `az extension add -n eventgrid`

### <a name="install-module-for-powershell"></a>Instalace modulu prostředí PowerShell

Pro prostředí PowerShell, je nutné [AzureRM.EventGrid modulu](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview).

V [cloud Shell](/azure/cloud-shell/quickstart-powershell):

* Instalace modulu `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Pro místní instalaci:

1. Otevřete konzolu Powershellu jako správce
1. Instalace modulu `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Pokud `-AllowPrerelease` parametru není k dispozici, použijte následující postup:

1. Spustit `Install-Module PowerShellGet -Force`
1. Spustit `Update-Module PowerShellGet`
1. Zavřete konzolu Powershellu
1. Znovu spusťte PowerShell jako správce
1. Instalace modulu `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`
