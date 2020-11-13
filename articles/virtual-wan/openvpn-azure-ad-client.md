---
title: 'VPN Gateway: klient VPN pro připojení P2S protokolu OpenVPN: ověřování Azure AD'
description: Naučte se používat P2S VPN pro připojení k virtuální síti pomocí ověřování Azure AD.
services: vpn-gateway
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 8e97a2f077efd4d00eec4a91645dc1b65057ebd9
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565003"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>Konfigurace klienta VPN pro připojení typu point-to-site přes protokol OpenVPN: Ověřování Azure AD

Tento článek vám pomůže nakonfigurovat klienta VPN pro připojení k virtuální síti pomocí sítě VPN typu Point-to-site a ověřování Azure Active Directory. Než se budete moct připojit a ověřit pomocí Azure AD, musíte nejdřív nakonfigurovat tenanta Azure AD. Další informace najdete v tématu [Konfigurace tenanta Azure AD](openvpn-azure-ad-tenant.md).

> [!NOTE]
> Ověřování Azure AD se podporuje jenom pro připojení OpenVPN® protokolu.
>

## <a name="working-with-client-profiles"></a><a name="profile"></a>Práce s profily klienta

Abyste se mohli připojit, je potřeba stáhnout klienta Azure VPN a nakonfigurovat profil klienta VPN na každém počítači, který se chce připojit k virtuální síti. Můžete vytvořit profil klienta na počítači, exportovat ho a pak ho naimportovat do dalších počítačů.

### <a name="to-download-the-azure-vpn-client"></a>Stažení klienta Azure VPN

