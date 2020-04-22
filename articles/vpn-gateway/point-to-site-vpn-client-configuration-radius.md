---
title: 'Brána Azure VPN: Vytvoření & instalace konfiguračních souborů klienta VPN – připojení Radius P2S'
description: Vytvořte konfigurační soubory klienta Windows, Mac OS X a Linux VPN pro připojení, která používají ověřování RADIUS.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: 69517d69a26364cf1cc950d7aaa849522decacf1
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732744"
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>Vytvoření a instalace konfiguračních souborů klienta VPN pro ověřování Protokolu P2S RADIUS

Chcete-li se připojit k virtuální síti přes point-to-site (P2S), musíte nakonfigurovat klientské zařízení, ze kterého se budete připojovat. Připojení P2S VPN můžete vytvářet z klientských zařízení se systémem Windows, Mac OS X a Linux. 

Pokud používáte ověřování RADIUS, existuje několik možností ověřování: ověřování uživatelským jménem a heslem, ověřování certifikátů a další typy ověřování. Konfigurace klienta VPN se liší pro každý typ ověřování. Chcete-li nakonfigurovat klienta VPN, použijte konfigurační soubory klienta, které obsahují požadovaná nastavení. Tento článek vám pomůže vytvořit a nainstalovat konfiguraci klienta VPN pro typ ověřování RADIUS, který chcete použít.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

Konfigurační pracovní postup pro ověřování Radius P2S je následující:

