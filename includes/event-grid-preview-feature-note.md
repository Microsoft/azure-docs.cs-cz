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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66814716"
---
Tato funkce je ve verzi Preview. Jeho použití, je nutné nainstalovat rozšíření ve verzi preview nebo modulu.

### <a name="install-extension-for-azure-cli"></a>Instalace rozšíření pro rozhraní příkazového řádku Azure

Rozhraní příkazového řádku Azure, je nutné [rozšíření služby Event Grid](/cli/azure/azure-cli-extensions-list).

V [cloud Shell](/azure/cloud-shell/quickstart):

* Pokud jste dříve nainstalovali rozšíření, aktualizujte `az extension update -n eventgrid`
* Pokud jste nenainstalovali rozšíření dříve, nainstalujte `az extension add -n eventgrid`

Pro místní instalaci:

1. [Instalace Azure CLI](/cli/azure/install-azure-cli). Ujistěte se, že máte nejnovější verzi, podle informací v `az --version`.
1. Odinstalujte předchozí verze rozšíření `az extension remove -n eventgrid`
1. Nainstalujte `eventgrid` rozšíření `az extension add -n eventgrid`

### <a name="install-module-for-powershell"></a>Instalace modulu prostředí PowerShell

Pro prostředí PowerShell, je nutné [AzureRM.EventGrid modulu](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview).

V [cloud Shell](/azure/cloud-shell/quickstart-powershell):

* Instalace modulu `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Pro místní instalaci:

1. Otevřete konzolu Powershellu jako správce
1. Instalace modulu `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Pokud `-AllowPrerelease` parametru není k dispozici, použijte následující postup:

1. Spusťte `Install-Module PowerShellGet -Force`.
1. Spusťte `Update-Module PowerShellGet`.
1. Zavřete konzolu Powershellu
1. Znovu spusťte PowerShell jako správce
1. Instalace modulu `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`
