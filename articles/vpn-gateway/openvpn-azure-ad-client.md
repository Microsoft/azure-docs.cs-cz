---
title: 'Brána VPN: Klient VPN pro připojení protokolu OpenVPN P2S: Ověřování Azure AD'
description: K připojení k virtuální síti pomocí ověřování Azure AD můžete použít P2S VPN.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: alzam
ms.openlocfilehash: 7bc28a03476e773325d14808e1c7ac99103b2d5d
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879441"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>Konfigurace klienta VPN pro připojení protokolu P2S OpenVPN: Ověřování Azure AD

Tento článek vám pomůže nakonfigurovat klienta VPN pro připojení k virtuální síti pomocí ověřování vpn bodu na místo a ověřování Azure Active Directory. Než se budete moci připojit a ověřit pomocí Azure AD, musíte nejprve nakonfigurovat klienta Azure AD. Další informace najdete [v tématu Konfigurace klienta Azure AD](openvpn-azure-ad-tenant.md).

> [!NOTE]
> Ověřování Azure AD je podporované jenom pro připojení protokolu OpenVPN®.
>

## <a name="working-with-client-profiles"></a><a name="profile"></a>Práce s profily klientů

Chcete-li se připojit, musíte stáhnout klienta VPN Azure a nakonfigurovat profil klienta VPN v každém počítači, který se chce připojit k virtuální síti. V počítači můžete vytvořit profil klienta, exportovat jej a pak jej importovat do dalších počítačů.

### <a name="to-download-the-azure-vpn-client"></a>Stažení klienta Azure VPN

