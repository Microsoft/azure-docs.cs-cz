---
title: Integrace serveru NPS s VPN Gateway ověřování protokolu RADIUS pro MFA
description: Popisuje integraci ověřování pomocí protokolu RADIUS služby Azure Gateway se serverem NPS pro Multi-Factor Authentication.
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: 208e99f61694f5a81a98dbc649e2a6035f57891b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018269"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Integrujte ověřování RADIUS brány Azure VPN Gateway se serverem NPS pro Multi-Factor Authentication 

Tento článek popisuje, jak integrovat server NPS (Network Policy Server) s ověřováním RADIUS služby Azure VPN Gateway za účelem doručování Multi-Factor Authentication (MFA) pro připojení VPN typu Point-to-site. 

## <a name="prerequisite"></a>Požadavek

Aby bylo možné vícefaktorové ověřování povolit, musí být uživatelé ve službě Azure Active Directory (Azure AD), která musí být synchronizovaná buď z místního nebo cloudového prostředí. Uživatel musí také mít již dokončen proces automatického zápisu pro MFA.  Další informace najdete v tématu [Nastavení účtu pro dvoustupňové ověřování](../active-directory/user-help/multi-factor-authentication-end-user-first-time.md) .

## <a name="detailed-steps"></a>Podrobné kroky

### <a name="step-1-create-a-virtual-network-gateway"></a>Krok 1: Vytvoření brány virtuální sítě

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ve virtuální síti, která bude hostovat bránu virtuální sítě, vyberte **podsítě** a pak vyberte **podsíť brány** , aby se vytvořila podsíť. 

    ![Obrázek týkající se přidání podsítě brány](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Vytvořte bránu virtuální sítě zadáním následujících nastavení:

    - **Typ brány**: Vyberte **VPN**.
    - **Typ sítě VPN**: vyberte **směrování založené na trasách**.
    - **SKU**: Vyberte typ SKU podle vašich požadavků.
    - **Virtuální síť**: vyberte virtuální síť, ve které jste vytvořili podsíť brány.

        ![Obrázek o nastavení brány virtuální sítě](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-ad-mfa"></a>Krok 2 konfigurace NPS pro Azure AD MFA

1. Na serveru NPS [nainstalujte rozšíření NPS pro Azure AD MFA](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension).
2. Otevřete konzolu NPS, klikněte pravým tlačítkem na **Klienti RADIUS** a pak vyberte **Nový**. Vytvořte klienta protokolu RADIUS zadáním následujících nastavení:

    - **Popisný název**: zadejte libovolný název.
    - **Adresa (IP nebo DNS)**: zadejte podsíť brány, kterou jste vytvořili v kroku 1.
    - **Sdílený tajný** klíč: zadejte libovolný tajný klíč a zapamatujte si ho pro pozdější použití.

      ![Obrázek o nastavení klienta RADIUS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  Na kartě **Upřesnit** nastavte název dodavatele na **Standard RADIUS** a ujistěte se, že políčko **Další možnosti** není zaškrtnuté.

    ![Obrázek o rozšířených nastaveních klienta RADIUS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Přejděte na **zásady**  >  **sítě** zásady, dvakrát klikněte na **připojení k zásadě serveru Microsoft Routing and Remote Access** , vyberte **udělit přístup** a pak klikněte na **OK**.

### <a name="step-3-configure-the-virtual-network-gateway"></a>Krok 3 konfigurace brány virtuální sítě

1. Přihlaste se k [Azure Portal](https://portal.azure.com).
2. Otevřete bránu virtuální sítě, kterou jste vytvořili. Ujistěte se, že typ brány je nastavený na **VPN** a že je typ sítě VPN **založený na směrování**.
3. Klikněte na položku nastavit **konfiguraci lokality**  >  **nyní** a potom zadejte následující nastavení:

    - **Fond adres**: zadejte podsíť brány, kterou jste vytvořili v kroku 1.
    - **Typ ověřování**: vyberte **ověřování RADIUS**.
    - **IP adresa serveru**: zadejte IP adresu serveru NPS.

      ![Obrázek o nastavení umístění na webu](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Další kroky

- [Azure AD Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)
- [Integrace stávající infrastruktury serveru NPS s Azure AD Multi-Factor Authentication](../active-directory/authentication/howto-mfa-nps-extension.md)