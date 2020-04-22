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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678502"
---
Než začnete s konfigurací, nainstalujte a importujte požadované moduly. K instalaci modulů v prostředí PowerShell potřebujete oprávnění správce.

1. Nainstalujte a importujte modul Az.
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Nainstalujte a importujte modul Az.Peering.
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Pomocí tohoto příkazu ověřte, zda jsou moduly importovány správně:
    ```powershell
    Get-Module
    ```
1. Přihlaste se ke svému účtu Azure pomocí tohoto příkazu:
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
> Pokud jste ještě nepřidružili asn a předplatné, postupujte podle pokynů v [přidružené masce ASN](../howto-subscription-association-powershell.md). Tato akce je nutné požádat o partnerský vztah.

> [!NOTE]
> Umístění skupiny prostředků je nezávislé na umístění, kde se rozhodnete nastavit partnerský vztah.
&nbsp;
