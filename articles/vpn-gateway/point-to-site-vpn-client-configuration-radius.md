---
title: 'Azure VPN Gateway: vytvoření & instalace konfiguračních souborů klienta VPN – připojení RADIUS P2S'
description: Vytvořte konfigurační soubory klienta VPN systému Windows, OS X a Linux pro připojení, která používají ověřování pomocí protokolu RADIUS.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: e6d811e19bb19c8c8bf96764cfcca2b1294f4a85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91440065"
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>Vytvoření a instalace konfiguračních souborů klienta VPN pro ověřování RADIUS P2S

Pokud se chcete připojit k virtuální síti přes Point-to-Site (P2S), musíte nakonfigurovat klientské zařízení, ze kterého se budete připojovat. Můžete vytvořit P2S připojení VPN z klientských zařízení s Windows, OS X a Linux. 

Pokud používáte ověřování RADIUS, je k dispozici více možností ověřování: ověřování uživatelského jména a hesla, ověřování certifikátů a další typy ověřování. Konfigurace klienta VPN se liší pro každý typ ověřování. Chcete-li nakonfigurovat klienta VPN, použijte konfigurační soubory klienta, které obsahují požadovaná nastavení. Tento článek vám pomůže vytvořit a nainstalovat konfiguraci klienta VPN pro typ ověřování RADIUS, který chcete použít.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

Pracovní postup konfigurace pro ověřování RADIUS P2S je následující:

