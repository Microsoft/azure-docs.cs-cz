---
title: 'Konfigurace klienta VPN pro připojení P2S VPN: ověřování Azure AD | Microsoft Docs'
description: P2S VPN můžete použít pro připojení k virtuální síti pomocí ověřování Azure AD.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: cherylmc
ms.openlocfilehash: 704dcd6335766a6058de4e520b8dcbca0d304c9d
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721472"
---
# <a name="configure-a-vpn-client-for-p2s-vpn-connections-azure-ad-authentication-preview"></a>Konfigurace klienta VPN pro připojení P2S VPN: ověřování Azure AD (Preview)

Tento článek vám pomůže nakonfigurovat klienta VPN pro připojení k virtuální síti pomocí sítě VPN typu Point-to-site a ověřování Azure Active Directory. Než se budete moct připojit a ověřit pomocí Azure AD, musíte nejdřív nakonfigurovat tenanta Azure AD. Další informace najdete v tématu [Konfigurace tenanta Azure AD](openvpn-azure-ad-tenant.md).

## <a name="profile"></a>Práce s profily klienta

Abyste se mohli připojit, je potřeba [Stáhnout](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) klienta Azure VPN a nakonfigurovat profil klienta VPN na každém počítači, který se chce připojit k virtuální síti. Můžete vytvořit profil klienta na počítači, exportovat ho a pak ho naimportovat do dalších počítačů.

### <a name="cert"></a>Vytvoření profilu klienta založeného na certifikátech

Při práci s profilem založeným na certifikátech se ujistěte, že jsou na klientském počítači nainstalované příslušné certifikáty. Další informace o certifikátech najdete v tématu [instalace klientských certifikátů](point-to-site-how-to-vpn-client-install-azure-cert.md).

  ![certifikační](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="radius"></a>Postup vytvoření profilu klienta protokolu RADIUS

  ![zaoblen](./media/openvpn-azure-ad-client/create/create-radius1.jpg)

### <a name="export"></a>Export a distribuce profilu klienta

Jakmile budete mít funkční profil a potřebujete ho distribuovat ostatním uživatelům, můžete ho exportovat pomocí následujících kroků:

1. Zvýrazněte profil klienta VPN, který chcete exportovat, vyberte **...** a pak vyberte **exportovat**.

    ![Export](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Vyberte umístění, do kterého chcete uložit tento profil, ponechte název souboru tak, jak je, a pak vyberte **Uložit** a uložte soubor XML.

    ![Export](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="import"></a>Import profilu klienta

1. Na stránce vyberte **importovat**.

    ![Importovat](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Přejděte k souboru XML profilu a vyberte ho. Když je vybraný soubor, vyberte **otevřít**.

    ![Importovat](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Zadejte název profilu a vyberte **Uložit**.

    ![Importovat](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Vyberte **připojit** a připojte se k síti VPN.

    ![Importovat](./media/openvpn-azure-ad-client/import/import4.jpg)

5. Po připojení se ikona změní na zelenou a znamená se **připojit**.

    ![Importovat](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="delete"></a>Odstranění profilu klienta

1. Vyberte tři tečky vedle profilu klienta, který chcete odstranit. Pak vyberte **Odebrat**.

    ![delete](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Vyberte **Odebrat** a odstraňte.

    ![delete](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="connection"></a>Vytvoření připojení

1. Na stránce vyberte **+** a potom **+ Přidat**.

    ![připojení](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Vyplňte informace o připojení. Pokud si hodnoty nejste jistí, obraťte se na správce. Po vyplnění hodnot vyberte **Uložit**.

    ![připojení](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Vyberte **připojit** a připojte se k síti VPN.

    ![připojení](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Vyberte správné přihlašovací údaje a pak vyberte **pokračovat**.

    ![připojení](./media/openvpn-azure-ad-client/create/create4.jpg)

5. Po úspěšném připojení se ikona změní na zelenou a znamená se **připojit**.

    ![připojení](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="autoconnect"></a>Automatické připojení

Tyto kroky vám pomůžou nakonfigurovat připojení pro automatické připojení s vždycky zapnutým.

1. Na domovské stránce klienta VPN vyberte **nastavení sítě VPN**.

    ![Automaticky](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. V dialogovém okně přepnout aplikace vyberte **Ano** .

    ![Automaticky](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Ujistěte se, že připojení, které chcete nastavit, ještě není připojené, zvýrazněte profil a zaškrtněte políčko **Připojit automaticky** .

    ![Automaticky](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Kliknutím na **připojit** zahajte připojení k síti VPN.

    ![Automaticky](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose"></a>Diagnostika problémů s připojením

1. K diagnostice problémů s připojením můžete použít nástroj pro **diagnostiku** . Vyberte **...** vedle připojení VPN, které chcete diagnostikovat, aby se nabídka zobrazila. Pak vyberte **Diagnostika**.

    ![diagnóz](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. Na stránce **Vlastnosti připojení** vyberte **Spustit diagnostiku**.

    ![diagnóz](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Přihlaste se pomocí svých přihlašovacích údajů.

    ![diagnóz](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Zobrazení výsledků diagnostiky.

    ![diagnóz](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [Vytvoření klienta Azure Active Directory pro P2S otevřená připojení VPN, která používají ověřování Azure AD](openvpn-azure-ad-tenant.md).
