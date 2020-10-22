---
title: Kurz – vytvoření segmentu sítě NSX-T v řešení Azure VMware
description: Naučte se vytvářet segmenty sítě NSX-T, které se používají pro virtuální počítače v vCenter.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: f88f186d2af10bcc114d64920a3ac489ef7be54f
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367665"
---
# <a name="tutorial-create-an-nsx-t-network-segment-in-azure-vmware-solution"></a>Kurz: Vytvoření segmentu sítě NSX-T v řešení Azure VMware

Virtuální počítače vytvořené v vCenter jsou umístěné do segmentů sítě vytvořených v NSX-T a jsou viditelné v vCenter.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Pokud chcete přidat segmenty sítě, přejděte do správce NSX-T.
> * Přidat nový segment sítě
> * Sledovat nový segment sítě v vCenter

## <a name="prerequisites"></a>Předpoklady

Abyste měli přístup k rozhraním pro správu vCenter a NSX-T, musíte mít privátní cloud řešení Azure VMware. Další informace najdete v kurzu [Konfigurace sítě](tutorial-configure-networking.md) .

## <a name="provision-a-network-segment-in-nsx-t"></a>Zřízení segmentu sítě v NSX-T

1. V vCenter pro váš privátní cloud vyberte **SDDC-Datacenter > sítě** a Všimněte si, že ještě neexistují žádné sítě.

   :::image type="content" source="media/nsxt/vcenter-without-ls01.png" alt-text="V vCenter pro váš privátní cloud vyberte SDDC-Datacenter > sítě a ještě neexistují žádné sítě.":::

1. V nástroji NSX-T Manager pro váš privátní cloud vyberte **sítě**.

   :::image type="content" source="media/nsxt/nsxt-network-overview.png" alt-text="V vCenter pro váš privátní cloud vyberte SDDC-Datacenter > sítě a ještě neexistují žádné sítě.":::

1. Vyberte **segmenty**.

   :::image type="content" source="media/nsxt/nsxt-select-segments.png" alt-text="V vCenter pro váš privátní cloud vyberte SDDC-Datacenter > sítě a ještě neexistují žádné sítě.":::

1. Na stránce Přehled segmentů NSX-T vyberte **Přidat segment**. Tři segmenty se vytvářejí v rámci zřizování privátního cloudu a nejde je použít pro virtuální počítače.  Pro tento účel budete muset přidat nový segment sítě.

   :::image type="content" source="media/nsxt/nsxt-segments-overview.png" alt-text="V vCenter pro váš privátní cloud vyberte SDDC-Datacenter > sítě a ještě neexistují žádné sítě.":::

1. Pojmenujte segment, zvolte předem nakonfigurovanou bránu Tier1 (TNTxx-T1) jako **připojenou bránu**, ponechte **typ** jako flexibilní, zvolte předem nakonfigurovanou **zónu** překrývající přenos (TNTxx – překryvný – TZ) a pak vyberte nastavit podsítě. Všechna ostatní nastavení v této části a profily **portů** a **segmentů** můžou zůstat ve výchozím nastavení, jako je konfigurace.

   :::image type="content" source="media/nsxt/nsxt-create-segment-specs.png" alt-text="V vCenter pro váš privátní cloud vyberte SDDC-Datacenter > sítě a ještě neexistují žádné sítě.":::

1. Nastavte IP adresu brány pro nový segment a pak vyberte **Přidat**. IP adresa, kterou použijete, musí být na překrývající se RFC1918 adres, což zajistí, že se můžete připojit k virtuálním počítačům v novém segmentu.

   :::image type="content" source="media/nsxt/nsxt-create-segment-gateway.png" alt-text="V vCenter pro váš privátní cloud vyberte SDDC-Datacenter > sítě a ještě neexistují žádné sítě.":::

1. Použijte nový segment sítě tak, že vyberete **použít** a pak konfiguraci uložíte pomocí **Uložit**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-apply.png" alt-text="V vCenter pro váš privátní cloud vyberte SDDC-Datacenter > sítě a ještě neexistují žádné sítě.":::

   :::image type="content" source="media/nsxt/nsxt-create-segment-save.png" alt-text="V vCenter pro váš privátní cloud vyberte SDDC-Datacenter > sítě a ještě neexistují žádné sítě.":::

1. Nyní byl vytvořen nový segment sítě a odmítnete možnost pokračovat v konfiguraci segmentu výběrem možnosti **ne**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-continue-no.png" alt-text="V vCenter pro váš privátní cloud vyberte SDDC-Datacenter > sítě a ještě neexistují žádné sítě.":::

1. Potvrďte, že se nový segment sítě nachází v NSX-T, a to tak, že vyberete **sítě > segmenty** a uvidíte nový segment v seznamu (v tomto případě "LS01").

   :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="V vCenter pro váš privátní cloud vyberte SDDC-Datacenter > sítě a ještě neexistují žádné sítě.":::

1. Potvrďte, že se nový segment sítě nachází v vCenter, a to tak, že vyberete **sítě > SDDC-datacentra** a v seznamu budete pozorovat nový segment (v tomto případě "LS01").

   :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="V vCenter pro váš privátní cloud vyberte SDDC-Datacenter > sítě a ještě neexistují žádné sítě.":::

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili segmenty sítě NSX-T, které se používají pro virtuální počítače v vCenter. Teď můžete [vytvořit knihovnu obsahu pro nasazení virtuálních počítačů v řešení Azure VMware](deploy-vm-content-library.md). Můžete také zřídit virtuální počítač v síti, kterou jste vytvořili v tomto kurzu.

Pokud ne, přejděte k dalšímu kurzu, kde se dozvíte, jak vytvořit ExpressRoute Global Reach partnerský vztah k privátnímu cloudu v řešení Azure VMware.

> [!div class="nextstepaction"]
> [Partnerských místních prostředí do privátního cloudu](tutorial-expressroute-global-reach-private-cloud.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
