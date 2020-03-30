---
title: Migrace z v1 na 2 – Azure Application Gateway
description: Tento článek ukazuje, jak migrovat Bránu aplikací Azure a bránu firewall webových aplikací z v1 na 2
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 9909c46015fffb3bea3eef094599312e28b935c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77046199"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Migrace brány aplikace Azure a brány firewall webových aplikací z v1 na 2

[Azure Application Gateway a Web Application Firewall (WAF) v2](application-gateway-autoscaling-zone-redundant.md) je teď k dispozici, nabízí další funkce, jako je automatické škálování a dostupnost zóny redundance. Existující brány v1 se však automaticky neupgradují na verzi v2. Pokud chcete migrovat z v1 na v2, postupujte podle kroků v tomto článku.

Migrace má dvě fáze:

1. Migrace konfigurace
2. Migrace provozu klienta

Tento článek popisuje migraci konfigurace. Migrace provozu klienta se liší v závislosti na konkrétním prostředí. Jsou však [k dispozici](#migrate-client-traffic)některá obecná doporučení na vysoké úrovni .

## <a name="migration-overview"></a>Přehled migrace

K dispozici je skript Azure PowerShell, který provádí následující akce:

* Vytvoří novou Standard_v2 nebo WAF_v2 bránu v podsíti virtuální sítě, kterou zadáte.
* Bezproblémově zkopíruje konfiguraci přidruženou k bráně v1 Standard nebo WAF do nově vytvořené brány Standard_V2 nebo WAF_V2.

### <a name="caveatslimitations"></a>Upozornění\Omezení

* Nová brána v2 má nové veřejné a soukromé IP adresy. Není možné přesunout IP adresy přidružené k existující bráně v1 bez problémů na v2. Můžete však přidělit existující (nepřidělenou) veřejnou nebo soukromou IP adresu nové bráně v2.
* Je nutné zadat ip adresní prostor pro jinou podsíť v rámci virtuální sítě, kde je umístěna vaše brána v1. Skript nemůže vytvořit bránu v2 v žádné existující podsítě, které již mají bránu v1. Pokud však existující podsíť již má bránu v2, může to stále fungovat za předpokladu, že je dostatek adresního místa IP.
* Chcete-li migrovat konfiguraci SSL, musíte zadat všechny certifikáty SSL používané v bráně v1.
* Pokud máte pro bránu V1 zapnutý režim FIPS, nebude migrována do nové brány v2. Režim FIPS není podporován ve v2.
* v2 nepodporuje Protokol IPv6, takže brány s povoleným protokolem IPv6 v1 se nemigrují. Pokud spustíte skript, nemusí být dokončen.
* Pokud brána v1 má pouze privátní IP adresu, skript vytvoří veřejnou IP adresu a privátní IP adresu pro novou bránu v2. v2 brány v současné době nepodporují pouze soukromé IP adresy.

## <a name="download-the-script"></a>Stáhněte si skript

Stáhněte si skript pro migraci z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureAppGWMigration).

## <a name="use-the-script"></a>Použití skriptu

V závislosti na místním nastavení prostředí prostředí PowerShellu a předvolbách máte dvě možnosti:

* Pokud nemáte nainstalované moduly Azure Az nebo vám nevadí odinstalovat moduly Azure Az, je `Install-Script` nejlepší použít možnost ke spuštění skriptu.
* Pokud potřebujete zachovat moduly Azure Az, je nejlepší stáhnout skript a spustit přímo.

Chcete-li zjistit, zda máte nainstalované `Get-InstalledModule -Name az`moduly Azure Az, spusťte . Pokud nevidíte žádné nainstalované Moduly Az, můžete `Install-Script` použít metodu.

### <a name="install-using-the-install-script-method"></a>Instalace pomocí metody Install-Script

Chcete-li použít tuto možnost, nesmíte mít v počítači nainstalované moduly Azure Az. Pokud jsou nainstalovány, zobrazí následující příkaz chybu. Můžete buď odinstalovat moduly Azure Az, nebo použít jinou možnost ke stažení skriptu ručně a spustit jej.
  
Spusťte skript pomocí následujícího příkazu:

`Install-Script -Name AzureAppGWMigration`

Tento příkaz také nainstaluje požadované moduly Az.  

### <a name="install-using-the-script-directly"></a>Instalace pomocí skriptu přímo

