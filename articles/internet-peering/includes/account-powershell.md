---
title: zahrnout soubor
titleSuffix: Azure
description: zahrnout soubor
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3a5f7157ef8f3645dd03ec93684238dd8bbc067e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774221"
---
Před zahájením konfigurace nainstalujte a importujte požadované moduly. K instalaci modulů v prostředí PowerShell budete potřebovat oprávnění správce.

1. Instalace a import modulu Az
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Instalace a import modulu Az.Peering
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Ověřte, zda jsou moduly importovány jemně pomocí níže uvedeného příkazu.
    ```powershell
    Get-Module
    ```
1. Přihlaste se ke svému účtu Azure pomocí následujícího příkazu.
    ```powershell
    Connect-AzAccount
    ```
1. Zkontrolujte předplatná pro účet a vyberte předplatné, ve kterém chcete vytvořit partnerský vztah.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. Pokud ještě nemáte skupinu prostředků, musíte ji vytvořit před vytvořením partnerského vztahu. Můžete tak učinit spuštěním následujícího příkazu:

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> Pokud jste ještě nepřidružili asn a předplatné, postupujte podle pokynů pro [přidružený partner peer ASN](../howto-subscription-association-powershell.md). To je nutné požádat o partnerský vztah.

> [!NOTE]
> Umístění skupiny prostředků je nezávislé na umístění, kde se rozhodnete nastavit partnerský vztah.
&nbsp;
