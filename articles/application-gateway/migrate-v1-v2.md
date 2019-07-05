---
title: Migrace Azure Application Gateway a Firewall webových aplikací z v1 na v2
description: V tomto článku se dozvíte, jak migrovat Azure Application Gateway a brány Firewall webových aplikací z v1 na v2
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 0fd605d7d502970dccd37da1f3f70fdadb1094a1
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550455"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Migrace Azure Application Gateway a Firewall webových aplikací z v1 na v2

[Azure Application Gateway a Firewall webových aplikací (WAF) v2](application-gateway-autoscaling-zone-redundant.md) je teď dostupný, nabízí další funkce, jako je automatické škálování a zóně dostupnosti redundance. Existující brány v1, ale nebudou automaticky aktualizovány na v2. Pokud chcete migrovat z v1 na v2, postupujte podle kroků v tomto článku.

Existují dvě fáze migrace:

1. Migraci konfigurace
2. Migrace přenosy klienta

Tento článek popisuje migraci konfigurace. Migrace klientů provoz se liší v závislosti na vašem konkrétním prostředí. Nicméně některé vysoké úrovně, obecná doporučení [jsou k dispozici](#migrate-client-traffic).

## <a name="migration-overview"></a>Přehled migrace

Skript Azure Powershellu je k dispozici, který provede následující akce:

* Vytvoří novou bránu Standard_v2 nebo WAF_v2 v podsíti virtuální sítě, který zadáte.
* Zkopíruje bezproblémově konfigurace související s bránou v1 Standard nebo WAF do nově vytvořené bráně Standard_V2 nebo WAF_V2.

### <a name="caveatslimitations"></a>Caveats\Limitations

* Nová brána v2 má nové veřejných a privátních IP adres. Není možné přesunout na IP adresy přidružené k existující bránu v1 na v2 bez problémů. Však můžete přidělit existující (nepřidělené) veřejné nebo privátní IP adresy k nové bráně v2.
* Je nutné zadat prostoru IP adres pro jinou podsíť ve vaší virtuální síti, kde se nachází brána v1. Skript nejde vytvořit bránu v2 v existující podsítě, které už máte bránu v1. Nicméně pokud už má existující podsítě brána v2, který může i nadále fungovat za předpokladu, že není dostatek adresní prostor IP adres.
* K migraci konfigurace protokolu SSL, je nutné zadat všechny certifikáty SSL používá v bráně v1.
* Pokud máte režimu FIPS povolené pro bránu V1, nebude se migrovat na nové bráně v2. Verze 2 nepodporuje režim FIPS.
* v2 nepodporuje IPv6, takže se nemigrují brány v1 podporuje protokol IPv6. Pokud spustíte skript, nemusí být dokončeno.
* Pokud brána v1 má pouze privátní IP adresy, skript vytvoří veřejnou IP adresu a privátní IP adresu pro novou bránu v2. brány v2 aktuálně není podporována pouze privátní IP adresy.

## <a name="download-the-script"></a>Stáhněte si skript

Stáhněte si skript pro migraci z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureAppGWMigration).

## <a name="use-the-script"></a>Pomocí skriptu

Existují dvě možnosti pro vás v závislosti na nastavení místního prostředí PowerShell a předvolby:

* Pokud nemáte nainstalované moduly Azure Az nebo nevadí odinstalace moduly Azure Az, nejlepší možností je použít `Install-Script` možnost pro spuštění skriptu.
* Pokud je potřeba nechat Azure Az moduly, nejlepším řešením je stáhnout skript a spustit přímo.

Chcete-li zjistit, jestli je nainstalované moduly Azure Az, spusťte `Get-InstalledModule -Name az`. Pokud nevidíte všechny nainstalované moduly Az, a pak můžete použít `Install-Script` metody.

### <a name="install-using-the-install-script-method"></a>Instalace pomocí instalačního skriptu, který – metoda

Pokud chcete použít tuto možnost, nesmí mít moduly Azure Az v počítači nainstalovaný. Pokud máte nainstalované, následující příkaz zobrazí chybu. Můžete odinstalovat moduly Azure Az, nebo použijte jinou možnost ručně stáhnout skript a spustit ho.
  
Spusťte skript následujícím příkazem:

`Install-Script -Name AzureAppGWMigration`

Tento příkaz nainstaluje taky požadované moduly Az.  

### <a name="install-using-the-script-directly"></a>Instalace přímo pomocí skriptu

