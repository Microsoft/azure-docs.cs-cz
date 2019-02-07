---
title: Vzdálené připojení k zařízení StorSimple | Dokumentace Microsoftu
description: Vysvětluje, jak nakonfigurovat zařízení pro vzdálenou správu a jak se připojit k prostředí Windows PowerShell pro StorSimple přes protokol HTTP nebo HTTPS.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/02/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 05bec60f4c56c98e9b910b50e858656a2e5554b2
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816488"
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>Vzdálené připojení k zařízení StorSimple 8000 series

## <a name="overview"></a>Přehled

Můžete vzdáleně připojit k zařízení pomocí Windows Powershellu. Když se připojíte tímto způsobem, se nezobrazí nabídka. (Zobrazí nabídku pouze v případě, že použijete konzole sériového portu zařízení pro připojení.) Pomocí vzdálené komunikace Windows Powershellu připojit k určité prostředí runspace. Můžete také určit jazyk zobrazení.

Další informace o použití vzdálené komunikace Windows Powershellu ke správě vašeho zařízení, přejděte na [pomocí Windows Powershellu pro StorSimple ke správě zařízení StorSimple](storsimple-8000-windows-powershell-administration.md).

Tento kurz vysvětluje, jak nakonfigurovat zařízení pro vzdálenou správu a jak se připojit k prostředí Windows PowerShell pro StorSimple. Vzdálené připojení přes Windows PowerShell můžete použít protokol HTTP nebo HTTPS. Když se rozhodujete, jak se připojit k prostředí Windows PowerShell pro StorSimple, zvažte následující informace:

* Připojení přímo ke konzole sériového portu zařízení je zabezpečené, ale připojení ke konzole sériového portu přes síťové přepínače není. Buďte opatrní rizika zabezpečení při připojování ke konzole sériového portu zařízení přes síťové přepínače.
* Připojení přes relaci protokolu HTTP může nabízet vyšší bezpečnost než připojení prostřednictvím sériové konzoly v síti. I když to není nejbezpečnější metodou, je přijatelné v důvěryhodných sítích.
* Připojení přes relaci protokolu HTTPS pomocí certifikátu podepsaného svým držitelem je nejbezpečnější a doporučená možnost.

Můžete vzdáleně připojit k rozhraní Windows PowerShell. Ve výchozím nastavení však není povolen vzdálený přístup k zařízení StorSimple pomocí rozhraní Windows PowerShell. Je nutné nejprve povolit vzdálené správy na zařízení, a pak v klientském počítači, který slouží k přístupu k zařízení.

Podle kroků popsaných v tomto článku byly provedeny v hostitelském systému, systémem Windows Server 2012 R2.

## <a name="connect-through-http"></a>Připojení prostřednictvím protokolu HTTP

Připojení k prostředí Windows PowerShell pro StorSimple přes relaci protokolu HTTP poskytuje lepší zabezpečení než připojení prostřednictvím konzoly sériového portu zařízení StorSimple. I když to není nejbezpečnější metodou, je přijatelné v důvěryhodných sítích.

Na webu Azure portal nebo konzoly sériového portu můžete použít ke konfiguraci vzdálené správy. Vyberte jednu z následujících postupů:

