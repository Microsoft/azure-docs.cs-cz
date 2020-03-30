---
title: Reverzní DNS pro služby Azure – Azure DNS
description: S tímto studijním programem můžete začít konfigurovat reverzní vyhledávání DNS pro služby hostované v Azure.
services: dns
documentationcenter: na
author: rohinkoul
manager: KumudD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 073e84ece11f6817bfe2c5a94735ec6e16dac4fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76932376"
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Konfigurace reverzního DNS pro služby hostované v Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tento článek vysvětluje, jak nakonfigurovat zpětné vyhledávání DNS pro služby hostované v Azure.

Služby v Azure používají IP adresy přiřazené Azure a vlastněné Microsoftem. Tyto reverzní záznamy DNS (záznamy PTR) musí být vytvořeny v odpovídajících zónách zpětného vyhledávání DNS vlastněných společností Microsoft. Tento článek vysvětluje, jak to udělat.

Tento scénář by neměl být zaměňován se schopností [hostovat reverzní zóny vyhledávání DNS pro přiřazené rozsahy IP adres v Azure DNS](dns-reverse-dns-hosting.md). V takovém případě musí být rozsahy IP představované zónou zpětného vyhledávání přiřazeny vaší organizaci, obvykle vaším poskytovateli.

Před přečtením tohoto článku byste měli být obeznámeni s tímto [přehledem reverzní DNS a podpory v Azure](dns-reverse-dns-overview.md).

Ve službě Azure DNS jsou výpočetní prostředky (například virtuální počítače, škálovací sady virtuálních počítačů nebo clustery Service Fabric) vystaveny prostřednictvím prostředku PublicIpAddress. Reverzní vyhledávání DNS jsou konfigurovány pomocí vlastnosti ReverseFqdn adresy PublicIpAddress.


Reverzní DNS není aktuálně podporovánpro službu Azure App Service.

## <a name="validation-of-reverse-dns-records"></a>Ověření reverzních záznamů DNS

Třetí strana by neměla být schopna vytvářet reverzní záznamy DNS pro mapování služby Azure do domén DNS. Aby se tomu zabránilo, Azure umožňuje pouze vytvoření reverzní hod DNS záznam, kde název domény zadaný v reverzní záznam DNS je stejný jako nebo překládá na název DNS nebo IP adresu PublicIpAddress nebo cloudové služby ve stejném předplatném Azure.

Toto ověření se provádí pouze v případě, že je nastaven nebo změněn reverzní záznam DNS. Periodické opětovné ověření se neprovádí.

Například: Předpokládejme, že prostředek PublicIpAddress má název DNS contosoapp1.northus.cloudapp.azure.com a IP adresu 23.96.52.53. ReverseFqdn pro PublicIpAddress lze zadat jako:
* Název DNS pro adresu PublicIpAddress, contosoapp1.northus.cloudapp.azure.com
* Název DNS pro jinou adresu PublicIpAddress ve stejném předplatném, například contosoapp2.westus.cloudapp.azure.com
* Vanity DNS název, například app1.contoso.com, tak dlouho, dokud tento název je *nejprve* nakonfigurován jako CNAME k contosoapp1.northus.cloudapp.azure.com nebo na jinou PublicIpAddress ve stejném předplatném.
* Ješitný název DNS, například app1.contoso.com, pokud je tento název *nejprve* nakonfigurován jako záznam A na adresu IP 23.96.52.53 nebo na ADRESU IP jiné homailu PublicIpAddress ve stejném předplatném.

Stejná omezení platí pro reverzní DNS pro cloudové služby.


## <a name="reverse-dns-for-publicipaddress-resources"></a>Reverzní DNS pro prostředky PublicIpAddress

Tato část obsahuje podrobné pokyny, jak nakonfigurovat reverzní prostředky DNS pro PublicIpAddress v modelu nasazení Správce prostředků, a to buď pomocí Azure PowerShell, Azure classic CLI nebo Azure CLI. Konfigurace reverzní dns pro prostředky PublicIpAddress není aktuálně podporována prostřednictvím portálu Azure.

Azure aktuálně podporuje reverzní DNS pouze pro prostředky IPv4 PublicIpAddress. Není podporován pro IPv6.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Přidání reverzního DNS do existujících adres PublicIpAddresses

#### <a name="powershell"></a>PowerShell

Aktualizace reverzního DNS na existující adresu PublicIpAddress:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

Chcete-li přidat reverzní DNS do existující adresy PublicIpAddress, která ještě nemá název DNS, musíte také zadat název DNS:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Azure Classic CLI

Přidání reverzního DNS do existující adresy PublicIpAddress:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

Chcete-li přidat reverzní DNS do existující adresy PublicIpAddress, která ještě nemá název DNS, musíte také zadat název DNS:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Azure CLI

Přidání reverzního DNS do existující adresy PublicIpAddress:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Chcete-li přidat reverzní DNS do existující adresy PublicIpAddress, která ještě nemá název DNS, musíte také zadat název DNS:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>Vytvoření veřejné IP adresy s reverzním DNS

