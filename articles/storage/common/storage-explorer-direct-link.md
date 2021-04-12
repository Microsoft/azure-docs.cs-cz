---
title: Přímý odkaz Průzkumník služby Azure Storage | Microsoft Docs
description: Dokumentace k Průzkumník služby Azure Storage přímý odkaz
services: storage
author: chuye
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: 121df1e1c3ab6d0741d3e4da1144a86938da70ed
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582219"
---
# <a name="azure-storage-explorer-direct-link"></a>Průzkumník služby Azure Storage přímý odkaz

V systému Windows a macOS Průzkumník služby Storage podporuje identifikátory URI s `storageexplorer://` protokolem. Tyto identifikátory URI se označují jako přímé odkazy. Přímý odkaz odkazuje na prostředek Azure Storage v Průzkumník služby Storage. Po Průzkumník služby Storage se otevře přímý odkaz a přejde k prostředku, na který odkazuje. Tento článek popisuje, jak přímé odkazy fungují a jak je můžete použít.

## <a name="how-direct-links-work"></a>Jak fungují přímé odkazy

Průzkumník služby Storage používá stromové zobrazení k vizualizaci prostředků v Azure. Přímý odkaz obsahuje hierarchické informace pro propojený uzel prostředku ve stromové struktuře. Když je následován přímý odkaz, Průzkumník služby Storage se otevře a přijme parametry přímo v odkazu. Průzkumník služby Storage pak pomocí těchto parametrů přejde do odkazovaného prostředku ve stromovém zobrazení.

> [!IMPORTANT]
> Abyste mohli přímý odkaz pracovat, musíte být přihlášeni a mít potřebná oprávnění pro přístup k propojenému prostředku.

## <a name="parameters"></a>Parametry

Průzkumník služby Storage přímý odkaz vždy začíná protokolem `storageexplorer://` . Následující tabulka vysvětluje jednotlivé možné parametry v přímém propojení.

Parametr | Popis
:---------| :---------
`v`         | Verze protokolu přímého propojení.
`accountid` | ID prostředku Azure Resource Manager účtu úložiště pro propojený prostředek. Pokud je propojeným prostředkem účet úložiště, toto ID bude Azure Resource Manager ID prostředku tohoto účtu úložiště. V opačném případě toto ID bude Azure Resource Manager ID prostředku účtu úložiště, ke kterému propojený prostředek patří.
`resourcetype` | Nepovinný parametr. Používá se pouze v případě, že propojený prostředek je kontejner objektů blob, sdílená složka, fronta nebo tabulka. Musí to být jedna z těchto položek: "Azure. BlobContainer", "Azure.. Share", "Azure. Queue", "Azure. a Shared".
`resourcename` | Nepovinný parametr. Používá se pouze v případě, že propojený prostředek je kontejner objektů blob, sdílená složka, fronta nebo tabulka. Název propojeného prostředku.

Tady je ukázkový přímý odkaz na kontejner objektů BLOB. 
`storageexplorer://v=1&accountid=/subscriptions/the_subscription_id/resourceGroups/the_resource_group_name/providers/Microsoft.Storage/storageAccounts/the_storage_account_name&subscriptionid=the_subscription_id&resourcetype=Azure.BlobContainer&resourcename=the_blob_container_name`

## <a name="get-a-direct-link-from-storage-explorer"></a>Získat přímý odkaz z Průzkumník služby Storage

Pomocí Průzkumník služby Storage můžete získat přímý odkaz na prostředek. Otevřete kontextovou nabídku uzlu pro prostředek ve stromovém zobrazení. Pak pomocí akce kopírovat přímý odkaz zkopírujte přímý odkaz na schránku.

## <a name="direct-link-limitations"></a>Omezení přímého propojení

Přímé odkazy se podporují jenom pro prostředky v uzlech předplatného. Kromě toho jsou podporovány pouze následující typy prostředků:

- Účty úložiště
- Kontejnery objektů blob
- Fronty
- Sdílené složky
- Tabulky
