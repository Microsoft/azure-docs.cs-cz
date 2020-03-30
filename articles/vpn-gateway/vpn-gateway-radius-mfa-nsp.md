---
title: Integrace serveru NPS s ověřováním RADIUS brány VPN pro vícefaktorové ověřování
description: Popisuje integraci ověřování RADIUS brány Azure se serverem NPS pro vícefaktorové ověřování.
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: 941b6ac86941824351f83592998e8735e3eb8ee5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780364"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Integrace ověřování RADIUS brány Azure VPN se serverem NPS pro vícefaktorové ověřování 

Článek popisuje, jak integrovat server NPS (Network Policy Server) s ověřováním RADIUS brány Azure VPN, aby bylo k poskytování vícefaktorového ověřování (MFA) pro připojení VPN na místě. 

## <a name="prerequisite"></a>Požadavek

Chcete-li povolit vícefaktorové zabezpečení, musí být uživatelé ve službě Azure Active Directory (Azure AD), které musí být synchronizovány z místního nebo cloudového prostředí. Uživatel musí mít také již dokončen proces automatického zápisu pro vícefaktorové ověřování.  Další informace najdete v tématu [Nastavení účtu pro dvoustupňové ověření](../active-directory/user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="detailed-steps"></a>Podrobné kroky

### <a name="step-1-create-a-virtual-network-gateway"></a>Krok 1: Vytvoření brány virtuální sítě

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ve virtuální síti, která bude hostitelem brány virtuální sítě, vyberte **Podsítě**a pak vyberte **podsíť Brána** a vytvořte podsíť. 

    ![Obrázek o přidání podsítě brány](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Vytvořte bránu virtuální sítě zadáním následujících nastavení:

    - **Typ brány**: Vyberte **VPN**.
    - **Typ VPN**: Vyberte **možnost Route-based**.
    - **Skladová položka**: Vyberte typ skladové položky na základě vašich požadavků.
    - **Virtuální síť**: Vyberte virtuální síť, ve které jste vytvořili podsíť brány.

        ![Obrázek o nastavení brány virtuální sítě](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-mfa"></a>Krok 2 Konfigurace serveru NPS pro Azure MFA

1. Na serveru NPS [nainstalujte rozšíření NPS pro Azure MFA](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension).
2. Otevřete konzolu NPS, klepněte pravým tlačítkem myši na **radius klienty**a vyberte **nový**. Vytvořte klienta RADIUS zadáním následujících nastavení:

    - **Popisný název**: Zadejte libovolný název.
    - **Adresa (IP nebo DNS):** Zadejte podsíť brány, kterou jste vytvořili v kroku 1.
    - **Sdílený tajný klíč**: zadejte libovolný tajný klíč a zapamatujte si jej pro pozdější použití.

      ![Obrázek o nastavení klienta RADIUS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  Na kartě **Upřesnit** nastavte název dodavatele na **RADIUS Standard** a ujistěte se, že není zaškrtnuté políčko **Další možnosti.**

    ![Obrázek o rozšířeném nastavení klienta RADIUS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Přejděte na **zásady** > **zásad y Zásady ochrany sítí**, poklepejte na **položku Připojení k zásadám serveru Směrování a vzdálený přístup společnosti Microsoft,** vyberte možnost **Udělit přístup**a klepněte na tlačítko **OK**.

### <a name="step-3-configure-the-virtual-network-gateway"></a>Krok 3 Konfigurace brány virtuální sítě

1. Přihlaste se na [portál Azure](https://portal.azure.com).
2. Otevřete bránu virtuální sítě, kterou jste vytvořili. Ujistěte se, že typ brány je nastaven na **VPN** a že typ VPN je **založen na směrování**.
3. Klikněte **na Přejděte na konfiguraci** > lokality**Konfigurovat nyní**a zadejte následující nastavení:

    - **Fond adres**: V kroku 1 zadejte podsíť brány, kterou jste vytvořili.
    - **Typ ověřování**: Vyberte **ověřování RADIUS**.
    - **IP adresa serveru**: Zadejte adresu IP serveru NPS.

      ![Obrázek o nastavení přejděte na web](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Další kroky

- [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)
- [Integrace vaší stávající infrastruktury NPS se službou Multi-Factor Authentication](../active-directory/authentication/howto-mfa-nps-extension.md)
