---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 5/11/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b941084c8a196081c2443364ed3fb52868386670
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84464971"
---
Přejděte do účtu úložiště, pro který chcete vytvořit privátní koncový bod. V obsahu pro účet úložiště vyberte **připojení privátního koncového bodu**a potom **+ privátní koncový bod** pro vytvoření nového privátního koncového bodu. 

[![Snímek obrazovky s položkou připojení privátního koncového bodu v obsahu účtu úložiště](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png)](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png#lightbox)

Výsledný Průvodce má několik stránek, které je potřeba dokončit.

V okně **základy** vyberte požadovanou skupinu prostředků, název a oblast pro váš soukromý koncový bod. To může být cokoli, co potřebujete, ale nemusí odpovídat účtu úložiště jakýmkoli způsobem, i když musíte vytvořit privátní koncový bod ve stejné oblasti jako virtuální síť, ve které chcete vytvořit privátní koncový bod.

![Snímek obrazovky s oddílem základy oddílu Vytvoření privátního koncového bodu](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-1.png)

V okně **prostředek** vyberte přepínač pro **připojení k prostředku Azure ve složce Můj adresář**. V části **typ prostředku**vyberte pro typ prostředku možnost **Microsoft. Storage/storageAccounts** . Pole **prostředek** je účet úložiště se sdílenou složkou Azure, ke které se chcete připojit. Cílový dílčí prostředek je **soubor**, protože se jedná o soubory Azure.

Okno **Konfigurace** umožňuje vybrat konkrétní virtuální síť a podsíť, do které chcete přidat privátní koncový bod. Musíte vybrat odlišnou podsíť z podsítě, do které jste přidali koncový bod služby. Okno konfigurace obsahuje také informace pro vytvoření nebo aktualizaci privátní zóny DNS. Doporučujeme používat výchozí `privatelink.file.core.windows.net` zónu.

![Snímek obrazovky konfiguračního oddílu](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-2.png)

Kliknutím na tlačítko **zkontrolovat + vytvořit** vytvořte privátní koncový bod. 