Tento [odkaz](https://go.microsoft.com/fwlink/?linkid=2117554) použijte ke stažení klienta Azure VPN. Ujistěte se, že klient Azure VPN má oprávnění ke spuštění na pozadí. Chcete-li zkontrolovat/povolit oprávnění, postupujte podle následujících kroků:

1. Přejděte na Úvodní obrazovku a vyberte Nastavení > aplikace Privacy > Pozadí.
2. V části Aplikace na pozadí zkontrolujte, jestli je zapnutá **zapnutá chyba, kdy jsou aplikace spuštěné na pozadí.**
3. V části Zvolte, které aplikace se můžou spouštět na pozadí, zapněte nastavení klienta Azure VPN **na Zapnuto**.

  ![Oprávnění](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>Vytvoření profilu klienta založeného na certifikátu

Při práci s profilem založeným na certifikátu zkontrolujte, zda jsou v klientském počítači nainstalovány příslušné certifikáty. Další informace o certifikátech naleznete v [tématu Instalace klientských certifikátů](point-to-site-how-to-vpn-client-install-azure-cert.md).

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>Vytvoření profilu klienta RADIUS

  ![Radius](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> Tajný klíč serveru lze exportovat do profilu klienta P2S VPN.  Pokyny k exportu profilu klienta naleznete [zde](about-vpn-profile-download.md).
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>Export a distribuce profilu klienta

Jakmile máte pracovní profil a potřebujete jej distribuovat ostatním uživatelům, můžete jej exportovat pomocí následujících kroků:

1. Zvýrazněte profil klienta VPN, který chcete exportovat, vyberte **...** a pak vyberte **Exportovat**.

    ![export](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Vyberte umístění, do kterého chcete uložit tento profil, ponechte název souboru tak, jak je, a pak vyberte **Uložit,** chcete-li soubor XML uložit.

    ![export](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>Import profilu klienta

1. Na stránce vyberte **Importovat**.

    ![import](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Přejděte do souboru XML profilu a vyberte ho. S vybraným souborem vyberte **Otevřít**.

    ![import](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Zadejte název profilu a vyberte **Uložit**.

    ![import](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Chcete-li se připojit k síti VPN, vyberte **možnost Připojit.**

    ![import](./media/openvpn-azure-ad-client/import/import4.jpg)

5. Po připojení se ikona změní na zelenou a řekne **Připojeno**.

    ![import](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Odstranění profilu klienta

1. Vyberte tři tečky vedle profilu klienta, který chcete odstranit. Potom vyberte **Odebrat**.

    ![delete](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Vyberte **Odebrat,** chcete-li odstranit.

    ![delete](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>Vytvoření připojení

1. Na stránce vyberte položku **+**, potom + **Přidat**.

    ![připojení](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Vyplňte informace o připojení. Pokud si nejste jisti hodnotami, obraťte se na správce. Po vyplnění hodnot vyberte **Uložit**.

    ![připojení](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Chcete-li se připojit k síti VPN, vyberte **možnost Připojit.**

    ![připojení](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Vyberte správná pověření a pak vyberte **Pokračovat**.

    ![připojení](./media/openvpn-azure-ad-client/create/create4.jpg)

5. Po úspěšném připojení se ikona změní na zelenou a řekne **Připojeno**.

    ![připojení](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>Automatické připojení

Tyto kroky vám pomohou nakonfigurovat připojení tak, aby se automaticky připojovalo pomocí funkce Vždy zapnuto.

1. Na domovské stránce klienta VPN vyberte **nastavení SÍTĚ VPN**.

    ![auto](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. V dialogovém poli přepínat aplikace vyberte **Ano.**

    ![auto](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Zkontrolujte, zda připojení, které chcete nastavit, ještě není připojeno, pak zvýrazněte profil a zaškrtněte políčko **Připojit se automaticky.**

    ![auto](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Chcete-li zahájit připojení VPN, vyberte **možnost Připojit.**

    ![auto](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnostika problémů s připojením

1. Chcete-li diagnostikovat problémy s připojením, můžete použít nástroj **diagnostika.** Vyberte **...** vedle připojení VPN, které chcete diagnostikovat odhalit menu. Pak vyberte **Diagnostikovat**.

    ![Diagnostikovat](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. Na stránce **Vlastnosti připojení** vyberte **Spustit diagnózu**.

    ![Diagnostikovat](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Přihlaste se pomocí svých přihlašovacích údajů.

    ![Diagnostikovat](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Prohlédněte si výsledky diagnostiky.

    ![Diagnostikovat](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>Nejčastější dotazy

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Jak přidám přípony DNS do klienta VPN?

Můžete upravit stažený soubor XML profilu a přidat ** \< \<dnssuffixes \<>dnssufix \<> /dnssufix>/dnssuffixes>** tagy

```
<azvpnprofile>
<clientconfig>

    <dnssuffixes>
          <dnssuffix>.mycorp.com</dnssuffix>
          <dnssuffix>.xyz.com</dnssuffix>
          <dnssuffix>.etc.net</dnssuffix>
    </dnssuffixes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>Jak přidám do klienta VPN vlastní servery DNS?

Můžete upravit stažený soubor XML profilu a přidat ** \<dnsserver>\<dnsserver> \</dnsserver>\</dnsservers>** tagy

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

> [!NOTE]
> Klient OpenVPN Azure AD využívá položky zásad překladu názvů DNS (NRPT), což znamená, že servery DNS nebudou uvedeny pod výstupem . `ipconfig /all` Chcete-li potvrdit nastavení dns v provozu, podívejte se na [zásady Get-DnsClientNrptPolicy](https://docs.microsoft.com/powershell/module/dnsclient/get-dnsclientnrptpolicy?view=win10-ps) v prostředí PowerShell.
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>Jak přidám do klienta VPN vlastní trasy?

Můžete upravit stažený soubor XML profilu a přidat ** \<trasy includeroutes \<>trasu>\<cíl \<>maska \<> /cíl \<ová>/maska>\</route>\</includeRoutes>tagy**

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>Jak zablokuji (vylučuji) trasy z klienta VPN?

Můžete upravit stažený soubor XML profilu a přidat ** \<příkazy \<>\<>trasu \<>\< \<>\<>cíl>maska> /cílová>\</trasa>/trasa>/excludetrasy>tagy**

```
<azvpnprofile>
<clientconfig>

    <excluderoutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </excluderoutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="can-i-import-the-profile-from-a-command-line-prompt"></a>Mohu profil importovat z příkazového řádku?

Profil můžete importovat z příkazového řádku umístěním staženého souboru **azurevpnconfig.xml** do **%userprofile%\AppData\Local\Packages\Microsoft.AzureVpn_8wekyb3d8bbwe\LocalState** a spuštěním následujícího příkazu:

```
azurevpn -i azurevpnconfig.xml 
```
vynutit import použít **-f** přepínač také


## <a name="next-steps"></a>Další kroky

Další informace najdete [v tématu Vytvoření klienta Služby Azure Active Directory pro připojení P2S Open VPN, která používají ověřování Azure AD](openvpn-azure-ad-tenant.md).
