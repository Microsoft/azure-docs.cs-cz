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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774221"
---
Než začnete s konfigurací, nainstalujte a importujte požadované moduly. K instalaci modulů do PowerShellu budete potřebovat oprávnění správce.

1. Instalace a import AZ Module
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Instalace a importující modul AZ. peering
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Pomocí níže uvedeného příkazu ověřte, že jsou moduly naimportovány správně.
    ```powershell
    Get-Module
    ```
1. Přihlaste se ke svému účtu Azure pomocí následujícího příkazu.
    ```powershell
    Connect-AzAccount
    ```
1. Ověřte odběry účtu a vyberte předplatné, ve kterém chcete vytvořit partnerský vztah.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. Pokud ještě nemáte skupinu prostředků, musíte ji před vytvořením partnerského vztahu vytvořit. Je to tak, že spustíte následující příkaz:

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> Pokud jste ještě nepřiřadili ASN a předplatné, postupujte podle kroků pro [přidružení partnerského čísla ASN](../howto-subscription-association-powershell.md). To je nutné pro vyžádání partnerského vztahu.

> [!NOTE]
> Umístění skupiny prostředků je nezávislé na umístění, kde se rozhodnete nastavit partnerský vztah.
&nbsp;
