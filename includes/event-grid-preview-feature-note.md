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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66814716"
---
Tato funkce je ve verzi Preview. Chcete-li jej použít, musíte nainstalovat rozšíření náhledu nebo modul.

### <a name="install-extension-for-azure-cli"></a>Rozšíření instalace pro Azure CLI

Pro Azure CLI potřebujete [rozšíření Grid událostí](/cli/azure/azure-cli-extensions-list).

V [CloudShell](/azure/cloud-shell/quickstart):

* Pokud jste rozšíření nainstalovali dříve, aktualizujte ho`az extension update -n eventgrid`
* Pokud jste rozšíření ještě nenainstalovali, nainstalujte ho.`az extension add -n eventgrid`

Pro místní instalaci:

1. [Nainstalujte příkazové příkazové nebo tonové zaokrocené.](/cli/azure/install-azure-cli) Ujistěte se, že máte nejnovější verzi, kontrolou s `az --version`.
1. Odinstalace předchozích verzí rozšíření`az extension remove -n eventgrid`
1. Nainstalujte `eventgrid` rozšíření pomocí`az extension add -n eventgrid`

### <a name="install-module-for-powershell"></a>Instalační modul pro PowerShell

Pro PowerShell potřebujete [modul AzureRM.EventGrid](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview).

V [CloudShell](/azure/cloud-shell/quickstart-powershell):

* Instalace modulu`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Pro místní instalaci:

1. Otevření konzoly PowerShell u správce
1. Instalace modulu`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Pokud `-AllowPrerelease` parametr není k dispozici, použijte následující kroky:

1. Spusťte `Install-Module PowerShellGet -Force`.
1. Spusťte `Update-Module PowerShellGet`.
1. Zavření konzoly PowerShellu
1. Restartování prostředí PowerShell jako správce
1. Instalace modulu`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`
