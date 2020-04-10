---
title: Konfigurace zabezpečení rozdělení sloučení
description: Nastavte certifikáty x409 pro šifrování pomocí služby rozdělení/sloučení pro elastické škálování.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: a5ea0fd252d1792d4c40cc6d7869f4ba57edc1ad
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011357"
---
# <a name="split-merge-security-configuration"></a>Konfigurace zabezpečení rozdělení sloučení

Chcete-li použít službu Rozdělit/sloučit, musíte správně nakonfigurovat zabezpečení. Služba je součástí funkce Elastické škálování databáze Microsoft Azure SQL Database. Další informace naleznete v [tématu Elastic Scale Split and Merge Service Tutorial](sql-database-elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Konfigurace certifikátů

Certifikáty jsou konfigurovány dvěma způsoby. 

1. [Konfigurace certifikátu TLS/SSL](#to-configure-the-tlsssl-certificate)
2. [Konfigurace klientských certifikátů](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>Chcete-li získat certifikáty

Certifikáty lze získat od veřejných certifikačních úřadů (CA) nebo od [certifikační služby systému Windows](https://msdn.microsoft.com/library/windows/desktop/aa376539.aspx). Jedná se o upřednostňované metody získání certifikátů.

Pokud tyto možnosti nejsou k dispozici, můžete generovat **certifikáty podepsané svým držitelem**.

## <a name="tools-to-generate-certificates"></a>Nástroje pro generování certifikátů

* [Makecert.exe](https://msdn.microsoft.com/library/bfsktky3.aspx)
* [pvk2pfx.exe](https://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>Spuštění nástrojů

* Na příkazovém řádku pro vývojáře pro vizuální studia najdete v [příkazovém řádku sady Visual Studio.](https://msdn.microsoft.com/library/ms229859.aspx) 
  
    Pokud je nainstalován, přejděte na:
  
        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
* Získání sady WDK z [Windows 8.1: Stáhněte si sady a nástroje](https://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-tlsssl-certificate"></a>Konfigurace certifikátu TLS/SSL

K šifrování komunikace a ověření serveru je vyžadován certifikát TLS/SSL. Vyberte nejpoužitelnější ze tří níže uvedených scénářů a proveďte všechny jeho kroky:

### <a name="create-a-new-self-signed-certificate"></a>Vytvoření nového certifikátu podepsaného svým držitelem

1. [Vytvoření certifikátu podepsaného svým držitelem](#create-a-self-signed-certificate)
2. [Vytvořit soubor PFX pro certifikát TLS/SSL podepsaný svým držitelem](#create-pfx-file-for-self-signed-tlsssl-certificate)
3. [Nahrání certifikátu TLS/SSL do cloudové služby](#upload-tlsssl-certificate-to-cloud-service)
4. [Aktualizace certifikátu TLS/SSL v konfiguračním souboru služby](#update-tlsssl-certificate-in-service-configuration-file)
5. [Dovozní certifikační úřad TLS/SSL](#import-tlsssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Použití existujícího certifikátu z úložiště certifikátů
1. [Export certifikátu TL/SSL z úložiště certifikátů](#export-tlsssl-certificate-from-certificate-store)
2. [Nahrání certifikátu TLS/SSL do cloudové služby](#upload-tlsssl-certificate-to-cloud-service)
3. [Aktualizace certifikátu TLS/SSL v konfiguračním souboru služby](#update-tlsssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Použití existujícího certifikátu v souboru PFX
1. [Nahrání certifikátu TLS/SSL do cloudové služby](#upload-tlsssl-certificate-to-cloud-service)
2. [Aktualizace certifikátu TLS/SSL v konfiguračním souboru služby](#update-tlsssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>Konfigurace klientských certifikátů
K ověření požadavků na službu jsou vyžadovány klientské certifikáty. Vyberte nejpoužitelnější ze tří níže uvedených scénářů a proveďte všechny jeho kroky:

### <a name="turn-off-client-certificates"></a>Vypnutí klientských certifikátů
1. [Vypnout ověřování pomocí klientského certifikátu](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Vydanit nové klientské certifikáty podepsané svým držitelem
1. [Vytvoření certifikačního úřadu podepsaného svým držitelem](#create-a-self-signed-certification-authority)
2. [Nahrání certifikátu certifikační autority do cloudové služby](#upload-ca-certificate-to-cloud-service)
3. [Aktualizace certifikátu certifikační autority v konfiguračním souboru služby](#update-ca-certificate-in-service-configuration-file)
4. [Vydat klientské certifikáty](#issue-client-certificates)
5. [Vytvoření souborů PFX pro klientské certifikáty](#create-pfx-files-for-client-certificates)
6. [Importovat klientský certifikát](#import-client-certificate)
7. [Kopírovat kryptografické otisky klientského certifikátu](#copy-client-certificate-thumbprints)
8. [Konfigurace povolených klientů v konfiguračním souboru služby](#configure-allowed-clients-in-the-service-configuration-file)

### <a name="use-existing-client-certificates"></a>Použití existujících klientských certifikátů
1. [Najít veřejný klíč certifikační autority](#find-ca-public-key)
2. [Nahrání certifikátu certifikační autority do cloudové služby](#upload-ca-certificate-to-cloud-service)
3. [Aktualizace certifikátu certifikační autority v konfiguračním souboru služby](#update-ca-certificate-in-service-configuration-file)
4. [Kopírovat kryptografické otisky klientského certifikátu](#copy-client-certificate-thumbprints)
5. [Konfigurace povolených klientů v konfiguračním souboru služby](#configure-allowed-clients-in-the-service-configuration-file)
6. [Konfigurace kontroly odvolání klientského certifikátu](#configure-client-certificate-revocation-check)

## <a name="allowed-ip-addresses"></a>Povolené IP adresy
Přístup ke koncovým bodům služby může být omezen na určité rozsahy adres IP.

## <a name="to-configure-encryption-for-the-store"></a>Konfigurace šifrování pro úložiště
K šifrování přihlašovacích údajů, které jsou uloženy v úložišti metadat, je vyžadován certifikát. Vyberte nejpoužitelnější ze tří níže uvedených scénářů a proveďte všechny jeho kroky:

### <a name="use-a-new-self-signed-certificate"></a>Použití nového certifikátu podepsaného svým držitelem
1. [Vytvoření certifikátu podepsaného svým držitelem](#create-a-self-signed-certificate)
2. [Vytvořit soubor PFX pro šifrovací certifikát podepsaný svým držitelem](#create-pfx-file-for-self-signed-tlsssl-certificate)
3. [Nahrání šifrovacího certifikátu do cloudové služby](#upload-encryption-certificate-to-cloud-service)
4. [Aktualizovat šifrovací certifikát v konfiguračním souboru služby](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Použití existujícího certifikátu z úložiště certifikátů
1. [Export šifrovacího certifikátu z úložiště certifikátů](#export-encryption-certificate-from-certificate-store)
2. [Nahrání šifrovacího certifikátu do cloudové služby](#upload-encryption-certificate-to-cloud-service)
3. [Aktualizovat šifrovací certifikát v konfiguračním souboru služby](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Použití existujícího certifikátu v souboru PFX
1. [Nahrání šifrovacího certifikátu do cloudové služby](#upload-encryption-certificate-to-cloud-service)
2. [Aktualizovat šifrovací certifikát v konfiguračním souboru služby](#update-encryption-certificate-in-service-configuration-file)

## <a name="the-default-configuration"></a>Výchozí konfigurace
Výchozí konfigurace odepře veškerý přístup ke koncovému bodu HTTP. Toto je doporučené nastavení, protože požadavky na tyto koncové body mohou přenášet citlivé informace, jako jsou pověření databáze.
Výchozí konfigurace umožňuje veškerý přístup ke koncovému bodu HTTPS. Toto nastavení může být dále omezeno.

### <a name="changing-the-configuration"></a>Změna konfigurace
Skupina pravidel řízení přístupu, která platí pro koncový bod a koncový bod, je ** \<nakonfigurována v části>endpointAcls** v **konfiguračním souboru služby**.

```xml
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
</EndpointAcls>
```

Pravidla ve skupině řízení přístupu \<jsou konfigurována v části AccessControl name=""> konfiguračního souboru služby. 

Formát je vysvětlen v dokumentaci k seznamům řízení přístupu k síti.
Chcete-li například povolit přístup ke koncovému bodu HTTPS pouze ip adresy v rozsahu 100.100.0.0 až 100.100.255.255, budou pravidla vypadat takto:

```xml
<AccessControl name="Retricted">
    <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
    <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
</AccessControl>
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />
</EndpointAcls>
```

## <a name="denial-of-service-prevention"></a>Prevence odmítnutí služby
Existují dva různé mechanismy podporované pro detekci a prevenci útoků odmítnutí služby:

* Omezit počet souběžných požadavků na vzdáleného hostitele (ve výchozím nastavení vypnuto)
* Omezit rychlost přístupu na vzdáleného hostitele (ve výchozím nastavení zapnuto)

Ty jsou založeny na funkcích dále zdokumentovaných v dynamickém zabezpečení IP ve službě IIS. Při změně této konfigurace si dejte pozor na následující faktory:

* Chování proxy serverů a zařízení pro překlad síťových adres nad informacemi vzdáleného hostitele
* Každý požadavek na libovolný prostředek ve webové roli je považován (například načítání skriptů, obrázků atd.)

## <a name="restricting-number-of-concurrent-accesses"></a>Omezení počtu souběžných přístupů
Nastavení, která konfigurují toto chování jsou:

```xml
<Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
<Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />
```

Změňte DynamicIpRestrictionDenyByConcurrentRequests na true povolit tuto ochranu.

## <a name="restricting-rate-of-access"></a>Omezení přístupu
Nastavení, která konfigurují toto chování jsou:

```xml
<Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
<Setting name="DynamicIpRestrictionMaxRequests" value="100" />
<Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />
```

## <a name="configuring-the-response-to-a-denied-request"></a>Konfigurace odpovědi na zamítnutý požadavek
Následující nastavení konfiguruje odpověď na zamítnutý požadavek:

```xml
<Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
```

Další podporované hodnoty naleznete v dokumentaci k dynamickému zabezpečení protokolu IP ve správě IIS.

## <a name="operations-for-configuring-service-certificates"></a>Operace pro konfiguraci certifikátů služeb
Toto téma je pouze orientační. Postupujte podle kroků konfigurace uvedených v:

* Konfigurace certifikátu TLS/SSL
* Konfigurace klientských certifikátů

## <a name="create-a-self-signed-certificate"></a>Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)
Provést:

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha256 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Přizpůsobení:

* -n s adresou URL služby. Zástupné znaky ("CN=*.cloudapp.net") a alternativní názvy ("CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net") jsou podporovány.
* -e s datem vypršení platnosti certifikátu Vytvořte silné heslo a zadejte ho po zobrazení výzvy.

## <a name="create-pfx-file-for-self-signed-tlsssl-certificate"></a>Vytvoření souboru PFX pro certifikát TLS/SSL podepsaný svým držitelem
Provést:

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Zadejte heslo a pak exportujte certifikát s těmito možnostmi:

* Ano, export ovat soukromý klíč
* Exportovat všechny rozšířené vlastnosti

## <a name="export-tlsssl-certificate-from-certificate-store"></a>Export certifikátu TLS/SSL z úložiště certifikátů
* Najít certifikát
* Klikněte na Akce - > všechny úkoly - > export...
* Vývozní certifikát do . Soubor PFX s těmito možnostmi:
  * Ano, export ovat soukromý klíč
  * Zahrnout všechny certifikáty do certifikační cesty, pokud je to možné *Exportovat všechny rozšířené vlastnosti

## <a name="upload-tlsssl-certificate-to-cloud-service"></a>Nahrání certifikátu TLS/SSL do cloudové služby
Nahrajte certifikát se stávajícím nebo generovaným . Soubor PFX s párem klíčů TLS:

* Zadejte heslo chránící informace o soukromém klíči.

## <a name="update-tlsssl-certificate-in-service-configuration-file"></a>Aktualizace certifikátu TLS/SSL v konfiguračním souboru služby
Aktualizujte hodnotu kryptografického otisku následujícího nastavení v konfiguračním souboru služby kryptografickým otiskem certifikátu nahraného do cloudové služby:

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-tlsssl-certification-authority"></a>Importovat certifikační autoritu TLS/SSL
Postupujte podle následujících kroků ve všech účtech nebo počítači, který bude komunikovat se službou:

* Poklepejte na soubor . Soubor CER v Průzkumníkovi Windows
* V dialogovém okně Certifikát klikněte na Instalovat certifikát...
* Import certifikátu do úložiště důvěryhodných kořenových certifikačních úřadů

## <a name="turn-off-client-certificate-based-authentication"></a>Vypnutí ověřování pomocí klientského certifikátu
Je podporováno pouze ověřování založené na klientských certifikátech a jeho zakázání umožní veřejný přístup ke koncovým bodům služby, pokud nejsou zavedeny jiné mechanismy (například Virtuální síť Microsoft Azure).

Chcete-li funkci vypnout, změňte tato nastavení na hodnotu false v konfiguračním souboru služby:

```xml
<Setting name="SetupWebAppForClientCertificates" value="false" />
<Setting name="SetupWebserverForClientCertificates" value="false" />
```

Potom zkopírujte stejný kryptografický otisk jako certifikát TLS/SSL v nastavení certifikátu certifikační autority:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="create-a-self-signed-certification-authority"></a>Vytvoření certifikačního úřadu podepsaného svým držitelem
Chcete-li vytvořit certifikát podepsaný svým držitelem, který bude fungovat jako certifikační úřad, proveďte následující kroky:

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha256 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Chcete-li jej přizpůsobit

* -e s datem vypršení platnosti certifikátu

## <a name="find-ca-public-key"></a>Najít veřejný klíč certifikační autority
Všechny klientské certifikáty musí být vydány certifikačníautoritou důvěryhodnou službou. Najděte veřejný klíč certifikačníautority, která vydala klientské certifikáty, které budou použity k ověřování, aby bylo možné jej odeslat do cloudové služby.

Pokud soubor s veřejným klíčem není k dispozici, exportujte jej z úložiště certifikátů:

* Najít certifikát
  * Hledání klientského certifikátu vydaného stejným certifikačním úřadem
* Poklepejte na certifikát.
* V dialogovém okně Certifikát vyberte kartu Certifikační cesta.
* Poklepejte na položku certifikační autority v cestě.
* Udělejte si poznámky o vlastnostech certifikátu.
* Zavřete dialogové okno **Certifikát.**
* Najít certifikát
  * Vyhledejte výše uvedenou certifikační autoritu.
* Klikněte na Akce - > všechny úkoly - > export...
* Vývozní certifikát do . CER s těmito možnostmi:
  * **Ne, neexportovat soukromý klíč**
  * Pokud je to možné, zahrňte všechny certifikáty do certifikační cesty.
  * Exportovat všechny rozšířené vlastnosti.

## <a name="upload-ca-certificate-to-cloud-service"></a>Nahrání certifikátu certifikační autority do cloudové služby
Nahrajte certifikát se stávajícím nebo generovaným . soubor CER s veřejným klíčem certifikační autority.

## <a name="update-ca-certificate-in-service-configuration-file"></a>Aktualizace certifikátu certifikační autority v konfiguračním souboru služby
Aktualizujte hodnotu kryptografického otisku následujícího nastavení v konfiguračním souboru služby kryptografickým otiskem certifikátu nahraného do cloudové služby:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

Aktualizujte hodnotu následujícího nastavení stejným kryptografickým otiskem:

```xml
<Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
```

## <a name="issue-client-certificates"></a>Vydávání klientských certifikátů
Každý jednotlivec oprávněný k přístupu ke službě by měl mít klientský certifikát vydaný pro jejich výhradní použití a měl by zvolit své vlastní silné heslo pro ochranu svého soukromého klíče. 

Následující kroky musí být provedeny ve stejném počítači, kde byl vygenerován a uložen certifikát certifikační autority podepsaný svým držitelem:

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha256 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Přizpůsobení:

* -n s ID pro klienta, který bude ověřen pomocí tohoto certifikátu
* -e s datem vypršení platnosti certifikátu
* MyID.pvk a MyID.cer s jedinečnými názvy souborů pro tento klientský certifikát

Tento příkaz vyzve k vytvoření hesla a jeho použití jednou. Použijte silné heslo.

## <a name="create-pfx-files-for-client-certificates"></a>Vytvoření souborů PFX pro klientské certifikáty
Pro každý vygenerovaný klientský certifikát proveďte:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Přizpůsobení:

    MyID.pvk and MyID.cer with the filename for the client certificate

Zadejte heslo a pak exportujte certifikát s těmito možnostmi:

* Ano, export ovat soukromý klíč
* Exportovat všechny rozšířené vlastnosti
* Osoba, které je tento certifikát vydáván, by měla zvolit heslo pro export

## <a name="import-client-certificate"></a>Importovat klientský certifikát
Každá osoba, pro kterou byl vydán klientský certifikát, by měla importovat dvojici klíčů v počítačích, které bude používat ke komunikaci se službou:

* Poklepejte na soubor . Soubor PFX v Průzkumníkovi Windows
* Importujte certifikát do osobního úložiště s alespoň touto možností:
  * Zahrnout všechny rozšířené vlastnosti zaškrtnuté

## <a name="copy-client-certificate-thumbprints"></a>Kopírování kryptografických otisků klientského certifikátu
Každá osoba, pro kterou byl vydán klientský certifikát, musí postupovat takto, aby získala kryptografický otisk svého certifikátu, který bude přidán do konfiguračního souboru služby:

* Spustit certmgr.exe
* Výběr karty Osobní
* Poklepejte na klientský certifikát, který má být použit pro ověřování.
* V zobrazeném dialogovém okně Certifikát vyberte kartu Podrobnosti.
* Ujistěte se, že se zobrazuje vše
* V seznamu vyberte pole s názvem Kryptografický otisk.
* Kopírování hodnoty kryptografického otisku
  * Odstranění neviditelných znaků Unicode před první číslicí
  * Odstranit všechny mezery

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Konfigurace povolených klientů v konfiguračním souboru služby
Aktualizujte hodnotu následujícího nastavení v konfiguračním souboru služby pomocí seznamu kryptografických otisků klientských certifikátů, které umožňují přístup ke službě:

```xml
<Setting name="AllowedClientCertificateThumbprints" value="" />
```

## <a name="configure-client-certificate-revocation-check"></a>Konfigurace kontroly odvolání klientského certifikátu
Výchozí nastavení nekontroluje u certifikačního úřadu stav odvolání klientského certifikátu. Chcete-li zapnout kontroly, pokud certifikační úřad, který vydal klientské certifikáty podporuje takové kontroly, změňte následující nastavení s jednou z hodnot definovaných ve výčtu X509RevocationMode:

```xml
<Setting name="ClientCertificateRevocationCheck" value="NoCheck" />
```

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>Vytvoření souboru PFX pro šifrovací certifikáty podepsané svým držitelem
Pro šifrovací certifikát spusťte:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Přizpůsobení:

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Zadejte heslo a pak exportujte certifikát s těmito možnostmi:

* Ano, export ovat soukromý klíč
* Exportovat všechny rozšířené vlastnosti
* Heslo budete potřebovat při nahrávání certifikátu do cloudové služby.

## <a name="export-encryption-certificate-from-certificate-store"></a>Export šifrovacího certifikátu z úložiště certifikátů
* Najít certifikát
* Klikněte na Akce - > všechny úkoly - > export...
* Vývozní certifikát do . Soubor PFX s těmito možnostmi: 
  * Ano, export ovat soukromý klíč
  * Pokud je to možné, zahrnout všechny certifikáty do certifikační cesty 
* Exportovat všechny rozšířené vlastnosti

## <a name="upload-encryption-certificate-to-cloud-service"></a>Nahrání šifrovacího certifikátu do cloudové služby
Nahrajte certifikát se stávajícím nebo generovaným . Soubor PFX s párem šifrovacího klíče:

* Zadejte heslo chránící informace o soukromém klíči.

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Aktualizace šifrovacího certifikátu v konfiguračním souboru služby
Aktualizujte hodnotu kryptotisku následujících nastavení v konfiguračním souboru služby kryptografickým otiskem certifikátu nahraného do cloudové služby:

```xml
<Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="common-certificate-operations"></a>Běžné operace certifikátů
* Konfigurace certifikátu TLS/SSL
* Konfigurace klientských certifikátů

## <a name="find-certificate"></a>Najít certifikát
Postupujte následovně:

1. Spusťte mmc.exe.
2. Soubor -> Přidat nebo odebrat modul snap-in...
3. Vyberte **certifikáty**.
4. Klikněte na **Přidat**.
5. Zvolte umístění úložiště certifikátů.
6. Klikněte na **Finish** (Dokončit).
7. Klikněte na tlačítko **OK**.
8. Rozbalte **certifikáty**.
9. Rozbalte uzel úložiště certifikátů.
10. Rozbalte podřízený uzel certifikátu.
11. Vyberte certifikát v seznamu.

## <a name="export-certificate"></a>Export certifikátu
V **Průvodci exportem certifikátu**:

1. Klikněte na **Další**.
2. Vyberte **Ano**a potom **exportujte soukromý klíč**.
3. Klikněte na **Další**.
4. Vyberte požadovaný formát výstupního souboru.
5. Zkontrolujte požadované možnosti.
6. Zkontrolujte **heslo**.
7. Zadejte silné heslo a potvrďte ho.
8. Klikněte na **Další**.
9. Zadejte nebo vyhledejte název souboru, kam chcete certifikát uložit (použijte . Rozšíření PFX).
10. Klikněte na **Další**.
11. Klikněte na **Finish** (Dokončit).
12. Klikněte na tlačítko **OK**.

## <a name="import-certificate"></a>Import certifikátu
V Průvodci importem certifikátu:

1. Vyberte umístění obchodu.
   
   * Vyberte **aktuálního uživatele,** pokud ke službě budou přistupovat pouze procesy spuštěné pod aktuálním uživatelem.
   * Vyberte **možnost Místní počítač,** pokud budou ke službě přistupovat jiné procesy v tomto počítači.
2. Klikněte na **Další**.
3. Pokud importujete ze souboru, potvrďte cestu k souboru.
4. Pokud import . Soubor PFX:
   1. Zadejte heslo chránící soukromý klíč.
   2. Vybrat volby importu
5. Vyberte certifikáty "Umístit" v následujícím obchodě
6. Klikněte na **Browse** (Procházet).
7. Vyberte požadované úložiště.
8. Klikněte na **Finish** (Dokončit).
   
   * Pokud bylo vybráno úložiště Důvěryhodné kořenové certifikační autority, klepněte na tlačítko **Ano**.
9. Ve všech oknech dialogových oken klikněte na **OK.**

## <a name="upload-certificate"></a>Nahrání certifikátu
Na webu [Azure Portal](https://portal.azure.com/)

1. Vyberte **Možnost Cloudové služby**.
2. Vyberte cloudovou službu.
3. V horní nabídce klikněte na **Certifikáty**.
4. Na dolním panelu klikněte na **Nahrát**.
5. Vyberte soubor certifikátu.
6. Pokud se jedná o . PFX, zadejte heslo pro soukromý klíč.
7. Po dokončení zkopírujte kryptografický otisk certifikátu z nové položky v seznamu.

## <a name="other-security-considerations"></a>Další aspekty zabezpečení
Nastavení TLS popsané v tomto dokumentu šifrují komunikaci mezi službou a jejími klienty při použití koncového bodu HTTPS. To je důležité, protože pověření pro přístup k databázi a potenciálně další citlivé informace jsou obsaženy v komunikaci. Všimněte si však, že služba přetrvává interní stav, včetně pověření, ve svých interních tabulkách v databázi Microsoft Azure SQL, které jste poskytli pro úložiště metadat v předplatném Microsoft Azure. Tato databáze byla definována jako součást následujícího nastavení v konfiguračním souboru služby (. CSCFG soubor): 

```xml
<Setting name="ElasticScaleMetadata" value="Server=…" />
```

Pověření uložená v této databázi jsou šifrována. Jako osvědčený postup však zajistěte, aby webové i pracovní role nasazení služeb byly aktuální a zabezpečené, protože obě mají přístup k databázi metadat a k certifikátu používanému pro šifrování a dešifrování uložených přihlašovacích údajů. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

