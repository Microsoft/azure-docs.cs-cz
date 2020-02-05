---
title: Řešení Azure VMware (AVS) – přidělit veřejné IP adresy
description: Popisuje, jak přidělit veřejné IP adresy pro virtuální počítače v prostředí privátního cloudu služby AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 87133f5efb9f096d3fdb0956aab1caac58b4bd94
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024292"
---
# <a name="allocate-public-ip-addresses-for-avs-private-cloud-environment"></a>Přidělit veřejné IP adresy pro prostředí privátního cloudu pro funkci AVS

Otevřete kartu veřejné IP adresy na stránce síť a přidělte veřejné IP adresy pro virtuální počítače v prostředí privátního cloudu služby AVS.

1. [Přejděte na portál pro funkci AVS](access-cloudsimple-portal.md) a v postranní nabídce vyberte **síť** .
2. Vyberte **veřejné IP adresy**.
3. Klikněte na **nová veřejná IP adresa**.

    ![Stránka veřejné IP adresy](media/public-ips-page.png)

4. Zadejte název pro identifikaci položky IP adresy.
5. Ponechte výchozí umístění.
6. V případě potřeby změňte časový limit nečinnosti pomocí posuvníku.
7. Zadejte místní IP adresu, pro kterou chcete přiřadit veřejnou IP adresu.
8. Zadejte přidružený název DNS.
9. Klikněte na **Submit** (Odeslat).

![Přidělit veřejné IP adresy](media/network-public-ip-allocate.png)

Spustí se úkol přidělení veřejné IP adresy. Stav úlohy můžete zjistit na stránce **aktivity > úlohy** . Po dokončení přidělení se nová položka zobrazí na stránce veřejné IP adresy.