Chcete-li vytvořit novou adresu PublicIpAddress s již zadanou vlastností reverse DNS:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

#### <a name="azure-classic-cli"></a>Azure Classic CLI

```azurecli
azure network public-ip create -n PublicIp -g MyResourceGroup -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network public-ip create --name PublicIp --resource-group MyResourceGroup --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>Zobrazit reverzní DNS pro existující adresu PublicIpAddress

Chcete-li zobrazit nakonfigurovanou hodnotu pro existující adresu PublicIpAddress:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
```

#### <a name="azure-classic-cli"></a>Azure Classic CLI

```azurecli
azure network public-ip show -n PublicIp -g MyResourceGroup
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network public-ip show --name PublicIp --resource-group MyResourceGroup
```

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Odebrání reverzního DNS z existujících veřejných IP adres

Odebrání reverzní vlastnosti DNS z existující adresy PublicIpAddress:

#### <a name="powershell"></a>PowerShell

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Azure Classic CLI

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup –f ""
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn ""
```


## <a name="configure-reverse-dns-for-cloud-services"></a>Konfigurace reverzního DNS pro cloudové služby

Tato část obsahuje podrobné pokyny, jak nakonfigurovat reverzní DNS pro cloudové služby v klasickém modelu nasazení pomocí Azure PowerShellu. Konfigurace reverzního DNS pro cloudové služby není podporovaná prostřednictvím portálu Azure, klasického příkazového příkazového příkazového příkazu Azure nebo příkazového příkazového příkazu Konziuma nebo příkazového příkazového příkazu Konziuma.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Přidání reverzního DNS do stávajících cloudových služeb

Přidání reverzního záznamu DNS do existující cloudové služby:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>Vytvoření cloudové služby s reverzním DNS

Vytvoření nové cloudové služby s již zadanou vlastností reverse DNS:

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>Zobrazit reverzní DNS pro existující cloudové služby

Zobrazení reverzní vlastnosti DNS pro existující cloudovou službu:

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Odebrání reverzního DNS z existujících cloudových služeb

Odebrání reverzní vlastnosti DNS z existující cloudové služby:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>Nejčastější dotazy

### <a name="how-much-do-reverse-dns-records-cost"></a>Kolik stojí reverzní záznamy DNS?

Jsou volní!  Neexistuje žádné další náklady na reverzní záznamy DNS nebo dotazy.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Vyřeší se z internetu zpětně dns záznamy?

Ano. Jakmile nastavíte reverzní vlastnost DNS pro vaši službu Azure, Azure spravuje všechny delegace DNS a zóny DNS potřebné k zajištění, že reverzní záznam DNS vyřeší pro všechny uživatele internetu.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Jsou pro mé služby Azure vytvořeny výchozí reverzní záznamy DNS?

Ne. Reverzní DNS je funkce opt-in. Pokud se rozhodnete je nekonfigurovat, nebudou vytvořeny žádné výchozí reverzní záznamy DNS.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Jaký je formát plně kvalifikovaného názvu domény (FQDN)?

Soubory FQDN jsou určeny v předsunutovém pořadí a musí být ukončeny tečkou (například "app1.contoso.com.").

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>Co se stane, pokud se nezdaří kontrola ověření zadaný reverzní dns?

V případě, že se nezdaří kontrola ověření dns, operace konfigurace reverzního záznamu DNS se nezdaří. Podle potřeby opravte zpětnou hodnotu DNS a opakujte akci.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Můžu nakonfigurovat reverzní DNS pro Azure App Service?

Ne. Reverzní DNS není podporována pro službu Azure App Service.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Můžu pro svou službu Azure nakonfigurovat více reverzních DNS záznamů?

Ne. Azure podporuje jeden reverzní záznam DNS pro každou cloudovou službu Azure nebo PublicIpAddress.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>Lze nakonfigurovat reverzní DNS pro prostředky IPv6 PublicIpAddress?

Ne. Azure aktuálně podporuje reverzní DNS jenom pro prostředky IPv4 PublicIpAddress a cloudové služby.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Můžu posílat e-maily do externích domén ze svých výpočetních služeb Azure?

Technická možnost odesílat e-maily přímo z nasazení Azure závisí na typu předplatného. Bez ohledu na typ předplatného společnost Microsoft doporučuje k odesílání odchozí pošty používat důvěryhodné služby přenosu pošty. Další podrobnosti najdete [v tématu Rozšířené zabezpečení Azure pro odesílání e-mailů – aktualizace z listopadu 2017](https://blogs.msdn.microsoft.com/mast/2017/11/15/enhanced-azure-security-for-sending-emails-november-2017-update/).

## <a name="next-steps"></a>Další kroky

Další informace o reverzním DNS naleznete v [tématu reverzní vyhledávání DNS na Wikipedii](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Přečtěte si, jak [hostovat zónu zpětného vyhledávání pro rozsah IP adres přiřazený k isp v Azure DNS](dns-reverse-dns-for-azure-services.md).