1. [Nastavte službu Azure VPN Gateway pro připojení P2S](point-to-site-how-to-radius-ps.md).
2. [Nastavte server RADIUS pro ověřování](point-to-site-how-to-radius-ps.md#radius). 
3. **Získejte konfiguraci klienta VPN pro zvolenou možnost ověřování a použijte ji k nastavení klienta VPN** (Tento článek).
4. [Dokončete konfiguraci P2S a připojte](point-to-site-how-to-radius-ps.md)se.

>[!IMPORTANT]
>Pokud v konfiguraci sítě VPN typu Point-to-site dojde ke změnám po vygenerování konfiguračního profilu klienta VPN, jako je například typ protokolu VPN nebo typ ověřování, musíte na zařízení uživatelů vygenerovat a nainstalovat novou konfiguraci klienta VPN.
>
>

Chcete-li použít části v tomto článku, nejprve se rozhodněte, jaký typ ověřování chcete použít: uživatelské jméno/heslo, certifikát nebo jiné typy ověřování. Každá část obsahuje kroky pro Windows, OS X a Linux (k dispozici jsou jenom omezené kroky).


## <a name="usernamepassword-authentication"></a><a name="adeap"></a>Ověřování uživatelského jména a hesla

Ověřování uživatelského jména a hesla můžete nakonfigurovat buď tak, že použijete službu Active Directory, nebo nechcete používat službu Active Directory. V obou případech se ujistěte, že všichni připojující uživatelé mají přihlašovací údaje k uživatelskému jménu nebo heslu, které se dají ověřit prostřednictvím protokolu RADIUS.

Když konfigurujete ověřování uživatelského jména a hesla, můžete vytvořit pouze konfiguraci pro EAP-MSCHAPv2 protokol pro ověřování uživatelského jména a hesla. V příkazech `-AuthenticationMethod` je `EapMSChapv2` .

### <a name="1-generate-vpn-client-configuration-files"></a><a name="usernamefiles"></a> 1. generování konfiguračních souborů klienta VPN

Konfigurační soubory klienta VPN můžete vygenerovat pomocí Azure Portal nebo pomocí Azure PowerShell.

#### <a name="azure-portal"></a>portál Azure

1. Přejděte do brány virtuální sítě.
2. Klikněte na **Konfigurace Point-to-site**.
3. Klikněte na **stáhnout klienta VPN**.
4. Vyberte klienta a vyplňte všechny požadované informace.
5. Kliknutím na **Stáhnout** vygenerujte soubor. zip.
6. Soubor. zip se stáhne obvykle do složky Stažené soubory.

#### <a name="azure-powershell"></a>Azure PowerShell

Vygenerujte konfigurační soubory klienta VPN pro použití s ověřováním uživatelského jména a hesla. Konfigurační soubory klienta VPN můžete vygenerovat pomocí následujícího příkazu:

```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
Spuštění příkazu vrátí odkaz. Zkopírujte a vložte odkaz na webový prohlížeč a stáhněte **VpnClientConfiguration.zip**. Rozbalte soubor pro zobrazení následujících složek: 
 
* **WindowsAmd64** a **WindowsX86**: tyto složky obsahují instalační balíčky Windows 64-bit a 32 instalačních balíčků. 
* **Obecné**: Tato složka obsahuje obecné informace, které můžete použít k vytvoření vlastní konfigurace klienta VPN. Tuto složku nepotřebujete pro konfigurace ověřování uživatelského jména a hesla.
* **Mac**: Pokud jste nakonfigurovali IKEv2 při vytváření brány virtuální sítě, zobrazí se složka s názvem **Mac** , která obsahuje soubor **mobileconfig** . Tento soubor použijete ke konfiguraci klientů se systémem Mac.

Pokud jste již vytvořili konfigurační soubory klienta, můžete je načíst pomocí `Get-AzVpnClientConfiguration` rutiny. Pokud však provedete jakékoli změny v konfiguraci sítě VPN P2S, například typ protokolu sítě VPN nebo typ ověřování, konfigurace se automaticky neaktualizuje. Chcete-li `New-AzVpnClientConfiguration` vytvořit nové stažení konfigurace, je nutné spustit rutinu.

K načtení dříve generovaných konfiguračních souborů klienta použijte následující příkaz:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a> 2. konfigurace klientů VPN

Můžete nakonfigurovat tyto klienty VPN:

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [Linux s využitím klient strongswan](#adlinuxcli)
 
#### <a name="windows-vpn-client-setup"></a><a name="adwincli"></a>Instalace klienta sítě VPN systému Windows

Stejný konfigurační balíček klienta VPN můžete použít na každém klientském počítači s Windows, pokud verze odpovídá architektuře pro klienta. Seznam podporovaných klientských operačních systémů najdete v části [Nejčastější dotazy](vpn-gateway-vpn-faq.md#P2S).

Pomocí následujících kroků nakonfigurujte nativního klienta VPN systému Windows pro ověřování certifikátů:

1. Vyberte konfigurační soubory klienta VPN, které odpovídají architektuře počítače s Windows. V případě architektury 64 procesor vyberte balíček Instalační služby **VpnClientSetupAmd64** . V případě architektury 32 procesor vyberte balíček Instalační služby **VpnClientSetupX86** . 
2. Balíček nainstalujete tak, že na něj dvakrát kliknete. Pokud se zobrazí automaticky otevírané okno filtru SmartScreen, klikněte na **Další informace**  >  **spustit i přesto**.
3. V klientském počítači přejděte na **nastavení sítě** a vyberte **síť VPN**. Připojení k síti VPN zobrazuje název virtuální sítě, ke které se připojuje. 

#### <a name="mac-os-x-vpn-client-setup"></a><a name="admaccli"></a>Nastavení klienta VPN pro Mac (OS X)

1. Vyberte soubor **VpnClientSetup mobileconfig** a odešlete ho každému uživateli. Můžete použít e-mail nebo jinou metodu.

2. Vyhledejte soubor **mobileconfig** na počítači Mac.

   ![Umístění souboru mobileconfig](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)

3. Volitelný krok – Pokud chcete zadat vlastní DNS, přidejte do souboru **mobileconfig** následující řádky:

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
4. Dvojím kliknutím na profil ho nainstalujte a vyberte **pokračovat**. Název profilu je stejný jako název vaší virtuální sítě.

   ![Zpráva instalace](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
5. Vyberte **pokračovat** pro důvěřování odesilateli profilu a pokračujte v instalaci.

   ![Potvrzovací zpráva](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
6. Během instalace profilu máte možnost zadat uživatelské jméno a heslo pro ověřování pomocí sítě VPN. Tyto informace není nutné zadávat. Pokud tak učiníte, informace se uloží a automaticky se použijí při zahájení připojení. Pokračujte výběrem **nainstalovat** .

   ![Pole uživatelského jména a hesla pro síť VPN](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
7. Zadejte uživatelské jméno a heslo pro oprávnění, která jsou vyžadována pro instalaci profilu na vašem počítači. Vyberte **OK**.

   ![Pole uživatelského jména a hesla pro instalaci profilu](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
8. Po instalaci se profil zobrazí v dialogovém okně **profily** . Toto dialogové okno můžete také otevřít později v části **Předvolby systému**.

   ![Dialogové okno profily](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
9. Chcete-li získat přístup k připojení k síti VPN, otevřete dialogové okno **síť** v části **Předvolby systému**.

   ![Ikony v systémových preferencích](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
10. Připojení VPN se zobrazuje jako **IkeV2-VPN**. Název můžete změnit tak, že aktualizujete soubor **mobileconfig** .

    ![Podrobnosti o připojení VPN](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
11. Vyberte **nastavení ověřování**. V seznamu vyberte **uživatelské jméno** a zadejte svoje přihlašovací údaje. Pokud jste dříve zadali přihlašovací údaje, pak se **uživatelské jméno** automaticky vybere v seznamu a uživatelské jméno a heslo budou předem vyplněné. Kliknutím na **OK** uložte nastavení.

    ![Snímek obrazovky, který zobrazuje rozevírací seznam "nastavení ověřování" s vybraným názvem "username".](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
12. Zpátky v dialogovém okně **síť** vyberte **použít** a uložte změny. Připojení spustíte tak, že vyberete **připojit**.

#### <a name="linux-vpn-client-setup-through-strongswan"></a><a name="adlinuxcli"></a>Nastavení klienta VPN pro Linux prostřednictvím klient strongswan

Následující pokyny byly vytvořeny prostřednictvím klient strongswan 5.5.1 na Ubuntu 17.0.4. Vlastní obrazovky se můžou lišit v závislosti na vaší verzi Linux a klient strongswan.

1. Otevřete **terminál** pro instalaci **klient strongswan** a jeho správce sítě spuštěním příkazu v příkladu. Pokud se zobrazí chyba, která souvisí s `libcharon-extra-plugins` , nahraďte ji `strongswan-plugin-eap-mschapv2` .

   ```Terminal
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Vyberte ikonu **správce sítě** (šipka nahoru/dolů) a pak vyberte **Upravit připojení**.

   ![Výběr možnosti upravit připojení ve Správci sítě](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Kliknutím na tlačítko **Přidat** vytvořte nové připojení.

   ![Tlačítko Přidat pro připojení](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. V rozevírací nabídce vyberte **IPSec/IKEv2 (klient strongswan)** a pak vyberte **vytvořit**. V tomto kroku můžete připojení přejmenovat.

   ![Výběr typu připojení](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Otevřete soubor **VpnSettings.xml** z **Obecné** složky stažených konfiguračních souborů klienta. Najděte značku s názvem `VpnServer` a zkopírujte název, který začíná `azuregateway` a končí `.cloudapp.net` .

   ![Obsah souboru VpnSettings.xml](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Tento název vložte do pole **adresa** nového připojení k síti VPN v části **Brána** . V dalším kroku vyberte ikonu složky na konci pole **certifikát** , přejděte do složky **Obecné** a vyberte soubor **VpnServerRoot** .
7. V části **klient** tohoto připojení vyberte **EAP** pro **ověřování** a zadejte své uživatelské jméno a heslo. Je možné, že budete muset vybrat ikonu zámku na pravé straně a uložit tyto informace. Pak vyberte **Uložit**.

   ![Úprava nastavení připojení](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Vyberte ikonu **správce sítě** (šipka nahoru/dolů) a najeďte myší na **připojení VPN**. Zobrazí se připojení VPN, které jste vytvořili. Pokud chcete připojení iniciovat, vyberte ho.

   ![Připojení VPN RADIUS ve Správci sítě](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certificate-authentication"></a><a name="certeap"></a>Ověření certifikátu
 
Můžete vytvořit konfigurační soubory klienta VPN pro ověřování certifikátů RADIUS, které používá protokol EAP-TLS. Obvykle se certifikát vydaný podnikem používá k ověření uživatele pro síť VPN. Ujistěte se, že všichni připojující se uživatelé mají na svých zařízeních nainstalovaný certifikát a že váš server RADIUS může certifikát ověřit.

>[!NOTE]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

V příkazech `-AuthenticationMethod` je `EapTls` . Při ověřování certifikátu klient ověří server RADIUS ověřením jeho certifikátu. `-RadiusRootCert` je soubor. cer obsahující kořenový certifikát, který se používá k ověření serveru RADIUS.

Každé klientské zařízení VPN vyžaduje nainstalovaný klientský certifikát. V některých případech má zařízení s Windows více klientských certifikátů. Při ověřování může to mít za následek místní dialogové okno, ve kterém jsou uvedené všechny certifikáty. Uživatel pak musí zvolit certifikát, který chcete použít. Správný certifikát lze odfiltrovat zadáním kořenového certifikátu, kterému má klientský certifikát zřetězit. 

`-ClientRootCert` je soubor. CER, který obsahuje kořenový certifikát. Je to volitelný parametr. Pokud zařízení, ke kterému se chcete připojit, má jenom jeden certifikát klienta, nemusíte tento parametr zadávat.

### <a name="1-generate-vpn-client-configuration-files"></a><a name="certfiles"></a>1. generování konfiguračních souborů klienta VPN

Vygenerujte konfigurační soubory klienta VPN pro použití s ověřováním certifikátů. Konfigurační soubory klienta VPN můžete vygenerovat pomocí následujícího příkazu:
 
```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

Spuštění příkazu vrátí odkaz. Zkopírujte a vložte odkaz na webový prohlížeč a stáhněte VpnClientConfiguration.zip. Rozbalte soubor pro zobrazení následujících složek:

* **WindowsAmd64** a **WindowsX86**: tyto složky obsahují instalační balíčky Windows 64-bit a 32 instalačních balíčků. 
* **GenericDevice**: Tato složka obsahuje obecné informace, které se používají k vytvoření vlastní konfigurace klienta VPN.

Pokud jste již vytvořili konfigurační soubory klienta, můžete je načíst pomocí `Get-AzVpnClientConfiguration` rutiny. Pokud však provedete jakékoli změny v konfiguraci sítě VPN P2S, například typ protokolu sítě VPN nebo typ ověřování, konfigurace se automaticky neaktualizuje. Chcete-li `New-AzVpnClientConfiguration` vytvořit nové stažení konfigurace, je nutné spustit rutinu.

K načtení dříve generovaných konfiguračních souborů klienta použijte následující příkaz:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a> 2. konfigurace klientů VPN

Můžete nakonfigurovat tyto klienty VPN:

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (podporováno, zatím žádné kroky v článcích)

#### <a name="windows-vpn-client-setup"></a><a name="certwincli"></a>Instalace klienta sítě VPN systému Windows

1. Vyberte konfigurační balíček a nainstalujte ho do klientského zařízení. V případě architektury 64 procesor vyberte balíček Instalační služby **VpnClientSetupAmd64** . V případě architektury 32 procesor vyberte balíček Instalační služby **VpnClientSetupX86** . Pokud se zobrazí automaticky otevírané okno filtru SmartScreen, klikněte na **Další informace**  >  **spustit i přesto**. Můžete také balíček uložit k instalaci na další klientské počítače.
2. Každý klient vyžaduje klientský certifikát pro ověření. Nainstalujte certifikát klienta. Informace o klientských certifikátech najdete v tématu [klientské certifikáty pro Point-to-site](vpn-gateway-certificates-point-to-site.md). Informace o instalaci vygenerovaného certifikátu najdete v tématu [instalace certifikátu na klienty Windows](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. V klientském počítači přejděte na **nastavení sítě** a vyberte **síť VPN**. Připojení k síti VPN zobrazuje název virtuální sítě, ke které se připojuje.

#### <a name="mac-os-x-vpn-client-setup"></a><a name="certmaccli"></a>Nastavení klienta VPN pro Mac (OS X)

Pro každé zařízení Mac, které se připojuje ke službě Azure Virtual Network, musíte vytvořit samostatný profil. Důvodem je to, že tato zařízení vyžadují, aby byl v profilu zadaný uživatelský certifikát pro ověření. **Obecná** složka obsahuje všechny informace, které jsou nutné k vytvoření profilu:

* **VpnSettings.xml** obsahuje důležitá nastavení, jako je například adresa serveru a typ tunelového propojení.
* **VpnServerRoot. cer** obsahuje kořenový certifikát, který je potřeba k ověření brány VPN během nastavení připojení P2S.
* **RadiusServerRoot. cer** obsahuje kořenový certifikát, který je při ověřování nutný k ověření serveru RADIUS.

Pomocí následujících kroků nakonfigurujete nativního klienta VPN na Macu pro ověřování certifikátů:

1. Importujte kořenové certifikáty **VpnServerRoot** a **RadiusServerRoot** do svého počítače Mac. Zkopírujte každý soubor na počítač Mac, dvakrát na něj klikněte a pak vyberte **Přidat**.

   ![Přidání certifikátu VpnServerRoot](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

   ![Přidání certifikátu RadiusServerRoot](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Každý klient vyžaduje klientský certifikát pro ověření. Nainstalujte klientský certifikát do klientského zařízení.
3. V části **Předvolby sítě** otevřete dialogové okno **síť** . Tuto možnost vyberte **+** , pokud chcete vytvořit nový profil připojení klienta VPN pro připojení P2S ke službě Azure Virtual Network.

   Hodnota **rozhraní** je **VPN** a **typ sítě VPN** je **IKEv2**. V poli **název služby** zadejte název profilu a pak vyberte **vytvořit** a vytvořte profil připojení klienta VPN.

   ![Informace o rozhraní a názvu služby](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. V **Obecné** složce v souboru **VpnSettings.xml** Zkopírujte hodnotu značky **VpnServer** . Vložte tuto hodnotu do polí **Adresa serveru** a **vzdálený identifikátor** v profilu. Pole **místní ID** nechte prázdné.

   ![Informace o serveru](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. Vyberte **nastavení ověřování** a vyberte **certifikát**. 

   ![Nastavení ověřování](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. Klikněte na **Vybrat** a vyberte certifikát, který chcete použít pro ověřování.

   ![Výběr certifikátu pro ověřování](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **Volba identity** zobrazí seznam certifikátů, ze kterých si můžete vybrat. Vyberte vhodný certifikát a pak vyberte **pokračovat**.

   ![Seznam zvolit identitu](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. Do pole **místní ID** zadejte název certifikátu (z kroku 6). V tomto příkladu je to **ikev2Client.com**. Pak kliknutím na tlačítko **použít** změny uložte.

   ![Pole místní ID](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. V dialogovém okně **síť** vyberte **použít** a uložte všechny změny. Pak vyberte **připojit** a spusťte připojení P2S k virtuální síti Azure.

## <a name="working-with-other-authentication-types-or-protocols"></a><a name="otherauth"></a>Práce s jinými typy ověřování nebo protokoly

Chcete-li použít jiný typ ověřování (například jednorázové heslo) nebo použít jiný ověřovací protokol (například PEAP-MSCHAPv2 namísto protokolu EAP-MSCHAPv2), je nutné vytvořit vlastní konfigurační profil klienta VPN. Chcete-li vytvořit profil, potřebujete informace, jako je například IP adresa brány virtuální sítě, typ tunelu a trasy děleného tunelového propojení. Tyto informace můžete získat pomocí následujících kroků:

1. Pomocí `Get-AzVpnClientConfiguration` rutiny vygenerujte konfiguraci klienta VPN pro EapMSChapv2.

2. Rozbalte soubor VpnClientConfiguration.zip a vyhledejte složku **GenericDevice** . Ignorujte složky, které obsahují instalační programy Windows pro 64 bitové a 32 architektury.
 
3. Složka **GenericDevice** obsahuje soubor XML s názvem **VpnSettings**. Tento soubor obsahuje všechny požadované informace:

   * **VpnServer**: plně kvalifikovaný název domény služby Azure VPN Gateway. Toto je adresa, ke které se klient připojuje.
   * **VpnType**: typ tunelu, který používáte pro připojení.
   * **Trasy**: trasy, které musíte nakonfigurovat ve vašem profilu, aby se přes tunel P2S odesílal jenom provoz, který je vázaný pro virtuální síť Azure.
   
   Složka **GenericDevice** obsahuje také soubor. cer s názvem **VpnServerRoot**. Tento soubor obsahuje kořenový certifikát, který je potřeba k ověření brány Azure VPN Gateway během nastavení připojení P2S. Nainstalujte certifikát do všech zařízení, která se budou připojovat ke službě Azure Virtual Network.

## <a name="next-steps"></a>Další kroky

Vraťte se k článku a [dokončete konfiguraci P2S](point-to-site-how-to-radius-ps.md).

Informace o řešení potíží s P2S najdete v tématu [řešení potíží s připojením Point-to-site v Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
