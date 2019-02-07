---
title: 'Vytvoření a instalace konfiguračních souborů klienta VPN pro připojení P2S RADIUS: PowerShell: Azure | Dokumentace Microsoftu'
description: Vytvoření klienta Windows, Mac OS X a Linux VPN konfigurační soubory pro připojení, která používají ověřování pomocí protokolu RADIUS.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/07/2018
ms.author: cherylmc
ms.openlocfilehash: 8fc2c487a374a34cd9a7642a45fd59c04061b398
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55817814"
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>Vytvoření a instalace konfiguračních souborů klienta VPN pro ověřování P2S pomocí protokolu RADIUS

Pro připojení k virtuální síti přes point-to-site (P2S), musíte nakonfigurovat klientské zařízení, ze kterého budete připojovat. Můžete vytvořit připojení P2S VPN z Windows, Mac OS X a Linux klientských zařízení. 

Při použití ověřování pomocí protokolu RADIUS, existuje několik možností ověřování: ověřování uživatelského jména a hesla, ověřování pomocí certifikátu a jiné typy ověřování. Konfigurace klienta VPN se liší pro jednotlivé typy ověřování. Klienta VPN nakonfigurujete pomocí konfiguračních souborů klienta, které obsahují požadovaná nastavení. Tento článek vám pomůže vytvořit a nainstalovat konfigurace klienta VPN pro typ ověřování RADIUS, který chcete použít.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

Pracovní postup konfigurace ověřování P2S RADIUS je následujícím způsobem:

1. [Nastavení brány Azure VPN pro připojení P2S](point-to-site-how-to-radius-ps.md).
2. [Nastavení serveru RADIUS pro ověřování](point-to-site-how-to-radius-ps.md#radius). 
3. **Získání konfigurace klienta VPN pro možnost ověřování podle vašeho výběru a použít ho k nastavení klienta VPN** (Tento článek).
4. [Dokončete konfiguraci P2S a připojte](point-to-site-how-to-radius-ps.md).

>[!IMPORTANT]
>Pokud jsou všechny změny konfigurace point-to-site VPN od vygenerování profil konfigurace klienta VPN, jako je protokol typu sítě VPN nebo typ ověřování, musíte vygenerovat a nainstalovat nové konfigurace klienta VPN na zařízeních uživatelů.
>
>

Pokud chcete použít části v tomto článku, nejdřív se rozhodněte, jaký typ ověřování, kterou chcete použít: uživatelského jména a hesla, certifikát nebo jiné typy ověřování. Každý oddíl obsahuje kroky pro Windows, Mac OS X a Linux (v tuto chvíli k dispozici omezené kroky).

## <a name="adeap"></a>Ověřování uživatelského jména a hesla

Můžete nakonfigurovat ověřování uživatelského jména a hesla pomocí služby Active Directory nebo nepoužívá služby Active Directory. Pomocí obou scénářích Ujistěte se, že všechny připojujících se uživatelů pověření uživatelského jména a hesla, které mohou být ověřeni pomocí protokolu RADIUS.

Když nakonfigurujete ověřování uživatelského jména a hesla, můžete vytvořit pouze konfigurace ověřování protokolu EAP-MSCHAPv2 uživatelského jména a hesla. V příkazech `-AuthenticationMethod` je `EapMSChapv2`.

### <a name="usernamefiles"></a> 1. Generovat konfiguračních souborů klienta VPN

Generovat konfiguračních souborů klienta VPN pro použití s ověřováním. Konfiguračních souborů klienta VPN můžete vygenerovat pomocí následujícího příkazu:

```powershell 
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
Spuštěním příkazu vrátí odkaz. Zkopírujte a vložte odkaz do webového prohlížeče a stáhněte **VpnClientConfiguration.zip**. Rozbalte soubor zobrazíte následující složky: 
 
* **WindowsAmd64** a **WindowsX86**: Tyto složky obsahují balíčky Instalační služby systému Windows 64-bit a 32-bit. 
* **Obecný**: Tato složka obsahuje obecné informace, které použijete k vytvoření vlastní konfigurace klienta VPN. Nepotřebujete tuto složku pro konfiguraci ověřování uživatelského jména a hesla.
* **Mac**: Pokud jste nakonfigurovali IKEv2, při vytváření brány virtuální sítě, zobrazí se složku s názvem **Mac** , která obsahuje **mobileconfig** souboru. Tento soubor použijete ke konfiguraci klienti se systémem Mac.

Pokud jste nevytvořili klienta konfigurační soubory, můžete je načíst s použitím `Get-AzureRmVpnClientConfiguration` rutiny. Ale pokud provedete změny konfigurace P2S VPN, jako je protokol typu sítě VPN nebo typ ověřování konfigurace se neaktualizuje automaticky. Je třeba spustit `New-AzureRmVpnClientConfiguration` rutiny vytvořte nový soubor ke stažení konfigurace.

Pokud chcete načíst dříve generovaného klienta konfigurační soubory, použijte následující příkaz:

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="setupusername"></a> 2. Konfigurace klientů VPN

Můžete nakonfigurovat následující klienti VPN:

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [Linux pomocí strongSwan](#adlinuxcli)
 
#### <a name="adwincli"></a>Instalace klienta Windows VPN

Na každém klientském počítači s Windows, můžete použít stejný konfigurační balíček klienta VPN, za předpokladu, že jeho verze odpovídá architektuře klienta. Seznam klientských operačních systémů, které jsou podporovány, naleznete v tématu [nejčastější dotazy k](vpn-gateway-vpn-faq.md#P2S).

Použijte následující postup ke konfiguraci nativního klienta VPN ve Windows pro ověření certifikátu:

1. Vyberte konfigurační soubory klienta VPN, které odpovídají architektuře počítače s Windows. Pro 64bitový procesor architekturu, vyberte **VpnClientSetupAmd64** balíček Instalační služby. Architektura procesoru 32-bit, zvolte **VpnClientSetupX86** balíček Instalační služby. 
2. Chcete-li nainstalovat balíček, poklepejte na něj. Pokud se zobrazí automaticky otevírané okno filtru SmartScreen, vyberte **informace** > **přesto spustit**.
3. Na klientském počítači přejděte do **nastavení sítě** a vyberte **VPN**. Připojení k síti VPN zobrazuje název virtuální sítě, ke které se připojuje. 

#### <a name="admaccli"></a>Instalace klienta VPN MAC (OS X)

1. Vyberte **VpnClientSetup mobileconfig** souboru a jeho odeslání do jednotlivých uživatelů. Můžete použít e-mailu nebo jiným způsobem.

2. Vyhledejte **mobileconfig** soubor na počítači Mac.

   ![Umístění souboru mobileconfig](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)

3. Volitelný krok – Pokud chcete zadat vlastní DNS, přidejte následující řádky do **mobileconfig** souboru:
```xml
    <key>DNS</key>
    <dict>
      <key>ServerAddresses</key>
        <array>
            <string>10.0.0.132</string>
        <array>
      <key>SupplementalMatchDomains</key>
        <array>
            <string>TestDomain.com</string>
        </array>
    </dict> 
```
4. Dvakrát klikněte na profil, který chcete nainstalovat a vyberte **pokračovat**. Název profilu je stejný jako název virtuální sítě.

   ![Zpráva instalace](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
5. Vyberte **pokračovat** důvěřovat odesílatele profilu a pokračujte v instalaci.

   ![Potvrzovací zpráva](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
6. Během instalace profilu máte možnost zadat uživatelské jméno a heslo pro ověřování sítě VPN. Není nutné zadat tyto informace. Pokud tak učiníte, informace jsou uloženy a automaticky použije při inicializaci připojení. Vyberte **nainstalovat** pokračovat.

   ![Pole uživatelského jména a hesla pro síť VPN](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
7. Zadejte uživatelské jméno a heslo pro oprávnění potřebná k instalaci profilu ve vašem počítači. Vyberte **OK**.

   ![Pole uživatelského jména a hesla pro instalace profilu](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
8. Po dokončení instalace profilu je viditelná ve **profily** dialogové okno. Můžete také otevřít toto dialogové později z **předvolby systému**.

   ![Dialogové okno "Profily"](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
9. Chcete-li získat přístup k připojení k síti VPN, otevřete **sítě** dialogové okno z **předvolby systému**.

   ![Ikony v předvolbách systému](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
10. Připojení k síti VPN se zobrazí jako **IkeV2 VPN**. Název můžete změnit aktualizací **mobileconfig** souboru.

   ![Podrobnosti o připojení k síti VPN](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
11. Vyberte **nastavení ověřování**. Vyberte **uživatelské jméno** v seznamu a zadejte svoje přihlašovací údaje. Pokud jste zadali přihlašovací údaje dříve, pak **uživatelské jméno** je automaticky jsou předem vybranou v seznamu a uživatelské jméno a heslo. Vyberte **OK** uložte nastavení.

    ![Nastavení ověřování](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
12. Zpátky **sítě** dialogovém okně vyberte **použít** a uložte změny. Chcete-li iniciovat připojení, vyberte **připojit**.

#### <a name="adlinuxcli"></a>Instalace klienta VPN systému Linux přes strongSwan

Prostřednictvím strongSwan 5.5.1 na Ubuntu 17.0.4 byly vytvořeny podle následujících pokynů. Skutečné obrazovky se může lišit v závislosti na vaší verzi operačních systémů Linux a strongSwan.

1. Otevřít **terminálu** instalace **strongSwan** a jeho správce sítě spuštěním příkazu v příkladu. Pokud se zobrazí chyba, která souvisí s `libcharon-extra-plugins`, nahraďte ho hodnotou `strongswan-plugin-eap-mschapv2`.

   ```Terminal
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Vyberte **správce sítě** ikona (šipka/dolů – šipku nahoru) a vyberte **upravit připojení**.

   ![Výběr "Upravit připojení" ve Správci sítě](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Vyberte **přidat** pro vytvoření nového připojení.

   !["Přidat" tlačítko pro připojení](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. Vyberte **protokolu IPsec nebo IKEv2 (strongswan)** z rozevírací nabídky a pak vyberte **vytvořit**. Připojení v tomto kroku můžete přejmenovat.

   ![Výběr typu připojení](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Otevřít **VpnSettings.xml** soubor **obecný** složky konfiguračních souborů klienta stažené. Požadovanou značku, volá `VpnServer` a zkopírujte název, počínaje `azuregateway` a konče `.cloudapp.net`.

   ![Obsah souboru VpnSettings.xml](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Vložte tento název do **adresu** pole nové připojení k síti VPN v **brány** oddílu. Potom vyberte ikonu složky na konci **certifikát** pole, přejděte na **obecný** a pak zvolte položku **VpnServerRoot** souboru.
7. V **klienta** části připojení vyberte **EAP** pro **ověřování**a zadejte uživatelské jméno a heslo. Možná budete muset vybrat ikonu zámku na pravé straně, chcete-li uložit tyto informace. Potom vyberte **Uložit**.

   ![Úprava nastavení připojení](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Vyberte **správce sítě** ikona (šipka/dolů – šipku nahoru) a podržte ukazatel myši nad **připojení k síti VPN**. Zobrazí se připojení VPN, které jste vytvořili. Chcete-li iniciovat připojení, vyberte ji.

   ![Připojení "VPN Radius" ve Správci sítě](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certeap"></a>Ověřování pomocí certifikátu
 
Klient VPN můžete vytvořit konfigurační soubory pro ověřování pomocí protokolu RADIUS certifikát, který používá protokol EAP-TLS. Certifikát vydala organizace obvykle slouží k ověřování uživatele pro síť VPN. Ujistěte se, že všechny připojujících se uživatelů certifikát nainstalovaný na jejich zařízeních, a, že váš server protokolu RADIUS můžete ověřit certifikát.

>[!NOTE]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

V příkazech `-AuthenticationMethod` je `EapTls`. Při ověřování certifikátu klienta ověří RADIUS server ověřením certifikátu. `-RadiusRootCert` je soubor .cer, který obsahuje kořenový certifikát, který se používá k ověření serveru RADIUS.

Každé zařízení klient VPN vyžaduje nainstalovaný klientský certifikát. Zařízení s Windows v některých případech má více klientských certifikátů. Během ověřování to může způsobit vyskakovací dialogové okno, které jsou uvedeny všechny certifikáty. Potom musí uživatel vybrat certifikát, který chcete použít. Dají se odfiltrovat správný certifikát zadáním kořenový certifikát, který by měly být zřetězené klientský certifikát. 

`-ClientRootCert` je soubor .cer, který obsahuje kořenový certifikát. Je volitelný parametr. Pokud zařízení, které chcete připojit z má pouze jeden klientský certifikát, není nutné tento parametr zadán.

### <a name="certfiles"></a>1. Generovat konfiguračních souborů klienta VPN

Generovat konfiguračních souborů klienta VPN pro použití s ověřováním pomocí certifikátu. Konfiguračních souborů klienta VPN můžete vygenerovat pomocí následujícího příkazu:
 
```powershell
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

Spuštěním příkazu vrátí odkaz. Zkopírujte a vložte odkaz do webového prohlížeče a stáhněte VpnClientConfiguration.zip. Rozbalte soubor zobrazíte následující složky:

* **WindowsAmd64** a **WindowsX86**: Tyto složky obsahují balíčky Instalační služby systému Windows 64-bit a 32-bit. 
* **GenericDevice**: Tato složka obsahuje obecné informace, které se používá k vytvoření vlastní konfigurace klienta VPN.

Pokud jste nevytvořili klienta konfigurační soubory, můžete je načíst s použitím `Get-AzureRmVpnClientConfiguration` rutiny. Ale pokud provedete změny konfigurace P2S VPN, jako je protokol typu sítě VPN nebo typ ověřování konfigurace se neaktualizuje automaticky. Je třeba spustit `New-AzureRmVpnClientConfiguration` rutiny vytvořte nový soubor ke stažení konfigurace.

Pokud chcete načíst dříve generovaného klienta konfigurační soubory, použijte následující příkaz:

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="setupusername"></a> 2. Konfigurace klientů VPN

Můžete nakonfigurovat následující klienti VPN:

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (podporováno, žádné články kroky ještě)

#### <a name="certwincli"></a>Instalace klienta Windows VPN

1. Vyberte balíček pro konfiguraci a nainstalujte ho na klientském zařízení. Pro 64bitový procesor architekturu, vyberte **VpnClientSetupAmd64** balíček Instalační služby. Architektura procesoru 32-bit, zvolte **VpnClientSetupX86** balíček Instalační služby. Pokud se zobrazí automaticky otevírané okno filtru SmartScreen, vyberte **informace** > **přesto spustit**. Můžete také balíček uložit k instalaci na další klientské počítače.
2. Každý klient vyžaduje klientský certifikát pro ověřování. Instalace klientského certifikátu. Informace o klientských certifikátů najdete v tématu [klientské certifikáty pro point-to-site](vpn-gateway-certificates-point-to-site.md). Pokud chcete nainstalovat certifikát, který byl vygenerován, naleznete v tématu [nainstalujte certifikát na klientech Windows](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Na klientském počítači přejděte do **nastavení sítě** a vyberte **VPN**. Připojení k síti VPN zobrazuje název virtuální sítě, ke které se připojuje.

#### <a name="certmaccli"></a>Instalace klienta VPN MAC (OS X)

Musíte vytvořit samostatný profil pro každé zařízení Mac, který se připojuje k virtuální síti Azure. Je to proto, že tato zařízení musí být uživatelský certifikát pro ověřování, aby zadaný v profilu. **Obecný** složka obsahuje všechny informace potřebné k vytvoření profilu:

* **VpnSettings.xml** obsahuje důležitá nastavení, jako je typ serveru adresu a tunelové propojení.
* **VpnServerRoot.cer** obsahuje kořenový certifikát, který je potřeba pro ověření během instalace připojení P2S VPN gateway.
* **RadiusServerRoot.cer** obsahuje kořenový certifikát, který je potřeba pro ověření serveru RADIUS během ověřování.

Použijte následující postup ke konfiguraci nativního klienta VPN v systému Mac pro ověření certifikátu:

1. Import **VpnServerRoot** a **RadiusServerRoot** kořenové certifikáty do vašeho macu. Každý soubor zkopírovat do počítače Mac, poklepejte na něj a pak vyberte **přidat**.

   ![Přidání certifikátu do VpnServerRoot](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

   ![Přidání certifikátu do RadiusServerRoot](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Každý klient vyžaduje klientský certifikát pro ověřování. Instalace klientského certifikátu na klientském zařízení.
3. Otevřít **sítě** dialogové okno pod **sítě Předvolby**. Vyberte **+** k vytvoření nového profilu připojení klienta VPN pro připojení P2S k virtuální síti Azure.

   **Rozhraní** hodnotu **VPN**a **typ sítě VPN** hodnotu **IKEv2**. Zadejte název profilu, který **název služby** a potom vyberte **vytvořit** k vytvoření profilu připojení klienta VPN.

   ![Informace o název rozhraní a služeb](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. V **obecný** složky, ze **VpnSettings.xml** soubor, zkopírujte **VpnServer** hodnota značky. Vložte tuto hodnotu v **adresa serveru** a **vzdáleného ID** polí profilu. Nechte **místní ID** pole prázdné.

   ![Informace o serveru](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. Vyberte **nastavení ověřování**a vyberte **certifikát**. 

   ![Nastavení ověřování](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. Klikněte na tlačítko **vyberte** vybrat certifikát, který chcete použít pro ověřování.

   ![Vybrat certifikát pro ověřování](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **Zvolte Identity** zobrazí seznam certifikáty, abyste lze vybírat. Vyberte správný certifikát a pak vyberte **pokračovat**.

   !["Zvolte identita" seznamu](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. V **místní ID** , zadejte název certifikátu (z kroku 6). V tomto příkladu má **ikev2Client.com**. Vyberte **použít** tlačítko Uložit změny.

   ![Pole "Místní Identifikátor"](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. V **sítě** dialogu **použít** uložte všechny změny. Vyberte **připojit** spustit připojení typu P2S k virtuální síti Azure.

## <a name="otherauth"></a>Práce s jinými typy ověřování nebo protokoly

Použít jiný typ ověřování (například ověřování jednorázovým HESLEM) nebo použít jinou ověřovací protokol (například přes protokol PEAP-MSCHAPv2 místo protokolu EAP-MSCHAPv2), musíte vytvořit vlastní profil konfigurace klienta VPN. Vytvořte profil, budete potřebovat informace, například IP adresa brány virtuální sítě, Typ tunelového propojení a děleného tunelového propojení trasy. Tyto informace můžete získat pomocí následujících kroků:

1. Použití `Get-AzureRmVpnClientConfiguration` rutiny pro generování konfigurace klienta VPN pro EapMSChapv2. Pokyny najdete v této části tohoto článku.

2. Rozbalte soubor VpnClientConfiguration.zip a hledejte **GenericDevice** složky. Ignorujte složky, které obsahují Windows instalačních programů pro 64bitové a 32bitové architektury.
 
3. **GenericDevice** složka obsahuje soubor XML s názvem **VpnSettings**. Tento soubor obsahuje všechny požadované informace:

   * **VpnServer**: Plně kvalifikovaný název domény služby Azure VPN gateway. Toto je adresa, která se klient připojuje k.
   * **VpnType**: Typ tunelového propojení, který použijete k připojení.
   * **Trasy**: Trasy, které je třeba nakonfigurovat ve svém profilu tak, aby se odesílají pouze provoz, který je vázaný virtuální síť Azure prostřednictvím tunelového připojení P2S.
   
   **GenericDevice** složka obsahuje také soubor .cer s názvem **VpnServerRoot**. Tento soubor obsahuje kořenový certifikát, který je potřeba ověřit Azure VPN gateway během instalace připojení P2S. Nainstalujte certifikát na všech zařízeních, které se připojují k virtuální síti Azure.

## <a name="next-steps"></a>Další postup

Vraťte se do článku do [dokončete konfiguraci P2S](point-to-site-how-to-radius-ps.md).

P2S informace, o odstraňování potíží naleznete v tématu [připojení point-to-site řešení potíží s Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
