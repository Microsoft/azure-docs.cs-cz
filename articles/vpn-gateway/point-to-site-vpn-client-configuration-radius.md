---
title: 'Vytvoření a instalace souborů konfigurace klienta VPN pro připojení P2S RADIUS: prostředí PowerShell: Azure | Microsoft Docs'
description: Vytvoření klienta Windows a Mac OS X, Linux VPN konfigurační soubory pro připojení, které používají ověřování pomocí protokolu RADIUS.
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
ms.openlocfilehash: 19b1090a37ae1f97537fcabe128e7958fc26a96a
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235885"
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>Vytvoření a instalace souborů konfigurace klienta VPN pro ověřování pomocí protokolu RADIUS P2S

Připojení k virtuální síti přes point-to-site (P2S), musíte nakonfigurovat klientského zařízení, které budete připojení z. Můžete vytvořit připojení k síti VPN P2S z Windows, Mac OS X a Linux klientských zařízení. 

Pokud používáte ověřování pomocí protokolu RADIUS, existuje více možností ověřování: ověřování uživatelského jména a hesla, ověřování pomocí certifikátu a dalších typů ověřování. Konfigurace klienta VPN se liší pro jednotlivé typy ověřování. Ke konfiguraci klienta VPN, je použít klienta konfigurační soubory, které obsahují požadovaná nastavení. Tento článek vám pomůže vytvořit a instalovat konfigurace klienta VPN pro typ ověřování RADIUS, který chcete použít.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

Pracovní postup konfigurace ověřování P2S RADIUS vypadá takto:

