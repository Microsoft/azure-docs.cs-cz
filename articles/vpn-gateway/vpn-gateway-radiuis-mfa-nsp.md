---
title: Zabezpečení Azure VPN gateway ověřování pomocí protokolu RADIUS se serverem NPS pro ověřování službou Multi-Factor Authentication | Dokumentace Microsoftu
description: Popisuje integrace Azure gateway ověření služby RADIUS se serverem NPS pro ověřování službou Multi-Factor Authentication.
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
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 12e2f96e66305370499fbacf9ae6a834a22eb504
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50412495"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Integrace ověření služby RADIUS brány Azure VPN se serverem NPS pro ověřování službou Multi-Factor Authentication 

Tento článek popisuje, jak integrovat Azure VPN gateway ověřování pomocí protokolu RADIUS k poskytování služby Multi-Factor Authentication (MFA) pro připojení VPN typu point-to-site Server NPS (Network Policy Server). 

## <a name="prerequisite"></a>Požadavek

Jak zapnout MFA, uživatelé musí být v Azure Active Directory (Azure AD), která musí synchronizovat z buď místní nebo cloudové prostředí. Kromě toho uživatel musí dokončili proces automatické registrace pro vícefaktorové ověřování.  Další informace najdete v tématu [nastavit účtu pro dvoustupňové ověřování.](../active-directory/user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="detailed-steps"></a>Podrobné kroky

### <a name="step-1-create-a-virtual-network-gateway"></a>Krok 1: Vytvoření brány virtuální sítě

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ve virtuální síti, který bude hostitelem brány virtuální sítě, vyberte **podsítě**a pak vyberte **podsíť brány** vytvořit podsíť. 

    ![Obrázek o tom, jak přidat podsíť brány](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Vytvořte bránu virtuální sítě tak, že zadáte následující nastavení:

    - **Typ brány**: Vyberte **VPN**.
    - **Typ sítě VPN**: vyberte **založené na trasách**.
    - **Skladová položka**: Vyberte typ skladové položky na základě vašich požadavků.
    - **Virtuální síť**: vyberte virtuální síť, ve kterém vytvoříte podsíť brány.

        ![Obrázek o nastavení brány virtuální sítě](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-mfa"></a>Krok 2 konfigurace serveru NPS pro Azure MFA

1. Na serveru NPS [nainstalovat rozšíření NPS pro Azure MFA](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension).
2. Otevřete konzolu poskytovatelů síťových služeb, klikněte pravým tlačítkem na **RADUIS klienti**a pak vyberte **nový**. Vytvoření klienta RADUIS tak, že zadáte následující nastavení:

    - **Popisný název**: Zadejte libovolný název.
    - **Adresa (IP nebo DNS)**: Zadejte podsíť brány, kterou jste vytvořili v kroku 1.
    - **Sdílený tajný klíč**: Zadejte jakékoli tajný klíč a nezapomeňte ho pro pozdější použití.

    ![Obrázek nastavení RADUIS klientů](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  Na **Upřesnit** kartu, nastavte název dodavatele **RADIUS Standard** a ujistěte se, že **další možnosti** není zaškrtnuté políčko.

    ![Obrázek o pokročilé nastavení RADUIS klienta](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Přejděte na **zásady** > **zásady sítě**, dvakrát klikněte na panel **připojení k serveru Microsoft Routing a vzdálený přístup** zásad, vyberte  **Udělení přístupu**a potom klikněte na tlačítko **OK**.

### <a name="step-3-configure-the-virtual-network-gateway"></a>Krok 3 konfigurace brány virtuální sítě

1. Přihlaste se k [webu Azure portal](https://portal.azure.com).
2. Otevřete bránu virtuální sítě, kterou jste vytvořili. Ujistěte se, že typ brány je nastaven na **VPN** a zda je typ sítě VPN **založené na trasách**.
3. Klikněte na tlačítko **přejděte na konfigurace lokality** > **nakonfigurovat**a pak zadejte následující nastavení:

    - **Fond adres**: Zadejte podsíť brány, kterou jste vytvořili v kroku 1.
    - **Typ ověřování**: vyberte **ověřování pomocí protokolu RADIUS**.
    - **IP adresa serveru**: Zadejte IP adresu serveru NPS.

    ![Obrázek o, přejděte na nastavení lokality](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Další postup

- [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)
- [Integrace vaší stávající infrastruktury NPS se službou Multi-Factor Authentication](../active-directory/authentication/howto-mfa-nps-extension.md)
