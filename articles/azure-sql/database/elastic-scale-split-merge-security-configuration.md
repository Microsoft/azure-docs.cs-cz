---
title: Konfigurace zabezpečení dělení a slučování
description: Nastavte certifikáty x409 pro šifrování pomocí služby dělení a sloučení pro elastické škálování.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 235efc550fd47d4244a5bf081c75d5e824a8e4b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793428"
---
# <a name="split-merge-security-configuration"></a>Konfigurace zabezpečení dělení a slučování
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Chcete-li používat službu dělení a sloučení, je nutné správně nakonfigurovat zabezpečení. Služba je součástí funkce elastického škálování Azure SQL Database. Další informace najdete v [kurzu rozdělení elastického škálování a sloučení služby](elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Konfigurace certifikátů

Certifikáty jsou konfigurovány dvěma způsoby. 

1. [Konfigurace certifikátu TLS/SSL](#to-configure-the-tlsssl-certificate)
2. [Konfigurace klientských certifikátů](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>Získání certifikátů

Certifikáty lze získat z veřejných certifikačních autorit (CAs) nebo z [certifikační služby systému Windows](/windows/win32/seccrypto/certificate-services). Jedná se o preferované metody pro získání certifikátů.

Pokud tyto možnosti nejsou k dispozici, můžete vygenerovat **certifikáty podepsané svým držitelem**.

## <a name="tools-to-generate-certificates"></a>Nástroje pro generování certifikátů

* [makecert.exe](/previous-versions/dotnet/netframework-4.0/bfsktky3(v=vs.100))
* [pvk2pfx.exe](/windows-hardware/drivers/devtest/pvk2pfx)

### <a name="to-run-the-tools"></a>Spuštění nástrojů

* Z Developer Command Prompt pro Visual studia se podívejte na [Visual Studio Command Prompt](/dotnet/framework/tools/developer-command-prompt-for-vs) . 
  
    Pokud je nainstalován, přejít na:
  
    ```console
    %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
    ```

* Získat WDK z [Windows 8.1: stažení sad a nástrojů](https://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-tlsssl-certificate"></a>Konfigurace certifikátu TLS/SSL

K šifrování komunikace a ověření serveru je vyžadován certifikát TLS/SSL. Vyberte nejvíce pro tři scénáře a proveďte všechny kroky:

### <a name="create-a-new-self-signed-certificate"></a>Vytvoření nového certifikátu podepsaného svým držitelem

1. [Vytvoření certifikátu Self-Signed](#create-a-self-signed-certificate)
2. [Vytvořit soubor PFX pro Self-Signed certifikát TLS/SSL](#create-pfx-file-for-self-signed-tlsssl-certificate)
3. [Nahrání certifikátu TLS/SSL do cloudové služby](#upload-tlsssl-certificate-to-cloud-service)
4. [Aktualizace certifikátu TLS/SSL v konfiguračním souboru služby](#update-tlsssl-certificate-in-service-configuration-file)
5. [Importovat certifikační autoritu TLS/SSL](#import-tlsssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Použití existujícího certifikátu z úložiště certifikátů
1. [Export certifikátu TLS/SSL z úložiště certifikátů](#export-tlsssl-certificate-from-certificate-store)
2. [Nahrání certifikátu TLS/SSL do cloudové služby](#upload-tlsssl-certificate-to-cloud-service)
3. [Aktualizace certifikátu TLS/SSL v konfiguračním souboru služby](#update-tlsssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Použití existujícího certifikátu v souboru PFX
1. [Nahrání certifikátu TLS/SSL do cloudové služby](#upload-tlsssl-certificate-to-cloud-service)
2. [Aktualizace certifikátu TLS/SSL v konfiguračním souboru služby](#update-tlsssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>Konfigurace klientských certifikátů
Aby bylo možné ověřovat požadavky na službu, jsou vyžadovány klientské certifikáty. Vyberte nejvíce pro tři scénáře a proveďte všechny kroky:

### <a name="turn-off-client-certificates"></a>Vypnutí klientských certifikátů
1. [Vypnout ověřování Certificate-Based klienta](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Vystavení nových klientských certifikátů podepsaných svým držitelem
1. [Vytvoření certifikační autority Self-Signed](#create-a-self-signed-certification-authority)
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
1. [Vytvoření certifikátu Self-Signed](#create-a-self-signed-certificate)
2. [Vytvořit soubor PFX pro Self-Signed šifrovací certifikát](#create-pfx-file-for-self-signed-tlsssl-certificate)
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
Skupina pravidel řízení přístupu, která platí pro a koncový bod, je nakonfigurovaná v **\<EndpointAcls>** části **konfiguračního souboru služby**.

```xml
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
</EndpointAcls>
```

Pravidla ve skupině řízení přístupu jsou konfigurována v \<AccessControl name=""> části konfiguračního souboru služby. 

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

* Konfigurace certifikátu TLS/SSL
* Konfigurace klientských certifikátů

## <a name="create-a-self-signed-certificate"></a>Vytvoření certifikátu podepsaného svým držitelem (self-signed certificate)
Spustit

```console
makecert ^
  -n "CN=myservice.cloudapp.net" ^
  -e MM/DD/YYYY ^
  -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
  -a sha256 -len 2048 ^
  -sv MySSL.pvk MySSL.cer
```

Přizpůsobení:

* -n s adresou URL služby. Jsou podporovány zástupné znaky (CN = *. cloudapp. NET) a alternativní názvy (CN = myservice1. cloudapp. NET, CN = myservice2. cloudapp. NET).
* -e s datem vypršení platnosti certifikátu vytvořte silné heslo a zadejte ho po zobrazení výzvy.

## <a name="create-pfx-file-for-self-signed-tlsssl-certificate"></a>Vytvořit soubor PFX pro certifikát TLS/SSL podepsaný svým držitelem
Spustit

```console
pvk2pfx -pvk MySSL.pvk -spc MySSL.cer
```

Zadejte heslo a pak exportujte certifikát s těmito možnostmi:

* Ano, exportovat privátní klíč
* Exportovat všechny rozšířené vlastnosti

## <a name="export-tlsssl-certificate-from-certificate-store"></a>Export certifikátu TLS/SSL z úložiště certifikátů
* Najít certifikát
* Klikněte na akce-> všechny úkoly – > exportovat...
* Exportujte certifikát do. Soubor PFX s těmito možnostmi:
  * Ano, exportovat privátní klíč
  * Zahrnout všechny certifikáty na cestě k certifikátu, pokud je to možné * exportovat všechny rozšířené vlastnosti

## <a name="upload-tlsssl-certificate-to-cloud-service"></a>Nahrání certifikátu TLS/SSL do cloudové služby
Nahrajte certifikát s existující nebo vygenerovanou. Soubor PFX s dvojicí klíčů TLS:

* Zadejte heslo chránící informace privátního klíče.

## <a name="update-tlsssl-certificate-in-service-configuration-file"></a>Aktualizace certifikátu TLS/SSL v konfiguračním souboru služby
Aktualizujte hodnotu kryptografického otisku následujícího nastavení v konfiguračním souboru služby pomocí kryptografického otisku certifikátu nahraného do cloudové služby:

```console
<Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="import-tlsssl-certification-authority"></a>Importovat certifikační autoritu TLS/SSL
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

Pak zkopírujte stejný kryptografický otisk jako certifikát TLS/SSL v nastavení certifikátu certifikační autority:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="create-a-self-signed-certification-authority"></a>Vytvoření certifikační autority podepsané svým držitelem
Provedením následujících kroků vytvořte certifikát podepsaný svým držitelem, který bude sloužit jako certifikační autorita:

```console
makecert ^
-n "CN=MyCA" ^
-e MM/DD/YYYY ^
 -r -cy authority -h 1 ^
 -a sha256 -len 2048 ^
  -sr localmachine -ss my ^
  MyCA.cer
```

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
* Klikněte na akce-> všechny úkoly – > exportovat...
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

```console
makecert ^
  -n "CN=My ID" ^
  -e MM/DD/YYYY ^
  -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
  -a sha256 -len 2048 ^
  -in "MyCA" -ir localmachine -is my ^
  -sv MyID.pvk MyID.cer
```

Customiz

* -n s identifikátorem ID pro klienta, který bude ověřen pomocí tohoto certifikátu
* -e s datem vypršení platnosti certifikátu
* MyID. PVK a MyID. cer s jedinečnými názvy souborů pro tento klientský certifikát

Tento příkaz zobrazí výzvu k vytvoření a následnému použití hesla. Použijte silné heslo.

## <a name="create-pfx-files-for-client-certificates"></a>Vytvořit soubory PFX pro klientské certifikáty
Pro každý generovaný klientský certifikát spusťte:

```console
pvk2pfx -pvk MyID.pvk -spc MyID.cer
```

Customiz

```console
MyID.pvk and MyID.cer with the filename for the client certificate
```

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

* Spustit certmgr.exe
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

```console
pvk2pfx -pvk MyID.pvk -spc MyID.cer
```

Customiz

```console
MyID.pvk and MyID.cer with the filename for the encryption certificate
```

Zadejte heslo a pak exportujte certifikát s těmito možnostmi:

* Ano, exportovat privátní klíč
* Exportovat všechny rozšířené vlastnosti
* Při odesílání certifikátu do cloudové služby budete potřebovat heslo.

## <a name="export-encryption-certificate-from-certificate-store"></a>Exportovat šifrovací certifikát z úložiště certifikátů
* Najít certifikát
* Klikněte na akce-> všechny úkoly – > exportovat...
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
* Konfigurace certifikátu TLS/SSL
* Konfigurace klientských certifikátů

## <a name="find-certificate"></a>Najít certifikát
Postupujte takto:

1. Spusťte mmc.exe.
2. Soubor – > přidat nebo odebrat modul snap-in...
3. Vyberte **certifikáty**.
4. Klikněte na **Přidat**.
5. Vyberte umístění úložiště certifikátů.
6. Klikněte na **Finish** (Dokončit).
7. Klikněte na **OK**.
8. Rozbalte položku **certifikáty**.
9. Rozbalte uzel úložiště certifikátů.
10. Rozbalte uzel podřízený certifikát.
11. V seznamu vyberte certifikát.

## <a name="export-certificate"></a>Export certifikátu
V **Průvodci exportem certifikátu**:

1. Klikněte na **Next** (Další).
2. Vyberte **Ano** a pak **exportujte privátní klíč**.
3. Klikněte na **Next** (Další).
4. Vyberte požadovaný formát výstupního souboru.
5. Ověřte požadované možnosti.
6. Ověřte **heslo**.
7. Zadejte silné heslo a potvrďte ho.
8. Klikněte na **Next** (Další).
9. Zadejte nebo vyhledejte název souboru, kam se má certifikát Uložit (použijte. Přípona PFX).
10. Klikněte na **Next** (Další).
11. Klikněte na **Finish** (Dokončit).
12. Klikněte na **OK**.

## <a name="import-certificate"></a>Import certifikátu
V Průvodci importem certifikátu:

1. Vyberte umístění úložiště.
   
   * Vyberte **aktuálního uživatele** , pokud budou ke službě přistupovat jenom procesy spuštěné v rámci aktuálního uživatele.
   * Vyberte možnost **místní počítač** , pokud budou k této službě přistupovat jiné procesy v tomto počítači.
2. Klikněte na **Next** (Další).
3. Při importu ze souboru potvrďte cestu k souboru.
4. Při importu. Soubor PFX:
   1. Zadejte heslo pro ochranu privátního klíče.
   2. Vybrat možnosti importu
5. Vyberte "umístit" certifikáty v následujícím úložišti
6. Klikněte na **Browse** (Procházet).
7. Vyberte požadované úložiště.
8. Klikněte na **Finish** (Dokončit).
   
   * Pokud se zvolilo úložiště Důvěryhodné kořenové certifikační autority, klikněte na **Ano**.
9. Ve všech dialogových oknech klikněte na **OK** .

## <a name="upload-certificate"></a>Nahrání certifikátu
V [Azure Portal](https://portal.azure.com/)

1. Vyberte **Cloud Services**.
2. Vyberte cloudovou službu.
3. V horní nabídce klikněte na **certifikáty**.
4. Na dolním panelu klikněte na **Odeslat**.
5. Vyberte soubor certifikátu.
6. Pokud se jedná o. Soubor PFX, zadejte heslo pro privátní klíč.
7. Po dokončení zkopírujte kryptografický otisk certifikátu z nové položky v seznamu.

## <a name="other-security-considerations"></a>Další otázky zabezpečení
Nastavení TLS popsaná v tomto dokumentu šifrují komunikaci mezi službou a jejími klienty při použití koncového bodu HTTPS. To je důležité, protože přihlašovací údaje pro přístup k databázi a potenciálně jiné citlivé informace jsou obsaženy v komunikaci. Upozorňujeme však, že služba uchovává vnitřní stav, včetně přihlašovacích údajů, ve svých vnitřních tabulkách v databázi v Azure SQL Database, které jste v předplatném Microsoft Azure zadali pro úložiště metadat. Tato databáze byla definována jako součást následujícího nastavení v konfiguračním souboru služby (. Soubor. CSCFG): 

```xml
<Setting name="ElasticScaleMetadata" value="Server=…" />
```

Přihlašovací údaje uložené v této databázi jsou šifrované. Osvědčeným postupem je ale zajistit, aby byly webové i pracovní role nasazení služeb aktuální a zabezpečené, protože oba mají přístup k databázi metadat a k certifikátu používanému k šifrování a dešifrování uložených přihlašovacích údajů. 

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]