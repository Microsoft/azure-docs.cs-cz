---
title: Přidat segment sítě NSX-T
description: Postup přidání síťového segmentu NSX-T pro řešení Azure VMware.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 14d698413d31af2dcbbdea5f37ec7f24f65199ad
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "103462116"
---
<!-- Used in manage-dhcp.md and tutorial-nsx-t-network-segment.md -->

1. Ve Správci NSX-T vyberte   >  **segmenty** sítě a pak vyberte **Přidat segment**. 

   :::image type="content" source="../media/nsxt/nsxt-segments-overview.png" alt-text="Snímek obrazovky ukazující, jak přidat nový segment":::

1. Zadejte název segmentu.

1. Vyberte bránu 1 (TNTxx-T1) jako **připojenou bránu** a ponechte **typ** jako flexibilní.

1. Vyberte předem nakonfigurovanou zónu překrývající **přenos** (TNTXX-PŘEKRYVNÉ – TZ) a pak vyberte **nastavit podsítě**. 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-specs.png" alt-text="Nastavte název segmentu, připojenou bránu a typ a přenosovou zónu a pak vyberte nastavit podsíť.":::

1. Zadejte IP adresu brány a pak vyberte **Přidat**. 

   >[!IMPORTANT]
   >IP adresa musí být na překrývající se RFC1918 adres, která zajišťuje připojení k virtuálním počítačům v novém segmentu.

   :::image type="content" source="../media/nsxt/nsxt-create-segment-gateway.png" alt-text="Nastavte IP adresu brány pro nový segment a pak vyberte Přidat.":::

1. Vyberte **použít** a pak **Uložit**.

1. Vyberte **ne** , pokud chcete, aby možnost pokračovala v konfiguraci segmentu. 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-continue-no.png" alt-text="Kliknutím na ne můžete odmítnout další konfiguraci nově vytvořeného segmentu sítě.":::

1. Potvrďte přítomnost nového segmentu sítě. V tomto příkladu je **LS01** nový segment sítě.

   1. Ve Správci NSX-T vyberte   >  **segmenty** sítě. 

      :::image type="content" source="../media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Ověřte, že se nový segment sítě nachází v NSX-T.":::

   1. V vCenter vyberte **síť > SDDC-Datacenter**.

      :::image type="content" source="../media/nsxt/vcenter-with-ls01-2.png" alt-text="Potvrďte, že se nový segment sítě nachází v vCenter.":::