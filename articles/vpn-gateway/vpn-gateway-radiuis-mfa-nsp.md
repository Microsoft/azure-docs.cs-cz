---
title: Zabezpečené Azure VPN gateway ověřování RADIUS se serverem NPS pro službu Multi-Factor Authentication | Microsoft Docs
description: Popisuje integrovat Azure brány ověřování pomocí protokolu RADIUS serveru NPS pro službu Multi-Factor Authentication.
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: willchen
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/13/2018
ms.author: genli
ms.openlocfilehash: c9985f6ad8721460e973d3c43f1f035506ae697c
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100070"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Ověřování RADIUS brány Azure VPN integrovat NPS server pro službu Multi-Factor Authentication 

Článek popisuje, jak integrovat Server NPS (Network Policy Server) s ověřování RADIUS brány Azure VPN k poskytování služby Multi-Factor Authentication (MFA) pro připojení VPN typu point-to-site. 

## <a name="prerequisite"></a>Požadavek

Pokud chcete povolit MFA, uživatelé musí být v Azure Active Directory (Azure AD), které musí být synchronizovaná s programem buď místní nebo cloudové prostředí. Také uživatel musí již dokončili proces automatické registrace mfa.  Další informace najdete v tématu [nastavit účtu pro dvoustupňové ověření](../active-directory/authentication/end-user/current/multi-factor-authentication-end-user-first-time.md)

## <a name="detailed-steps"></a>Podrobné kroky

### <a name="step-1-create-a-virtual-network-gateway"></a>Krok 1: Vytvoření brány virtuální sítě

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ve virtuální síti, který bude hostitelem bránu virtuální sítě, vyberte **podsítě**a potom vyberte **podsíť brány** vytvořit podsíť. 

    ![Obrázek o tom, jak přidat podsíť brány](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Vytvoření brány virtuální sítě tak, že zadáte následující nastavení:

    - **Typ brány**: Vyberte **VPN**.
    - **Typ sítě VPN**: vyberte **založené na trasách**.
    - **Skladová položka**: Vyberte typ SKU podle svých požadavků.
    - **Virtuální síť**: vyberte virtuální síť, ve které jste vytvořili podsíť brány.

        ![Obrázek o nastavení brány virtuální sítě](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-mfa"></a>Krok 2 konfigurace na server NPS pro Azure MFA

1. Na serveru NPS [instalace serveru NPS rozšíření pro Azure MFA](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension).
2. Otevřete konzolu NSP, klikněte pravým tlačítkem na **RADUIS klienti**a potom vyberte **nový**. Vytvoření klienta RADUIS tak, že zadáte následující nastavení:

    - **Popisný název**: napište libovolný název.
    - **Adresa (IP adresu nebo DNS)**: Zadejte podsíť brány, kterou jste vytvořili v kroku 1.
    - **Sdílený tajný klíč**: Zadejte jakékoli tajný klíč a nezapomeňte pro pozdější použití.

    ![Obrázek o nastavení klienta RADUIS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  Na **Upřesnit** kartě, název dodavatele nastavte **RADIUS standardní** a ujistěte se, že **další možnosti** není zaškrtnuté políčko.

    ![Bitovou kopii RADUIS klienta rozšířené možnosti nastavení](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Přejděte na **zásady** > **zásady sítě**, dvakrát klikněte na **připojení k serveru Microsoft Routing a vzdálený přístup** zásad, vyberte  **Udělení přístupu**a potom klikněte na **OK**.

### <a name="step-3-configure-the-virtual-network-gateway"></a>Krok 3 konfigurovat bránu virtuální sítě

1. Přihlaste se k [portál Azure](https://portal.azure.com).
2. Otevřete bránu virtuální sítě, kterou jste vytvořili. Ujistěte se, že typ brány je nastaven na **VPN** a zda je typ sítě VPN **založené na trasách**.
3. Klikněte na tlačítko **přejděte na konfigurace lokality** > **nyní nakonfigurovat**a pak zadejte následující nastavení:

    - **Fond adres**: Zadejte podsíť brány, který jste vytvořili v kroku 1.
    - **Typ ověřování**: vyberte **ověřování pomocí protokolu RADIUS**.
    - **IP adresa serveru**: Zadejte IP adresu serveru NPS.

    ![Obrázek o, přejděte na nastavení lokality](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Další postup

- [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)
- [Integrace vaší stávající infrastruktury NPS se službou Multi-Factor Authentication](../active-directory/authentication/howto-mfa-nps-extension.md)
