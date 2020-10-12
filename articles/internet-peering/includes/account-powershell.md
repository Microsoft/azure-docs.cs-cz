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
ms.openlocfilehash: beffb2babefd86c2807e21e9337cba66f42fcfc2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81678502"
---
Než začnete s konfigurací, nainstalujte a importujte požadované moduly. K instalaci modulů v PowerShellu potřebujete oprávnění správce.

1. Nainstalujte a importujte modul AZ Module.
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Nainstalujte a importujte modul AZ. peering.
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Pomocí tohoto příkazu ověřte, že se moduly naimportovaly správně:
    ```powershell
    Get-Module
    ```
1. Přihlaste se ke svému účtu Azure pomocí tohoto příkazu:
    ```powershell
    Connect-AzAccount
    ```
1. Ověřte odběry účtu a vyberte předplatné, ve kterém chcete vytvořit partnerský vztah.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. Pokud ještě nemáte skupinu prostředků, musíte ji před vytvořením partnerského vztahu vytvořit. Můžete to udělat spuštěním následujícího příkazu:

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> Pokud jste ještě nepřiřadili ASN a předplatné, postupujte podle kroků v části [přidružení partnerského čísla ASN](../howto-subscription-association-powershell.md). Tato akce je nutná k vyžádání partnerského vztahu.

> [!NOTE]
> Umístění skupiny prostředků je nezávislé na umístění, kde se rozhodnete nastavit partnerský vztah.
&nbsp;
