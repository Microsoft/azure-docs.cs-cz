---
title: Řešení Azure VMware podle clouduSimple – konfigurace DNS pro privátní cloudový cloud
description: Popisuje, jak nastavit překlad názvů DNS pro přístup k serveru vCenter na cloudu CloudSimple Private Cloud z místních pracovních stanic.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c2d69d21eb46d502a45c9df1dfaaa947d26ef7c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246107"
---
# <a name="configure-dns-for-name-resolution-for-private-cloud-vcenter-access-from-on-premises-workstations"></a>Konfigurace služby DNS pro překlad názvů pro privátní cloud ový přístup vCenter z místních pracovních stanic

Chcete-li získat přístup k serveru vCenter na cloudu CloudSimple Private Cloud z místních pracovních stanic, musíte nakonfigurovat překlad adres DNS, aby server vCenter mohl být adresován názvem hostitele i IP adresou.

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-private-cloud"></a>Získání IP adresy dns serveru pro privátní cloud

1. Přihlaste se k [portálu CloudSimple](access-cloudsimple-portal.md).

2. Přejděte do**privátního cloudu** **Resources** > a vyberte privátní cloud, ke kterému se chcete připojit.

3. Na **stránce Souhrn** privátního cloudu v části **Základní informace**zkopírujte IP adresu ip serveru DNS privátního cloudu.

    ![Privátní cloudové servery DNS](media/private-cloud-dns-server.png)


Pro konfiguraci DNS použijte některou z těchto možností.

* [Vytvoření zóny na serveru DNS pro *.cloudsimple.io](#create-a-zone-on-a-microsoft-windows-dns-server)
* [Vytvořte podmíněný server pro předávání na místním serveru DNS, který vyřeší *.cloudsimple.io](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>Vytvoření zóny na serveru DNS pro *.cloudsimple.io

Zónu můžete nastavit jako zónu se zakázaným inzerováním a přejděte na servery DNS v privátním cloudu pro překlad názvů. Tato část obsahuje informace o použití serveru BIND DNS nebo serveru Microsoft Windows DNS.

### <a name="create-a-zone-on-a-bind-dns-server"></a>Vytvoření zóny na serveru BIND DNS

Konkrétní soubor a parametry, které chcete konfigurovat, se mohou lišit v závislosti na individuálním nastavení dns.

Například pro výchozí konfiguraci serveru BIND upravte soubor /etc/named.conf na serveru DNS a přidejte následující informace o zóně.

```
zone "az.cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>Vytvoření zóny na serveru Microsoft Windows DNS

1. Klepněte pravým tlačítkem myši na server DNS a vyberte příkaz **Nová zóna**. 
  
    ![Nová zóna](media/DNS01.png)
2. Vyberte **zónu se zakázaným inzerováním** a klepněte na tlačítko **Další**.

    ![Nová zóna](media/DNS02.png)
3. Vyberte příslušnou možnost v závislosti na prostředí a klepněte na tlačítko **Další**.

    ![Nová zóna](media/DNS03.png)
4. Vyberte **oblast dopředné vyhledávání** a klepněte na tlačítko **Další**.

    ![Nová zóna](media/DNS01.png)
5. Zadejte název zóny a klepněte na tlačítko **Další**.

    ![Nová zóna](media/DNS05.png)
6. Zadejte IP adresy serverů DNS pro privátní cloud, které jste získali z portálu CloudSimple.

    ![Nová zóna](media/DNS06.png)
7. Podle potřeby klepněte na tlačítko **Další** a dokončete nastavení průvodce.

## <a name="create-a-conditional-forwarder"></a>Vytvoření podmíněného předávání

Podmíněný server pro předávání předá všechny požadavky na překlad názvů DNS určenému serveru. Při tomto nastavení je jakýkoli požadavek na *.cloudsimple.io předán serverům DNS umístěným v privátním cloudu. Následující příklady ukazují, jak nastavit servery pro předávání na různých typech serverů DNS.

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>Vytvoření podmíněného serveru pro předávání na serveru BIND DNS

Konkrétní soubor a parametry, které chcete konfigurovat, se mohou lišit v závislosti na individuálním nastavení dns.

Například pro výchozí konfiguraci serveru BIND upravte soubor /etc/named.conf na serveru DNS a přidejte následující informace o podmíněném předávání.

```
zone "az.cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>Vytvoření podmíněného serveru pro předávání na serveru Microsoft Windows DNS

1. Otevřete Správce DNS na serveru DNS.
2. Klikněte pravým tlačítkem na **Podmíněné předávání** a vyberte možnost pro přidání nového podmíněného předávání.

    ![Podmíněný server pro předávání 1 Windows DNS](media/DNS08.png)
3. Zadejte doménu DNS a ADRESU IP serverů DNS v privátním cloudu a klepněte na tlačítko **OK**.
