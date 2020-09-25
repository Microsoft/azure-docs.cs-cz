---
title: Migrace z verze V1 na v2 – Azure Application Gateway
description: V tomto článku se dozvíte, jak migrovat službu Azure Application Gateway a firewall webových aplikací z verze V1 na verzi v2.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/31/2020
ms.author: victorh
ms.openlocfilehash: 653e432ca445451fc9da7155137052b9916d0d92
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91311593"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Migrace služby Azure Application Gateway a firewall webových aplikací z verze V1 na verzi 2

K dispozici je teď [Azure Application Gateway a firewall webových aplikací (WAF) v2](application-gateway-autoscaling-zone-redundant.md) a nabízí další funkce, jako je automatické škálování a redundance zóny dostupnosti. Existující brány v1 se však automaticky neupgradují na verzi v2. Chcete-li provést migraci z verze V1 na verzi 2, postupujte podle kroků v tomto článku.

Migrace probíhá ve dvou fázích:

1. Migrace konfigurace
2. Migrace klientského provozu

Tento článek popisuje migraci konfigurace. Migrace provozu klientů se liší v závislosti na konkrétním prostředí. [Jsou však k dispozici](#migrate-client-traffic)některá obecná doporučení.

## <a name="migration-overview"></a>Přehled migrace

K dispozici je skript Azure PowerShell, který provede následující akce:

* Vytvoří novou Standard_v2 nebo WAF_v2 bránu v podsíti virtuální sítě, kterou zadáte.
* Bez problémů zkopíruje konfiguraci spojenou s bránou v1 Standard nebo WAF na nově vytvořenou Standard_V2 nebo WAF_V2 bránu.

### <a name="caveatslimitations"></a>Caveats\Limitations

* Nová brána v2 má nové veřejné a privátní IP adresy. Není možné bezproblémově přesunout IP adresy přidružené k existující bráně V1 na v2. K nové bráně v2 ale můžete přiřadit existující (nepřiřazenou) veřejnou nebo privátní IP adresu.
* Je nutné zadat adresní prostor IP adres pro jinou podsíť ve virtuální síti, kde se nachází brána v1. Skript nemůže vytvořit bránu V2 v žádné z existujících podsítí, které již mají bránu v1. Nicméně pokud již existující podsíť má bránu v2, která může fungovat i v případě, že je k dispozici dostatek adresního prostoru IP adres.
* Pokud máte skupinu zabezpečení sítě nebo trasy definované uživatelem přidružené k podsíti brány v2, ujistěte se, že splňují [požadavky NSG](../application-gateway/configuration-infrastructure.md#network-security-groups) a [udr požadavky](../application-gateway/configuration-infrastructure.md#supported-user-defined-routes) na úspěšnou migraci.
* [Zásady koncového bodu služby virtuální sítě](../virtual-network/virtual-network-service-endpoint-policies-overview.md) se v Application Gateway podsíti v tuto chvíli nepodporují.
* Při migraci konfigurace TLS/SSL musíte zadat všechny certifikáty TLS/SSL použité v bráně v1.
* Pokud máte pro bránu v1 povolený režim FIPS, nebude se migrovat na novou bránu v2. V v2 není podporován režim FIPS.
* V2 nepodporuje protokol IPv6, protože brány V1 s povoleným protokolem IPv6 nejsou migrovány. Pokud skript spustíte, nemusí se dokončit.
* Pokud má brána v1 pouze privátní IP adresu, skript vytvoří veřejnou IP adresu a soukromou IP adresu pro novou bránu v2. brány v2 aktuálně nepodporují pouze privátní IP adresy.
* Do aplikace nebudou předány hlavičky s názvy, které obsahují cokoli jiného než písmena, číslice, spojovníky a podtržítka. To platí pouze pro názvy hlaviček, nikoli pro hodnoty hlaviček. Jedná se o zásadní změnu z verze V1.

## <a name="download-the-script"></a>Stáhnout skript

Stáhněte si skript migrace z  [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureAppGWMigration).

## <a name="use-the-script"></a>Použití skriptu

V závislosti na nastaveních a preferencích místního prostředí PowerShellu jsou k dispozici dvě možnosti:

* Pokud nemáte nainstalované moduly AZ pro Azure nebo si nejste připustili odinstalaci modulů AZ pro Azure, nejlepší možností je použít `Install-Script` možnost ke spuštění skriptu.
* Pokud potřebujete zachovat moduly Azure AZ, nejlepším řešením je stáhnout skript a spustit ho přímo.

Pokud chcete zjistit, jestli máte nainstalované moduly Azure AZ, spusťte `Get-InstalledModule -Name az` . Pokud nevidíte žádné nainstalované moduly AZ, můžete použít `Install-Script` metodu.

### <a name="install-using-the-install-script-method"></a>Instalace pomocí metody install-Script

Pokud chcete použít tuto možnost, musíte mít v počítači nainstalované moduly AZ pro Azure. Pokud jsou nainstalovány, následující příkaz zobrazí chybu. Můžete buď odinstalovat moduly AZ pro Azure, nebo použít jinou možnost ke stažení skriptu ručně a jeho spuštění.
  
Pokud chcete získat nejnovější verzi, spusťte skript s následujícím příkazem:

`Install-Script -Name AzureAppGWMigration -Force`

Tento příkaz nainstaluje také požadované moduly AZ Modules.  

### <a name="install-using-the-script-directly"></a>Instalace pomocí skriptu přímo

Pokud máte nainstalované některé moduly Azure AZ a nemůžete je odinstalovat (nebo je nechcete odinstalovat), můžete ručně stáhnout skript pomocí karty **Ruční stažení** v odkazu ke stažení skriptu. Skript se stáhne jako nezpracovaný soubor nupkg. Pokud chcete skript nainstalovat z tohoto souboru nupkg, přečtěte si téma [Ruční stažení balíčku](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Spuštění skriptu:

1. Slouží `Connect-AzAccount` k připojení k Azure.

1. Pomocí nástroje `Import-Module Az` importujte moduly AZ.

1. `Get-Help AzureAppGWMigration.ps1`Pro prohlédnutí požadovaných parametrů spusťte příkaz:

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

   Parametry skriptu:
   * **ResourceID: [String]: požadováno** – Toto je ID prostředku Azure pro stávající bránu Standard v1 nebo WAF v1. Tuto řetězcovou hodnotu zjistíte tak, že přejdete na Azure Portal, vyberete svůj prostředek Application Gateway nebo WAF a kliknete na odkaz **vlastnosti** pro bránu. ID prostředku se nachází na dané stránce.

     K získání ID prostředku můžete také spustit následující příkazy Azure PowerShell:

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [String]: Required** – jedná se o adresní prostor IP adres, který jste přiřadili (nebo chcete přidělit) pro novou podsíť, která obsahuje novou bránu v2. Toto musí být zadáno v zápisu CIDR. Například: 10.0.0.0/24. Tuto podsíť nemusíte vytvářet předem. Skript ji vytvoří za vás, pokud neexistuje.
   * **appgwName: [řetězec]: volitelné**. Toto je řetězec, který zadáte pro použití jako název nové Standard_v2 nebo WAF_v2 bránu. Pokud tento parametr není zadaný, použije se název vaší stávající brány V1 s příponou *_V2* připojena.
   * **sslCertificates: [PSApplicationGatewaySslCertificate]: volitelné**.  Seznam oddělený čárkami PSApplicationGatewaySslCertificate objektů, které vytvoříte pro reprezentaci certifikátů TLS/SSL z vaší brány V1, se musí nahrát do nové brány v2. Pro každý z vašich certifikátů TLS/SSL nakonfigurovaných pro bránu Standard v1 nebo WAF v1 můžete vytvořit nový objekt PSApplicationGatewaySslCertificate pomocí příkazu, který je `New-AzApplicationGatewaySslCertificate` tady zobrazený. Budete potřebovat cestu k souboru certifikátu TLS/SSL a k heslu.

     Tento parametr je volitelný jenom v případě, že nemáte naslouchací procesy protokolu HTTPS nakonfigurované pro bránu v1 nebo WAF. Pokud máte aspoň jedno nastavení naslouchacího procesu HTTPS, musíte zadat tento parametr.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

     V `$mySslCert1, $mySslCert2` předchozím příkladu můžete předat (oddělené čárkami) jako hodnoty pro tento parametr ve skriptu.
   * **trustedRootCertificates: [PSApplicationGatewayTrustedRootCertificate]: volitelné**. Čárkami oddělený seznam objektů PSApplicationGatewayTrustedRootCertificate, které vytvoříte pro reprezentaci [důvěryhodných kořenových certifikátů](ssl-overview.md) pro ověřování instancí back-endu z vaší brány v2.
   
      ```azurepowershell
      $certFilePath = ".\rootCA.cer"
      $trustedCert = New-AzApplicationGatewayTrustedRootCertificate -Name "trustedCert1" -CertificateFile $certFilePath
      ```

      Chcete-li vytvořit seznam objektů PSApplicationGatewayTrustedRootCertificate, přečtěte si téma [New-AzApplicationGatewayTrustedRootCertificate](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0).
   * **privateIpAddress: [řetězec]: volitelné**. Konkrétní privátní IP adresa, kterou chcete přidružit k nové bráně v2.  Tato hodnota musí být ze stejné virtuální sítě, kterou přidělíte pro novou bránu v2. Pokud tato hodnota není zadaná, skript přidělí privátní IP adresu pro bránu v2.
   * **publicIpResourceId: [řetězec]: volitelné**. ResourceId existující prostředek veřejné IP adresy (Standard SKU) ve vašem předplatném, který chcete přidělit nové bráně v2. Pokud tento parametr nezadáte, skript přidělí novou veřejnou IP adresu ve stejné skupině prostředků. Název je název brány v2 s připojením *-IP* .
   * **validateMigration: [přepínač]: volitelné**. Tento parametr použijte v případě, že chcete, aby skript po vytvoření brány v2 a kopii konfigurace provedl některé základní ověřování v porovnání s konfigurací. Ve výchozím nastavení se neprovádí žádné ověření.
   * **enableAutoScale: [přepínač]: volitelné**. Tento parametr použijte, pokud chcete, aby skript po vytvoření nové brány v2 povolil automatické škálování na nové bráně v2. Ve výchozím nastavení je automatické škálování zakázané. Vždycky je můžete kdykoli později aktivovat na nově vytvořené bráně v2.

1. Spusťte skript s použitím příslušných parametrů. Dokončení může trvat pět až 7 minut.

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

## <a name="migrate-client-traffic"></a>Migrace klientského provozu

Nejdřív dvakrát ověřte, že skript úspěšně vytvořil novou bránu v2 s přesnou konfigurací, která se migruje z vaší brány v1. Můžete to ověřit z Azure Portal.

Odešlete také malý objem provozu prostřednictvím brány v2 jako manuální test.
  
Tady je několik scénářů, kdy vaše aktuální Aplikační brána (Standard) může přijímat klientský provoz a naše doporučení pro každé z nich:

* **Vlastní zóna DNS (například contoso.com), která odkazuje na IP adresu front-endu (pomocí záznamu A) přidruženého k bráně Standard v1 nebo WAF v1**.

    Můžete aktualizovat svůj záznam DNS tak, aby odkazoval na front-end IP nebo popisek DNS přidružený k vaší Standard_v2 aplikační bráně. V závislosti na hodnotě TTL nakonfigurované na záznamu DNS může chvíli trvat, než se veškerý provoz klienta migruje na novou bránu v2.
* **Vlastní zóna DNS (například contoso.com), která odkazuje na popisek DNS (například: *myappgw.eastus.cloudapp.Azure.com* pomocí záznamu CNAME) přidruženého k vaší bráně v1**.

   Máte dvě možnosti:

  * Pokud ve své aplikační bráně používáte veřejné IP adresy, můžete provést řízenou a podrobnou migraci pomocí Traffic Manager profilu pro přírůstkové směrování provozu (metoda váženého směrování provozu) do nové brány v2.

    Můžete to udělat tak, že přidáte popisky DNS aplikačních bran V1 a v2 do [profilu Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)a CNAME svůj vlastní záznam DNS (například `www.contoso.com` ) do domény Traffic Manager (například contoso.trafficmanager.NET).
  * Nebo můžete aktualizovat záznam DNS pro vlastní doménu tak, aby odkazoval na popisek DNS nové aplikační brány v2. V závislosti na hodnotě TTL nakonfigurované na záznamu DNS může chvíli trvat, než se veškerý provoz klienta migruje na novou bránu v2.
* **Vaši klienti se připojují k IP adrese front-endu vaší aplikační brány**.

   Aktualizujte své klienty tak, aby používaly IP adresy přidružené k nově vytvořené aplikační bráně v2. Doporučujeme, abyste IP adresy nepoužívali přímo. Zvažte použití popisku názvu DNS (například yourgateway.eastus.cloudapp.azure.com) přidruženého k vaší aplikační bráně, kterou můžete použít k záznamu CNAME do vlastní zóny DNS (například contoso.com).

## <a name="common-questions"></a>Časté dotazy

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Existují nějaká omezení Azure PowerShell skriptu pro migraci konfigurace z V1 na v2?

Ano. Podívejte se na [Upozornění a omezení](#caveatslimitations).

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>Je tento článek a Azure PowerShell skript použitelný pro Application Gateway produkt WAF? 

Ano.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>Přepíná skript Azure PowerShell také přenos dat z mé brány V1 na nově vytvořenou bránu v2?

No. Azure PowerShell skript migruje pouze konfiguraci. Skutečná migrace provozu je vaší zodpovědností a vaším ovládacím prvkem.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>Je nová brána v2 vytvořená o správné velikosti skriptu Azure PowerShell pro zpracování všech přenosů, které aktuálně obsluhuje moje brána v1?

Skript Azure PowerShell vytvoří novou bránu v2, která má odpovídající velikost pro zpracování provozu v existující bráně v1. Automatické škálování je ve výchozím nastavení zakázané, ale při spuštění skriptu můžete povolit automatické škálování.

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>Nakonfigurovali jsem moji bránu V1, aby odesílala protokoly do služby Azure Storage. Replikuje tento skript tuto konfiguraci pro v2?

No. Skript nereplikuje tuto konfiguraci pro v2. Je nutné přidat konfiguraci protokolu odděleně od migrované brány v2.

### <a name="does-this-script-support-certificates-uploaded-to-azure-keyvault-"></a>Podporuje tento skript certifikáty nahrané do služby Azure webtrezor?

No. V současné době skript nepodporuje certifikáty v trezoru klíčů. To se ale zvažuje i v budoucí verzi.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Narazili jsme na některé problémy s použitím tohoto skriptu. Jak získám pomoc?
  
Podporu Azure můžete kontaktovat v tématu "konfigurace a nastavení/migrace na verzi v2 SKU". Další informace o [podpoře Azure najdete tady](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Další kroky

[Další informace o Application Gateway v2](application-gateway-autoscaling-zone-redundant.md)
