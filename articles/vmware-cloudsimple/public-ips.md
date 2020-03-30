---
title: Řešení Azure VMware podle CloudSimple – přidělení veřejných IP adres
description: Popisuje, jak přidělit veřejné IP adresy pro virtuální počítače v prostředí privátního cloudu.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 565c9ad0fbd37b026a1ba555d83b9032d2efcba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024292"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>Přidělení veřejných IP adres pro prostředí privátního cloudu

Otevřete kartu Veřejné IP adresy na stránce Síť a přidělte veřejné IP adresy pro virtuální počítače v prostředí privátního cloudu.

1. [Přístup k portálu CloudSimple](access-cloudsimple-portal.md) a v boční nabídce vyberte **možnost Síť.**
2. Vyberte **veřejné IP adresy**.
3. Klepněte na **položku Nová veřejná adresa IP**.

    ![Stránka Veřejné IP adresy](media/public-ips-page.png)

4. Zadejte název pro identifikaci položky IP adresy.
5. Zachovat výchozí umístění.
6. V případě potřeby změňte časový limit nečinnosti pomocí posuvníku.
7. Zadejte místní ADRESU IP, ke které chcete přiřadit veřejnou IP adresu.
8. Zadejte přidružený název DNS.
9. Klepněte na **tlačítko Odeslat**.

![Přidělení veřejných IP služeb](media/network-public-ip-allocate.png)

Začíná úkol přidělení veřejné IP adresy. Stav úkolu můžete zkontrolovat na stránce **Aktivita > úkoly.** Po dokončení přidělení se nová položka zobrazí na stránce Veřejné IP adresy.
