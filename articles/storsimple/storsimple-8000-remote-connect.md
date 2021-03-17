---
title: Vzdálené připojení k zařízení StorSimple
description: Vysvětluje, jak nakonfigurovat zařízení pro vzdálenou správu a jak se připojit k Windows PowerShell pro StorSimple přes HTTP nebo HTTPS.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/02/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1da688dfb00b26ca6b561d5aa0fb548c221381c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85514570"
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>Vzdálené připojení k zařízení řady StorSimple 8000

## <a name="overview"></a>Přehled

K zařízení se můžete vzdáleně připojit prostřednictvím Windows PowerShellu. Když se připojíte tímto způsobem, nevidíte nabídku. (Nabídka se zobrazí jenom v případě, že se k připojení použije konzola sériového portu na zařízení.) Pomocí vzdálené komunikace Windows PowerShellu se připojíte ke konkrétnímu prostředí runspace. Můžete také zadat jazyk zobrazení.

Další informace o tom, jak pomocí vzdálené komunikace Windows PowerShellu spravovat vaše zařízení, najdete v [Windows PowerShell pro StorSimple ke správě zařízení StorSimple](storsimple-8000-windows-powershell-administration.md).

V tomto kurzu se dozvíte, jak nakonfigurovat zařízení pro vzdálenou správu a jak se připojit k Windows PowerShell pro StorSimple. Pomocí protokolu HTTP nebo HTTPS se můžete vzdáleně připojit prostřednictvím Windows PowerShellu. Pokud však rozhodujete, jak se připojit k Windows PowerShell pro StorSimple, vezměte v úvahu následující informace:

* Přímé připojení ke konzole sériového portu zařízení je zabezpečené, ale připojení ke konzole sériového portu přes síťové přepínače není. Při připojování k konzole sériového zařízení přes síťové přepínače buďte opatrní bezpečnostní riziko.
* Připojení prostřednictvím relace HTTP může nabídnout větší zabezpečení než připojení prostřednictvím síťové konzoly přes síť. I když se nejedná o nejbezpečnější metodu, je přijatelné v důvěryhodných sítích.
* Připojení přes relaci HTTPS s certifikátem podepsaným svým držitelem je nejbezpečnější a doporučená možnost.

Vzdáleně se můžete připojit k rozhraní Windows PowerShell. Vzdálený přístup k zařízení StorSimple přes rozhraní Windows PowerShell není ve výchozím nastavení povolený. Nejdřív musíte na zařízení povolit vzdálenou správu a pak na klientovi, který se používá pro přístup k vašemu zařízení.

Kroky popsané v tomto článku byly provedeny v hostitelském systému se systémem Windows Server 2012 R2.

## <a name="connect-through-http"></a>Připojení prostřednictvím protokolu HTTP

Připojení k Windows PowerShell pro StorSimple prostřednictvím relace HTTP nabízí větší zabezpečení než připojení prostřednictvím sériové konzoly zařízení StorSimple. I když se nejedná o nejbezpečnější metodu, je přijatelné v důvěryhodných sítích.

Ke konfiguraci vzdálené správy můžete použít buď Azure Portal, nebo konzolu sériového portu. Vyberte z následujících postupů:

