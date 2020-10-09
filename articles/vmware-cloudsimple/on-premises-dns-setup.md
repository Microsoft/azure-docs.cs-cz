---
title: Řešení Azure VMware podle CloudSimple – konfigurace DNS pro privátní cloud CloudSimple
description: Popisuje způsob nastavení překladu názvů DNS pro přístup k serveru vCenter v privátním cloudu CloudSimple z místních pracovních stanic.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c2d69d21eb46d502a45c9df1dfaaa947d26ef7c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "79246107"
---
# <a name="configure-dns-for-name-resolution-for-private-cloud-vcenter-access-from-on-premises-workstations"></a>Konfigurace DNS pro překlad IP adres pro přístup k vCenter privátního cloudu z místních pracovních stanic

Pokud chcete získat přístup k serveru vCenter v privátním cloudu CloudSimple z místních pracovních stanic, musíte nakonfigurovat překlad adres DNS, aby se Server vCenter mohl adresovat podle názvu hostitele a také podle IP adresy.

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-private-cloud"></a>Získání IP adresy serveru DNS pro váš privátní cloud

1. Přihlaste se k [portálu CloudSimple](access-cloudsimple-portal.md).

2. Přejděte na **prostředky**  >  **privátní cloudy** a vyberte privátní cloud, ke kterému se chcete připojit.

3. Na stránce **Souhrn** v privátním cloudu v části **základní informace**ZKOPÍRUJTE IP adresu serveru DNS privátního cloudu.

    ![Servery DNS privátního cloudu](media/private-cloud-dns-server.png)


Pro konfiguraci DNS použijte některou z těchto možností.

* [Vytvořit zónu na serveru DNS pro *. cloudsimple.io](#create-a-zone-on-a-microsoft-windows-dns-server)
* [Pokud chcete vyřešit *. cloudsimple.io, vytvořte na místním serveru DNS podmíněný Server pro překlad.](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>Vytvořit zónu na serveru DNS pro *. cloudsimple.io

Zónu můžete nastavit jako zónu se zástupným inzerováním a nasměrovat na servery DNS v privátním cloudu pro překlad názvů. V této části najdete informace o použití serveru DNS BIND nebo serveru DNS Microsoft Windows.

### <a name="create-a-zone-on-a-bind-dns-server"></a>Vytvoření zóny na serveru DNS BIND

Konkrétní soubor a parametry, které je potřeba nakonfigurovat, se můžou lišit v závislosti na vašem individuálním nastavení DNS.

Například pro výchozí konfiguraci serveru BIND upravte soubor/etc/Named.conf na serveru DNS a přidejte následující informace o zóně.

```
zone "az.cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>Vytvoření zóny na serveru DNS Microsoft Windows

1. Klikněte pravým tlačítkem na server DNS a vyberte **Nová zóna**. 
  
    ![Nová zóna](media/DNS01.png)
2. Vyberte **zónu se zástupnými** procedurami a klikněte na **Další**.

    ![Nová zóna](media/DNS02.png)
3. V závislosti na vašem prostředí vyberte příslušnou možnost a klikněte na **Další**.

    ![Nová zóna](media/DNS03.png)
4. Vyberte **zónu dopředného vyhledávání** a klikněte na **Další**.

    ![Nová zóna](media/DNS01.png)
5. Zadejte název zóny a klikněte na **Další**.

    ![Nová zóna](media/DNS05.png)
6. Zadejte IP adresy serverů DNS vašeho privátního cloudu, které jste získali z portálu CloudSimple.

    ![Nová zóna](media/DNS06.png)
7. V případě potřeby klikněte na **Další** a dokončete instalaci Průvodce.

## <a name="create-a-conditional-forwarder"></a>Vytvoření podmíněného dopředné

Podmíněný Server pro přeposílání všechny požadavky na překlad názvů DNS na určený server. Při této instalaci se všechny požadavky na *. cloudsimple.io předávají na servery DNS nacházející se v privátním cloudu. Následující příklady ukazují, jak nastavit servery pro směrování na různých typech serverů DNS.

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>Vytvoření podmíněného přesměrování na serveru DNS BIND

Konkrétní soubor a parametry, které je potřeba nakonfigurovat, se můžou lišit v závislosti na vašem individuálním nastavení DNS.

Například pro výchozí konfiguraci serveru BIND upravte soubor/etc/Named.conf na serveru DNS a přidejte následující podmíněné informace pro předávání.

```
zone "az.cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>Vytvoření podmíněného přesměrování na serveru DNS Microsoft Windows

1. Otevřete Správce DNS na serveru DNS.
2. Klikněte pravým tlačítkem na **podmíněné přeposílání** a vyberte možnost přidání nového podmíněného dodávání.

    ![Podmíněný Server pro překládání 1 DNS systému Windows](media/DNS08.png)
3. Zadejte doménu DNS a IP adresu serverů DNS v privátním cloudu a klikněte na **OK**.
