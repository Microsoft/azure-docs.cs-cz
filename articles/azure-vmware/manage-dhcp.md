---
title: Správa DHCP pro řešení Azure VMware
description: Naučte se vytvářet a spravovat protokol DHCP pro privátní cloud řešení Azure VMware.
ms.topic: how-to
ms.custom: contperf-fy21q2
ms.date: 11/09/2020
ms.openlocfilehash: bcaba4274b0e6b423e9fa490c80fc57204d4e153
ms.sourcegitcommit: d488a97dc11038d9cef77a0235d034677212c8b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2020
ms.locfileid: "97708547"
---
# <a name="manage-dhcp-for-azure-vmware-solution"></a>Správa DHCP pro řešení Azure VMware

Aplikace a úlohy běžící v prostředí privátního cloudu vyžadují služby DHCP pro přiřazování IP adres.  V tomto článku se dozvíte, jak vytvořit a spravovat DHCP v řešení Azure VMware dvěma způsoby:

- Pokud k hostování serveru DHCP používáte NSX-T, budete muset [vytvořit server DHCP](#create-a-dhcp-server) a [předat ho k tomuto serveru](#create-dhcp-relay-service). Když vytvoříte server DHCP, přidáte také segment sítě a zadáte rozsah IP adres DHCP.   

- Pokud v síti používáte externí server DHCP třetí strany, budete muset [vytvořit předávací službu DHCP](#create-dhcp-relay-service). Když vytvoříte předávání na server DHCP, ať už k hostování serveru DHCP pomocí NSX-T nebo třetí strany, budete muset zadat rozsah IP adres DHCP.

>[!IMPORTANT]
>Pokud je server DHCP v místním datovém centru, nefunguje protokol DHCP u virtuálních počítačů v síti VMware HCX L2 Stretch.  NSX ve výchozím nastavení blokuje všechny požadavky DHCP před roztažením hodnoty L2. Řešení najdete v tématu [odeslání požadavků DHCP na místní server DHCP](#send-dhcp-requests-to-the-on-premises-dhcp-server) .


## <a name="create-a-dhcp-server"></a>Vytvoření serveru DHCP

Pokud chcete server DHCP hostovat pomocí NSX-T, vytvoříte server DHCP. Pak přidáte segment sítě a zadáte rozsah IP adres DHCP.

1. Ve Správci NSX-T vyberte **síť**  >  **DHCP** a pak vyberte **Přidat server**.

1. Jako **Typ serveru** vyberte **DHCP** , zadejte název a IP adresu serveru a pak vyberte **Uložit**.

   :::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="Přidat server DHCP" border="true":::

1. Vyberte **brány vrstvy 1**, vyberte svislé tři tečky u brány vrstvy 1 a pak vyberte **Upravit**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="Vyberte bránu, kterou chcete použít." border="true":::

1. Vyberte možnost **bez nastavení přidělení IP adres** pro přidání podsítě.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="Přidat podsíť" border="true":::

1. Jako **typ** vyberte **místní server DHCP**. 
   
1. Pro **Server DHCP** vyberte **výchozí DHCP** a pak vyberte **Uložit**.

1. Vyberte **Uložit** znovu a pak vyberte **Zavřít úpravy**.

### <a name="add-a-network-segment"></a>Přidat segment sítě

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]


## <a name="create-dhcp-relay-service"></a>Vytvořit předávací službu DHCP

Pokud chcete použít externí server DHCP třetí strany, budete muset vytvořit předávací službu DHCP. Určíte také rozsah IP adres DHCP ve Správci NSX-T. 

1. Ve Správci NSX-T vyberte **síť**  >  **DHCP** a pak vyberte **Přidat server**.

1. Jako **Typ serveru** vyberte **předávání DHCP** , zadejte název a IP adresu serveru a pak vyberte **Uložit**.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="vytvořit předávací službu DHCP" border="true":::

1. Vyberte **brány vrstvy 1**, vyberte svislé tři tečky u brány vrstvy 1 a pak vyberte **Upravit**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="Úprava brány vrstvy 1" border="true":::

1. Vyberte možnost **bez nastavení přidělení IP** adres pro definování přidělování IP adres.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="Upravit přidělování IP adres" border="true":::

1. Jako **typ** vyberte **Server DHCP**. 
   
1. Pro **Server DHCP** vyberte možnost **předávání DHCP** a pak vyberte **Uložit**.

1. Vyberte **Uložit** znovu a pak vyberte **Zavřít úpravy**.


## <a name="specify-the-dhcp-ip-address-range"></a>Zadejte rozsah IP adres DHCP.

1. Ve Správci NSX-T vyberte   >  **segmenty** sítě. 
   
1. V názvu segmentu vyberte svislá výpustka a vyberte **Upravit**.
   
1. Vyberte možnost **nastavit podsítě** a určete IP adresu DHCP pro podsíť. 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="segmenty sítě" border="true":::
      
1. V případě potřeby upravte IP adresu brány a zadejte IP adresu rozsahu DHCP. 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="Upravit podsítě" border="true":::
      
1. Vyberte **použít** a pak **Uložit**. Segmentu je přiřazen fond serverů DHCP.
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Fond serverů DHCP přiřazený k segmentu" border="true":::


## <a name="send-dhcp-requests-to-the-on-premises-dhcp-server"></a>Odeslání požadavků DHCP na místní server DHCP

Pokud chcete odesílat požadavky DHCP z virtuálních počítačů řešení Azure VMware v rozšířeném segmentu L2 na místní server DHCP, vytvoříte profil segmentu zabezpečení. 

1. Přihlaste se k místnímu serveru vCenter a v části **Domů** vyberte **HCX**.

1. V části **služby** vyberte **rozšíření sítě** .

1. Vyberte rozšíření sítě, které má podporovat požadavky DHCP z řešení Azure VMware do místního prostředí. 

1. Poznamenejte si název cílové sítě.  

   :::image type="content" source="media/manage-dhcp/hcx-find-destination-network.png" alt-text="Snímek obrazovky s rozšířením sítě v klientovi VMware vSphere" lightbox="media/manage-dhcp/hcx-find-destination-network.png":::

1. Ve Správci řešení Azure VMware NSX-T vyberte **sítě**  >    >  **profily segment segmentů segmentů**. 

1. Vyberte **Přidat profil segmentu** a pak **segment zabezpečení**.

   :::image type="content" source="media/manage-dhcp/add-segment-profile.png" alt-text="Snímek obrazovky s postupem, jak přidat profil segmentu v NSX-T" lightbox="media/manage-dhcp/add-segment-profile.png":::

1. Zadejte název a značku a pak nastavte přepínač **filtru BPDU** na zapnuto a všechny přepínače DHCP jsou vypnuté.

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png" alt-text="Snímek obrazovky, na kterém je zobrazený filtr BPDU, a DHCP se vypíná." lightbox="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png":::

1. V **seznamu povolených filtrů BPDU** odeberte všechny adresy Mac, pokud existují.  Pak vyberte **Uložit**.

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-allow-list.png" alt-text="Snímek obrazovky se adresami MAC v seznamu povolených filtrů BPDU":::

1. V části segmentace segmentů **sítě**  >    >  v oblasti hledání zadejte název definice sítě.

   :::image type="content" source="media/manage-dhcp/networking-segments-search.png" alt-text="Snímek obrazovky s polem filtru segmentů > sítě":::

1. V názvu segmentu vyberte svislá výpustka a vyberte **Upravit**.

   :::image type="content" source="media/manage-dhcp/edit-network-segment.png" alt-text="Snímek obrazovky s tlačítkem upravit pro segment" lightbox="media/manage-dhcp/edit-network-segment.png":::

1. Změňte **zabezpečení segmentu** na profil segmentu, který jste vytvořili dříve.

   :::image type="content" source="media/manage-dhcp/edit-segment-security.png" alt-text="Snímek obrazovky s polem zabezpečení segmentu" lightbox="media/manage-dhcp/edit-segment-security.png":::

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [údržbě hostitelů a správě životního cyklu](concepts-private-clouds-clusters.md#host-maintenance-and-lifecycle-management).