Pokud máte některé moduly Azure Az nainstalovaný a nelze je odinstalovat (nebo nechcete je odinstalovat), můžete ručně stáhnout pomocí skriptu **ruční stažení** kartu odkaz ke stažení skriptu. Skript se stáhne jako soubor nupkg nezpracované. Z tohoto souboru nupkg instalaci skriptu najdete v tématu [ruční stažení balíčku](https://docs.microsoft.com/powershell/gallery/how-to/working-with-packages/manual-download).

Spuštění skriptu:

1. Použití `Connect-AzAccount` pro připojení k Azure.

1. Použití `Import-Module Az` importujte Az moduly.

1. Spustit `Get-Help AzureAppGWMigration.ps1` prozkoumat požadované parametry:

   ```
   AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceName <public IP name string>
    -validateMigration -enableAutoScale
   ```

   Parametry skriptu:
   * **resourceId: [String]: Vyžaduje** – to je ID prostředku Azure pro váš stávající standardní verze 1 nebo v1 brány WAF. Najít tuto hodnotu řetězce, přejděte na web Azure Portal, vyberte aplikační brána nebo zdroj, WAF a klikněte na tlačítko **vlastnosti** odkaz pro bránu. ID prostředku, které se nachází na dané stránce.

     Můžete také spustit následující příkazy prostředí Azure PowerShell k získání ID prostředku:

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [String]:  Vyžaduje** – to je adresní prostor IP adres, který jste přiřadili (nebo chcete přidělit) pro novou podsíť, která obsahuje vaše nová brána v2. To je třeba zadat v notaci CIDR. Příklad: 10.0.0.0/24. Nemusíte předem vytvářet tuto podsíť. Skript vytvoří ho za vás, pokud neexistuje.
   * **appgwName: [String]: Volitelné**. Toto je řetězec, který chcete použít jako název pro novou bránu Standard_v2 nebo WAF_v2 zadáte. Pokud tento parametr nezadáte, název existující v1 brány se použije s příponou *_v2* připojí.
   * **sslCertificates: [PSApplicationGatewaySslCertificate]: Volitelné**.  Čárkou oddělený seznam PSApplicationGatewaySslCertificate objekty, které vytvoříte pro reprezentaci ze brána v1 certifikáty SSL musí být odeslán do nové brány v2. Pro každý z vašich certifikáty SSL, které jsou nakonfigurované pro standardní verze 1 nebo v1 brány WAF, můžete vytvořit nový objekt PSApplicationGatewaySslCertificate prostřednictvím `New-AzApplicationGatewaySslCertificate` příkaz je vidět tady. Potřebujete cestu k souboru certifikátu protokolu SSL a heslo.

       Jenom tento parametr je volitelný, pokud nemáte nakonfigurované pro v1 brány nebo WAF naslouchací procesy HTTPS. Pokud máte aspoň jeden nastavení naslouchacího procesu protokolu HTTPS, musíte zadat Tento parametr.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

      Můžete předat `$mySslCert1, $mySslCert2` (oddělený čárkami) v předchozím příkladu jako hodnoty pro tento parametr ve skriptu.
   * **trustedRootCertificates: [PSApplicationGatewayTrustedRootCertificate]: Volitelné**. Čárkou oddělený seznam PSApplicationGatewayTrustedRootCertificate objekty, které vytvoříte pro reprezentaci [důvěryhodných kořenových certifikátů](ssl-overview.md) ověřování back-endových instancí ze brána v2.  

      Vytvoření seznamu objektů PSApplicationGatewayTrustedRootCertificate najdete v tématu [New-AzApplicationGatewayTrustedRootCertificate](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0).
   * **privateIpAddress: [String]: Volitelné**. Konkrétní privátní IP adresu, kterou chcete přidružit k nové bráně v2.  Toto musí být ze stejné virtuální síti, přidělit nový brány v2. Pokud není zadaný, skript přiděluje privátní IP adresy pro bránu v2.
    * **publicIpResourceId: [String]: Volitelné**. ResourceId prostředek veřejné IP adresy (standardní SKU) v rámci vašeho předplatného, které chcete přidělit k nové bráně v2. Pokud není zadaný, skript přidělí novou veřejnou IP adresu ve stejné skupině prostředků. Název je název brány v2 s *- IP* připojí.
   * **validateMigration: [přepínače]: Volitelné**. Tento parametr použijte, pokud chcete skript provádět některé základní konfigurace porovnání ověření po vytvoření brány v2 a kopii konfigurace. Ve výchozím nastavení nebude ověřen.
   * **enableAutoScale: [přepínače]: Volitelné**. Tento parametr použijte, pokud chcete skript, který chcete povolit automatické škálování na novou bránu v2 po jeho vytvoření. Standardně je zakázáno automatické škálování. Můžete vždy ručně ji povolit později na nově vytvořený v2 brány.

1. Spusťte skript pomocí příslušné parametry. Může trvat 5 až sedm minut na dokončení.

    **Příklad**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $Certs `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceId "MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>Migrace přenosy klienta

Nejprve pečlivě zkontrolujte, že skript úspěšně vytvořili novou bránu v2 s přesnou konfiguraci migrovat ze brána v1. Můžete to ověřit na webu Azure Portal.

Malé množství provoz prostřednictvím brány v2 také odešlete jako manuálního testu.
  
Tady je několik scénářů, kde se může zobrazit aktuální aplikační brána (Standard), komunikace s klienty a naše doporučení pro každý z nich:

* **Vlastní zónu DNS (např. contoso.com), který odkazuje na IP adresu front-endu (pomocí záznamu) přidružené k standardní verze 1 nebo v1 brány WAF**.

    Můžete aktualizovat záznam DNS tak, aby odkazoval na front-endové IP nebo DNS popisek přidružený Standard_v2 application gateway. V závislosti na hodnotu TTL nakonfigurované na záznam DNS může trvat nějakou dobu veškerý provoz klienta k migraci na nové bráně v2.
* **Vlastní zónu DNS (např. contoso.com), který odkazuje na název DNS (například: *myappgw.eastus.cloudapp.azure.com* pomocí záznamu CNAME) přidružené k vaší bráně v1**.

   Máte dvě možnosti:

  * Pokud používáte veřejné IP adresy ve vaší službě application gateway, můžete provést řízené, detailní migraci pomocí profilu služby Traffic Manager pro přírůstkové směrování provozu (metodu směrování provozu vážený) k nové bráně v2.

    Můžete to provést tak, že přidáte názvy DNS z aplikačních bran v1 i v2 k [profil služby Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)a CNAMEing vlastní záznam DNS (například www.contoso.com) na doménu Traffic Manageru (např. contoso.trafficmanager.NET).
  * Nebo můžete aktualizovat záznam DNS vaší vlastní domény tak, aby odkazoval na název DNS služby application gateway nové v2. V závislosti na hodnotu TTL nakonfigurované na záznam DNS může trvat nějakou dobu veškerý provoz klienta k migraci na nové bráně v2.
* **Vaši klienti připojovat k front-endu adresy IP služby application gateway**.

   Aktualizujte vaši klienti používat IP adresy přidružené k application gateway nově vytvořený v2. Doporučujeme, nepoužívejte IP adresy přímo. Zvažte použití popisku názvu DNS (například yourgateway.eastus.cloudapp.azure.com) přidružené k vaše brána application gateway, ke kterým můžete záznam CNAME pro vlastní vlastní zóny DNS (např. contoso.com).

## <a name="common-questions"></a>Časté dotazy

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Existují nějaká omezení pomocí skriptu prostředí Azure PowerShell k migraci konfigurace z v1 na v2?

Ano. Zobrazit [upozornění/omezení](#caveatslimitations).

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>Je možné použít i produktu waf služby Application Gateway v tomto článku a skriptu Azure Powershellu? 

Ano.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>Skript Azure Powershellu také přepne provozu ze služby Brána Moje v1 na v2 nově vytvořené brány?

Ne. Skript Azure Powershellu migruje pouze konfigurace. Skutečný provoz migrace je vaší odpovědností a v ovládacím prvku.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>Má nová brána v2 vytvořen skriptem prostředí Azure PowerShell správnou zpracovat veškerý provoz, který je aktuálně poskytovaný mám v1 bránu?

Skript Azure Powershellu vytvoří novou bránu v2 s odpovídající velikost pro zpracování provozu na existující bránu v1. Automatické škálování je ve výchozím nastavení zakázané, ale při spuštění skriptu můžete povolit automatické škálování.

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>Jsem nakonfiguroval Moje brány v1 odeslat protokoly do služby Azure storage. Skript replikovat tuto konfiguraci pro v2 také?

Ne. Skript není replikovat tuto konfiguraci pro v2. Musíte přidat konfiguraci protokolu samostatně k bráně migrované v2.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Můžu narazili na některé problémy s pomocí tohoto skriptu. Jak získám pomoc?
  
Můžete poslat e-mailu appgwmigrationsup@microsoft.com, otevřete případ podpory se podpory Azure, nebo proveďte obojí.

## <a name="next-steps"></a>Další postup

[Další informace o Application Gateway v2](application-gateway-autoscaling-zone-redundant.md)