1. [Nastaví bránu Azure VPN pro připojení P2S](point-to-site-how-to-radius-ps.md).
2. [Nastavit server RADIUS pro ověřování](point-to-site-how-to-radius-ps.md#radius). 
3. **Konfigurace klienta VPN pro možnost ověřování podle svého výběru získat a použít ho k nastavení klienta VPN** (v tomto článku).
4. [Dokončete konfiguraci P2S a připojte](point-to-site-how-to-radius-ps.md).

>[!IMPORTANT]
>Je-li vygenerovat profil konfigurace klienta VPN, jako je například protokol typ sítě VPN nebo typ ověřování, se změny v konfiguraci point-to-site VPN musí vygenerovat a nainstalujte nové konfigurace klienta VPN na zařízeních vašich uživatelů.
>
>

Pokud chcete použít v částech v tomto článku, nejprve rozhodnout, jaký typ ověřování, kterou chcete použít: uživatelské jméno a heslo, certifikát nebo další typy ověřování. Každá část obsahuje kroky pro Windows, Mac OS X a Linux (nyní k dispozici omezená kroky).

## <a name="adeap"></a>Ověřování uživatelského jména a hesla

Můžete nakonfigurovat ověřování uživatelského jména a hesla pomocí služby Active Directory nebo nechcete použít služby Active Directory. Buď scénář Ujistěte se, aby všichni uživatelé připojující mít přihlašovací údaje uživatelského jména a hesla, které lze ověřit pomocí protokolu RADIUS.

Když nakonfigurujete ověřování uživatelského jména a hesla, můžete vytvořit pouze konfiguraci pro ověřovací protokol EAP-MSCHAPv2 uživatelského jména a hesla. V příkazech `-AuthenticationMethod` je `EapMSChapv2`.

### <a name="usernamefiles"></a> 1. Generování souborů konfigurace klienta VPN

Generování souborů konfigurace klienta VPN pro použití s ověřováním uživatelského jména a hesla. Konfigurační soubory klienta VPN můžete vygenerovat pomocí následujícího příkazu:

```powershell 
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
Spuštěním příkazu vrátí odkaz. Zkopírujte a vložte odkaz do webového prohlížeče, chcete-li stáhnout **VpnClientConfiguration.zip**. Rozbalte soubor zobrazíte následující složky: 
 
* **WindowsAmd64** a **WindowsX86**: tyto složky obsahují balíčky Instalační služby systému Windows 64bitové a 32bitové v uvedeném pořadí. 
* **Obecné**: Tato složka obsahuje obecné informace, které použijete k vytvoření vlastní konfigurace klienta VPN. Tato složka není nutné pro ověření konfigurace uživatelského jména a hesla.
* **Mac**: Pokud jste nakonfigurovali IKEv2, když vytvoříte bránu virtuální sítě, zobrazí složka s názvem **Mac** obsahující **mobileconfig** souboru. Tento soubor můžete použít ke konfiguraci klienti se systémem Mac.

Pokud jste již vytvořili klienta konfigurační soubory, můžete je načíst pomocí `Get-AzureRmVpnClientConfiguration` rutiny. Ale pokud provedete konfiguraci P2S VPN, jako je například protokol typ sítě VPN nebo typ ověřování, všechny změny konfigurace nebudou automaticky aktualizovány. Musíte spustit `New-AzureRmVpnClientConfiguration` vytvořte nové konfigurace stahování.

Pokud chcete načíst dříve generovaného klienta konfigurační soubory, použijte následující příkaz:

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="setupusername"></a> 2. Konfigurace klientů VPN

Můžete nakonfigurovat následující klienti VPN:

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [Linux pomocí strongSwan](#adlinuxcli)
 
#### <a name="adwincli"></a>Instalace klienta VPN ve Windows

Na každém klientském počítači s Windows, můžete použít stejný balíček konfigurace klienta VPN, tak dlouho, dokud verze odpovídá architektuře pro klienta. Seznam klientské operační systémy, které jsou podporovány, naleznete v části [– nejčastější dotazy](vpn-gateway-vpn-faq.md#P2S).

Pomocí následujícího postupu můžete nakonfigurovat Nativní klient VPN ve Windows pro ověření certifikátu:

1. Vyberte konfigurační soubory klienta VPN, které odpovídá architektuře počítače Windows. Pro 64bitový procesor architekturu, vyberte **VpnClientSetupAmd64** balíček Instalační služby. Pro architekturu procesoru 32-bit, vyberte **VpnClientSetupX86** balíček Instalační služby. 
2. K instalaci balíčku, klikněte dvakrát na jeho. Pokud se zobrazí automaticky otevírané okno SmartScreen, vyberte **více informací o** > **přesto spustit**.
3. Na klientském počítači přejděte do **nastavení sítě** a vyberte **VPN**. Připojení k síti VPN zobrazuje název virtuální sítě, ke které se připojuje. 

#### <a name="admaccli"></a>Instalace klienta VPN MAC (OS X)

1. Vyberte **VpnClientSetup mobileconfig** souboru a jeho odeslání jednotlivých uživatelů. Můžete vytvořit e-mailu nebo jiným způsobem.

2. Vyhledejte **mobileconfig** souboru na Mac.

   ![Umístění souboru mobilconfig](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)
3. Dvakrát klikněte na profil, který chcete nainstalovat a vyberte **pokračovat**. Název profilu je stejný jako název virtuální sítě.

   ![Zpráva instalace](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
4. Vyberte **pokračovat** vytvoříte vztah důvěryhodnosti odesílatel profilu a pokračujte v instalaci.

   ![Potvrzovací zpráva](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
5. Během instalace profilu máte možnost zadat uživatelské jméno a heslo pro ověřování sítě VPN. Není nutné zadat tyto informace. Pokud tak učiníte, informace jsou uloženy a automaticky používá při inicializaci připojení. Vyberte **nainstalovat** pokračovat.

   ![Pole uživatelské jméno a heslo pro síť VPN](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
6. Zadejte uživatelské jméno a heslo pro oprávnění potřebná k instalaci profilu ve vašem počítači. Vyberte **OK**.

   ![Uživatelské jméno a heslo polí při instalaci profilu](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
7. Po instalaci profilu se zobrazí na **profily** dialogové okno. Můžete také otevřít dialogové okno později z **předvolbách systému**.

   ![Dialogové okno "Profily"](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
8. Chcete-li získat přístup k připojení k síti VPN, otevřete **sítě** dialogové okno z **předvolbách systému**.

   ![Ikony v předvolbách systému](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
9. Připojení k síti VPN se zobrazí jako **IkeV2 VPN**. Můžete změnit název aktualizací **mobileconfig** souboru.

   ![Podrobnosti o připojení k síti VPN](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
10. Vyberte **nastavení ověřování**. Vyberte **uživatelské jméno** v seznamu a zadejte svá pověření. Pokud jste zadali přihlašovací údaje dříve, pak **uživatelské jméno** automaticky vybrat v seznamu a uživatelské jméno a heslo jsou předem. Vyberte **OK** uložte nastavení.

    ![Nastavení ověřování](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
11. Zpět v **sítě** dialogové okno, vyberte **použít** a uložte změny. Chcete-li iniciovat připojení, vyberte **Connect**.

#### <a name="adlinuxcli"></a>Nastavení klienta Linux VPN prostřednictvím strongSwan

Prostřednictvím strongSwan 5.5.1 na Ubuntu 17.0.4 byly vytvořeny následující pokyny. Skutečné obrazovky se mohou lišit v závislosti na vaší verzí systémů Linux a strongSwan.

1. Otevřete **Terminálové** k instalaci **strongSwan** a jeho správce sítě spuštěním příkazu v příkladu. Pokud obdržíte chybu, která souvisí s `libcharon-extra-plugins`, nahraďte ho `strongswan-plugin-eap-mschapv2`.

   ```Terminal
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Vyberte **správce sítě** ikona (šipka nebo nižší šipku nahoru) a vyberte **upravit připojení**.

   !["Upravit připojení" výběr v správce sítě](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Vyberte **přidat** tlačítko vytvořte nové připojení.

   ![Tlačítko "Přidat" pro připojení](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. Vyberte **protokolu IPsec nebo IKEv2 (strongswan)** z rozevírací nabídky a pak vyberte **vytvořit**. Můžete přejmenovat připojení v tomto kroku.

   ![Vyberte typ připojení](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Otevřete **VpnSettings.xml** souboru z **Obecné** složky stažené klienta konfigurační soubory. Nalézt značku názvem `VpnServer` a zkopírujte název, počínaje `azuregateway` a konče `.cloudapp.net`.

   ![Obsah souboru VpnSettings.xml](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Vložte tento název do **adresu** pole nové připojení VPN v **brány** části. Potom vyberte ikonu složky na konci **certifikát** pole, přejděte na **Obecné** složky a vyberte **VpnServerRoot** souboru.
7. V **klienta** části připojení vyberte **EAP** pro **ověřování**a zadejte uživatelské jméno a heslo. Můžete chtít vyberte ikonu zámku na pravé straně uložit tyto informace. Potom vyberte **Uložit**.

   ![Úprava nastavení připojení](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Vyberte **správce sítě** ikona (šipka nebo nižší šipku nahoru) a hover přes **připojení k síti VPN**. Najdete v části připojení k síti VPN, který jste vytvořili. Chcete-li iniciovat připojení, vyberte ho.

   !["VPN Radius" připojení ve Správci sítě](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certeap"></a>Ověřování pomocí certifikátu
 
Klient VPN můžete vytvořit konfigurační soubory pro ověřování protokolu RADIUS certifikát, který používá protokol EAP-TLS. Certifikát vydaný enterprise se obvykle používá k ověření uživatele pro síť VPN. Ujistěte se, zda všichni uživatelé připojující certifikátu nainstalovaného na jejich zařízení, a že váš server RADIUS můžete ověřit certifikát.

>[!NOTE]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

V příkazech `-AuthenticationMethod` je `EapTls`. Při ověřování certifikátu klienta ověří RADIUS server ověřením svůj certifikát. `-RadiusRootCert` je soubor .cer, který obsahuje kořenový certifikát, který slouží k ověření serveru RADIUS.

Každé zařízení klient VPN vyžaduje certifikát nainstalovaného klienta. V některých případech zařízení se systémem Windows má více klientských certifikátů. Při ověřování to může způsobit automaticky otevírané okno dialogové okno se zobrazí všechny certifikáty. Uživatel pak musí vybrat certifikát, který chcete použít. Správný certifikát můžete odfiltrovat zadáním kořenový certifikát, který by měly být zřetězené klientský certifikát. 

`-ClientRootCert` je soubor .cer, který obsahuje kořenový certifikát. Je volitelný parametr. Pokud zařízení, ke kterému se chcete připojit z má jen jeden klientský certifikát, nemáte, zadejte tento parametr.

### <a name="certfiles"></a>1. Generování souborů konfigurace klienta VPN

Generování souborů konfigurace klienta VPN pro použití s ověřováním pomocí certifikátu. Konfigurační soubory klienta VPN můžete vygenerovat pomocí následujícího příkazu:
 
```powershell
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

Spuštěním příkazu vrátí odkaz. Zkopírujte a vložte odkaz do webového prohlížeče, chcete-li stáhnout VpnClientConfiguration.zip. Rozbalte soubor zobrazíte následující složky:

* **WindowsAmd64** a **WindowsX86**: tyto složky obsahují balíčky Instalační služby systému Windows 64bitové a 32bitové v uvedeném pořadí. 
* **GenericDevice**: Tato složka obsahuje obecné informace, které se používá k vytvoření vlastní konfigurace klienta VPN.

Pokud jste již vytvořili klienta konfigurační soubory, můžete je načíst pomocí `Get-AzureRmVpnClientConfiguration` rutiny. Ale pokud provedete konfiguraci P2S VPN, jako je například protokol typ sítě VPN nebo typ ověřování, všechny změny konfigurace nebudou automaticky aktualizovány. Musíte spustit `New-AzureRmVpnClientConfiguration` vytvořte nové konfigurace stahování.

Pokud chcete načíst dříve generovaného klienta konfigurační soubory, použijte následující příkaz:

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="setupusername"></a> 2. Konfigurace klientů VPN

Můžete nakonfigurovat následující klienti VPN:

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (nepodporuje žádné článku kroky ještě)

#### <a name="certwincli"></a>Instalace klienta VPN ve Windows

1. Vyberte konfigurační balíček a nainstalujte ho v klientském zařízení. Pro 64bitový procesor architekturu, vyberte **VpnClientSetupAmd64** balíček Instalační služby. Pro architekturu procesoru 32-bit, vyberte **VpnClientSetupX86** balíček Instalační služby. Pokud se zobrazí automaticky otevírané okno SmartScreen, vyberte **více informací o** > **přesto spustit**. Můžete také balíček uložit k instalaci na další klientské počítače.
2. Každý klient vyžaduje klientský certifikát pro ověřování. Nainstalujte certifikát klienta. Informace o klientských certifikátů najdete v tématu [klientské certifikáty pro point-to-site](vpn-gateway-certificates-point-to-site.md). Chcete-li nainstalovat certifikát, který byl vytvořen, přečtěte si téma [nainstalovat certifikát pro klienty Windows](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Na klientském počítači přejděte do **nastavení sítě** a vyberte **VPN**. Připojení k síti VPN zobrazuje název virtuální sítě, ke které se připojuje.

#### <a name="certmaccli"></a>Instalace klienta VPN MAC (OS X)

Musíte vytvořit samostatný profil pro každé zařízení Mac, který se připojuje k virtuální síti Azure. Je to proto, že tato zařízení vyžadují uživatelský certifikát pro ověřování, aby se zadaná v profilu. **Obecné** složka obsahuje všechny informace, které je nutné k vytvoření profilu:

* **VpnSettings.xml** obsahuje důležitá nastavení, jako je například typ serveru adresu a tunelové propojení.
* **VpnServerRoot.cer** obsahuje kořenový certifikát, který je nutné k ověření služby VPN gateway během instalace připojení P2S.
* **RadiusServerRoot.cer** obsahuje kořenový certifikát, který je nutné k ověření serveru RADIUS během ověřování.

Pomocí následujícího postupu můžete nakonfigurovat Nativní klient VPN v systému Mac pro ověření certifikátu:

1. Import **VpnServerRoot** a **RadiusServerRoot** kořenové certifikáty pro vaše Mac. Zkopírovat každý soubor do počítače Mac, dvakrát na ni klikněte a potom vyberte **přidat**.

   ![Přidání certifikátu VpnServerRoot](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

   ![Přidání certifikátu RadiusServerRoot](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Každý klient vyžaduje klientský certifikát pro ověřování. Nainstalujte certifikát klienta v klientském zařízení.
3. Otevřete **sítě** dialogové okno pod **předvolby sítě**. Vyberte **+** k vytvoření nového profilu připojení VPN klienta pro připojení P2S k virtuální síti Azure.

   **Rozhraní** hodnota je **VPN**a **typ sítě VPN** hodnota je **IKEv2**. Zadejte název pro profil v **název služby** a pak vyberte **vytvořit** k vytvoření profilu připojení klienta VPN.

   ![Informace o název rozhraní a služby](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. V **Obecné** složky, z **VpnSettings.xml** souboru, zkopírujte **VpnServer** hodnota značky. Vložte tuto hodnotu v **adresu serveru** a **vzdáleného ID** polí profilu. Ponechte **místní ID** pole prázdné.

   ![Informace o serveru](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. Vyberte **nastavení ověřování**a vyberte **certifikát**. 

   ![Nastavení ověřování](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. Klikněte na tlačítko **vyberte** vybrat certifikát, který chcete používat pro ověřování.

   ![Vybrat certifikát pro ověřování](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **Vyberte Identity** zobrazí seznam certifikátů můžete vybírat. Vyberte vhodný certifikát a pak vyberte **pokračovat**.

   !["Vyberte Identity" seznamu](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. V **místní ID** pole, zadejte název certifikátu (z kroku 6). V tomto příkladu má **ikev2Client.com**. Pak vyberte **použít** tlačítko a uložte změny.

   ![Pole "Místní ID"](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. V **sítě** dialogové okno, vyberte **použít** uložit všechny změny. Pak vyberte **Connect** spustit P2S připojení k virtuální síti Azure.

## <a name="otherauth"></a>Práce s jinými typy ověřování nebo protokoly

Chcete použít ověřování jiný typ (například OTP) nebo použijte jiný ověřovací protokol (například protokol PEAP-MSCHAPv2 místo EAP-MSCHAPv2), musíte vytvořit vlastní profil konfigurace klienta VPN. Chcete-li vytvořit profil, musíte informace, jako je IP adresa brány virtuální sítě, Typ tunelového propojení a děleného tunelového propojení směrování. Tyto informace můžete získat pomocí následujících kroků:

1. Použití `Get-AzureRmVpnClientConfiguration` rutiny ke generování konfigurace klienta VPN pro EapMSChapv2. Pokyny najdete v tématu [v této části](#ccradius) článku.

2. Rozbalte soubor VpnClientConfiguration.zip a podívejte se **GenenericDevice** složky. Ignorujte složky, které obsahují Instalační služba systému Windows pro 64bitové a 32bitové architektury.
 
3. **GenenericDevice** složka obsahuje soubor XML s názvem **VpnSettings**. Tento soubor obsahuje všechny požadované informace:

   * **VpnServer**: plně kvalifikovaný název domény služby Azure VPN gateway. Toto je adresa, která se klient připojuje k.
   * **VpnType**: vytvoření tunelu pro typ, který používáte pro připojení.
   * **Trasy**: tras, které jste nakonfigurovali v profilu tak, aby přes tunel P2S je odesláno pouze provoz, který je vázaný virtuální síť Azure.
   
   **GenenericDevice** složka také obsahuje soubor .cer s názvem **VpnServerRoot**. Tento soubor obsahuje kořenový certifikát, který je nutné k ověření služby Azure VPN gateway během instalace připojení P2S. Nainstalujte certifikát na všech zařízeních, která se budou připojovat k virtuální síti Azure.

## <a name="next-steps"></a>Další postup

Vrátit do článku do [dokončit konfiguraci P2S](point-to-site-how-to-radius-ps.md).

P2S řešení potíží s informace najdete v tématu [připojení point-to-site řešení potíží s Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).