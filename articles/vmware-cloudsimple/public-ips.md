---
title: Řešení Azure VMware podle CloudSimple – přidělení veřejných IP adres
description: Popisuje, jak přidělit veřejné IP adresy pro virtuální počítače v prostředí privátního cloudu.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bb46ad726cd3b99324e9bb96b998ed1b4da885de
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899180"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>Přidělit veřejné IP adresy pro prostředí privátního cloudu

Otevřete kartu veřejné IP adresy na stránce síť a přidělte veřejné IP adresy pro virtuální počítače ve vašem prostředí privátního cloudu.

1. [Přejděte na portál CloudSimple](access-cloudsimple-portal.md) a v postranní nabídce vyberte **síť** .
2. Vyberte **veřejné IP adresy**.
3. Klikněte na **nová veřejná IP adresa**.

    ![Stránka veřejné IP adresy](media/public-ips-page.png)

4. Zadejte název pro identifikaci položky IP adresy.
5. Ponechte výchozí umístění.
6. V případě potřeby změňte časový limit nečinnosti pomocí posuvníku.
7. Zadejte místní IP adresu, pro kterou chcete přiřadit veřejnou IP adresu.
8. Zadejte přidružený název DNS.
9. Klikněte na **Odeslat**.

![Přidělit veřejné IP adresy](media/network-public-ip-allocate.png)

Spustí se úkol přidělení veřejné IP adresy. Stav úlohy můžete zjistit na stránce **aktivity > úlohy** . Po dokončení přidělení se nová položka zobrazí na stránce veřejné IP adresy.