1. [Nastavte bránu Azure VPN pro připojení P2S](point-to-site-how-to-radius-ps.md).
2. [Nastavte server RADIUS pro ověřování](point-to-site-how-to-radius-ps.md#radius). 
3. **Získejte konfiguraci klienta VPN pro možnost ověření podle vašeho výběru a použijte ji k nastavení klienta VPN** (tento článek).
4. [Dokončete konfiguraci P2S a připojte se](point-to-site-how-to-radius-ps.md).

>[!IMPORTANT]
>Pokud po vygenerování konfiguračního profilu klienta VPN dojde ke změnám v konfiguraci sítě VPN typu point-to-site, například typ protokolu VPN nebo typ ověřování, musíte vygenerovat a nainstalovat novou konfiguraci klienta VPN do zařízení uživatelů.
>
>

Chcete-li použít oddíly v tomto článku, nejprve se rozhodněte, jaký typ ověřování chcete použít: uživatelské jméno/heslo, certifikát nebo jiné typy ověřování. Každá část obsahuje kroky pro Windows, Mac OS X a Linux (v tuto chvíli jsou k dispozici omezené kroky).


## <a name="usernamepassword-authentication"></a><a name="adeap"></a>Ověření uživatelského jména/hesla

Ověřování uživatelským jménem a heslem můžete nakonfigurovat tak, aby používalo službu Active Directory nebo nepoužívalo službu Active Directory. V obou případech se ujistěte, že všichni připojení uživatelé mají uživatelské jméno nebo heslo pověření, které lze ověřit prostřednictvím RADIUS.

Při konfiguraci ověřování uživatelského jména a hesla můžete vytvořit pouze konfiguraci pro protokol ověřování uživatelského jména a hesla Protokolu EAP-MSCHAPv2. V příkazech `-AuthenticationMethod` je `EapMSChapv2`.

### <a name="1-generate-vpn-client-configuration-files"></a><a name="usernamefiles"></a>1. Generování konfiguračních souborů klienta VPN

Konfigurační soubory klienta VPN můžete generovat pomocí portálu Azure nebo pomocí Azure PowerShellu.

#### <a name="azure-portal"></a>portál Azure

1. Přejděte na bránu virtuální sítě.
2. Klepněte **na položku Konfigurace bodu k webu**.
3. Klepněte na tlačítko **Stáhnout klienta VPN**.
4. Vyberte klienta a vyplňte všechny požadované informace.
5. Chcete-li vygenerovat soubor ZIP, klepněte na **tlačítko Stáhnout.**
6. Soubor ZIP se stáhne, obvykle do složky Soubory ke stažení.

#### <a name="azure-powershell"></a>Azure PowerShell

Vygenerujte konfigurační soubory klienta VPN pro použití s ověřováním uživatelského jména a hesla. Konfigurační soubory klienta VPN můžete generovat pomocí následujícího příkazu:

```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
Spuštění příkazu vrátí odkaz. Zkopírujte a vložte odkaz do webového prohlížeče a stáhněte si **soubor VpnClientConfiguration.zip**. Chcete-li zobrazit následující složky, rozbalte soubor: 
 
* **WindowsAmd64** a **WindowsX86**: Tyto složky obsahují 64bitové a 32bitové instalační balíčky systému Windows. 
* **Obecný**: Tato složka obsahuje obecné informace, které používáte k vytvoření vlastní konfigurace klienta VPN. Tuto složku nepotřebujete pro konfigurace ověřování uživatelským jménem a heslem.
* **Mac**: Pokud jste při vytváření brány virtuální sítě nakonfigurovali IKEv2, zobrazí se složka s názvem **Mac,** která obsahuje soubor **mobileconfig.** Tento soubor slouží ke konfiguraci klientů Mac.

Pokud jste již vytvořili konfigurační soubory `Get-AzVpnClientConfiguration` klienta, můžete je načíst pomocí rutiny. Pokud však provedete nějaké změny konfigurace sítě P2S VPN, například typu protokolu VPN nebo typu ověřování, konfigurace se automaticky neaktualizuje. Chcete-li `New-AzVpnClientConfiguration` vytvořit novou konfiguraci ke stažení, je nutné rutinu spustit.

Chcete-li načíst dříve generované konfigurační soubory klienta, použijte následující příkaz:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a>2. Konfigurace klientů VPN

Můžete nakonfigurovat následující klienty VPN:

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [Linux pomocí strongSwan](#adlinuxcli)
 
#### <a name="windows-vpn-client-setup"></a><a name="adwincli"></a>Nastavení klienta VPN systému Windows

Můžete použít stejný balíček konfigurace klienta VPN v každém klientském počítači se systémem Windows, pokud verze odpovídá architektuře pro klienta. Seznam podporovaných klientských operačních systémů naleznete v [nejčastějších dotazech](vpn-gateway-vpn-faq.md#P2S).

Pomocí následujících kroků nakonfigurujte nativního klienta VPN systému Windows pro ověřování certifikátů:

1. Vyberte konfigurační soubory klienta VPN, které odpovídají architektuře počítače s Windows. Pro 64bitovou architekturu procesoru zvolte instalační balíček **VpnClientSetupAmd64.** Pro 32bitovou architekturu procesoru zvolte instalační balíček **VpnClientSetupX86.** 
2. Chcete-li balíček nainstalovat, poklepejte na něj. Pokud se zobrazí automaticky otevírané okno SmartScreen, vyberte přesto **spustit další** > **informace**.
3. V klientském počítači přejděte na **položku Nastavení sítě** a vyberte **možnost VPN**. Připojení k síti VPN zobrazuje název virtuální sítě, ke které se připojuje. 

#### <a name="mac-os-x-vpn-client-setup"></a><a name="admaccli"></a>Nastavení klienta VPN mac (OS X)

1. Vyberte soubor **VpnClientSetup mobileconfig** a odešlete jej každému uživateli. Můžete použít e-mail nebo jinou metodu.

2. Vyhledejte soubor **mobileconfig** na Macu.

   ![Umístění souboru mobileconfig](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)

3. Volitelný krok – Chcete-li zadat vlastní DNS, přidejte do souboru **mobileconfig** následující řádky:

   ```xml
    <key>DNS</key>
    <dict>
      <key>ServerAddresses</key>
        <array>
            <string>10.0.0.132</string>
        </array>
      <key>SupplementalMatchDomains</key>
        <array>
            <string>TestDomain.com</string>
        </array>
    </dict> 
   ```
4. Poklepejte na profil, chcete-li jej nainstalovat, a vyberte **pokračovat**. Název profilu je stejný jako název virtuální sítě.

   ![Zpráva o instalaci](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
5. Výběrem **možnosti Pokračovat** důvěřujte odesílateli profilu a pokračujte v instalaci.

   ![Potvrzovací zpráva](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
6. Během instalace profilu máte možnost zadat uživatelské jméno a heslo pro ověřování vpn. Není povinné zadávat tyto informace. Pokud tak učiníte, informace se uloží a automaticky použijí při navázání připojení.Chcete-li pokračovat, vyberte **možnost Instalovat.**

   ![Pole uživatelského jména a hesla pro VPN](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
7. Zadejte uživatelské jméno a heslo pro oprávnění, která jsou nutná k instalaci profilu do počítače. Vyberte **OK**.

   ![Pole uživatelského jména a hesla pro instalaci profilu](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
8. Po instalaci je profil viditelný v dialogovém okně **Profily.** Toto dialogové okno můžete také otevřít později z **programu Předvolby systému**.

   ![Dialogové okno Profily](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
9. Chcete-li získat přístup k připojení VPN, otevřete dialogové okno **Síť** z **programu Předvolby systému**.

   ![Ikony v předvolbách systému](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
10. Připojení VPN se zobrazí jako **IkeV2-VPN**. Název můžete změnit aktualizací souboru **mobileconfig.**

    ![Podrobnosti o připojení VPN](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
11. Vyberte **Nastavení ověřování**. V seznamu vyberte **Uživatelské jméno** a zadejte svá pověření. Pokud jste pověření zadali dříve, bude v seznamu automaticky vybráno **uživatelské jméno** a uživatelské jméno a heslo jsou předem vyplněny. Chcete-li nastavení uložit, vyberte **ok.**

    ![Nastavení ověřování](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
12. V dialogovém okně **Síť** vyberte **Použít,** chcete-li změny uložit. Chcete-li zahájit připojení, vyberte **možnost Připojit**.

#### <a name="linux-vpn-client-setup-through-strongswan"></a><a name="adlinuxcli"></a>Nastavení klienta Linux VPN přes strongSwan

Následující pokyny byly vytvořeny prostřednictvím strongSwan 5.5.1 na Ubuntu 17.0.4. Skutečné obrazovky se mohou lišit v závislosti na vaší verzi Linuxu a strongSwan.

1. Otevřete **terminál** pro instalaci **strongSwan** a jeho Network Manager spuštěním příkazu v příkladu. Pokud se zobrazí chyba, která `libcharon-extra-plugins`souvisí `strongswan-plugin-eap-mschapv2`s , nahraďte ji .

   ```Terminal
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Vyberte ikonu **Správce sítě** (šipka nahoru/šipka dolů) a vyberte **Upravit připojení**.

   ![Výběr "Upravit připojení" v programu Network Manager](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Chcete-li vytvořit nové připojení, vyberte tlačítko **Přidat.**

   ![Tlačítko "Přidat" pro připojení](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. V rozevírací nabídce vyberte **IPsec/IKEv2 (strongswan)** a pak vyberte **Vytvořit**. V tomto kroku můžete připojení přejmenovat.

   ![Výběr typu připojení](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Otevřete soubor **VpnSettings.xml** z **obecné** složky stažených konfiguračních souborů klienta. Najděte volanou `VpnServer` značku a `azuregateway` zkopírujte `.cloudapp.net`název, začínající a končící písmenem .

   ![Obsah souboru VpnSettings.xml](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Vložte tento název do pole **Adresa** nového připojení VPN v části **Brána.** Dále vyberte ikonu složky na konci pole **Certifikát,** přejděte do **obecné** složky a vyberte soubor **VpnServerRoot.**
7. V části **Klient** připojení vyberte **protokol EAP** pro **ověřování**a zadejte své uživatelské jméno a heslo. Chcete-li tyto informace uložit, bude pravděpodobně nutné vybrat ikonu zámku vpravo. Potom vyberte **Uložit**.

   ![Úpravy nastavení připojení](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Vyberte ikonu **Network Manager** (šipka nahoru/šipka dolů) a najeďte na **položku Připojení VPN**. Zobrazí se připojení VPN, které jste vytvořili. Chcete-li připojení zahájit, vyberte jej.

   ![Připojení "VPN Radius" ve správci sítě](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certificate-authentication"></a><a name="certeap"></a>Ověření certifikátu
 
Můžete vytvořit konfigurační soubory klienta VPN pro ověřování certifikátů RADIUS, které používají protokol EAP-TLS. Certifikát vydaný podnikem se obvykle používá k ověření uživatele pro síť VPN. Ujistěte se, že všichni připojení uživatelé mají certifikát nainstalovaný na svých zařízeních, a že server RADIUS můžete ověřit certifikát.

>[!NOTE]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

V příkazech `-AuthenticationMethod` je `EapTls`. Během ověřování certifikátu klient ověří server RADIUS ověřením jeho certifikátu. `-RadiusRootCert`je soubor CER obsahující kořenový certifikát, který se používá k ověření serveru RADIUS.

Každé klientské zařízení VPN vyžaduje nainstalovaný klientský certifikát. Někdy má zařízení se systémem Windows více klientských certifikátů. Během ověřování to může mít za následek vyskakovací dialogové okno se seznamem všech certifikátů. Uživatel pak musí zvolit certifikát, který chcete použít. Správný certifikát lze odfiltrovat zadáním kořenového certifikátu, ke kterému by měl klientský certifikát zřetězit. 

`-ClientRootCert`je soubor CER, který obsahuje kořenový certifikát. Je to volitelný parametr. Pokud zařízení, ze kterého se chcete připojit, má pouze jeden klientský certifikát, není nutné tento parametr zadávat.

### <a name="1-generate-vpn-client-configuration-files"></a><a name="certfiles"></a>1. Generování konfiguračních souborů klienta VPN

Vygenerujte konfigurační soubory klienta VPN pro použití s ověřováním certifikátů. Konfigurační soubory klienta VPN můžete generovat pomocí následujícího příkazu:
 
```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

Spuštění příkazu vrátí odkaz. Zkopírujte a vložte odkaz do webového prohlížeče a stáhněte si soubor VpnClientConfiguration.zip. Chcete-li zobrazit následující složky, rozbalte soubor:

* **WindowsAmd64** a **WindowsX86**: Tyto složky obsahují 64bitové a 32bitové instalační balíčky systému Windows. 
* **GenericDevice**: Tato složka obsahuje obecné informace, které se používají k vytvoření vlastní konfigurace klienta VPN.

Pokud jste již vytvořili konfigurační soubory `Get-AzVpnClientConfiguration` klienta, můžete je načíst pomocí rutiny. Pokud však provedete nějaké změny konfigurace sítě P2S VPN, například typu protokolu VPN nebo typu ověřování, konfigurace se automaticky neaktualizuje. Chcete-li `New-AzVpnClientConfiguration` vytvořit novou konfiguraci ke stažení, je nutné rutinu spustit.

Chcete-li načíst dříve generované konfigurační soubory klienta, použijte následující příkaz:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a>2. Konfigurace klientů VPN

Můžete nakonfigurovat následující klienty VPN:

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (podporováno, zatím žádné kroky k článku)

#### <a name="windows-vpn-client-setup"></a><a name="certwincli"></a>Nastavení klienta VPN systému Windows

1. Vyberte konfigurační balíček a nainstalujte jej do klientského zařízení. Pro 64bitovou architekturu procesoru zvolte instalační balíček **VpnClientSetupAmd64.** Pro 32bitovou architekturu procesoru zvolte instalační balíček **VpnClientSetupX86.** Pokud se zobrazí automaticky otevírané okno SmartScreen, vyberte přesto **spustit další** > **informace**. Můžete také balíček uložit k instalaci na další klientské počítače.
2. Každý klient vyžaduje pro ověřování klientský certifikát. Nainstalujte klientský certifikát. Informace o klientských certifikátech naleznete [v tématu Klientské certifikáty pro bod k webu](vpn-gateway-certificates-point-to-site.md). Informace o instalaci certifikátu, který byl vygenerován, naleznete [v tématu Instalace certifikátu v klientech systému Windows](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. V klientském počítači přejděte na **položku Nastavení sítě** a vyberte **možnost VPN**. Připojení k síti VPN zobrazuje název virtuální sítě, ke které se připojuje.

#### <a name="mac-os-x-vpn-client-setup"></a><a name="certmaccli"></a>Nastavení klienta VPN mac (OS X)

Musíte vytvořit samostatný profil pro každé zařízení Mac, které se připojuje k virtuální síti Azure. Důvodem je, že tato zařízení vyžadují, aby byl v profilu zadán uživatelský certifikát pro ověřování. **Obecná** složka obsahuje všechny informace potřebné k vytvoření profilu:

* **Soubor VPNSettings.xml** obsahuje důležitá nastavení, například adresu serveru a typ tunelového propojení.
* **Soubor VpnServerRoot.cer** obsahuje kořenový certifikát, který je nutný k ověření brány VPN během nastavení připojení P2S.
* **RadiusServerRoot.cer** obsahuje kořenový certifikát, který je nutný k ověření serveru RADIUS během ověřování.

Pomocí následujících kroků nakonfigurujte nativního klienta VPN na Macu pro ověřování certifikátů:

1. Importujte kořenové certifikáty **VpnServerRoot** a **RadiusServerRoot** do macu. Zkopírujte každý soubor do Macu, poklikejte na něj a pak vyberte **Přidat**.

   ![Přidání certifikátu VpnServerRoot](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

   ![Přidání certifikátu RadiusServerRoot](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Každý klient vyžaduje pro ověřování klientský certifikát. Nainstalujte klientský certifikát do klientského zařízení.
3. Otevřete dialogové okno **Síť** v části **Předvolby sítě**. Tuto **+** možnost vyberte, chcete-li vytvořit nový profil připojení klienta VPN pro připojení P2S k virtuální síti Azure.

   Hodnota **rozhraní** je **VPN**a hodnota **Typu VPN** je **IKEv2**. Do pole **Název služby** zadejte název profilu a pak vyberte **Vytvořit,** chcete-li vytvořit profil připojení klienta VPN.

   ![Informace o rozhraní a názvu služby](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. V **obecné** složce zkopírujte ze souboru **VpnSettings.xml** hodnotu značky **VpnServer.** Vložte tuto hodnotu do polí **Adresa serveru** a **Vzdálené ID** profilu. Pole **Místní id** ponechejte prázdné.

   ![Informace o serveru](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. Vyberte **Nastavení ověřování**a vyberte **Certifikát**. 

   ![Nastavení ověřování](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. Kliknutím na **Vybrat** vyberte certifikát, který chcete použít pro ověřování.

   ![Výběr certifikátu pro ověřování](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **Zvolte Identita** zobrazí seznam certifikátů, ze kterých si můžete vybrat. Vyberte správný certifikát a pak vyberte **Pokračovat**.

   ![Seznam "Zvolit identitu"](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. Do pole **Místní ID** zadejte název certifikátu (z kroku 6). V tomto příkladu je **ikev2Client.com**. Potom vyberte tlačítko **Použít** pro uložení změn.

   ![Pole "Místní ID"](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. V dialogovém okně **Síť** vyberte **Použít,** chcete-li uložit všechny změny. Potom vyberte **Připojit** a spusťte připojení P2S k virtuální síti Azure.

## <a name="working-with-other-authentication-types-or-protocols"></a><a name="otherauth"></a>Práce s jinými typy ověřování nebo protokoly

Chcete-li použít jiný typ ověřování (například OTP) nebo použít jiný ověřovací protokol (například PEAP-MSCHAPv2 místo EAP-MSCHAPv2), musíte vytvořit vlastní profil konfigurace klienta VPN. K vytvoření profilu potřebujete informace, jako je ip adresa brány virtuální sítě, typ tunelu a trasy rozděleného tunelového propojení. Tyto informace můžete získat pomocí následujících kroků:

1. Pomocí `Get-AzVpnClientConfiguration` rutiny vygenerujte konfiguraci klienta VPN pro EapMSChapv2.

2. Rozbalte soubor VpnClientConfiguration.zip a vyhledejte složku **GenericDevice.** Ignorujte složky, které obsahují instalační programy systému Windows pro 64bitové a 32bitové architektury.
 
3. Složka **GenericDevice** obsahuje soubor XML s názvem **VpnSettings**. Tento soubor obsahuje všechny požadované informace:

   * **VpnServer**: FQDN brány Azure VPN. Toto je adresa, ke které se klient připojuje.
   * **VpnType**: Typ tunelového propojení, který používáte pro připojení.
   * **Trasy**: Trasy, které je nutné nakonfigurovat ve svém profilu tak, aby pouze přenosy, které jsou vázány pro virtuální síť Azure je odeslána přes tunelové propojení P2S.
   
   Složka **GenericDevice** také obsahuje soubor CER s názvem **VpnServerRoot**. Tento soubor obsahuje kořenový certifikát, který je nutný k ověření brány Azure VPN během nastavení připojení P2S. Nainstalujte certifikát na všechna zařízení, která se připojí k virtuální síti Azure.

## <a name="next-steps"></a>Další kroky

Vraťte se do článku a [dokončete konfiguraci P2S](point-to-site-how-to-radius-ps.md).

Informace o řešení potíží s P2S najdete [v tématu Poradce při potížích s připojením Azure point-to-site](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
