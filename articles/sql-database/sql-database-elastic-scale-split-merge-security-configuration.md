---
title: Konfigurace zabezpečení dělení a slučování
description: Nastavte certifikáty x409 pro šifrování pomocí služby dělení a sloučení pro elastické škálování.
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
ms.openlocfilehash: 98d645fc76010d96bc016a63b4882979f3489698
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690140"
---
# <a name="split-merge-security-configuration"></a>Konfigurace zabezpečení dělení a slučování

Chcete-li používat službu dělení a sloučení, je nutné správně nakonfigurovat zabezpečení. Služba je součástí funkce elastického škálování Microsoft Azure SQL Database. Další informace najdete v [kurzu rozdělení elastického škálování a sloučení služby](sql-database-elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Konfigurace certifikátů

Certifikáty jsou konfigurovány dvěma způsoby. 

1. [Konfigurace certifikátu SSL](#to-configure-the-ssl-certificate)
2. [Konfigurace klientských certifikátů](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>Získání certifikátů

Certifikáty lze získat z veřejných certifikačních autorit (CAs) nebo z [certifikační služby systému Windows](https://msdn.microsoft.com/library/windows/desktop/aa376539.aspx). Jedná se o preferované metody pro získání certifikátů.

Pokud tyto možnosti nejsou k dispozici, můžete vygenerovat **certifikáty podepsané svým držitelem**.

## <a name="tools-to-generate-certificates"></a>Nástroje pro generování certifikátů

* [Makecert. exe](https://msdn.microsoft.com/library/bfsktky3.aspx)
* [Pvk2Pfx. exe](https://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>Spuštění nástrojů

* Z Developer Command Prompt pro Visual studia se podívejte na [Visual Studio Command Prompt](https://msdn.microsoft.com/library/ms229859.aspx) . 
  
    Pokud je nainstalován, přejít na:
  
        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
* Získat WDK z [Windows 8.1: stažení sad a nástrojů](https://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-ssl-certificate"></a>Konfigurace certifikátu SSL

K šifrování komunikace a ověření serveru je vyžadován certifikát SSL. Vyberte nejvíce pro tři scénáře a proveďte všechny kroky:

### <a name="create-a-new-self-signed-certificate"></a>Vytvoření nového certifikátu podepsaného svým držitelem

1. [Vytvoření certifikátu podepsaného svým držitelem](#create-a-self-signed-certificate)
2. [Vytvořit soubor PFX pro certifikát SSL podepsaný svým držitelem](#create-pfx-file-for-self-signed-ssl-certificate)
3. [Odeslat certifikát SSL do cloudové služby](#upload-ssl-certificate-to-cloud-service)
4. [Aktualizovat certifikát SSL v konfiguračním souboru služby](#update-ssl-certificate-in-service-configuration-file)
5. [Importovat certifikační autoritu SSL](#import-ssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Použití existujícího certifikátu z úložiště certifikátů
1. [Exportovat certifikát SSL z úložiště certifikátů](#export-ssl-certificate-from-certificate-store)
2. [Odeslat certifikát SSL do cloudové služby](#upload-ssl-certificate-to-cloud-service)
3. [Aktualizovat certifikát SSL v konfiguračním souboru služby](#update-ssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Použití existujícího certifikátu v souboru PFX
1. [Odeslat certifikát SSL do cloudové služby](#upload-ssl-certificate-to-cloud-service)
2. [Aktualizovat certifikát SSL v konfiguračním souboru služby](#update-ssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>Konfigurace klientských certifikátů
Aby bylo možné ověřovat požadavky na službu, jsou vyžadovány klientské certifikáty. Vyberte nejvíce pro tři scénáře a proveďte všechny kroky:

### <a name="turn-off-client-certificates"></a>Vypnutí klientských certifikátů
1. [Vypnout ověřování na základě certifikátu klienta](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Vystavení nových klientských certifikátů podepsaných svým držitelem
1. [Vytvoření certifikační autority podepsané svým držitelem](#create-a-self-signed-certification-authority)
2. [Nahrání certifikátu certifikační autority do cloudové služby](#upload-ca-certificate-to-cloud-service)
3. [Aktualizace certifikátu certifikační autority v konfiguračním souboru služby](#update-ca-certificate-in-service-configuration-file)
4. [Vystavení certifikátů klienta](#issue-client-certificates)
5. [Vytvořit soubory PFX pro klientské certifikáty](#create-pfx-files-for-client-certificates)
6. [Importovat klientský certifikát](#import-client-certificate)
7. [Kopírovat kryptografické otisky klientského certifikátu](#copy-client-certificate-thumbprints)
8. [Konfigurace povolených klientů v konfiguračním souboru služby](#configure-allowed-clients-in-the-service-configuration-file)

### <a name="use-existing-client-certificates"></a>Použít existující klientské certifikáty
1. [Najít veřejný klíč certifikační autority](#find-ca-public-key)
2. [Nahrání certifikátu certifikační autority do cloudové služby](#upload-ca-certificate-to-cloud-service)
3. [Aktualizace certifikátu certifikační autority v konfiguračním souboru služby](#update-ca-certificate-in-service-configuration-file)
4. [Kopírovat kryptografické otisky klientského certifikátu](#copy-client-certificate-thumbprints)
5. [Konfigurace povolených klientů v konfiguračním souboru služby](#configure-allowed-clients-in-the-service-configuration-file)
6. [Konfigurovat kontrolu odvolání klientského certifikátu](#configure-client-certificate-revocation-check)

## <a name="allowed-ip-addresses"></a>Povolené IP adresy
Přístup k koncovým bodům služby lze omezit na konkrétní rozsahy IP adres.

## <a name="to-configure-encryption-for-the-store"></a>Konfigurace šifrování pro úložiště
K šifrování přihlašovacích údajů, které jsou uložené v úložišti metadat, se vyžaduje certifikát. Vyberte nejvíce pro tři scénáře a proveďte všechny kroky:

### <a name="use-a-new-self-signed-certificate"></a>Použití nového certifikátu podepsaného svým držitelem
1. [Vytvoření certifikátu podepsaného svým držitelem](#create-a-self-signed-certificate)
2. [Vytvořit soubor PFX pro šifrovací certifikát podepsaný svým držitelem](#create-pfx-file-for-self-signed-ssl-certificate)
3. [Nahrát šifrovací certifikát do cloudové služby](#upload-encryption-certificate-to-cloud-service)
4. [Aktualizovat šifrovací certifikát v konfiguračním souboru služby](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Použít existující certifikát z úložiště certifikátů
1. [Exportovat šifrovací certifikát z úložiště certifikátů](#export-encryption-certificate-from-certificate-store)
2. [Nahrát šifrovací certifikát do cloudové služby](#upload-encryption-certificate-to-cloud-service)
3. [Aktualizovat šifrovací certifikát v konfiguračním souboru služby](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Použít existující certifikát v souboru PFX
1. [Nahrát šifrovací certifikát do cloudové služby](#upload-encryption-certificate-to-cloud-service)
2. [Aktualizovat šifrovací certifikát v konfiguračním souboru služby](#update-encryption-certificate-in-service-configuration-file)

## <a name="the-default-configuration"></a>Výchozí konfigurace
Výchozí konfigurace odepírá přístup ke koncovému bodu HTTP. Toto je doporučené nastavení, protože žádosti do těchto koncových bodů mohou přenášet citlivé informace, jako jsou přihlašovací údaje databáze.
Výchozí konfigurace umožňuje přístup ke koncovému bodu HTTPS. Toto nastavení může být omezeno.

### <a name="changing-the-configuration"></a>Změna konfigurace
Skupina pravidel řízení přístupu, která platí pro a koncový bod, se konfiguruje v části **\<EndpointAcls >** v **konfiguračním souboru služby**.

```xml
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
</EndpointAcls>
```

Pravidla ve skupině řízení přístupu se konfigurují v \<AccessControl název = "" > konfiguračního souboru služby. 

Formátování je vysvětleno v části Access Control sítě seznam v dokumentaci.
Pokud třeba chcete, aby se pro přístup ke koncovému bodu HTTPS 100.100.0.0 jenom IP adresy v rozsahu, který bude 100.100.255.255, budou tato pravidla vypadat takto:

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
K detekci a zabránění útokům DOS (Denial of Service) se podporují dva různé mechanismy:

* Omezit počet souběžných požadavků na vzdáleného hostitele (ve výchozím nastavení vypnuté)
* Omezení rychlosti přístupu na vzdáleného hostitele (ve výchozím nastavení zapnuté)

Jsou založené na funkcích popsaných v dynamickém zabezpečení protokolu IP v IIS. Při změně této konfigurace mějte na těchto faktorách tyto faktory:

* Chování proxy serverů a zařízení překladu síťových adres přes informace o vzdáleném hostiteli
* Každý požadavek na libovolný prostředek ve webové roli se považuje za (například načítání skriptů, obrázků atd.).

## <a name="restricting-number-of-concurrent-accesses"></a>Omezení počtu souběžných přístupů
Nastavení, která konfigurují toto chování:

```xml
<Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
<Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />
```

Pokud chcete povolit tuto ochranu, změňte DynamicIpRestrictionDenyByConcurrentRequests na true.

## <a name="restricting-rate-of-access"></a>Omezení přístupu
Nastavení, která konfigurují toto chování:

```xml
<Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
<Setting name="DynamicIpRestrictionMaxRequests" value="100" />
<Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />
```

## <a name="configuring-the-response-to-a-denied-request"></a>Konfigurace odpovědi na zamítnutý požadavek
Následující nastavení nakonfiguruje odpověď na zamítnutý požadavek:

```xml
<Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
```

Další podporované hodnoty najdete v dokumentaci k dynamickému zabezpečení protokolu IP v IIS.

## <a name="operations-for-configuring-service-certificates"></a>Operace konfigurace certifikátů služby
Toto téma je pouze pro referenci. Postupujte podle kroků konfigurace popsaných v části:

* Konfigurace certifikátu SSL
* Konfigurace klientských certifikátů

## <a name="create-a-self-signed-certificate"></a>Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)
Spustit

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha256 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Přizpůsobení:

* -n s adresou URL služby. Jsou podporovány zástupné znaky (CN = *. cloudapp. NET) a alternativní názvy (CN = myservice1. cloudapp. NET, CN = myservice2. cloudapp. NET).
* -e s datem vypršení platnosti certifikátu vytvořte silné heslo a zadejte ho po zobrazení výzvy.

## <a name="create-pfx-file-for-self-signed-ssl-certificate"></a>Vytvořit soubor PFX pro certifikát SSL podepsaný svým držitelem
Spustit

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Zadejte heslo a pak exportujte certifikát s těmito možnostmi:

* Ano, exportovat privátní klíč
* Exportovat všechny rozšířené vlastnosti

## <a name="export-ssl-certificate-from-certificate-store"></a>Exportovat certifikát SSL z úložiště certifikátů
* Najít certifikát
* Klikněte na akce-> všechny úkoly – > Exportovat...
* Exportujte certifikát do. Soubor PFX s těmito možnostmi:
  * Ano, exportovat privátní klíč
  * Zahrnout všechny certifikáty na cestě k certifikátu, pokud je to možné * exportovat všechny rozšířené vlastnosti

## <a name="upload-ssl-certificate-to-cloud-service"></a>Odeslat certifikát SSL do cloudové služby
Nahrajte certifikát s existující nebo vygenerovanou. Soubor PFX s dvojicí klíčů SSL:

* Zadejte heslo chránící informace privátního klíče.

## <a name="update-ssl-certificate-in-service-configuration-file"></a>Aktualizovat certifikát SSL v konfiguračním souboru služby
Aktualizujte hodnotu kryptografického otisku následujícího nastavení v konfiguračním souboru služby pomocí kryptografického otisku certifikátu nahraného do cloudové služby:

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-ssl-certification-authority"></a>Importovat certifikační autoritu SSL
Postupujte podle těchto kroků u všech účtů a počítačů, které budou komunikovat se službou:

* Dvakrát klikněte na. Soubor CER v Průzkumníkovi Windows
* V dialogovém okně certifikát klikněte na nainstalovat certifikát...
* Importovat certifikát do úložiště důvěryhodných kořenových certifikačních autorit

## <a name="turn-off-client-certificate-based-authentication"></a>Vypnout ověřování na základě certifikátu klienta
Je podporováno pouze ověřování na základě certifikátu klienta a jeho zakázání umožní veřejný přístup k koncovým bodům služby, pokud nejsou zavedeny jiné mechanismy (například Microsoft Azure Virtual Network).

V konfiguračním souboru služby změňte tato nastavení na false, aby se funkce vypnula:

```xml
<Setting name="SetupWebAppForClientCertificates" value="false" />
<Setting name="SetupWebserverForClientCertificates" value="false" />
```

Pak zkopírujte stejný kryptografický otisk jako certifikát SSL v nastavení certifikátu certifikační autority:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="create-a-self-signed-certification-authority"></a>Vytvoření certifikační autority podepsané svým držitelem
Provedením následujících kroků vytvořte certifikát podepsaný svým držitelem, který bude sloužit jako certifikační autorita:

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha256 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Přizpůsobení

* -e s datem vypršení platnosti certifikátu

## <a name="find-ca-public-key"></a>Najít veřejný klíč certifikační autority
Všechny klientské certifikáty musí být vydané certifikační autoritou, kterou služba důvěřuje. Vyhledejte veřejný klíč pro certifikační autoritu, která vystavila klientské certifikáty, které se budou používat k ověřování, aby ho bylo možné odeslat do cloudové služby.

Pokud soubor s veřejným klíčem není dostupný, exportujte ho z úložiště certifikátů:

* Najít certifikát
  * Vyhledejte klientský certifikát vydaný stejnou certifikační autoritou.
* Dvakrát klikněte na certifikát.
* Vyberte kartu cesta k certifikátu v dialogovém okně certifikát.
* Dvakrát klikněte na položku CA v cestě.
* Poznamenejte si vlastnosti certifikátu.
* Zavřete dialogové okno **certifikát** .
* Najít certifikát
  * Vyhledejte certifikační autoritu, kterou jste si poznamenali výše.
* Klikněte na akce-> všechny úkoly – > Exportovat...
* Exportujte certifikát do. CER s těmito možnostmi:
  * **Ne, neexportovat privátní klíč**
  * Pokud je to možné, zahrňte všechny certifikáty do cesty certifikátu.
  * Exportujte všechny rozšířené vlastnosti.

## <a name="upload-ca-certificate-to-cloud-service"></a>Nahrání certifikátu certifikační autority do cloudové služby
Nahrajte certifikát s existující nebo vygenerovanou. Soubor CER s veřejným klíčem certifikační autority

## <a name="update-ca-certificate-in-service-configuration-file"></a>Aktualizace certifikátu certifikační autority v konfiguračním souboru služby
Aktualizujte hodnotu kryptografického otisku následujícího nastavení v konfiguračním souboru služby pomocí kryptografického otisku certifikátu nahraného do cloudové služby:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

Aktualizujte hodnotu následujícího nastavení se stejným kryptografickým otiskem:

```xml
<Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
```

## <a name="issue-client-certificates"></a>Vystavení certifikátů klienta
Každý jednotlivec autorizovaný pro přístup ke službě by měl mít klientský certifikát vydaný pro výhradní použití a měl by zvolit vlastní silné heslo pro ochranu privátního klíče. 

Následující kroky musí být spuštěny ve stejném počítači, ve kterém byl vytvořen a uložen certifikát certifikační autority podepsané svým držitelem:

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha256 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Customiz

* -n s identifikátorem ID pro klienta, který bude ověřen pomocí tohoto certifikátu
* -e s datem vypršení platnosti certifikátu
* MyID. PVK a MyID. cer s jedinečnými názvy souborů pro tento klientský certifikát

Tento příkaz zobrazí výzvu k vytvoření a následnému použití hesla. Používejte silné heslo.

## <a name="create-pfx-files-for-client-certificates"></a>Vytvořit soubory PFX pro klientské certifikáty
Pro každý generovaný klientský certifikát spusťte:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Customiz

    MyID.pvk and MyID.cer with the filename for the client certificate

Zadejte heslo a pak exportujte certifikát s těmito možnostmi:

* Ano, exportovat privátní klíč
* Exportovat všechny rozšířené vlastnosti
* Uživatel, kterému se certifikát vystavuje, by měl zvolit heslo pro export.

## <a name="import-client-certificate"></a>Importovat klientský certifikát
Každý jednotlivec, pro který byl klientský certifikát vystaven, by měl importovat dvojici klíčů v počítačích, které budou používat ke komunikaci se službou:

* Dvakrát klikněte na. Soubor PFX v Průzkumníkovi Windows
* Importujte certifikát do osobního úložiště pomocí aspoň této možnosti:
  * Zahrnout všechny rozšířené vlastnosti zaškrtnuté

## <a name="copy-client-certificate-thumbprints"></a>Kopírovat kryptografické otisky klientského certifikátu
Každý jednotlivec, pro který byl klientský certifikát vystaven, musí při získání kryptografického otisku certifikátu, který se přidá do konfiguračního souboru služby, použít následující postup:

* Spuštění certmgr. exe
* Vyberte kartu osobní.
* Poklikejte na certifikát klienta, který se má použít k ověřování.
* V dialogu s certifikátem, který se otevře, vyberte kartu Podrobnosti.
* Ujistěte se, že zobrazení zobrazuje vše.
* V seznamu vyberte pole s názvem kryptografický otisk.
* Kopírovat hodnotu kryptografického otisku
  * Před první číslicí odstranit neviditelné znaky Unicode
  * Odstranit všechny mezery

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Konfigurace povolených klientů v konfiguračním souboru služby
Aktualizujte hodnotu následujícího nastavení v konfiguračním souboru služby pomocí čárkami odděleného seznamu kryptografických otisků klientských certifikátů povolených přístupu ke službě:

```xml
<Setting name="AllowedClientCertificateThumbprints" value="" />
```

## <a name="configure-client-certificate-revocation-check"></a>Konfigurovat kontrolu odvolání klientského certifikátu
Výchozí nastavení nekontroluje u certifikační autority stav odvolání certifikátu klienta. Chcete-li zapnout kontroly, pokud certifikační autorita, která vystavila klientské certifikáty, podporuje takové kontroly, změňte následující nastavení pomocí jedné z hodnot definovaných ve výčtu X509RevocationMode:

```xml
<Setting name="ClientCertificateRevocationCheck" value="NoCheck" />
```

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>Vytvořit soubor PFX pro šifrovací certifikáty podepsané svým držitelem
Pro šifrovací certifikát spusťte:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Customiz

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Zadejte heslo a pak exportujte certifikát s těmito možnostmi:

* Ano, exportovat privátní klíč
* Exportovat všechny rozšířené vlastnosti
* Při odesílání certifikátu do cloudové služby budete potřebovat heslo.

## <a name="export-encryption-certificate-from-certificate-store"></a>Exportovat šifrovací certifikát z úložiště certifikátů
* Najít certifikát
* Klikněte na akce-> všechny úkoly – > Exportovat...
* Exportujte certifikát do. Soubor PFX s těmito možnostmi: 
  * Ano, exportovat privátní klíč
  * Zahrnout všechny certifikáty na cestě k certifikátu, pokud je to možné 
* Exportovat všechny rozšířené vlastnosti

## <a name="upload-encryption-certificate-to-cloud-service"></a>Nahrát šifrovací certifikát do cloudové služby
Nahrajte certifikát s existující nebo vygenerovanou. Soubor PFX s dvojicí šifrovacího klíče:

* Zadejte heslo chránící informace privátního klíče.

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Aktualizovat šifrovací certifikát v konfiguračním souboru služby
Aktualizujte hodnotu kryptografického otisku následujícího nastavení v konfiguračním souboru služby pomocí kryptografického otisku certifikátu nahraného do cloudové služby:

```xml
<Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="common-certificate-operations"></a>Běžné operace s certifikáty
* Konfigurace certifikátu SSL
* Konfigurace klientských certifikátů

## <a name="find-certificate"></a>Najít certifikát
Postupujte následovně:

1. Spusťte MMC. exe.
2. Soubor – > Přidat nebo odebrat modul snap-in...
3. Vyberte **certifikáty**.
4. Klikněte na **Přidat**.
5. Vyberte umístění úložiště certifikátů.
6. Klikněte na **Dokončit**.
7. Klikněte na tlačítko **OK**.
8. Rozbalte položku **certifikáty**.
9. Rozbalte uzel úložiště certifikátů.
10. Rozbalte uzel podřízený certifikát.
11. V seznamu vyberte certifikát.

## <a name="export-certificate"></a>Export certifikátu
V **Průvodci exportem certifikátu**:

1. Klikněte na **Další**.
2. Vyberte **Ano**a pak **exportujte privátní klíč**.
3. Klikněte na **Další**.
4. Vyberte požadovaný formát výstupního souboru.
5. Ověřte požadované možnosti.
6. Ověřte **heslo**.
7. Zadejte silné heslo a potvrďte ho.
8. Klikněte na **Další**.
9. Zadejte nebo vyhledejte název souboru, kam se má certifikát Uložit (použijte. Přípona PFX).
10. Klikněte na **Další**.
11. Klikněte na **Dokončit**.
12. Klikněte na tlačítko **OK**.

## <a name="import-certificate"></a>Importovat certifikát
V Průvodci importem certifikátu:

1. Vyberte umístění úložiště.
   
   * Vyberte **aktuálního uživatele** , pokud budou ke službě přistupovat jenom procesy spuštěné v rámci aktuálního uživatele.
   * Vyberte možnost **místní počítač** , pokud budou k této službě přistupovat jiné procesy v tomto počítači.
2. Klikněte na **Další**.
3. Při importu ze souboru potvrďte cestu k souboru.
4. Při importu. Soubor PFX:
   1. Zadejte heslo pro ochranu privátního klíče.
   2. Vybrat možnosti importu
5. Vyberte "umístit" certifikáty v následujícím úložišti
6. Klikněte na **Browse** (Procházet).
7. Vyberte požadované úložiště.
8. Klikněte na **Dokončit**.
   
   * Pokud se zvolilo úložiště Důvěryhodné kořenové certifikační autority, klikněte na **Ano**.
9. Ve všech dialogových oknech klikněte na **OK** .

## <a name="upload-certificate"></a>Nahrání certifikátu
Na webu [Azure Portal](https://portal.azure.com/)

1. Vyberte **Cloud Services**.
2. Vyberte cloudovou službu.
3. V horní nabídce klikněte na **certifikáty**.
4. Na dolním panelu klikněte na **Odeslat**.
5. Vyberte soubor certifikátu.
6. Pokud se jedná o. Soubor PFX, zadejte heslo pro privátní klíč.
7. Po dokončení zkopírujte kryptografický otisk certifikátu z nové položky v seznamu.

## <a name="other-security-considerations"></a>Další otázky zabezpečení
Nastavení SSL popsaná v tomto dokumentu šifrují komunikaci mezi službou a jejími klienty při použití koncového bodu HTTPS. To je důležité, protože přihlašovací údaje pro přístup k databázi a potenciálně jiné citlivé informace jsou obsaženy v komunikaci. Upozorňujeme však, že služba uchovává vnitřní stav, včetně přihlašovacích údajů, ve svých interních tabulkách v Microsoft Azure SQL Database, které jste poskytli pro úložiště metadat v rámci Microsoft Azure předplatného. Tato databáze byla definována jako součást následujícího nastavení v konfiguračním souboru služby (. Soubor. CSCFG): 

```xml
<Setting name="ElasticScaleMetadata" value="Server=…" />
```

Přihlašovací údaje uložené v této databázi jsou šifrované. Osvědčeným postupem je ale zajistit, aby byly webové i pracovní role nasazení služeb aktuální a zabezpečené, protože oba mají přístup k databázi metadat a k certifikátu používanému k šifrování a dešifrování uložených přihlašovacích údajů. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

