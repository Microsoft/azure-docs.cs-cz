---
title: Konfigurace zabezpečení dělení a slučování | Dokumentace Microsoftu
description: Nastavit x409 certifikáty pro šifrování s služby dělení a slučování pro elastické škálování.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: de758d38626107f28211f79a4772c3e887085776
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53599822"
---
# <a name="split-merge-security-configuration"></a>Konfigurace zabezpečení dělení a slučování
Použití služby dělení a slučování, musíte správně nakonfigurovat zabezpečení. Tato služba je součástí funkce elastické škálování služby Microsoft Azure SQL Database. Další informace najdete v tématu [elastické škálování rozdělení a sloučení kurz Service](sql-database-elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Konfigurace certifikátů
Certifikáty jsou nakonfigurovat dvěma způsoby. 

1. [Konfigurace certifikátu SSL](#to-configure-the-ssl-certificate)
2. [Ke konfiguraci klientských certifikátů](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>Chcete-li získat certifikáty
Certifikáty můžete získat z veřejné certifikační autority (CA) nebo [služby certifikátů Windows](https://msdn.microsoft.com/library/windows/desktop/aa376539.aspx). Toto jsou upřednostňované metody k získání certifikátů.

Pokud tyto možnosti nejsou k dispozici, můžete vygenerovat **certifikáty podepsané svým držitelem**.

## <a name="tools-to-generate-certificates"></a>Nástroje pro generování certifikátů
* [makecert.exe](https://msdn.microsoft.com/library/bfsktky3.aspx)
* [pvk2pfx.exe](https://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>Chcete-li spustit nástroje
* Z příkazový řádek vývojáře pro Visual Studia, najdete v článku [příkazový řádek sady Visual Studio](https://msdn.microsoft.com/library/ms229859.aspx) 
  
    Pokud je nainstalovaná, přejděte na:
  
        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
* Získat ze sady WDK [Windows 8.1: Stažení sad a nástrojů](https://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-ssl-certificate"></a>Konfigurace certifikátu SSL
Certifikát SSL se vyžaduje k zašifrování komunikace a ověřování serveru. Zvolte nejvíce hodí následující tři scénáře a spusťte všechny jeho kroky:

### <a name="create-a-new-self-signed-certificate"></a>Vytvořit nový certifikát podepsaný svým držitelem
1. [Vytvořit certifikát podepsaný svým držitelem](#create-a-self-signed-certificate)
2. [Vytvoření souboru PFX pro certifikát SSL podepsaný svým držitelem](#create-pfx-file-for-self-signed-ssl-certificate)
3. [Nahrát certifikát SSL pro Cloudovou službu](#upload-ssl-certificate-to-cloud-service)
4. [Aktualizace certifikátu SSL v konfiguračním souboru služby](#update-ssl-certificate-in-service-configuration-file)
5. [Importovat certifikační autorita protokolu SSL](#import-ssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Pokud chcete použít existující certifikát z úložiště certifikátů
1. [Exportovat certifikát SSL od certifikátu Store](#export-ssl-certificate-from-certificate-store)
2. [Nahrát certifikát SSL pro Cloudovou službu](#upload-ssl-certificate-to-cloud-service)
3. [Aktualizace certifikátu SSL v konfiguračním souboru služby](#update-ssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Pokud chcete použít existující certifikát v souboru PFX
1. [Nahrát certifikát SSL pro Cloudovou službu](#upload-ssl-certificate-to-cloud-service)
2. [Aktualizace certifikátu SSL v konfiguračním souboru služby](#update-ssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>Ke konfiguraci klientských certifikátů
Klientské certifikáty jsou nutné pro ověřování požadavků na službu. Zvolte nejvíce hodí následující tři scénáře a spusťte všechny jeho kroky:

### <a name="turn-off-client-certificates"></a>Vypnout klientské certifikáty
1. [Vypnout ověřování pomocí certifikátu klienta](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Vydat nové certifikáty podepsané svým držitelem klienta
1. [Vytvoření podepsaný certifikační autoritou](#create-a-self-signed-certification-authority)
2. [Nahrajte certifikát certifikační Autority do cloudové služby](#upload-ca-certificate-to-cloud-service)
3. [Aktualizace certifikátu certifikační Autority v konfiguračním souboru služby](#update-ca-certificate-in-service-configuration-file)
4. [Klientské certifikáty](#issue-client-certificates)
5. [Vytvoření souborů PFX pro klientské certifikáty](#create-pfx-files-for-client-certificates)
6. [Import certifikátu klienta](#Import-Client-Certificate)
7. [Zkopírujte kryptografické otisky certifikátu klienta](#copy-client-certificate-thumbprints)
8. [Konfigurace povolených klientů v konfiguračním souboru služby](#configure-allowed-clients-in-the-service-configuration-file)

### <a name="use-existing-client-certificates"></a>Použít existující klientské certifikáty
1. [Najít veřejný klíč certifikační Autority](#find-ca-public-key)
2. [Nahrajte certifikát certifikační Autority do cloudové služby](#Upload-CA-certificate-to-cloud-service)
3. [Aktualizace certifikátu certifikační Autority v konfiguračním souboru služby](#Update-CA-Certificate-in-Service-Configuration-File)
4. [Zkopírujte kryptografické otisky certifikátu klienta](#Copy-Client-Certificate-Thumbprints)
5. [Konfigurace povolených klientů v konfiguračním souboru služby](#configure-allowed-clients-in-the-service-configuration-file)
6. [Konfigurace kontroly odvolání certifikátu klienta](#Configure-Client-Certificate-Revocation-Check)

## <a name="allowed-ip-addresses"></a>Povolené IP adresy
Přístup ke koncovým bodům služby je možné omezit na konkrétní rozsahy IP adres.

## <a name="to-configure-encryption-for-the-store"></a>Ke konfiguraci šifrování pro úložiště
Certifikát je potřeba šifrovat přihlašovací údaje, které jsou uloženy v úložišti metadat. Zvolte nejvíce hodí následující tři scénáře a spusťte všechny jeho kroky:

### <a name="use-a-new-self-signed-certificate"></a>Použití nového certifikátu podepsaného svým držitelem
1. [Vytvořit certifikát podepsaný svým držitelem](#create-a-self-signed-certificate)
2. [Vytvoření souboru PFX pro šifrovací certifikát podepsaný svým držitelem](#create-pfx-file-for-self-signed-ssl-certificate)
3. [Nahrát šifrovací certifikát do cloudové služby](#upload-encryption-certificate-to-cloud-service)
4. [Aktualizovat certifikát pro šifrování v konfiguračním souboru služby](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Použít existující certifikát z úložiště certifikátů
1. [Exportovat šifrovací certifikát z certifikátu Store](#export-encryption-certificate-from-certificate-store)
2. [Nahrát šifrovací certifikát do cloudové služby](#upload-encryption-certificate-to-cloud-service)
3. [Aktualizovat certifikát pro šifrování v konfiguračním souboru služby](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Použít existující certifikát v souboru PFX
1. [Nahrát šifrovací certifikát do cloudové služby](#upload-encryption-certificate-to-cloud-service)
2. [Aktualizovat certifikát pro šifrování v konfiguračním souboru služby](#update-encryption-certificate-in-service-configuration-file)

## <a name="the-default-configuration"></a>Výchozí konfigurace
Výchozí konfigurace zakazuje veškerý přístup ke koncovému bodu HTTP. Toto je doporučené nastavení, protože žádosti s těmito koncovými body může provádět citlivé informace, jako jsou přihlašovací údaje databáze.
Výchozí konfigurace umožňuje veškerý přístup ke koncovému bodu HTTPS. Toto nastavení může být dále s omezeným přístupem.

### <a name="changing-the-configuration"></a>Změna konfigurace
Skupina pravidla pro řízení přístupu, které se vztahují a koncový bod se konfigurují v **<EndpointAcls>** tématu **konfigurační soubor služby**.

    <EndpointAcls>
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
    </EndpointAcls>

Jsou nakonfigurovaná pravidla ve skupině pro řízení přístupu v <AccessControl name=""> oddílu služby konfiguračního souboru. 

Tento formát je vysvětlený v dokumentaci k seznamy řízení přístupu k síti.
Například pokud chcete povolit jen IP adresy v rozsahu 100.100.0.0 k 100.100.255.255 přístup ke koncovému bodu HTTPS, pravidel bude vypadat takto:

    <AccessControl name="Retricted">
      <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
      <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
    </AccessControl>
    <EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />

## <a name="denial-of-service-prevention"></a>Odmítnutí služby ochrany před únikem informací
Existují dva různé mechanismy, které jsou podporované pro odhalování a prevenci útoků s cílem odepření služeb:

* Omezit počet souběžných požadavků na vzdáleného hostitele (ve výchozím nastavení vypnuté)
* Omezit rychlost přístupu na vzdáleného hostitele (na ve výchozím nastavení)

Tyto jsou založené na funkcích, které jsou popsána v dynamické zabezpečení protokolu IP ve službě IIS. Při změně této konfigurace dávejte ale pozor na následujících faktorech:

* Chování proxy servery a překlad síťových adres zařízení přes vzdálený hostitel informace
* Každý požadavek na prostředek ve webové roli se považuje za (například načítání skripty, obrázků apod.)

## <a name="restricting-number-of-concurrent-accesses"></a>Omezíte počet souběžných přístupů
Nastavení, které toto chování nakonfigurovat, musí být:

    <Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
    <Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />

Změňte DynamicIpRestrictionDenyByConcurrentRequests na true, pokud chcete povolit tuto ochranu.

## <a name="restricting-rate-of-access"></a>Omezení frekvence přístupu
Nastavení, které toto chování nakonfigurovat, musí být:

    <Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
    <Setting name="DynamicIpRestrictionMaxRequests" value="100" />
    <Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />

## <a name="configuring-the-response-to-a-denied-request"></a>Konfigurace odpověď na žádost o odepření
Následující nastavení konfiguruje odpověď na žádost o odepření:

    <Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
Naleznete v dokumentaci pro ostatní podporované hodnoty pro dynamické zabezpečení protokolu IP ve službě IIS.

## <a name="operations-for-configuring-service-certificates"></a>Operace konfigurace certifikátů služby
Toto téma se týká jenom pro referenci. Postupujte podle kroků konfigurace uvedených v:

* Konfigurace certifikátu SSL
* Nakonfigurujte klientské certifikáty

## <a name="create-a-self-signed-certificate"></a>Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)
Spusťte:

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha256 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Chcete-li přizpůsobit:

* -n se adresa URL služby. Zástupné znaky ("CN = * .cloudapp .net") a alternativní názvy ("CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net") jsou podporovány.
* -e se datum vypršení platnosti certifikátu vytvořit silné heslo a jeho po zobrazení výzvy zadejte.

## <a name="create-pfx-file-for-self-signed-ssl-certificate"></a>Vytvořte soubor PFX pro certifikát SSL podepsaný svým držitelem
Spusťte:

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Zadejte heslo a potom export certifikátu s těmito možnostmi:

* Ano, exportovat privátní klíč
* Exportovat všechny rozšířené vlastnosti

## <a name="export-ssl-certificate-from-certificate-store"></a>Exportovat certifikát SSL z úložiště certifikátů.
* Najít certifikát
* Klikněte na tlačítko Akce -> všechny úkoly -> Export...
* Exportujte certifikát do. Soubor PFX pomocí těchto možností:
  * Ano, exportovat privátní klíč
  * Pokud je to možné zahrnout všechny certifikáty na cestě k certifikátu * exportovat všechny rozšířené vlastnosti

## <a name="upload-ssl-certificate-to-cloud-service"></a>Nahrát certifikát SSL pro cloudovou službu
Nahrávání certifikátu s existujícím nebo vygenerovat. Soubor PFX pomocí páru klíčů SSL:

* Zadejte heslo ochrana privátního klíče

## <a name="update-ssl-certificate-in-service-configuration-file"></a>Aktualizace certifikátu SSL v konfiguračním souboru služby
Kryptografický otisk certifikátu nahrát do cloudové služby aktualizujte hodnoty kryptografického otisku následující nastavení v konfiguračním souboru služby:

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-ssl-certification-authority"></a>Importovat certifikační autorita protokolu SSL
Postupujte podle těchto kroků ve všech účtu nebo počítač, který bude komunikovat se službou:

* Dvakrát klikněte. Soubor CER v Průzkumníku Windows
* V dialogovém okně Certifikát klikněte na tlačítko Nainstalovat certifikát...
* Importujte certifikát do úložiště Důvěryhodné kořenové certifikační autority

## <a name="turn-off-client-certificate-based-authentication"></a>Vypnout ověřování pomocí certifikátu klienta
Je podporován pouze na základě certifikátů ověření klienta a jeho zakázání proto umožní veřejný přístup ke koncovým bodům služby, pokud ostatní mechanismy jsou na místě (například Microsoft Azure Virtual Network).

Změna těchto nastavení na hodnotu false v konfiguračním souboru služby, chcete-li vypnout funkci:

    <Setting name="SetupWebAppForClientCertificates" value="false" />
    <Setting name="SetupWebserverForClientCertificates" value="false" />

V nastavení certifikátu certifikační Autority, zkopírujte se stejným kryptografickým otiskem jako certifikát SSL:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="create-a-self-signed-certification-authority"></a>Vytvoření podepsaný certifikační autoritou
Proveďte následující kroky k vytvoření certifikátu podepsaného svým držitelem tak, aby fungoval jako certifikační autorita:

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha256 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Přizpůsobení

* -e s data vypršení platnosti certifikátu

## <a name="find-ca-public-key"></a>Najít veřejný klíč certifikační Autority
Všechny klientské certifikáty musí byl vydán certifikační autoritou důvěryhodná služba. Najdete veřejný klíč pro certifikační autoritu, která vydala certifikáty, které se chystáte použít k ověření, aby bylo možné nahrát do cloudové služby klienta.

Pokud není k dispozici soubor s veřejným klíčem, můžete ho exportujte z úložiště certifikátů:

* Najít certifikát
  * Vyhledejte klientský certifikát vydaný certifikační autoritou stejné
* Dvakrát klikněte na certifikát.
* Vyberte kartu cestě k certifikátu v dialogovém okně certifikátu.
* Dvakrát klikněte na položku certifikační Autority v cestě.
* Poznámky ve vlastnostech certifikátu.
* Zavřít **certifikát** dialogového okna.
* Najít certifikát
  * Vyhledejte certifikační Autority, jak je uvedeno nahoře.
* Klikněte na tlačítko Akce -> všechny úkoly -> Export...
* Exportujte certifikát do. CER s těmito možnostmi:
  * **Ne, neexportovat privátní klíč**
  * Pokud je to možné zahrnout všechny certifikáty na cestě k certifikátu.
  * Exportujte všechny rozšířené vlastnosti.

## <a name="upload-ca-certificate-to-cloud-service"></a>Nahrajte certifikát certifikační Autority do cloudové služby
Nahrávání certifikátu s existujícím nebo vygenerovat. Soubor CER pomocí veřejného klíče certifikační Autority.

## <a name="update-ca-certificate-in-service-configuration-file"></a>Certifikát certifikační Autority aktualizace v konfiguračním souboru služby
Kryptografický otisk certifikátu nahrát do cloudové služby aktualizujte hodnoty kryptografického otisku následující nastavení v konfiguračním souboru služby:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

Aktualizujte hodnotu toto nastavení se stejným kryptografickým otiskem:

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />

## <a name="issue-client-certificates"></a>Vystavování certifikátů klienta
Jednotlivých oprávnění pro přístup ke službě musí mít klientský certifikát vydaný pro svoje výhradní použití a zvolit silné heslo k ochraně jeho privátní klíč. 

Ve stejném počítači, kde byl certifikát podepsaný svým držitelem certifikační Autority generovány a ukládají je třeba spustit následující kroky:

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha256 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Přizpůsobení:

* -n s ID klienta, která bude ověřena pomocí tohoto certifikátu
* -e se datum vypršení platnosti certifikátu
* MyID.pvk a MyID.cer s jedinečné názvy souborů pro tento certifikát klienta

Tento příkaz vyzve k zadání hesla k vytvořit a použít jednou. Použijte silné heslo.

## <a name="create-pfx-files-for-client-certificates"></a>Vytvoření souborů PFX pro klientské certifikáty
Pro každý certifikát generovaného klienta spusťte:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Přizpůsobení:

    MyID.pvk and MyID.cer with the filename for the client certificate

Zadejte heslo a potom export certifikátu s těmito možnostmi:

* Ano, exportovat privátní klíč
* Exportovat všechny rozšířené vlastnosti
* Osoba, na koho se vydává tento certifikát by měl vybrat heslo pro export

## <a name="import-client-certificate"></a>Import certifikátu klienta
Jednotlivé uživatele, pro kterého se klientský certifikát vystavil importujte pár klíčů na počítačích, které se používají ke komunikaci se službou:

* Dvakrát klikněte. Soubor PFX v Průzkumníku Windows
* Importovat certifikát do osobního úložiště s alespoň tuto možnost:
  * Zahrnout všechny rozšířené vlastnosti checked

## <a name="copy-client-certificate-thumbprints"></a>Zkopírujte kryptografické otisky certifikátu klienta
Jednotlivé uživatele, pro kterého se klientský certifikát vystavil musí následujícím postupem, aby bylo možné získat kryptografický otisk jejich certifikát, který se přidá do konfiguračního souboru služby:

* Spustit certmgr.exe
* Vyberte kartu Osobní
* Dvakrát klikněte na klientský certifikát pro ověřování
* V dialogovém okně certifikátů, které se otevře vyberte kartu Podrobnosti
* Ujistěte se, že zobrazit se zobrazuje všechny
* Vyberte pole s názvem kryptografický otisk do seznamu
* Zkopírujte hodnotu kryptografického otisku ** odstranění neviditelné znaky znakové sady Unicode před první číslice ** odstranit jenom mezery.

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Konfigurace klientů povolené v konfiguračním souboru služby
Aktualizujte hodnotu následující nastavení v konfiguračním souboru služby s čárkou oddělený seznam kryptografických otisků klientských certifikátů, povolí se přístup ke službě:

    <Setting name="AllowedClientCertificateThumbprints" value="" />

## <a name="configure-client-certificate-revocation-check"></a>Konfigurace kontroly odvolání certifikátu klienta
Ve výchozím nastavení nekontroluje s certifikační autoritou pro stav odvolání certifikátů klienta. Chcete-li kontrol, pokud certifikační autorita, která vydala certifikáty klienta podporuje tyto kontroly, změňte s některou z hodnot fronty definovaných ve výčtu X509RevocationMode následující nastavení:

    <Setting name="ClientCertificateRevocationCheck" value="NoCheck" />

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>Vytvořte soubor PFX pro certifikáty podepsané svým držitelem šifrování
Šifrovací certifikát spusťte tento příkaz:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Přizpůsobení:

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Zadejte heslo a potom export certifikátu s těmito možnostmi:

* Ano, exportovat privátní klíč
* Exportovat všechny rozšířené vlastnosti
* Budete potřebovat heslo při nahrávání certifikátu do cloudové služby.

## <a name="export-encryption-certificate-from-certificate-store"></a>Exportovat šifrovací certifikát z úložiště certifikátů.
* Najít certifikát
* Klikněte na tlačítko Akce -> všechny úkoly -> Export...
* Exportujte certifikát do. Soubor PFX pomocí těchto možností: 
  * Ano, exportovat privátní klíč
  * Pokud je to možné zahrnout všechny certifikáty na cestě k certifikátu 
* Exportovat všechny rozšířené vlastnosti

## <a name="upload-encryption-certificate-to-cloud-service"></a>Nahrát šifrovací certifikát do cloudové služby
Nahrávání certifikátu s existujícím nebo vygenerovat. Soubor PFX pomocí páru klíčů šifrování:

* Zadejte heslo ochrana privátního klíče

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Aktualizovat certifikát pro šifrování v konfiguračním souboru služby
Aktualizujte hodnotu kryptografického otisku z následujících nastavení v konfiguračním souboru služby s kryptografickým otiskem certifikátu nahrát do cloudové služby:

    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="common-certificate-operations"></a>Běžné operace s certifikáty
* Konfigurace certifikátu SSL
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
8. Rozbalte **certifikáty**.
9. Rozbalte uzel úložiště certifikátů.
10. Rozbalte uzel podřízené certifikátu.
11. V seznamu vyberte certifikát.

## <a name="export-certificate"></a>Export certifikátu
V **Průvodce exportem certifikátu**:

1. Klikněte na tlačítko **Další**.
2. Vyberte **Ano**, pak **exportovat soukromý klíč**.
3. Klikněte na tlačítko **Další**.
4. Vyberte požadovaný výstupní formát souboru.
5. Zkontrolujte požadované možnosti.
6. Zkontrolujte **heslo**.
7. Zadejte silné heslo a potvrďte ho.
8. Klikněte na tlačítko **Další**.
9. Zadejte nebo vyhledejte název souboru, kam se má certifikát uložit (použijte. Příponu PFX).
10. Klikněte na tlačítko **Další**.
11. Klikněte na **Dokončit**.
12. Klikněte na **OK**.

## <a name="import-certificate"></a>Importovat certifikát
V Průvodci importem certifikátu:

1. Vyberte umístění úložiště.
   
   * Vyberte **aktuálního uživatele** Pokud pouze procesů spuštěných v rámci aktuální uživatel bude mít přístup k službě
   * Vyberte **místního počítače** Pokud jiné procesy v tomto počítači bude mít přístup k službě
2. Klikněte na tlačítko **Další**.
3. Pokud import ze souboru, zkontrolujte cestu k souboru.
4. Pokud importujete. Soubor PFX:
   1. Zadejte heslo ochranou privátního klíče
   2. Vyberte možnosti importu
5. Vyberte "Místo" certifikáty do následujícího úložiště
6. Klikněte na **Browse** (Procházet).
7. Vyberte požadované úložiště.
8. Klikněte na **Dokončit**.
   
   * Pokud jste vybrali úložiště Důvěryhodné kořenové certifikační autority, klikněte na tlačítko **Ano**.
9. Klikněte na tlačítko **OK** na všechny systémy windows dialogové okno.

## <a name="upload-certificate"></a>Nahrání certifikátu
Na webu [Azure Portal](https://portal.azure.com/)

1. Vyberte **cloudových služeb**.
2. Vyberte cloudovou službu.
3. V horní nabídce klikněte na tlačítko **certifikáty**.
4. Na dolním panelu, klikněte na tlačítko **nahrát**.
5. Vyberte soubor certifikátu.
6. Pokud se jedná. PFX soubor, zadejte heslo pro privátní klíč.
7. Po dokončení, zkopírujte kryptografický otisk certifikátu z nové položky v seznamu.

## <a name="other-security-considerations"></a>Další informace o zabezpečení
Nastavení protokolu SSL, které jsou popsané v tomto dokumentu šifrování komunikace mezi službou a klienty při použití koncového bodu HTTPS. To je důležité, protože přihlašovací údaje pro přístup k databázi a případně také další citlivé informace, které jsou obsaženy v komunikaci. Upozorňujeme však, že služba ukládá vnitřní stav, včetně přihlašovacích údajů, v jeho vnitřní tabulky ve službě Microsoft Azure SQL database, který jste zadali pro metadata úložiště v rámci vašeho předplatného Microsoft Azure. Databáze byla definována jako součást následující nastavení v konfiguračním souboru služby (. Soubor .CSCFG): 

    <Setting name="ElasticScaleMetadata" value="Server=…" />

Přihlašovací údaje uložené v této databázi se zašifrují. Ale jako osvědčený postup, zajistěte webových a pracovních rolí vaše nasazení služby se udržovat a bezpečné jako služby, obě mají přístup k databázi metadat a certifikát, který slouží k šifrování a dešifrování uložené přihlašovací údaje. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

