---
title: Jak vytvořit a spravovat protokol DHCP
description: Tento článek vysvětluje, jak spravovat protokol DHCP v řešení Azure VMware.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: ba7c3983f55ed729c77d2f24d3ef311a00522148
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2020
ms.locfileid: "90069556"
---
# <a name="how-to-create-and-manage-dhcp-in-azure-vmware-solution"></a>Jak vytvořit a spravovat DHCP v řešení Azure VMWare

NSX-T poskytuje možnost konfigurovat protokol DHCP pro váš privátní cloud. Pokud plánujete použít NSX-T k hostování serveru DHCP, přečtěte si téma [vytvoření serveru DHCP](#create-dhcp-server). V opačném případě, pokud máte v síti externí server DHCP třetí strany a chcete předat požadavky na tento server DHCP, přečtěte si téma [Vytvoření předávací služby DHCP](#create-dhcp-relay-service).

## <a name="create-dhcp-server"></a>Vytvořit server DHCP

Pomocí následujících kroků můžete nakonfigurovat server DHCP v NSX-T.

V NSX Manageru přejděte na kartu **sítě** a v části **Správa IP adres**vyberte **DHCP** . Klikněte na tlačítko **Přidat server** . Pak zadejte název serveru a IP adresu serveru. Po dokončení vyberte **Uložit**.

:::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="Přidat server DHCP" border="true":::

### <a name="connect-dhcp-server-to-the-tier-1-gateway"></a>Připojte server DHCP k bráně úrovně 1.

1. Vyberte **brány úrovně 1**, vyberte bránu a vyberte **Upravit** .

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="Vyberte bránu, kterou chcete použít." border="true":::

1. Přidat podsíť výběrem možnosti **nenastavené přidělení IP adres**

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="Přidat podsíť" border="true":::

1. Na další obrazovce vyberte v rozevíracím seznamu **typ** možnost **místní server DHCP** . Pro **Server DHCP**vyberte **výchozí DHCP** a vyberte **Uložit**.

   :::image type="content" source="./media/manage-dhcp/set-ip-address-management.png" alt-text="vybrat možnosti pro server DHCP" border="true":::

1. V okně **brány vrstvy 1** vyberte **Uložit**. Na další obrazovce uvidíte **uložené změny**a dokončete výběr **Zavřít úpravy** .

### <a name="add-a-network-segment"></a>Přidat segment sítě

Po vytvoření serveru DHCP budete muset do něj přidat segmenty sítě.

1. V NSX-T vyberte kartu **síť** a v části **připojení**vyberte **segmenty** . Vyberte **Přidat segment**. Pojmenujte segment a připojení k bráně úrovně 1. V dalším kroku vyberte možnost **nastavit podsítě** pro konfiguraci nové podsítě. 

   :::image type="content" source="./media/manage-dhcp/add-segment.png" alt-text="Přidat nový segment sítě" border="true":::

1. V okně **nastavit podsítě** vyberte **Přidat podsíť**. Zadejte IP adresu brány a rozsah DHCP a vyberte **Přidat** a potom **použít** .

   :::image type="content" source="./media/manage-dhcp/add-subnet-segment.png" alt-text="Přidat segment sítě" border="true":::

1. Až budete hotovi, vyberte **Uložit** a dokončete přidání segmentu sítě.

   :::image type="content" source="./media/manage-dhcp/segments-complete.png" alt-text="dokončené segmenty" border="true":::

## <a name="create-dhcp-relay-service"></a>Vytvořit předávací službu DHCP

1. V okně NSX-T vyberte kartu **síť** a v části **Správa IP adres**vyberte **DHCP**. Vyberte **Přidat server**. Jako **Typ serveru** vyberte možnost předávání DHCP a zadejte název serveru a IP adresu pro Server Relay. Vyberte **Uložit** a uložte tak provedené změny.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="vytvořit server pro předávání DHCP" border="true":::

1. V části **připojení**vyberte **brány úrovně 1** . V bráně vrstvy 1 vyberte svislá výpustka a zvolte **Upravit**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="Úprava brány vrstvy 1" border="true":::

1. Vyberte možnost **bez nastavení přidělení IP** adres pro definování přidělování IP adres.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="Upravit přidělování IP adres" border="true":::

1. Do dialogového okna **Zadejte pro typ**možnost **Server DHCP Relay**. V rozevíracím seznamu **Relay protokolu DHCP** vyberte svůj server přenosu DHCP. Po dokončení vyberte **Uložit** .

   :::image type="content" source="./media/manage-dhcp/set-ip-address-management-relay.png" alt-text="nastavení správy IP adres" border="true":::

## <a name="specify-a-dhcp-range-ip-on-segment"></a>Zadejte IP adresu rozsahu DHCP v segmentu.

> [!NOTE]
> Tato konfigurace je nutná ke realizaci funkcí přenosu DHCP v segmentu klienta DHCP. 

1. V části **připojení**vyberte **segmenty**. Vyberte svislé elipsy a vyberte **Upravit**. Místo toho, pokud jste chtěli přidat nový segment, můžete vybrat **Přidat segment** a vytvořit nový segment.

   :::image type="content" source="./media/manage-dhcp/edit-segments.png" alt-text="Úprava podsítě sítě" border="true":::

1. Přidejte podrobnosti o segmentu. Vyberte hodnotu v **podsítích** nebo **nastavte podsítě** pro přidání nebo úpravu podsítě.

   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="segmenty sítě" border="true":::

1. Vyberte svislé elipsy a zvolte **Upravit**. Pokud potřebujete vytvořit novou podsíť, vyberte **Přidat podsíť** a vytvořte bránu a NAKONFIGURUJTE rozsah DHCP. Zadejte rozsah fondu IP adres a vyberte **použít**a pak vyberte **Uložit** .

   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="Upravit podsítě" border="true":::

1. Do segmentu se teď přiřadí fond serverů DHCP.

   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Fond serverů DHCP přiřazený k segmentu" border="true":::
