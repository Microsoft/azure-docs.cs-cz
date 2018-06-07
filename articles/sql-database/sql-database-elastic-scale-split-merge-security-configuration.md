---
title: Konfigurace zabezpečení rozdělení sloučení | Microsoft Docs
description: Nastavit x409 certifikáty pro šifrování se službou rozdělení či sloučení pro elastické škálování.
metakeywords: Elastic Database certificates security
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: bb2090aba61f32e79fe3a9fd950e6e3688193d7d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647081"
---
# <a name="split-merge-security-configuration"></a>Konfigurace zabezpečení rozdělení sloučení
Pokud chcete používat službu rozdělení či sloučení, musíte nakonfigurovat správně zabezpečení. Služba je součástí funkce elastické škálování sady Microsoft Azure SQL Database. Další informace najdete v tématu [elastické škálování rozdělení a sloučení kurz služby](sql-database-elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Konfigurace certifikátů
Certifikáty jsou nakonfigurovat dvěma způsoby. 

1. [Konfigurovat certifikát protokolu SSL](#to-configure-the-ssl-certificate)
2. [Ke konfiguraci klientských certifikátů](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>Chcete-li získat certifikáty
Certifikáty nelze získat z veřejné certifikační autority (CA) nebo [certifikát služby systému Windows](http://msdn.microsoft.com/library/windows/desktop/aa376539.aspx). Tyto jsou upřednostňované metody k získání certifikátů.

Pokud nejsou k dispozici tyto možnosti, můžete vygenerovat **certifikáty podepsané svým držitelem**.

## <a name="tools-to-generate-certificates"></a>Nástroje pro generování certifikátů
* [makecert.exe](http://msdn.microsoft.com/library/bfsktky3.aspx)
* [pvk2pfx.exe](http://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>Ke spuštění nástroje
* Z příkazového řádku pro vývojáře Visual Studia, najdete v části [Visual Studio – příkazový řádek](http://msdn.microsoft.com/library/ms229859.aspx) 
  
    Pokud nainstalovaná, přejděte na:
  
        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
* Získat WDK z [Windows 8.1: stažení sad a nástrojů](http://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-ssl-certificate"></a>Konfigurovat certifikát protokolu SSL
Je vyžadován certifikát SSL k zašifrování komunikace a ověřování serveru. Zvolte nejvíce použít následující tři scénáře a spusťte všechny jeho kroky:

### <a name="create-a-new-self-signed-certificate"></a>Vytvořit nový certifikát podepsaný svým držitelem
1. [Vytvořit certifikát podepsaný svým držitelem](#create-a-self-signed-certificate)
2. [Vytvoření souboru PFX pro certifikát SSL podepsaný svým držitelem](#create-pfx-file-for-self-signed-ssl-certificate)
3. [Nahrajte certifikát SSL pro cloudové služby](#upload-ssl-certificate-to-cloud-service)
4. [Aktualizovat certifikát SSL v konfiguračním souboru služby](#update-ssl-certificate-in-service-configuration-file)
5. [Import certifikační autorita protokolu SSL](#import-ssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Chcete-li použít stávající certifikát z úložiště certifikátů.
1. [Exportovat certifikát SSL z úložiště certifikátů.](#export-ssl-certificate-from-certificate-store)
2. [Nahrajte certifikát SSL pro cloudové služby](#upload-ssl-certificate-to-cloud-service)
3. [Aktualizovat certifikát SSL v konfiguračním souboru služby](#update-ssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Chcete-li použít existující certifikát v souboru PFX
1. [Nahrajte certifikát SSL pro cloudové služby](#upload-ssl-certificate-to-cloud-service)
2. [Aktualizovat certifikát SSL v konfiguračním souboru služby](#update-ssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>Ke konfiguraci klientských certifikátů
Klientské certifikáty jsou požadovány k ověřování žádostí ve službě. Zvolte nejvíce použít následující tři scénáře a spusťte všechny jeho kroky:

### <a name="turn-off-client-certificates"></a>Vypnout klientské certifikáty
1. [Vypněte ověřování na základě certifikátu klienta](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Vydat nové certifikáty podepsané svým držitelem klienta
1. [Vytvořit podepsaný certifikační autoritou](#create-a-self-signed-certification-authority)
2. [Nahrajte certifikát certifikační Autority do cloudové služby](#upload-ca-certificate-to-cloud-service)
3. [Aktualizovat certifikát certifikační Autority v konfiguračním souboru služby](#update-ca-certificate-in-service-configuration-file)
4. [Problém klientské certifikáty](#issue-client-certificates)
5. [Vytvářet soubory PFX pro certifikáty klientů](#create-pfx-files-for-client-certificates)
6. [Import certifikátu klienta](#Import-Client-Certificate)
7. [Zkopírujte kryptografické otisky certifikátu klienta](#copy-client-certificate-thumbprints)
8. [Konfigurace klientů povolených v konfiguračním souboru služby](#configure-allowed-clients-in-the-service-configuration-file)

### <a name="use-existing-client-certificates"></a>Použít existující klientské certifikáty
1. [Najít veřejného klíče certifikační Autority](#find-ca-public-key)
2. [Nahrajte certifikát certifikační Autority do cloudové služby](#Upload-CA-certificate-to-cloud-service)
3. [Aktualizovat certifikát certifikační Autority v konfiguračním souboru služby](#Update-CA-Certificate-in-Service-Configuration-File)
4. [Zkopírujte kryptografické otisky certifikátu klienta](#Copy-Client-Certificate-Thumbprints)
5. [Konfigurace klientů povolených v konfiguračním souboru služby](#configure-allowed-clients-in-the-service-configuration-file)
6. [Konfigurace Kontrola odvolání certifikátu klienta](#Configure-Client-Certificate-Revocation-Check)

## <a name="allowed-ip-addresses"></a>Povolené IP adresy
Přístup ke koncovým bodům služby lze omezit na konkrétní rozsahy IP adres.

## <a name="to-configure-encryption-for-the-store"></a>Konfigurace šifrování pro úložiště
K šifrování přihlašovacích údajů, které jsou uložené v úložišti metadat je vyžadován certifikát. Zvolte nejvíce použít následující tři scénáře a spusťte všechny jeho kroky:

### <a name="use-a-new-self-signed-certificate"></a>Použití nového certifikátu podepsaného svým držitelem
1. [Vytvořit certifikát podepsaný svým držitelem](#create-a-self-signed-certificate)
2. [Vytvořit soubor PFX pro šifrovací certifikát podepsaný svým držitelem](#create-pfx-file-for-self-signed-ssl-certificate)
3. [Nahrát šifrovací certifikát do cloudové služby](#upload-encryption-certificate-to-cloud-service)
4. [Aktualizovat certifikát šifrování v konfiguračním souboru služby](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Použít stávající certifikát z úložiště certifikátů.
1. [Exportovat šifrovací certifikát z úložiště certifikátů.](#export-encryption-certificate-from-certificate-store)
2. [Nahrát šifrovací certifikát do cloudové služby](#upload-encryption-certificate-to-cloud-service)
3. [Aktualizovat certifikát šifrování v konfiguračním souboru služby](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Použít stávající certifikát v souboru PFX
1. [Nahrát šifrovací certifikát do cloudové služby](#upload-encryption-certificate-to-cloud-service)
2. [Aktualizovat certifikát šifrování v konfiguračním souboru služby](#update-encryption-certificate-in-service-configuration-file)

## <a name="the-default-configuration"></a>Výchozí konfigurace
Výchozí konfigurace odmítne všechny přístup ke koncovému bodu HTTP. Toto je doporučená nastavení, protože žádosti, které chcete tyto koncové body mohou provádět citlivé údaje, jako jsou přihlašovací údaje databáze.
Výchozí konfigurace umožňuje veškerý přístup ke koncovému bodu HTTPS. Toto nastavení může být další s omezeným přístupem.

### <a name="changing-the-configuration"></a>Změna konfigurace
Skupiny pravidly řízení přístupu, která se týkají a koncového bodu se konfigurují v **<EndpointAcls>** tématu **konfigurační soubor služby**.

    <EndpointAcls>
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
    </EndpointAcls>

Pravidla ve skupině řízení přístupu jsou nakonfigurována v <AccessControl name=""> oddíl konfiguračního souboru služby. 

Formát je vysvětleno v dokumentaci seznamů řízení přístupu k síti.
Například povolit jenom IP adresy v rozsahu 100.100.0.0 k 100.100.255.255 pro přístup k koncový bod HTTPS, pravidla bude vypadat takto:

    <AccessControl name="Retricted">
      <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
      <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
    </AccessControl>
    <EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />

## <a name="denial-of-service-prevention"></a>Odmítnutí služby prevence
Existují dva různé mechanismy, které jsou podporovány rozpoznat a zabránit útokům odmítnutí služby:

* Omezit počet souběžných požadavků na vzdáleného hostitele (ve výchozím nastavení vypnuté)
* Omezit rychlost přístupu na vzdáleného hostitele (na ve výchozím nastavení)

Tyto jsou založené na funkce, které jsou popsána v dynamické IP zabezpečení ve službě IIS. Při změně této konfigurace mějte na paměti následující faktory:

* Chování zařízení překlad síťových adres přes informace o vzdáleného hostitele a proxy servery
* Každý k jakémukoli prostředku ve webové roli, je žádost považována za (například načítání skripty, obrázky atd.)

## <a name="restricting-number-of-concurrent-accesses"></a>Omezuje počet souběžných přístupů
Jsou nastavení, které konfiguraci tohoto chování:

    <Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
    <Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />

Změnit DynamicIpRestrictionDenyByConcurrentRequests na hodnotu true, chcete-li povolit tuto ochranu.

## <a name="restricting-rate-of-access"></a>Omezení rychlost přístupu
Jsou nastavení, které konfiguraci tohoto chování:

    <Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
    <Setting name="DynamicIpRestrictionMaxRequests" value="100" />
    <Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />

## <a name="configuring-the-response-to-a-denied-request"></a>Konfigurace odpověď na žádost o odepření
Toto nastavení konfiguruje odpověď na žádost o odepření:

    <Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
Naleznete v dokumentaci k zabezpečení dynamické IP ve službě IIS pro ostatní podporované hodnoty.

## <a name="operations-for-configuring-service-certificates"></a>Operace pro certifikáty služeb konfigurace
Toto téma se týká jenom pro referenci. Postupujte podle kroků konfigurace uvedených v:

* Konfigurovat certifikát protokolu SSL
* Nakonfigurujte klientské certifikáty

## <a name="create-a-self-signed-certificate"></a>Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)
Spusťte:

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha1 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Chcete-li přizpůsobit:

* -n se adresa URL služby. Zástupné znaky ("CN = * .cloudapp .net") a jsou podporovány alternativní názvy (CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net").
* -e s datem vypršení platnosti certifikátu vytvořit silné heslo a zadejte jej po zobrazení výzvy.

## <a name="create-pfx-file-for-self-signed-ssl-certificate"></a>Vytvořit soubor PFX pro certifikát SSL podepsaný svým držitelem
Spusťte:

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Zadejte heslo a poté exportujte certifikát pomocí těchto možností:

* Ano, exportovat soukromý klíč
* Exportovat všechny rozšířené vlastnosti

## <a name="export-ssl-certificate-from-certificate-store"></a>Exportovat certifikát SSL z úložiště certifikátů.
* Najít certifikát
* Klikněte na tlačítko Akce -> všechny úlohy -> Export...
* Export certifikátu do. Soubor PFX pomocí těchto možností:
  * Ano, exportovat soukromý klíč
  * Pokud je to možné zahrnout všechny certifikáty na cestě k certifikátu * exportovat všechny rozšířené vlastnosti

## <a name="upload-ssl-certificate-to-cloud-service"></a>Nahrajte certifikát SSL do cloudové služby
Nahrání certifikátu s existujícím nebo vygenerovat. Soubor PFX s dvojici klíčů SSL:

* Zadejte heslo ochranu privátního klíče

## <a name="update-ssl-certificate-in-service-configuration-file"></a>Aktualizovat certifikát SSL v konfiguračním souboru služby
Aktualizujte hodnotu kryptografického otisku následující nastavení v konfiguračním souboru služby kryptografický otisk certifikátu nahrán do cloudové služby:

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-ssl-certification-authority"></a>Import certifikační autorita protokolu SSL
Postupujte podle těchto kroků v všechny účet nebo počítač, který bude komunikovat se službou:

* Dvakrát klikněte na. Soubor CER v Průzkumníku Windows
* V dialogovém okně Certifikát klikněte na tlačítko Nainstalovat certifikát...
* Import certifikátu do úložiště důvěryhodných kořenových certifikačních autorit

## <a name="turn-off-client-certificate-based-authentication"></a>Vypněte ověřování na základě certifikátu klienta
Je podporován pouze na základě certifikátu ověření klienta a jejím zakázáním umožní pro veřejný přístup ke koncovým bodům služby, pokud ostatní mechanismy jsou na místě (například Microsoft Azure Virtual Network).

Změna těchto nastavení na hodnotu false v konfiguračním souboru služby, chcete-li tuto funkci vypnout:

    <Setting name="SetupWebAppForClientCertificates" value="false" />
    <Setting name="SetupWebserverForClientCertificates" value="false" />

V nastavení certifikátu certifikační Autority, zkopírujte stejným kryptografickým otiskem jako certifikát SSL:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="create-a-self-signed-certification-authority"></a>Vytvořit podepsaný certifikační autoritou
Provést následující kroky, chcete-li vytvořit certifikát podepsaný svým držitelem tak, aby fungoval jako certifikační autority:

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha1 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Chcete-li přizpůsobit ho

* -e s datem vypršení platnosti certifikační

## <a name="find-ca-public-key"></a>Najít veřejného klíče certifikační Autority
Všechny certifikáty klienta musí být vydány certifikační autoritou důvěryhodné službou. Nalezen veřejný klíč pro certifikační autoritu, která vydala certifikáty, které se chystáte použít k ověření, aby bylo možné nahrát do cloudové služby klienta.

Pokud je soubor s veřejný klíč není k dispozici, můžete ho exportujte z úložiště certifikátů:

* Najít certifikát
  * Vyhledejte klientský certifikát vydaný certifikační autoritou stejné
* Dvakrát klikněte na certifikát.
* Vyberte kartu cestě k certifikátu v dialogovém okně certifikát.
* Dvakrát klikněte na položku certifikační Autority v cestě.
* Poznamenejte ve vlastnostech certifikátu.
* Zavřít **certifikát** dialogové okno.
* Najít certifikát
  * Vyhledávání pro certifikační Autoritu uvedených výše.
* Klikněte na tlačítko Akce -> všechny úlohy -> Export...
* Export certifikátu do. CER pomocí těchto možností:
  * **Ne, neexportovat privátní klíč**
  * Pokud je to možné zahrnout všechny certifikáty na cestě k certifikátu.
  * Exportujte všechny rozšířené vlastnosti.

## <a name="upload-ca-certificate-to-cloud-service"></a>Nahrajte certifikát certifikační Autority do cloudové služby
Nahrání certifikátu s existujícím nebo vygenerovat. Soubor CER pomocí veřejného klíče certifikační Autority.

## <a name="update-ca-certificate-in-service-configuration-file"></a>Certifikát certifikační Autority aktualizace v konfiguračním souboru služby
Aktualizujte hodnotu kryptografického otisku následující nastavení v konfiguračním souboru služby kryptografický otisk certifikátu nahrán do cloudové služby:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

Aktualizujte hodnotu následující nastavení se stejným kryptografickým otiskem:

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />

## <a name="issue-client-certificates"></a>Vystavovat certifikáty klienta
Každé jednotlivé oprávnění k přístupu ke službě by měl mít klientský certifikát vydaný pro his/hers výhradní použití a by si měli vybrat, že his/hers vlastní silné heslo k ochraně jeho privátní klíč. 

Ve stejném počítači, kde se certifikát podepsaný svým držitelem certifikační Autority vygenerované a uložené musí provést následující kroky:

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha1 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Přizpůsobení:

* -n s ID pro klientovi, který bude ověřen s tímto certifikátem
* -e s datem vypršení platnosti certifikátu
* MyID.pvk a MyID.cer s jedinečné názvy souborů pro tento certifikát klienta

Tento příkaz zobrazí výzvu k zadání hesla pro vytvořen a pak použít jednou. Použijte silné heslo.

## <a name="create-pfx-files-for-client-certificates"></a>Vytvářet soubory PFX pro klientské certifikáty
Pro každý certifikát generovaného klienta spusťte příkaz:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Přizpůsobení:

    MyID.pvk and MyID.cer with the filename for the client certificate

Zadejte heslo a poté exportujte certifikát pomocí těchto možností:

* Ano, exportovat soukromý klíč
* Exportovat všechny rozšířené vlastnosti
* Jednotlivé, kterým se vydává tento certifikát by měl vybrat heslo export

## <a name="import-client-certificate"></a>Import certifikátu klienta
Každého uživatele, pro kterého klientský certifikát vystavil naimportovat páru klíčů v počítačích, které si bude používat pro komunikaci se službou:

* Dvakrát klikněte na. Soubor PFX v Průzkumníku Windows
* Import certifikátu do osobní úložiště s alespoň tuto možnost:
  * Zahrnout všechny rozšířené vlastnosti zaškrtnutí

## <a name="copy-client-certificate-thumbprints"></a>Zkopírujte kryptografické otisky certifikátu klienta
Každého uživatele, pro kterého klientský certifikát vystavil nutné provést následující kroky, aby bylo možné získat kryptografický otisk his/hers certifikát, který přidá do konfiguračního souboru služby:

* Spustit certmgr.exe
* Vyberte kartu Osobní
* Dvakrát klikněte na certifikát klienta, který se má použít pro ověřování
* V dialogovém okně certifikátů, které se otevře vyberte kartu s podrobnostmi
* Ujistěte se, že všechny zobrazit zobrazení
* Vyberte pole v seznamu s názvem kryptografický otisk
* Zkopírujte hodnotu kryptografického otisku ** odstranit neviditelné znaky znakové sady Unicode před první číslice ** odstranit všechny mezery

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Konfigurace klientů povolené v konfiguračním souboru služby
Čárkami oddělený seznam kryptografické otisky certifikátů klienta povolený přístup ke službě aktualizujte hodnotu následující nastavení v konfiguračním souboru služby:

    <Setting name="AllowedClientCertificateThumbprints" value="" />

## <a name="configure-client-certificate-revocation-check"></a>Konfigurace Kontrola odvolání certifikátu klienta
Ve výchozím nastavení nekontroluje s certifikační autoritou pro stav odvolání certifikátu klienta. Chcete-li zapnout kontrol, pokud certifikační autorita, která vydala certifikáty klienta podporuje tyto kontroly, změňte následující nastavení jedna z hodnot fronty definovaných ve výčtu X509RevocationMode:

    <Setting name="ClientCertificateRevocationCheck" value="NoCheck" />

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>Vytvořit soubor PFX pro certifikáty podepsané svým držitelem šifrování
Šifrovací certifikát spusťte tento příkaz:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Přizpůsobení:

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Zadejte heslo a poté exportujte certifikát pomocí těchto možností:

* Ano, exportovat soukromý klíč
* Exportovat všechny rozšířené vlastnosti
* Heslo budete potřebovat při nahrávání certifikát do cloudové služby.

## <a name="export-encryption-certificate-from-certificate-store"></a>Exportovat šifrovací certifikát z úložiště certifikátů.
* Najít certifikát
* Klikněte na tlačítko Akce -> všechny úlohy -> Export...
* Export certifikátu do. Soubor PFX pomocí těchto možností: 
  * Ano, exportovat soukromý klíč
  * Pokud je to možné zahrnout všechny certifikáty na cestě k certifikátu 
* Exportovat všechny rozšířené vlastnosti

## <a name="upload-encryption-certificate-to-cloud-service"></a>Nahrát šifrovací certifikát do cloudové služby
Nahrání certifikátu s existujícím nebo vygenerovat. Soubor PFX s páru klíčů šifrování:

* Zadejte heslo ochranu privátního klíče

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Aktualizovat certifikát šifrování v konfiguračním souboru služby
Aktualizujte hodnotu kryptografického otisku následující nastavení v konfiguračním souboru služby kryptografický otisk certifikátu nahrán do cloudové služby:

    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="common-certificate-operations"></a>Běžné operace certifikátu
* Konfigurovat certifikát protokolu SSL
* Nakonfigurujte klientské certifikáty

## <a name="find-certificate"></a>Najít certifikát
Postupujte následovně:

1. Spusťte mmc.exe.
2. Soubor -> Přidat nebo odebrat modul Snap-in...
3. Vyberte **certifikáty**.
4. Klikněte na tlačítko **Add** (Přidat).
5. Vyberte umístění úložiště certifikátů.
6. Klikněte na **Dokončit**.
7. Klikněte na **OK**.
8. Rozbalte položku **certifikáty**.
9. Rozbalte uzel úložiště certifikátů.
10. Rozbalte uzel podřízené certifikátu.
11. Vyberte certifikát v seznamu.

## <a name="export-certificate"></a>Export certifikátu
V **Průvodce exportem certifikátu**:

1. Klikněte na **Další**.
2. Vyberte **Ano**, pak **exportovat soukromý klíč**.
3. Klikněte na **Další**.
4. Vyberte formát požadovaný výstupní soubor.
5. Zkontrolujte si požadované možnosti.
6. Zkontrolujte **heslo**.
7. Zadejte silné heslo a potvrďte ho.
8. Klikněte na **Další**.
9. Zadejte nebo vyhledejte název souboru pro uložení certifikátu (použijte. Příponu PFX).
10. Klikněte na **Další**.
11. Klikněte na **Dokončit**.
12. Klikněte na **OK**.

## <a name="import-certificate"></a>Importovat certifikát
V Průvodci importem certifikátu:

1. Vyberte umístění úložiště.
   
   * Vyberte **aktuální uživatel** -li jen procesů spuštěných v rámci aktuální uživatel přistupuje k službě
   * Vyberte **místního počítače** Pokud jiné procesy v tomto počítači se přístup ke službě
2. Klikněte na **Další**.
3. Pokud import ze souboru, zkontrolujte cestu k souboru.
4. Pokud import. Soubor PFX:
   1. Zadejte heslo ochranou privátního klíče
   2. Vyberte možnosti importu
5. Vyberte certifikáty "Místo" v následujícím úložišti
6. Klikněte na **Browse** (Procházet).
7. Vyberte požadované úložiště.
8. Klikněte na **Dokončit**.
   
   * Pokud jste vybrali úložišti Důvěryhodné kořenové certifikační autority, klikněte na tlačítko **Ano**.
9. Klikněte na tlačítko **OK** na všechny systémy windows dialogové okno.

## <a name="upload-certificate"></a>Nahrání certifikátu
Na webu [Azure Portal](https://portal.azure.com/)

1. Vyberte **cloudových služeb**.
2. Vyberte cloudovou službu.
3. V horní nabídce klikněte na tlačítko **certifikáty**.
4. Na dolním panelu klikněte na tlačítko **nahrát**.
5. Vyberte soubor certifikátu.
6. Pokud je. Soubor PFX souboru, zadejte heslo pro privátní klíč.
7. Po dokončení, zkopírujte kryptografický otisk certifikátu z nové položky v seznamu.

## <a name="other-security-considerations"></a>Další aspekty zabezpečení
Nastavení protokolu SSL, které jsou popsané v tomto dokumentu šifrování komunikace mezi službou a jeho klienty, pokud koncový bod HTTPS se používá. To je důležité, protože přihlašovací údaje pro přístup k databázi a další potenciálně citlivé informace, které jsou obsaženy v komunikaci. Upozorňujeme však, že služba ukládá vnitřní stav, včetně přihlašovacích údajů, v jeho interní tabulky v databázi Microsoft Azure SQL, který jste zadali pro metadata úložiště v rámci vašeho předplatného Microsoft Azure. Tuto databázi byl definován jako součást následující nastavení v konfiguračním souboru služby (. Soubor .CSCFG): 

    <Setting name="ElasticScaleMetadata" value="Server=…" />

Přihlašovací údaje uložené v této databázi se šifrují. Však jako osvědčený postup, zajistěte webové a pracovní role vaše nasazení služby jsou pořád aktuální a co se mají přístup k databázi metadat a certifikát použitý k šifrování a dešifrování uložené přihlašovací údaje. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