Pomocí tohoto [odkazu](https://go.microsoft.com/fwlink/?linkid=2117554) si stáhněte klienta Azure VPN. Ujistěte se prosím, že má klient Azure VPN oprávnění běžet na pozadí. Chcete-li ověřit nebo povolit oprávnění, postupujte podle následujících kroků:

1. Pokračujte na Start a pak vyberte nastavení > ochrany osobních údajů > aplikace na pozadí.
2. V části aplikace na pozadí se ujistěte, že je zapnutá volba **Povolit aplikace spuštěné na pozadí** .
3. V části zvolit, které aplikace se můžou spouštět na pozadí, zapnout nastavení pro klienta Azure VPN na **zapnuto**.

  ![udělen](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>Vytvoření profilu klienta založeného na certifikátech

Při práci s profilem založeným na certifikátech se ujistěte, že jsou na klientském počítači nainstalované příslušné certifikáty. Další informace o certifikátech najdete v tématu [instalace klientských certifikátů](certificates-point-to-site.md).

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>Postup vytvoření profilu klienta protokolu RADIUS

  ![zaoblen](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> Tajný kód serveru se dá exportovat v profilu klienta VPN P2S.  Pokyny, jak exportovat profil klienta, najdete [tady](about-vpn-profile-download.md).
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>Export a distribuce profilu klienta

Jakmile budete mít funkční profil a potřebujete ho distribuovat ostatním uživatelům, můžete ho exportovat pomocí následujících kroků:

1. Zvýrazněte profil klienta VPN, který chcete exportovat, vyberte **...** a pak vyberte **exportovat**.

    ![Snímek obrazovky s vybraným exportem zobrazí z nabídky.](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Vyberte umístění, do kterého chcete uložit tento profil, ponechte název souboru tak, jak je, a pak vyberte **Uložit** a uložte soubor XML.

    ![Snímek obrazovky se zobrazí dialogové okno Uložit jako, kde můžete zadat název souboru.](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>Import profilu klienta

1. Na stránce vyberte **importovat**.

    ![Snímek obrazovky zobrazující import vybraný z nabídky plus](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Přejděte k souboru XML profilu a vyberte ho. Když je vybraný soubor, vyberte **otevřít**.

    ![Snímek obrazovky zobrazuje otevřené dialogové okno, ve kterém můžete vybrat soubor.](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Zadejte název profilu a vyberte **Uložit**.

    ![Snímek obrazovky zobrazuje název připojení, který jste přidali, a tlačítko Uložit.](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Vyberte **připojit** a připojte se k síti VPN.

    ![Snímek obrazovky se zobrazí tlačítko připojit pro připojení, které jste právě vytvořili.](./media/openvpn-azure-ad-client/import/import4.jpg)

5. Po připojení se ikona změní na zelenou a znamená se **připojit**.

    ![Snímek obrazovky zobrazuje připojení v připojeném stavu s možností odpojení.](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Odstranění profilu klienta

1. Vyberte tři tečky vedle profilu klienta, který chcete odstranit. Pak vyberte **Odebrat**.

    ![Snímek obrazovky se zobrazí z nabídky odebrat vybrané.](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Vyberte **Odebrat** a odstraňte.

    ![Snímek obrazovky se zobrazí potvrzovací dialogové okno s možností odebrat nebo zrušit.](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>Vytvoření připojení

1. Na stránce vyberte **+** a potom **+ Přidat**.

    ![Snímek obrazovky se zobrazí přidat vybrané z nabídky Plus.](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Vyplňte informace o připojení. Pokud si hodnoty nejste jistí, obraťte se na správce. Po vyplnění hodnot vyberte **Uložit**.

    ![Snímek obrazovky se zobrazí v podokně, kde můžete zadat požadované hodnoty.](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Vyberte **připojit** a připojte se k síti VPN.

    ![Snímek obrazovky se zobrazí tlačítko připojit pro vaše připojení.](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Vyberte správné přihlašovací údaje a pak vyberte **pokračovat**.

    ![Snímek obrazovky se zobrazí v dialogovém okně přihlásit se.](./media/openvpn-azure-ad-client/create/create4.jpg)

5. Po úspěšném připojení se ikona změní na zelenou a znamená se **připojit**.

    ![Snímek obrazovky ukazuje připojení v připojeném stavu.](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>Automatické připojení

Tyto kroky vám pomůžou nakonfigurovat připojení pro automatické připojení s vždycky zapnutým.

1. Na domovské stránce klienta VPN vyberte **nastavení sítě VPN**.

    ![Snímek obrazovky ukazuje připojení V P N, kde můžete vybrat nastavení V P N.](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. V dialogovém okně přepnout aplikace vyberte **Ano** .

    ![Snímek obrazovky se zobrazí ověřovací zpráva o přepínání aplikací.](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Ujistěte se, že připojení, které chcete nastavit, ještě není připojené, zvýrazněte profil a zaškrtněte políčko **Připojit automaticky** .

    ![Snímek obrazovky se zobrazí dialogové okno nastavení, kde můžete vybrat připojit automaticky.](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Kliknutím na **připojit** zahajte připojení k síti VPN.

    ![Snímek obrazovky se zobrazí tlačítko připojit.](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnostika problémů s připojením

1. K diagnostice problémů s připojením můžete použít nástroj pro **diagnostiku** . Vyberte **...** vedle připojení VPN, které chcete diagnostikovat, aby se nabídka zobrazila. Pak vyberte **Diagnostika**.

    ![Snímek obrazovky zobrazuje diagnostiku vybranou z nabídky.](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. Na stránce **Vlastnosti připojení** vyberte **Spustit diagnostiku**.

    ![Snímek obrazovky ukazuje tlačítko spustit diagnostiku pro připojení.](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Přihlaste se pomocí svých přihlašovacích údajů.

    ![Snímek obrazovky se zobrazí dialogové okno přihlášení pro tuto akci.](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Zobrazení výsledků diagnostiky.

    ![Snímek obrazovky zobrazuje výsledky diagnostiky.](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>Nejčastější dotazy

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Návody přidat do klienta VPN přípony DNS?

Můžete upravit stažený soubor XML profilu a přidat **\<dnssuffixes> \<dnssufix> \</dnssufix> \</dnssuffixes>** značky.

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

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>Návody do klienta VPN přidat vlastní servery DNS?

Můžete upravit stažený soubor XML profilu a přidat **\<dnsservers> \<dnsserver> \</dnsserver> \</dnsservers>** značky.

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
> Klient služby Azure AD OpenVPN využívá položky tabulky zásad překladu názvů DNS (NRPT), což znamená, že servery DNS nebudou uvedené ve výstupu `ipconfig /all` . Pokud chcete potvrdit nastavení používané v rámci služby DNS, podívejte se prosím do rutiny [Get-DnsClientNrptPolicy](/powershell/module/dnsclient/get-dnsclientnrptpolicy?view=win10-ps) v PowerShellu.
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>Návody přidat vlastní trasy k klientovi VPN?

Můžete upravit stažený soubor XML profilu a přidat **\<includeroutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</includeroutes>** značky.

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
### <a name="how-do-i-direct-all-traffic-to-the-vpn-tunnel-force-tunnel"></a>Návody směrovat veškerý provoz do tunelu VPN (vynucení tunelu)?

Můžete upravit stažený soubor XML profilu a přidat **\<includeroutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</includeroutes>** značky.

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>0.0.0.0</destination><mask>1</mask>
        </route>
        <route>
            <destination>128.0.0.0</destination><mask>1</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>Návody (vyloučit) trasy blokování z klienta VPN?

Můžete upravit stažený soubor XML profilu a přidat **\<excluderoutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</excluderoutes>** značky.

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
### <a name="can-i-import-the-profile-from-a-command-line-prompt"></a>Můžu importovat profil z příkazového řádku?

Profil můžete importovat z příkazového řádku tak, že do složky **%userprofile%\appdata\local\packages\ Microsoft.AzureVpn_8wekyb3d8bbwe \localstate** umístíte stažený soubor **azurevpnconfig.xml** a spustíte následující příkaz:

```
azurevpn -i azurevpnconfig.xml 
```
Pokud chcete vynutit, aby import použil i přepínač **-f**


## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [Vytvoření klienta Azure Active Directory pro P2S otevřená připojení VPN, která používají ověřování Azure AD](openvpn-azure-ad-tenant.md).