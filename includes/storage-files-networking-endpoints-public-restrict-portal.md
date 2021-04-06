---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 01/25/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3c76988557bfa338bcfb606f568036422a536c1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98798547"
---
Přejděte do účtu úložiště, pro který chcete omezit veřejný koncový bod na konkrétní virtuální sítě. V obsahu účtu úložiště vyberte **sítě**. 

V horní části stránky vyberte přepínač **vybrané sítě** . Tím se zruší počet nastavení pro kontrolu omezení veřejného koncového bodu. Kliknutím na **+ Přidat existující virtuální síť** vyberte konkrétní virtuální síť, která by měla mít povolený přístup k účtu úložiště prostřednictvím veřejného koncového bodu. To bude vyžadovat výběr virtuální sítě a podsítě pro tuto virtuální síť. 

Ověřte, zda **mají důvěryhodné služby Microsoftu přístup k tomuto účtu služby** , aby mohly být pro přístup k účtu úložiště důvěryhodné služby Microsoft, například synchronizace souborů Azure.

[![Snímek obrazovky okna sítě s konkrétní virtuální sítí s povoleným přístupem k účtu úložiště prostřednictvím veřejného koncového bodu](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png)](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png#lightbox)