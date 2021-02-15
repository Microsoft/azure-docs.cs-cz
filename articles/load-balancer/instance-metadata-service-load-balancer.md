---
title: Načtení informací o nástroji pro vyrovnávání zatížení pomocí Instance Metadata Service Azure
titleSuffix: Azure Load Balancer
description: Začínáme s používáním Azure Instance Metadata Service k získání informací o nástroji pro vyrovnávání zatížení.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: 0d7d08eb5e77e744fb6ce0abefc550bc79de4c8c
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519078"
---
# <a name="retrieve-load-balancer-information-by-using-the-azure-instance-metadata-service"></a>Načtení informací o nástroji pro vyrovnávání zatížení pomocí Instance Metadata Service Azure

IMDS (Azure Instance Metadata Service) poskytuje informace o aktuálně spuštěných instancích virtuálních počítačů. Služba je REST API, která je k dispozici na dobře známé, Nesměrovatelné IP adrese (169.254.169.254). 

Když umístíte virtuální počítač nebo instance sady virtuálních počítačů za Standard Load Balancer Azure, použijte IMDS k načtení metadat souvisejících s nástrojem pro vyrovnávání zatížení a instancemi.

Metadata obsahují následující informace pro virtuální počítače nebo sady škálování virtuálních počítačů:

* Veřejná IP adresa Standard SKU
* Konfigurace příchozího pravidla nástroje pro vyrovnávání zatížení jednotlivých privátních IP adres síťového rozhraní.
* Konfigurace odchozího pravidla nástroje pro vyrovnávání zatížení jednotlivých privátních IP adres síťového rozhraní.

## <a name="access-the-load-balancer-metadata-using-the-imds"></a>Přístup k metadatům nástroje pro vyrovnávání zatížení pomocí IMDS

Další informace o tom, jak získat přístup k metadatům nástroje pro vyrovnávání zatížení, najdete v tématu [použití instance metadata Service Azure pro přístup k informacím nástroje pro vyrovnávání zatížení](howto-load-balancer-imds.md).

## <a name="troubleshoot-common-error-codes"></a>Řešení běžných chybových kódů

Další informace o běžných kódech chyb a jejich metodách zmírnění najdete v tématu [řešení běžných chybových kódů při použití IMDS](troubleshoot-load-balancer-imds.md). 

## <a name="support"></a>Podpora

Pokud nemůžete získat odpověď na metadata po několika pokusech, vytvořte v Azure Portal problém podpory.

## <a name="next-steps"></a>Další kroky
Další informace o [Azure instance metadata Service](../virtual-machines/windows/instance-metadata-service.md)

[Nasazení standardního nástroje pro vyrovnávání zatížení](quickstart-load-balancer-standard-public-portal.md)

