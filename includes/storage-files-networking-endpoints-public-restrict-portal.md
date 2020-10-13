---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6c594dbab51c46c382c21b4d87595cd7322f6036
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84464977"
---
Přejděte do účtu úložiště, pro který chcete omezit veřejný koncový bod na konkrétní virtuální sítě. V obsahu účtu úložiště vyberte **brány firewall a virtuální sítě**. 

V horní části stránky vyberte přepínač **vybrané sítě** . Tím se zruší počet nastavení pro kontrolu omezení veřejného koncového bodu. Kliknutím na **+ Přidat existující virtuální síť** vyberte konkrétní virtuální síť, která by měla mít povolený přístup k účtu úložiště prostřednictvím veřejného koncového bodu. To bude vyžadovat výběr virtuální sítě a podsítě pro tuto virtuální síť. 

Ověřte, zda **mají důvěryhodné služby Microsoftu přístup k tomuto účtu služby** , aby mohly být pro přístup k účtu úložiště důvěryhodné služby Microsoft, například Azure File Sync.

[![Snímek obrazovky okna brány firewall a virtuální sítě s konkrétní virtuální sítí s povoleným přístupem k účtu úložiště prostřednictvím veřejného koncového bodu](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png)](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png#lightbox)