* Povolení vzdálené správy přes protokol HTTP pomocí webu Azure portal
* [Povolení vzdálené správy přes protokol HTTP pomocí konzoly sériového portu](#use-the-serial-console-to-enable-remote-management-over-http)

Po povolení vzdálené správy pomocí následujícího postupu Příprava klienta vzdáleného připojení.

* [Příprava vzdáleného připojení klienta](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-portal-to-enable-remote-management-over-http"></a>Povolení vzdálené správy přes protokol HTTP pomocí webu Azure portal

Proveďte následující kroky na webu Azure Portal k povolení vzdálené správy přes protokol HTTP.

#### <a name="to-enable-remote-management-through-the-azure-portal"></a>Povolení vzdálené správy na webu Azure portal

1. Přejděte do služby Správce zařízení StorSimple. Vyberte **zařízení** a potom klikněte na zařízení, kterou chcete konfigurovat pro vzdálenou správu. Přejděte na **zařízení Nastavení > zabezpečení**.
2. V **nastavení zabezpečení** okna, klikněte na tlačítko **vzdálenou správu**.
3. V **vzdálenou správu** okno, nastavte **povolit vzdálenou správu** k **Ano**.
4. Teď můžete zvolit připojení pomocí protokolu HTTP. (Výchozí hodnota je připojení pomocí protokolu HTTPS). Ujistěte se, že je vybraná HTTP.
   
   > [!NOTE]
   > Připojení pomocí protokolu HTTP je přijatelné jenom v důvěryhodných sítích.
   
5. Klikněte na tlačítko **Uložit** a po zobrazení výzvy k potvrzení, vyberte **Ano**.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>Povolení vzdálené správy přes protokol HTTP pomocí konzoly sériového portu
Proveďte následující kroky v konzole sériového portu zařízení chcete povolit vzdálenou správu.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Jak povolit vzdálenou správu prostřednictvím konzoly sériového portu zařízení
1. V nabídce konzoly sériového portu vyberte možnost 1. Další informace o použití konzoly sériového portu v zařízení, přejděte na [připojit k prostředí Windows PowerShell pro StorSimple prostřednictvím konzoly sériového portu zařízení](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. Na příkazovém řádku zadejte: `Enable-HcsRemoteManagement –AllowHttp`
3. Se zobrazí oznámení o ohrožení zabezpečení pro připojení k zařízení pomocí protokolu HTTP. Po zobrazení výzvy potvrďte zadáním **Y**.
4. Ověřte, že je povolený protokol HTTP zadáním: `Get-HcsSystem`
5. Ověřte, že **RemoteManagementMode** pole zobrazí **HttpsAndHttpEnabled**. Následující obrázek znázorňuje tato nastavení v PuTTY.
   
     ![Sériového portu protokolu HTTPS a HTTP povoleno](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Příprava vzdáleného připojení klienta
Proveďte následující kroky na straně klienta, jak povolit vzdálenou správu.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Příprava klienta vzdáleného připojení
1. Spusťte relaci prostředí Windows PowerShell jako správce. Pokud používáte klienta Windows 10, ve výchozím nastavení, služba Vzdálená správa Windows nastavený na ruční. Je třeba spustit službu tak, že zadáte:

    `Start-Service WinRM`
    
2. Zadejte následující příkaz pro přidání IP adresy zařízení StorSimple do seznamu důvěryhodných hostitelů klienta:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     Nahraďte <*device_ip*> s IP adresou vašeho zařízení; například: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Zadejte následující příkaz, který zařízení přihlašovací údaje uložte do proměnné: 
   
    ```
    $cred = Get-Credential
    ```
    
4. V dialogovém okně, které se zobrazí:
   
   1. Zadejte uživatelské jméno v tomto formátu: *device_ip\SSAdmin*.
   2. Zadejte heslo správce zařízení, která byla nastavena při konfiguraci zařízení pomocí Průvodce instalací. Výchozí heslo je *Heslo1*.
5. Spusťte relaci prostředí Windows PowerShell na zařízení tak, že zadáte tento příkaz:
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > Chcete-li vytvořit relaci prostředí Windows PowerShell pro použití s virtuálním zařízením StorSimple, přidejte `–Port` parametr a zadat veřejný port, který jste nakonfigurovali v vzdálené komunikace pro virtuální zařízení StorSimple.
   
   
V tomto okamžiku byste měli mít aktivní vzdálené relace prostředí Windows PowerShell na zařízení.
   
![Vzdálená komunikace prostředí PowerShell pomocí protokolu HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Připojení přes protokol HTTPS

Připojení k prostředí Windows PowerShell pro StorSimple přes relaci protokolu HTTPS je nejbezpečnější a doporučená metoda vzdálené připojení k zařízení Microsoft Azure StorSimple. Následující postupy vysvětlují, jak nastavit sériové konzoly a klientských počítačů, aby mohli používat protokol HTTPS pro připojení k prostředí Windows PowerShell pro StorSimple.

Na webu Azure portal nebo konzoly sériového portu můžete použít ke konfiguraci vzdálené správy. Vyberte jednu z následujících postupů:

* Povolení vzdálené správy přes protokol HTTPS pomocí webu Azure portal
* [Povolení vzdálené správy přes protokol HTTPS pomocí konzoly sériového portu](#use-the-serial-console-to-enable-remote-management-over-https)

Po povolení vzdálené správy pomocí následujících postupů můžete připravit hostitele pro vzdálenou správu a připojte k zařízení ze vzdáleného hostitele.

* [Příprava hostitelském pro vzdálenou správu](#prepare-the-host-for-remote-management)
* [Připojení k zařízení ze vzdáleného hostitele](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-portal-to-enable-remote-management-over-https"></a>Povolení vzdálené správy přes protokol HTTPS pomocí webu Azure portal

Proveďte následující kroky na webu Azure Portal k povolení vzdálené správy přes protokol HTTPS.

#### <a name="to-enable-remote-management-over-https-from-the-azure-portal"></a>Povolení vzdálené správy přes protokol HTTPS na webu Azure Portal

1. Přejděte do služby Správce zařízení StorSimple. Vyberte **zařízení** a potom klikněte na zařízení, kterou chcete konfigurovat pro vzdálenou správu. Přejděte na **zařízení Nastavení > zabezpečení**.
2. V **nastavení zabezpečení** okna, klikněte na tlačítko **vzdálenou správu**.
3. U položky **Povolit vzdálenou správu** zvolte **Ano**.
4. Nyní můžete připojit pomocí protokolu HTTPS. (Výchozí hodnota je připojení pomocí protokolu HTTPS). Ujistěte se, že je vybrán protokol HTTPS.
5. Klikněte na tlačítko... a potom klikněte na tlačítko **Download Remote Management Certificate**. Zadejte umístění pro uložení tohoto souboru. Je potřeba nainstalovat tento certifikát na klientský nebo hostitelský počítač, který budete používat pro připojení k zařízení.
6. Klikněte na tlačítko **Uložit** a potom klikněte na tlačítko **Ano** po zobrazení výzvy k potvrzení.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>Povolení vzdálené správy přes protokol HTTPS pomocí konzoly sériového portu

Proveďte následující kroky v konzole sériového portu zařízení chcete povolit vzdálenou správu.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Jak povolit vzdálenou správu prostřednictvím konzoly sériového portu zařízení
1. V nabídce konzoly sériového portu vyberte možnost 1. Další informace o použití konzoly sériového portu v zařízení, přejděte na [připojit k prostředí Windows PowerShell pro StorSimple prostřednictvím konzoly sériového portu zařízení](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. Na příkazovém řádku zadejte:
   
     `Enable-HcsRemoteManagement`
   
    To by měl povolit HTTPS na vašem zařízení.
3. Ověřte, že protokol HTTPS se povolila zadáním: 
   
     `Get-HcsSystem`
   
    Ujistěte se, že **RemoteManagementMode** pole zobrazí **HttpsEnabled**. Následující obrázek znázorňuje tato nastavení v PuTTY.
   
     ![Sériový protokol HTTPS povoleno](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. Z výstupu `Get-HcsSystem`, zkopírujte sériové číslo zařízení a uložte ho pro pozdější použití.
   
   > [!NOTE]
   > Sériové číslo mapuje na název CN certifikátu.
   
5. Získáte certifikát pro vzdálenou správu tak, že zadáte: 
   
     `Get-HcsRemoteManagementCert`
   
    Zobrazí se certifikátu podobný následujícímu.
   
    ![Získat certifikát pro vzdálenou správu](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. Zkopírujte údaje certifikátu z **---BEGIN CERTIFICATE---** k **---END CERTIFICATE---** do textového editoru, třeba v poznámkovém bloku a uložte ho jako soubor .cer. (Bude tento soubor je zkopírovat na vzdáleného hostitele při přípravě hostiteli.)
   
   > [!NOTE]
   > Pokud chcete vygenerovat nový certifikát, použijte `Set-HcsRemoteManagementCert` rutiny.
   
### <a name="prepare-the-host-for-remote-management"></a>Příprava hostitelském pro vzdálenou správu

Příprava hostitelském počítači vzdáleného připojení, který používá relaci protokolu HTTPS, proveďte následující postup:

* [Importovat soubor .cer do kořenového úložiště klienta nebo vzdálený hostitel](#to-import-the-certificate-on-the-remote-host).
* [Přidání sériových čísel zařízení do souboru hostitelů na vzdáleného hostitele](#to-add-device-serial-numbers-to-the-remote-host).

Každá z předchozích postupů, je popsána níže.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Chcete-li importovat certifikát na vzdáleném hostiteli
1. Klikněte pravým tlačítkem na soubor .cer a vyberte **instalace certifikátu**. Otevře se Průvodce importem certifikátu.
   
    ![Průvodce importem certifikátu 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. Pro **Store umístění**vyberte **místního počítače**a potom klikněte na tlačítko **Další**.
3. Vyberte **všechny certifikáty umístit v následujícím úložišti**a potom klikněte na tlačítko **Procházet**. Přejděte do kořenového úložiště vzdáleného hostitele a potom klikněte na tlačítko **Další**.
   
    ![Průvodce importem certifikátu 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. Klikněte na **Dokončit**. Zobrazí se zpráva s oznámením, že import proběhl úspěšně.
   
    ![Průvodce importem certifikátu 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Chcete-li přidat sériová čísla zařízení ke vzdálenému hostiteli
1. Spusťte jako správce program Poznámkový blok a pak otevřete soubor hosts umístěný ve \Windows\System32\Drivers\etc.
2. Přidejte následující tři položky do souboru hostitelů: **DATA 0 IP adresu**, **pevná IP adresa adaptéru 0**, a **řadiče 1 pevnou IP adresu**.
3. Zadejte sériové číslo zařízení, který jste předtím uložili. Namapujte tento parametr na IP adresu jak je znázorněno na následujícím obrázku. Pro řadič 0 a řadič 1 připojit **prvku Controller0** a **prvku Controller1** na konci sériové číslo (název CN).
   
    ![Název CN přidání do souboru hostitelů](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. Uložte soubor hostitelů.

### <a name="connect-to-the-device-from-the-remote-host"></a>Připojení k zařízení ze vzdáleného hostitele

Zadejte relaci SSAdmin na vašem zařízení z klienta nebo vzdálený hostitel pomocí prostředí Windows PowerShell a protokolu SSL. Relace SSAdmin mapuje na možnost 1 [konzoly sériového portu](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) nabídce vašeho zařízení.

Na počítači, ze kterého chcete navázat vzdálené připojení prostředí Windows PowerShell proveďte následující postup.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-ssl"></a>Zadejte relaci SSAdmin na zařízení pomocí Windows Powershellu a SSL
1. Spusťte relaci prostředí Windows PowerShell jako správce. Pokud používáte klienta Windows 10, ve výchozím nastavení, služba Vzdálená správa Windows nastavený na ruční. Je třeba spustit službu tak, že zadáte:

    `Start-Service WinRM`

2. Přidáte IP adresu zařízení pro důvěryhodného hostitele klienta tak, že zadáte:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    Kde <*device_ip*> je IP adresa vašeho zařízení, třeba: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. K vytvoření nových přihlašovacích údajů, zadejte:
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    Kde <*IP cílové zařízení*> je IP adresa DATA 0 pro vaše zařízení; například **10.126.173.90** jak je znázorněno na předchozím obrázku ze souboru hosts. Také zadejte heslo správce pro vaše zařízení.
4. Vytvořte relaci zadáním:
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    Pro parametr – ComputerName v rutině, zadejte <*sériové číslo cílového zařízení*>. Tímto sériovým číslem byl namapován na IP adresu DATA 0 v souboru hostitelů na vzdáleného hostitele; například **SHX0991003G44MT** jak je znázorněno na následujícím obrázku.
5. Zadejte:
   
     `Enter-PSSession $session`
6. Budete muset počkat několik minut a pak už se připojíte k zařízení pomocí protokolu HTTPS přes protokol SSL. Zobrazí se zpráva, která označuje, že jste připojeni k zařízení.
   
    ![Vzdálená komunikace prostředí PowerShell pomocí protokolu HTTPS a SSL](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Další postup

* Další informace o [pomocí Windows Powershellu ke správě zařízení StorSimple](storsimple-8000-windows-powershell-administration.md).
* Další informace o [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

