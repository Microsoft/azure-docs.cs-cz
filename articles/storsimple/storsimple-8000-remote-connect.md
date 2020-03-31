---
title: Vzdálené připojení k zařízení StorSimple
description: Vysvětluje, jak nakonfigurovat zařízení pro vzdálenou správu a jak se připojit k prostředí Windows PowerShell pro StorSimple přes HTTP nebo HTTPS.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/02/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 58d61df932da06e32bb4c8f21a3a296b185f02d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80299006"
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>Vzdálená připojení k zařízení řady StorSimple 8000

## <a name="overview"></a>Přehled

K zařízení se můžete vzdáleně připojit prostřednictvím prostředí Windows PowerShell. Když se připojíte tímto způsobem, nevidíte nabídku. (Nabídku se zobrazí pouze v případě, že k připojení používáte sériovou konzolu na zařízení.) S windows powershellovou vzdálené komunikace se připojíte ke konkrétnímu runspace. Můžete také určit jazyk zobrazení.

Další informace o použití vzdálené komunikace prostředí Windows PowerShell ke správě zařízení naleznete v [části Windows PowerShell for StorSimple ke správě zařízení StorSimple](storsimple-8000-windows-powershell-administration.md).

Tento kurz vysvětluje, jak nakonfigurovat zařízení pro vzdálenou správu a potom jak se připojit k prostředí Windows PowerShell pro StorSimple. Pomocí protokolu HTTP nebo HTTPS můžete vzdáleně připojit přes Prostředí Windows PowerShell. Pokud se však rozhodujete o tom, jak se připojit k prostředí Windows PowerShell pro StorSimple, zvažte následující informace:

* Připojení přímo k sériové konzoli zařízení je bezpečné, ale připojení k konzoli sériového zařízení prostředzací přes síťové přepínače není. Při připojování k sériové konzoli zařízení prostředkem síťových přepínačů buďte opatrní před bezpečnostním rizikem.
* Připojení prostřednictvím relace PROTOKOLU HTTP může nabídnout větší zabezpečení než připojení prostřednictvím sériové konzoly v síti. I když se nejedná o nejbezpečnější metodu, je přijatelná v důvěryhodných sítích.
* Připojení prostřednictvím relace HTTPS s certifikátem podepsaným svým držitelem je nejbezpečnější a doporučená možnost.

Můžete se vzdáleně připojit k rozhraní prostředí Windows PowerShell. Vzdálený přístup k zařízení StorSimple prostřednictvím rozhraní prostředí Windows PowerShell však není ve výchozím nastavení povolen. Nejprve je nutné povolit vzdálenou správu na zařízení a potom na straně klienta, který se používá pro přístup k zařízení.

Kroky popsané v tomto článku byly provedeny v hostitelském systému se systémem Windows Server 2012 R2.

## <a name="connect-through-http"></a>Připojení přes protokol HTTP

Připojení k prostředí Windows PowerShell pro StorSimple prostřednictvím relace HTTP nabízí větší zabezpečení než připojení prostřednictvím sériové konzole zařízení StorSimple. I když se nejedná o nejbezpečnější metodu, je přijatelná v důvěryhodných sítích.

Ke konfiguraci vzdálené správy můžete použít portál Azure nebo sériovou konzolu. Vyberte si z následujících postupů:

* Povolení vzdálené správy přes protokol HTTP pomocí portálu Azure
* [Povolení vzdálené správy přes protokol HTTP pomocí konzoly sériového systému](#use-the-serial-console-to-enable-remote-management-over-http)

Po povolení vzdálené správy použijte následující postup k přípravě klienta na vzdálené připojení.

* [Příprava klienta na vzdálené připojení](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-portal-to-enable-remote-management-over-http"></a>Povolení vzdálené správy přes protokol HTTP pomocí portálu Azure

Chcete-li povolit vzdálenou správu přes protokol HTTP, proveďte na webu Azure Portal následující kroky.

#### <a name="to-enable-remote-management-through-the-azure-portal"></a>Povolení vzdálené správy prostřednictvím portálu Azure

1. Přejděte do služby Správce zařízení StorSimple. Vyberte **Zařízení** a pak vyberte a klikněte na zařízení, které chcete konfigurovat pro vzdálenou správu. Přejděte na **nastavení zařízení > zabezpečení**.
2. V okně **Nastavení zabezpečení** klepněte na **položku Vzdálená správa**.
3. V okně **Vzdálená správa** nastavte **možnost Povolit vzdálenou správu** na **ano**.
4. Teď můžete zvolit připojení pomocí protokolu HTTP. (Ve výchozím nastavení se lze připojit přes protokol HTTPS.) Ujistěte se, že je vybránprotokol HTTP.
   
   > [!NOTE]
   > Připojení pomocí protokolu HTTP je přijatelné jenom v důvěryhodných sítích.
   
5. Klepněte na tlačítko **Uložit** a po zobrazení výzvy k potvrzení vyberte **možnost Ano**.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>Povolení vzdálené správy přes protokol HTTP pomocí konzoly sériového systému
Chcete-li povolit vzdálenou správu, proveďte na konzole sériového zařízení následující kroky.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Povolení vzdálené správy prostřednictvím sériové konzole zařízení
1. V nabídce sériové konzole vyberte možnost 1. Další informace o použití sériové konzoly v zařízení naleznete v části [Připojení k prostředí Windows PowerShell for StorSimple prostřednictvím sériové konzoly zařízení](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. Na výzvu zadejte:`Enable-HcsRemoteManagement –AllowHttp`
3. Budete upozorněni na chyby zabezpečení použití protokolu HTTP pro připojení k zařízení. Po zobrazení výzvy potvrďte zadáním **y**.
4. Ověřte, zda je protokol HTTP povolen zadáním:`Get-HcsSystem`
5. Ověřte, zda pole **RemoteManagementMode** zobrazuje **technologii HttpsAndHttpEnabled**. Následující obrázek znázorňuje tato nastavení v PuTTY.
   
     ![Sériové https a HTTP povoleno](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Příprava klienta na vzdálené připojení
Chcete-li povolit vzdálenou správu, proveďte na straně klienta následující kroky.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Příprava klienta na vzdálené připojení
1. Spusťte relaci prostředí Windows PowerShell jako správce. Pokud používáte klienta windows 10, je ve výchozím nastavení služba Vzdálená správa systému Windows nastavena na ruční. Službu může být nutné spustit zadáním:

    `Start-Service WinRM`
    
2. Zadáním následujícího příkazu přidejte adresu IP zařízení StorSimple do seznamu důvěryhodných hostitelů klienta:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     Nahraďte> *<device_ip* IP adresou zařízení. například: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Zadáním následujícího příkazu uložte pověření zařízení do proměnné: 
   
    ```
    $cred = Get-Credential
    ```
    
4. V zobrazeném dialogovém okně:
   
   1. Zadejte uživatelské jméno v tomto formátu: *device_ip\SSAdmin*.
   2. Zadejte heslo správce zařízení, které bylo nastaveno při konfiguraci zařízení pomocí průvodce instalací. Výchozí heslo je *Password1*.
5. Spusťte relaci prostředí Windows PowerShell na zařízení zadáním tohoto příkazu:
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > Chcete-li vytvořit relaci prostředí Windows PowerShell pro použití s `–Port` virtuálním zařízením StorSimple, přidejte parametr a zadejte veřejný port, který jste nakonfigurovali ve vzdálené vazbě pro virtuální zařízení StorSimple.
   
   
V tomto okamžiku byste měli mít aktivní vzdálenou relaci prostředí Windows PowerShell k zařízení.
   
![Vzdálené komunikace prostředí PowerShell pomocí protokolu HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Připojení přes protokol HTTPS

Připojení k prostředí Windows PowerShell pro StorSimple prostřednictvím relace HTTPS je nejbezpečnější a doporučený způsob vzdáleného připojení k zařízení Microsoft Azure StorSimple. Následující postupy vysvětlují, jak nastavit sériovou konzolu a klientské počítače, abyste se mohli připojit k prostředí Windows PowerShell pro StorSimple pomocí protokolu HTTPS.

Ke konfiguraci vzdálené správy můžete použít portál Azure nebo sériovou konzolu. Vyberte si z následujících postupů:

* Povolení vzdálené správy přes protokol HTTPS pomocí portálu Azure
* [Povolení vzdálené správy prostředpou https pomocí konzoly sériového systému](#use-the-serial-console-to-enable-remote-management-over-https)

Po povolení vzdálené správy použijte následující postupy k přípravě hostitele na vzdálenou správu a připojení k zařízení ze vzdáleného hostitele.

* [Příprava hostitele pro vzdálenou správu](#prepare-the-host-for-remote-management)
* [Připojení k zařízení ze vzdáleného hostitele](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-portal-to-enable-remote-management-over-https"></a>Povolení vzdálené správy přes protokol HTTPS pomocí portálu Azure

Chcete-li povolit vzdálenou správu přes protokol HTTPS, proveďte na webu Azure Portal následující kroky.

#### <a name="to-enable-remote-management-over-https-from-the-azure-portal"></a>Povolení vzdálené správy přes protokol HTTPS z webu Azure Portal

1. Přejděte do služby Správce zařízení StorSimple. Vyberte **Zařízení** a pak vyberte a klikněte na zařízení, které chcete konfigurovat pro vzdálenou správu. Přejděte na **nastavení zařízení > zabezpečení**.
2. V okně **Nastavení zabezpečení** klepněte na **položku Vzdálená správa**.
3. U položky **Povolit vzdálenou správu** zvolte **Ano**.
4. Nyní se můžete připojit pomocí protokolu HTTPS. (Ve výchozím nastavení se lze připojit přes protokol HTTPS.) Ujistěte se, že je vybránprotokol HTTPS.
5. Klepněte... a potom klepněte na tlačítko **Stáhnout certifikát vzdálené správy**. Zadejte umístění pro uložení tohoto souboru. Tento certifikát je třeba nainstalovat do klientského nebo hostitelského počítače, který použijete k připojení k zařízení.
6. Po zobrazení **výzvy** k potvrzení klepněte na tlačítko Uložit a potom klepněte na tlačítko **Ano.**

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>Povolení vzdálené správy prostředpou https pomocí konzoly sériového systému

Chcete-li povolit vzdálenou správu, proveďte na konzole sériového zařízení následující kroky.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Povolení vzdálené správy prostřednictvím sériové konzole zařízení
1. V nabídce sériové konzole vyberte možnost 1. Další informace o použití sériové konzoly v zařízení naleznete v části [Připojení k prostředí Windows PowerShell for StorSimple prostřednictvím sériové konzoly zařízení](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. Na výzvu zadejte:
   
     `Enable-HcsRemoteManagement`
   
    To by mělo povolit protokol HTTPS na vašem zařízení.
3. Ověřte, zda byl protokol HTTPS povolen zadáním: 
   
     `Get-HcsSystem`
   
    Ujistěte se, že v poli **RemoteManagementMode** je **zobrazena funkce HttpsEnabled**. Následující obrázek znázorňuje tato nastavení v PuTTY.
   
     ![Sériový protokol HTTPS povolen](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. Z výstupu `Get-HcsSystem`aplikace zkopírujte sériové číslo zařízení a uložte jej pro pozdější použití.
   
   > [!NOTE]
   > Sériové číslo se mapuje na název kn v certifikátu.
   
5. Získání certifikátu vzdálené správy zadáním: 
   
     `Get-HcsRemoteManagementCert`
   
    Zobrazí se certifikát podobný následujícímu.
   
    ![Získání certifikátu vzdálené správy](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. Zkopírujte informace v certifikátu z **-----BEGIN CERTIFICATE-----** do **-----END CERTIFICATE-----** do textového editoru, jako je poznámkový blok, a uložte je jako soubor CER. (Tento soubor zkopírujete do vzdáleného hostitele při přípravě hostitele.)
   
   > [!NOTE]
   > Chcete-li vytvořit nový `Set-HcsRemoteManagementCert` certifikát, použijte rutinu.
   
### <a name="prepare-the-host-for-remote-management"></a>Příprava hostitele pro vzdálenou správu

Chcete-li připravit hostitelský počítač na vzdálené připojení, které používá relaci HTTPS, proveďte následující postupy:

* [Importujte soubor CER do kořenového úložiště klienta nebo vzdáleného hostitele](#to-import-the-certificate-on-the-remote-host).
* [Přidejte sériová čísla zařízení do souboru hosts na vzdáleném hostiteli](#to-add-device-serial-numbers-to-the-remote-host).

Každý z předchozích postupů je popsán níže.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Import certifikátu na vzdáleném hostiteli
1. Klepněte pravým tlačítkem myši na soubor CER a vyberte **možnost Instalovat certifikát**. Tím spustíte Průvodce importem certifikátu.
   
    ![Průvodce importem certifikátu 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. V **umístění obchodu**vyberte místní **počítač**a klepněte na tlačítko **Další**.
3. Vyberte **Umístit všechny certifikáty do následujícího obchodu**a klepněte na tlačítko **Procházet**. Přejděte do kořenového úložiště vzdáleného hostitele a klepněte na tlačítko **Další**.
   
    ![Průvodce importem certifikátu 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. Klikněte na **Finish** (Dokončit). Zobrazí se zpráva, která informuje o tom, že import byl úspěšný.
   
    ![Průvodce importem certifikátu 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Přidání sériových čísel zařízení do vzdáleného hostitele
1. Spusťte program Poznámkový blok jako správce a otevřete soubor hosts umístěný na adrese \Windows\System32\Drivers\etc.
2. Do souboru hosts přidejte následující tři položky: **IP adresa DATA 0**, Řadič 0 **Pevná IP adresa**a Řadič 1 **Pevná IP adresa**.
3. Zadejte sériové číslo zařízení, které jste uložili dříve. Namapujte ji na IP adresu, jak je znázorněno na následujícím obrázku. Pro řadič 0 a řadič 1 připojit **Controller0** a **Controller1** na konci sériového čísla (název CN).
   
    ![Přidání názvu CN do souboru hosts](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. Uložte soubor hosts.

### <a name="connect-to-the-device-from-the-remote-host"></a>Připojení k zařízení ze vzdáleného hostitele

Pomocí prostředí Windows PowerShell a TLS zadejte relaci SSAdmin v zařízení ze vzdáleného hostitele nebo klienta. Relace SSAdmin mapuje na možnost 1 v nabídce [sériové konzole](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) vašeho zařízení.

V počítači, ze kterého chcete vytvořit vzdálené připojení prostředí Windows PowerShell, proveďte následující postup.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-tls"></a>Zadání relace SSAdmin v zařízení pomocí prostředí Windows PowerShell a TLS
1. Spusťte relaci prostředí Windows PowerShell jako správce. Pokud používáte klienta windows 10, je ve výchozím nastavení služba Vzdálená správa systému Windows nastavena na ruční. Službu může být nutné spustit zadáním:

    `Start-Service WinRM`

2. Přidejte IP adresu zařízení do důvěryhodných hostitelů klienta zadáním:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    Tam, kde <*device_ip*> je IP adresa vašeho zařízení; například: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Chcete-li vytvořit nové pověření, zadejte:
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    Pokud <*IP adresa cílového zařízení*> je IP adresa DATA 0 pro vaše zařízení; například **10.126.173.90,** jak je znázorněno na předchozím obrázku souboru hosts. Zadejte také heslo správce pro vaše zařízení.
4. Vytvoření relace zadáním:
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    Pro parametr -ComputerName v rutině zadejte <*sériové číslo> cílového zařízení.* Toto sériové číslo bylo mapováno na IP adresu DATA 0 v souboru hosts na vzdáleném hostiteli. například **SHX0991003G44MT,** jak je znázorněno na následujícím obrázku.
5. Zadejte:
   
     `Enter-PSSession $session`
6. Budete muset počkat několik minut a pak budete připojeni k zařízení přes HTTPS přes TLS. Zobrazí se zpráva, že jste připojeni k zařízení.
   
    ![Vzdálené komunikace prostředí PowerShell pomocí protokolů HTTPS a TLS](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [správě zařízení StorSimple pomocí prostředí Windows PowerShell](storsimple-8000-windows-powershell-administration.md).
* Další informace o [správě zařízení StorSimple Device Manager pomocí služby StorSimple](storsimple-8000-manager-service-administration.md).

