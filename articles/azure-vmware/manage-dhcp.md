---
title: Jak vytvořit a spravovat protokol DHCP
description: Tento článek vysvětluje, jak spravovat protokol DHCP v řešení Azure VMware.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: cb74ed4474cc14081e59142f2f60915fccd47559
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461052"
---
# <a name="how-to-create-and-manage-dhcp-in-azure-vmware-solution"></a>Jak vytvořit a spravovat DHCP v řešení Azure VMware

NSX-T poskytuje možnost konfigurovat protokol DHCP pro váš privátní cloud. Pokud k hostování serveru DHCP používáte NSX-T, přečtěte si téma [vytvoření serveru DHCP](#create-dhcp-server). V opačném případě, pokud máte v síti externí server DHCP třetí strany, přečtěte si téma [Vytvoření předávací služby DHCP](#create-dhcp-relay-service).

## <a name="create-dhcp-server"></a>Vytvořit server DHCP

Pomocí následujících kroků můžete nakonfigurovat server DHCP v NSX-T.

1. V NSX Manageru přejděte na kartu **sítě** a vyberte **DHCP**. 
1. Vyberte **Přidat server** a pak zadejte název a IP adresu serveru. 
1. Vyberte **Uložit**.

:::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="Přidat server DHCP" border="true":::

### <a name="connect-dhcp-server-to-the-tier-1-gateway"></a>Připojte server DHCP k bráně úrovně 1.

1. Vyberte možnost **brány vrstvy 1**, bránu ze seznamu a pak vyberte **Upravit**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="Přidat server DHCP" border="true":::

1. Vyberte možnost **bez nastavení přidělení IP adres** pro přidání podsítě.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="Přidat server DHCP" border="true":::

1. Jako **typ**vyberte **místní server DHCP** . 
1. Pro **Server DHCP** vyberte **výchozí DHCP** a pak vyberte **Uložit**.


1. V okně **brány vrstvy 1** vyberte **Uložit**. 
1. Vyberte **Zavřít úpravy** a dokončete.

### <a name="add-a-network-segment"></a>Přidat segment sítě

Po vytvoření serveru DHCP budete muset do něj přidat segmenty sítě.

1. V NSX-T vyberte kartu **síť** a v části **připojení**vyberte **segmenty** . 
1. Vyberte **Přidat segment** a pojmenujte segment a připojení k bráně úrovně 1. 
1. Vyberte **nastavit podsítě** pro konfiguraci nové podsítě. 

   :::image type="content" source="./media/manage-dhcp/add-segment.png" alt-text="Přidat server DHCP" border="true":::

1. V okně **nastavit podsítě** vyberte **Přidat podsíť**. 
1. Zadejte IP adresu brány a rozsah DHCP a vyberte **Přidat** a potom **použít** .

1. Vyberte **Uložit** a přidejte nový segment sítě.

## <a name="create-dhcp-relay-service"></a>Vytvořit předávací službu DHCP

1. Vyberte kartu **síť** a v části **Správa IP adres**vyberte **DHCP**. 
1. Vyberte **Přidat server**. 
1. Jako **Typ serveru** vyberte předávání DHCP a zadejte název a IP adresu serveru pro přenos. 
1. Vyberte **Uložit**.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="Přidat server DHCP" border="true":::

1. V části **připojení**vyberte **brány úrovně 1** . 
1. V bráně vrstvy 1 vyberte svislá výpustka a vyberte **Upravit**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="Přidat server DHCP" border="true":::

1. Vyberte možnost **bez nastavení přidělení IP** adres pro definování přidělování IP adres.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="Přidat server DHCP" border="true":::

1. Jako **typ**vyberte **předávací server DHCP** .
1. Vyberte předávací server DHCP pro **předávání DHCP**. 
1. Vyberte **Uložit**.


## <a name="specify-a-dhcp-range-ip-on-a-segment"></a>Zadejte IP adresu rozsahu DHCP v segmentu.

> [!NOTE]
> Tato konfigurace je nutná ke realizaci funkcí přenosu DHCP v segmentu klienta DHCP. 

1. V části **připojení**vyberte **segmenty**. 
1. Vyberte svislé elipsy a vyberte **Upravit**. 

   >[!TIP]
   >Pokud chcete přidat nový segment, vyberte **Přidat segment**.

1. Přidejte podrobnosti o segmentu. 
1. Vyberte hodnotu v části **nastavit podsítě** a přidejte nebo upravte podsíť.

   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="Přidat server DHCP" border="true":::

1. Vyberte svislé elipsy a zvolte **Upravit**. Pokud potřebujete vytvořit novou podsíť, vyberte **Přidat podsíť** a vytvořte bránu a NAKONFIGURUJTE rozsah DHCP. 
1. Zadejte rozsah fondu IP adres a vyberte **použít**a pak vyberte **Uložit** .

   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="Přidat server DHCP" border="true":::

   K segmentu je přiřazen fond serverů DHCP.

   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Přidat server DHCP" border="true":::