Pokud máte nainstalované některé moduly Azure Az a nemůžete je odinstalovat (nebo je nechcete odinstalovat), můžete skript stáhnout ručně pomocí karty **Ruční stažení** v odkazu ke stažení skriptu. Skript je stažen jako nezpracovaný soubor nupkg. Chcete-li nainstalovat skript z tohoto souboru nupkg, přečtěte [si téma Ruční stahování balíčků](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Spuštění skriptu:

1. Slouží `Connect-AzAccount` k připojení k Azure.

1. Slouží `Import-Module Az` k importu modulů Az.

1. Spustit `Get-Help AzureAppGWMigration.ps1` a zkontrolovat požadované parametry:

   ```
   AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceId <public IP name string>
    -validateMigration -enableAutoScale
   ```

   Parametry pro skript:
   * **resourceId: [String]: Povinné** – Toto je ID prostředku Azure pro stávající standardní v1 nebo WAF v1 brány. Chcete-li najít tuto hodnotu řetězce, přejděte na portál Azure, vyberte aplikační bránu nebo prostředek WAF a klikněte na odkaz **Vlastnosti** brány. ID prostředku je umístěno na této stránce.

     Můžete také spustit následující příkazy Azure PowerShell u získání ID prostředku:

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **podsíťAddressRange: [String]: Povinné** - Toto je ip adresní prostor, který jste přidělili (nebo chcete přidělit) pro novou podsíť, která obsahuje novou bránu v2. To musí být uvedeno v zápisu CIDR. Například: 10.0.0.0/24. Tuto podsíť není nutné vytvářet předem. Skript jej vytvoří za vás, pokud neexistuje.
   * **appgwName: [String]: Volitelné**. Toto je řetězec, který zadáte jako název nové Standard_v2 nebo WAF_v2 brány. Pokud tento parametr není zadán, název vaší stávající brány v1 se použije s příponou *_v2* připojen.
   * **sslCertificates: [PSApplicationGatewaySslCertificate]: Volitelné**.  Seznam objektů PSApplicationGatewaySslCertificate oddělených čárkami, který vytvoříte k reprezentaci certifikátů SSL z brány v1, musí být odeslán do nové brány v2. Pro každý z vašich certifikátů SSL nakonfigurovaných pro bránu Standard v1 nebo WAF v1 můžete vytvořit nový objekt PSApplicationGatewaySslCertificate pomocí zde uvedeného příkazu. `New-AzApplicationGatewaySslCertificate` Potřebujete cestu k souboru SSL Cert a heslo.

     Tento parametr je volitelný pouze v případě, že nemáte naslouchací procesy HTTPS nakonfigurované pro bránu v1 nebo WAF. Pokud máte alespoň jedno nastavení naslouchacího procesu HTTPS, musíte zadat tento parametr.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

     Můžete předat `$mySslCert1, $mySslCert2` (čárka-oddělené) v předchozím příkladu jako hodnoty pro tento parametr ve skriptu.
   * **trustedRootCertificates: [PSApplicationGatewayTrustedRootCertificate]: Volitelné**. Seznam objektů PSApplicationGatewayTrustedRootCertificate oddělených čárkami, který vytvoříte jako [reprezentující důvěryhodné kořenové certifikáty](ssl-overview.md) pro ověřování back-endových instancí z brány v2.
   
      ```azurepowershell
      $certFilePath = ".\rootCA.cer"
      $trustedCert = New-AzApplicationGatewayTrustedRootCertificate -Name "trustedCert1" -CertificateFile $certFilePath
      ```

      Chcete-li vytvořit seznam objektů PSApplicationGatewayTrustedRootCertificate, přečtěte si [část New-AzApplicationGatewayTrustedRootCertificate](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0).
   * **privateIpAddress: [String]: Volitelné**. Konkrétní privátní IP adresa, kterou chcete přidružit k nové bráně v2.  Musí být ze stejné virtuální sítě, kterou přidělujete pro novou bránu v2. Pokud to není zadán, skript přidělí privátní IP adresu pro bránu v2.
   * **publicIpResourceId: [String]: Volitelné**. Id resourceId existující veřejné IP adresy (standardní Skladová položka) prostředku ve vašem předplatném, které chcete přidělit nové bráně v2. Pokud tento není zadán, skript přidělí novou veřejnou IP adresu ve stejné skupině prostředků. Název je název brány v2 s *připojenou -IP.*
   * **validateMigration: [switch]: Volitelné**. Tento parametr použijte, pokud chcete, aby skript provést některé základní ověření porovnání konfigurace po vytvoření brány v2 a kopie konfigurace. Ve výchozím nastavení se neprovádí žádné ověření.
   * **enableAutoScale: [switch]: Volitelné**. Tento parametr použijte, pokud chcete, aby skript povoloval automatické škálování na nové bráně v2 po jeho vytvoření. Ve výchozím nastavení je automatické škálování zakázáno. Vždy můžete ručně povolit později na nově vytvořené v2 brány.

1. Spusťte skript pomocí příslušných parametrů. Dokončení může trvat pět až sedm minut.

    **Příklad**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $mySslCert1,$mySslCert2 `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceId "/subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/publicIPAddresses/MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>Migrace provozu klienta

Nejprve zkontrolujte, zda skript úspěšně vytvořil novou bránu v2 s přesnou konfigurací přenesenou z brány v1. Můžete to ověřit z webu Azure Portal.

Také odeslat malé množství provozu prostřednictvím brány v2 jako ruční test.
  
Tady je několik scénářů, kde vaše aktuální aplikační brána (Standard) může přijímat provoz klienta, a naše doporučení pro každý z nich:

* **Vlastní zóna DNS (například contoso.com), která odkazuje na front-endovou IP adresu (pomocí záznamu A) přidruženou k bráně Standard v1 nebo WAF v1**.

    Záznam DNS můžete aktualizovat tak, aby ukazoval na front-endovou IP nebo POPISek DNS přidružený k vaší Standard_v2 aplikační bráně. V závislosti na ttl nakonfigurované na záznamu DNS může chvíli trvat, než se veškerý provoz klienta migruje do nové brány v2.
* **Vlastní zóna DNS (například contoso.com), která odkazuje na popisek DNS (například *myappgw.eastus.cloudapp.azure.com* pomocí záznamu CNAME) přidruženého k vaší bráně v1**.

   Máte dvě možnosti:

  * Pokud používáte veřejné IP adresy v aplikační bráně, můžete provést řízenou, granulární migraci pomocí profilu Traffic Manageru k postupnému směrování provozu (vážená metoda směrování provozu) do nové brány v2.

    To lze provést přidáním popisků DNS bran aplikace v1 i v2 do [profilu Traffic Manageru](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)a přidáním `www.contoso.com`vlastního záznamu DNS (například) do domény Traffic Manager (například contoso.trafficmanager.net).
  * Nebo můžete aktualizovat vlastní záznam DNS domény tak, aby ukazoval na popisek DNS nové brány aplikace v2. V závislosti na ttl nakonfigurované na záznamu DNS může chvíli trvat, než se veškerý provoz klienta migruje do nové brány v2.
* **Vaši klienti se připojují k ip adrese front-endu vaší aplikační brány**.

   Aktualizujte své klienty tak, aby používali IP adresy přidružené k nově vytvořené bráně aplikace v2. Doporučujeme, abyste ip adresy nepoužívali přímo. Zvažte použití popisku názvu DNS (například yourgateway.eastus.cloudapp.azure.com) přidruženého k vaší aplikační bráně, který můžete CNAME do vlastní zóny DNS (například contoso.com).

## <a name="common-questions"></a>Časté dotazy

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Existují nějaká omezení se skriptem Azure PowerShell pro migraci konfigurace z v1 na v2?

Ano. Viz [Upozornění/omezení](#caveatslimitations).

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>Je tento článek a skript Azure PowerShell použitelný i pro produkt WAF aplikační brány? 

Ano.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>Přepíná skript Azure PowerShellu také přenosy z mé brány v1 na nově vytvořenou bránu v2?

Ne. Skript Azure PowerShell emituje jenom konfiguraci. Skutečná migrace provozu je vaší odpovědností a řídíte se.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>Je nová brána v2 vytvořená skriptem Azure PowerShell ukteréhosi odpovídajícím způsobem pro zpracování všech přenosů, které aktuálně obsluhuje moje brána v1?

Skript Azure PowerShell vytvoří novou bránu v2 s odpovídající velikostí pro zpracování provozu na vaší stávající bráně v1. Automatické škálování je ve výchozím nastavení zakázáno, ale při spuštění skriptu můžete povolit automatické škálování.

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>Nakonfiguroval jsem bránu v1 pro odesílání protokolů do úložiště Azure. Replikuje skript tuto konfiguraci také pro v2?

Ne. Skript nereplikuje tuto konfiguraci pro v2. Je nutné přidat konfiguraci protokolu samostatně do migrované brány v2.

### <a name="does-this-script-support-certificates-uploaded-to-azure-keyvault-"></a>Podporuje tento skript certifikáty nahrané do azure keyvault?

Ne. V současné době skript nepodporuje certifikáty v KeyVault. To je však zvažováno pro budoucí verzi.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Narazil jsem na nějaké problémy s použitím tohoto skriptu. Jak mohu získat pomoc?
  
Můžete poslat e-mail na appgwmigrationsup@microsoft.com, otevřete případ podpory s podporou Azure nebo dělat obojí.

## <a name="next-steps"></a>Další kroky

[Další informace o aplikační bráně v2](application-gateway-autoscaling-zone-redundant.md)