* Povolit vzdálenou správu přes HTTP pomocí Azure Portal
* [Použití konzoly sériového portu k povolení vzdálené správy přes protokol HTTP](#use-the-serial-console-to-enable-remote-management-over-http)

Po povolení vzdálené správy použijte následující postup k přípravě klienta pro vzdálené připojení.

* [Příprava klienta pro vzdálené připojení](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-portal-to-enable-remote-management-over-http"></a>Povolit vzdálenou správu přes HTTP pomocí Azure Portal

Pokud chcete povolit vzdálenou správu přes protokol HTTP, proveďte následující kroky v Azure Portal.

#### <a name="to-enable-remote-management-through-the-azure-portal"></a>Povolení vzdálené správy prostřednictvím Azure Portal

1. Přejděte do služby Správce zařízení StorSimple. Vyberte **zařízení** a pak vyberte zařízení, které chcete nakonfigurovat pro vzdálenou správu. Přejít na **nastavení zařízení > zabezpečení**.
2. V okně **nastavení zabezpečení** klikněte na **Vzdálená správa**.
3. V okně **Vzdálená správa** nastavte **Povolit vzdálenou správu** na **Ano**.
4. Teď můžete zvolit připojení pomocí protokolu HTTP. (Výchozí hodnota je připojení přes protokol HTTPS.) Ujistěte se, že je vybrána možnost HTTP.
   
   > [!NOTE]
   > Připojení pomocí protokolu HTTP je přijatelné jenom v důvěryhodných sítích.
   
5. Klikněte na **Uložit** a po zobrazení výzvy k potvrzení vyberte **Ano**.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>Použití konzoly sériového portu k povolení vzdálené správy přes protokol HTTP
Chcete-li povolit vzdálenou správu, proveďte následující kroky v konzole sériového portu zařízení.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Povolení vzdálené správy přes konzolu sériového portu zařízení
1. V nabídce Konzola sériového portu vyberte možnost 1. Další informace o používání konzoly sériového portu na zařízení najdete v části [připojení k Windows PowerShell pro StorSimple prostřednictvím konzoly sériového portu zařízení](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. Do příkazového řádku zadejte: `Enable-HcsRemoteManagement –AllowHttp`
3. Zobrazí se oznámení o ohrožení zabezpečení pomocí protokolu HTTP pro připojení k zařízení. Po zobrazení výzvy potvrďte zadáním **Y**.
4. Zadáním příkazu ověřte, že je protokol HTTP povolený. `Get-HcsSystem`
5. Ověřte, že pole **RemoteManagementMode** zobrazuje **HttpsAndHttpEnabled**. Následující ilustrace znázorňuje tato nastavení v výstupu.
   
     ![Podpora sériového portu HTTPS a protokolu HTTP](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Příprava klienta pro vzdálené připojení
Na straně klienta proveďte následující kroky, aby bylo možné povolit vzdálenou správu.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Příprava klienta pro vzdálené připojení
1. Spusťte relaci Windows PowerShellu jako správce. Pokud používáte klienta Windows 10, je ve výchozím nastavení služba Vzdálená správa systému Windows nastavena na ruční. Službu možná budete muset spustit zadáním:

    `Start-Service WinRM`
    
2. Zadáním následujícího příkazu přidejte IP adresu zařízení StorSimple do seznamu důvěryhodných hostitelů klienta:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     Nahraďte <*device_ip*> IP adresou vašeho zařízení. například: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Zadáním následujícího příkazu uložte přihlašovací údaje zařízení do proměnné: 
   
    ```
    $cred = Get-Credential
    ```
    
4. V dialogovém okně, které se zobrazí:
   
   1. Zadejte uživatelské jméno v tomto formátu: *device_ip \ssadmin*.
   2. Zadejte heslo správce zařízení, které bylo nastaveno při konfiguraci zařízení pomocí Průvodce instalací nástroje. Výchozí heslo je *Heslo1*.
5. Spusťte relaci prostředí Windows PowerShell na zařízení zadáním tohoto příkazu:
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > Pokud chcete vytvořit relaci Windows PowerShellu pro použití s virtuálním zařízením StorSimple, přidejte `–Port` parametr a zadejte veřejný port, který jste nakonfigurovali v části Vzdálená komunikace pro virtuální zařízení StorSimple.
   
   
V tomto okamžiku byste měli mít k zařízení aktivní relaci vzdáleného Windows PowerShellu.
   
![Vzdálená komunikace PowerShellu pomocí protokolu HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Připojit prostřednictvím protokolu HTTPS

Připojení k Windows PowerShell pro StorSimple prostřednictvím relace HTTPS je nejbezpečnější a doporučenou metodou vzdáleného připojení k vašemu Microsoft Azure StorSimple zařízení. Následující postupy vysvětlují, jak nastavit sériovou konzolu a klientské počítače tak, aby se mohly pomocí protokolu HTTPS připojit k Windows PowerShell pro StorSimple.

Ke konfiguraci vzdálené správy můžete použít buď Azure Portal, nebo konzolu sériového portu. Vyberte z následujících postupů:

* Povolit vzdálenou správu přes HTTPS pomocí Azure Portal
* [Pomocí konzoly sériového portu povolte vzdálenou správu přes protokol HTTPS.](#use-the-serial-console-to-enable-remote-management-over-https)

Po povolení vzdálené správy použijte následující postupy k přípravě hostitele pro vzdálenou správu a připojte se k zařízení ze vzdáleného hostitele.

* [Příprava hostitele pro vzdálenou správu](#prepare-the-host-for-remote-management)
* [Připojit se k zařízení ze vzdáleného hostitele](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-portal-to-enable-remote-management-over-https"></a>Povolit vzdálenou správu přes HTTPS pomocí Azure Portal

Pokud chcete povolit vzdálenou správu přes protokol HTTPS, proveďte následující kroky v Azure Portal.

#### <a name="to-enable-remote-management-over-https-from-the-azure-portal"></a>Povolení vzdálené správy přes HTTPS z Azure Portal

1. Přejděte do služby Správce zařízení StorSimple. Vyberte **zařízení** a pak vyberte zařízení, které chcete nakonfigurovat pro vzdálenou správu. Přejít na **nastavení zařízení > zabezpečení**.
2. V okně **nastavení zabezpečení** klikněte na **Vzdálená správa**.
3. U položky **Povolit vzdálenou správu** zvolte **Ano**.
4. Nyní se můžete rozhodnout připojit pomocí protokolu HTTPS. (Výchozí hodnota je připojení přes protokol HTTPS.) Ujistěte se, že je vybraná možnost HTTPS.
5. Klikněte na... a potom klikněte na možnost **Stáhnout certifikát vzdálené správy**. Zadejte umístění pro uložení tohoto souboru. Tento certifikát je potřeba nainstalovat na klientský nebo hostitelský počítač, který budete používat pro připojení k zařízení.
6. Klikněte na **Uložit** a po zobrazení výzvy k potvrzení klikněte na **Ano** .

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>Pomocí konzoly sériového portu povolte vzdálenou správu přes protokol HTTPS.

Chcete-li povolit vzdálenou správu, proveďte následující kroky v konzole sériového portu zařízení.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Povolení vzdálené správy přes konzolu sériového portu zařízení
1. V nabídce Konzola sériového portu vyberte možnost 1. Další informace o používání konzoly sériového portu na zařízení najdete v části [připojení k Windows PowerShell pro StorSimple prostřednictvím konzoly sériového portu zařízení](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. Do příkazového řádku zadejte:
   
     `Enable-HcsRemoteManagement`
   
    To by mělo na svém zařízení umožňovat protokol HTTPS.
3. Zadáním příkazu zajistěte, že je protokol HTTPS povolený: 
   
     `Get-HcsSystem`
   
    Ujistěte se, že pole **RemoteManagementMode** zobrazuje **HttpsEnabled**. Následující ilustrace znázorňuje tato nastavení v výstupu.
   
     ![Sériové HTTPS povoleno](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. Z výstupu `Get-HcsSystem` zkopírujte sériové číslo zařízení a uložte ho pro pozdější použití.
   
   > [!NOTE]
   > Sériové číslo se mapuje na název CN v certifikátu.
   
5. Získejte certifikát pro vzdálenou správu zadáním: 
   
     `Get-HcsRemoteManagementCert`
   
    Zobrazí se certifikát podobný tomuto:.
   
    ![Získat certifikát pro vzdálenou správu](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. Zkopírujte informace v certifikátu z **-----začít s certifikátem-----** do textového editoru, jako je Poznámkový blok **----------** , a uložte ho jako soubor. cer. (Tento soubor budete kopírovat na svého vzdáleného hostitele při přípravě hostitele.)
   
   > [!NOTE]
   > Pokud chcete vygenerovat nový certifikát, použijte `Set-HcsRemoteManagementCert` rutinu.
   
### <a name="prepare-the-host-for-remote-management"></a>Příprava hostitele pro vzdálenou správu

Chcete-li připravit hostitelský počítač pro vzdálené připojení, které používá relaci HTTPS, proveďte následující postupy:

* [Importujte soubor. cer do kořenového úložiště klienta nebo vzdáleného hostitele](#to-import-the-certificate-on-the-remote-host).
* [Přidejte sériová čísla zařízení do souboru hostitelů na vzdáleném hostiteli](#to-add-device-serial-numbers-to-the-remote-host).

Všechny předchozí postupy jsou popsány níže.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Import certifikátu na vzdáleném hostiteli
1. Klikněte pravým tlačítkem na soubor. cer a vyberte **nainstalovat certifikát**. Tím spustíte Průvodce importem certifikátu.
   
    ![Průvodce importem certifikátu 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. V poli **umístění úložiště**vyberte **místní počítač**a pak klikněte na **Další**.
3. Vyberte možnost **umístit všechny certifikáty do následujícího úložiště**a pak klikněte na tlačítko **Procházet**. Přejděte do kořenového úložiště vzdáleného hostitele a klikněte na **Další**.
   
    ![Průvodce importem certifikátu 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. Klikněte na **Finish** (Dokončit). Zobrazí se zpráva oznamující, že import proběhl úspěšně.
   
    ![Průvodce importem certifikátu 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Přidání sériových čísel zařízení ke vzdálenému hostiteli
1. Spusťte Poznámkový blok jako správce a pak otevřete soubor Hosts umístěný na adrese \Windows\System32\Drivers\etc.
2. Do souboru hostitelů přidejte následující tři položky: **IP adresa data 0**, **Pevná IP adresa řadiče 0**a **Pevná IP adresa řadiče 1**.
3. Zadejte sériové číslo zařízení, které jste předtím uložili. Namapujte ji na IP adresu, jak je znázorněno na následujícím obrázku. Pro řadič 0 a řadič 1 přidejte **prvku Controller0** a **prvku Controller1** na konci sériového čísla (název CN).
   
    ![Přidání názvu CN do souboru hostitelů](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. Uložte soubor hostitelů.

### <a name="connect-to-the-device-from-the-remote-host"></a>Připojit se k zařízení ze vzdáleného hostitele

Pomocí prostředí Windows PowerShell a TLS zadejte do zařízení relaci SSAdmin ze vzdáleného hostitele nebo klienta. Relace SSAdmin se mapuje na možnost 1 v nabídce [konzoly sériového portu](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) vašeho zařízení.

V počítači, ze kterého chcete vzdálené připojení Windows PowerShell, proveďte následující postup.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-tls"></a>Zadání relace SSAdmin do zařízení pomocí Windows PowerShellu a TLS
1. Spusťte relaci Windows PowerShellu jako správce. Pokud používáte klienta Windows 10, je ve výchozím nastavení služba Vzdálená správa systému Windows nastavena na ruční. Službu možná budete muset spustit zadáním:

    `Start-Service WinRM`

2. Přidejte IP adresu zařízení do důvěryhodných hostitelů klienta zadáním:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    Kde <*device_ip*> je IP adresa vašeho zařízení; například: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Pokud chcete vytvořit nové přihlašovací údaje, zadejte:
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    Kde <*IP adresa cílového zařízení*> je IP adresa 0 pro vaše zařízení; například **10.126.173.90** , jak je znázorněno na předchozím obrázku souboru Hosts. Zadejte také heslo správce pro vaše zařízení.
4. Vytvořte relaci zadáním:
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    Pro parametr-ComputerName v rutině zadejte <*sériové číslo> cílového zařízení* . Toto sériové číslo bylo namapováno na IP adresu dat 0 v souboru hostitelů na vzdáleném hostiteli. například **SHX0991003G44MT** , jak je znázorněno na následujícím obrázku.
5. Zadejte:
   
     `Enter-PSSession $session`
6. Budete muset několik minut počkat a pak se k zařízení připojíte přes HTTPS přes TLS. Zobrazí se zpráva s informacemi o tom, že jste připojení k vašemu zařízení.
   
    ![Vzdálená komunikace PowerShellu pomocí protokolu HTTPS a TLS](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [použití Windows PowerShellu ke správě zařízení StorSimple](storsimple-8000-windows-powershell-administration.md).
* Přečtěte si další informace o [používání služby StorSimple Device Manager ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

