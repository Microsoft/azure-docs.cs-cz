---
title: Zpětné vyhledávání DNS pro služby Azure | Dokumentace Microsoftu
description: Další informace o konfiguraci zpětného vyhledávání DNS pro služby hostované v Azure
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: victorh
ms.openlocfilehash: 4a9a1b5599468df6bc85cc1d535b577c508dd0a9
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55995642"
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Konfigurace reverzních záznamů DNS pro služby hostované v Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tento článek vysvětluje postup konfigurace reverzního vyhledávání DNS pro služby hostované v Azure.

Služby v Azure pomocí IP adresy přiřazené přes Azure a ve vlastnictví společnosti Microsoft. Tyto reverzních záznamů DNS (záznam PTR) musí být vytvořeny v odpovídající vlastněným microsoftem zón reverzního vyhledávání DNS. Tento článek vysvětluje, jak to provést.

Tento scénář, neměly by být zaměňovány s možností [hostování zón reverzního vyhledávání DNS pro vaši přiřazené rozsahy IP adres v Azure DNS](dns-reverse-dns-hosting.md). V takovém případě rozsahy IP adres, který je reprezentován zóny zpětného vyhledávání musí přiřadit k organizaci, obvykle podle svého poskytovatele internetových služeb.

Před čtením tohoto článku, měli byste se seznámit s tím [přehled reverzní DNS a podporu v Azure](dns-reverse-dns-overview.md).

V Azure DNS výpočetní prostředky (třeba virtuální počítače, škálovací sady virtuálních počítačů nebo clustery Service Fabric) jsou přístupné prostřednictvím prostředků PublicIpAddress. Zpětné vyhledávání DNS se konfigurují pomocí vlastnosti "ReverseFqdn" pro PublicIpAddress.


Reverzních záznamů DNS není aktuálně podporována pro službu Azure App Service.

## <a name="validation-of-reverse-dns-records"></a>Ověření reverzních záznamů DNS

Třetí strany by neměl být schopen vytvořit reverzních záznamů DNS pro jejich mapování služby Azure na svoje DNS domény. Chcete-li tomu zabránit, Azure pouze umožňuje vytváření zpětný záznam DNS, kde název domény zadaný v zpětný záznam DNS je stejný jako nebo se překládá na název DNS nebo IP adresu PublicIpAddress nebo cloudové služby v rámci stejného předplatného Azure.

Toto ověření se provádí, pouze když zpětný záznam DNS se nastavit nebo změnit. Pravidelné opakované ověření se neprovádí.

Příklad: Předpokládejme, že má prostředků PublicIpAddress contosoapp1.northus.cloudapp.azure.com název DNS a IP adresu 23.96.52.53. ReverseFqdn pro PublicIpAddress se dá nastavit jako:
* Název DNS pro adresu PublicIpAddress, contosoapp1.northus.cloudapp.azure.com
* Název DNS pro jinou adresu PublicIpAddress ve stejném předplatném, jako je například contosoapp2.westus.cloudapp.azure.com
* Vlastní název serveru DNS, jako je například app1.contoso.com, tak dlouho, dokud se tento název *první* nakonfigurovaný jako záznam CNAME contosoapp1.northus.cloudapp.azure.com, nebo na jinou adresu PublicIpAddress ve stejném předplatném.
* Vlastní název serveru DNS, jako je například app1.contoso.com, tak dlouho, dokud se tento název *první* nakonfigurovaný jako záznam 23.96.52.53 na IP adresu nebo IP adresu na jinou PublicIpAddress ve stejném předplatném.

Stejné omezení platí pro zpětné vyhledávání DNS pro cloudové služby.


## <a name="reverse-dns-for-publicipaddress-resources"></a>Zpětné vyhledávání DNS pro adresu PublicIpAddress prostředky

Tato část obsahuje podrobné pokyny, jak nakonfigurovat reverzních záznamů DNS pro adresu PublicIpAddress prostředky v modelu nasazení Resource Manager pomocí Azure Powershellu, příkazového řádku Azure classic nebo Azure CLI. Konfigurace reverzních záznamů DNS pro adresu PublicIpAddress prostředky se aktuálně nepodporuje prostřednictvím webu Azure portal.

Azure nyní podporují zpětné vyhledávání DNS jenom za prostředky PublicIpAddress protokolu IPv4. Není podporováno pro protokol IPv6.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Přidat do existující PublicIpAddresses reverzních záznamů DNS

#### <a name="powershell"></a>PowerShell

Chcete-li přidat do existující adresu PublicIpAddress reverzních záznamů DNS:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

Chcete-li přidat reverzních záznamů DNS pro existující adresu PublicIpAddress, která ještě nemá název DNS, musíte také zadat název DNS:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Azure Classic CLI

Chcete-li přidat do existující adresu PublicIpAddress reverzních záznamů DNS:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

Chcete-li přidat reverzních záznamů DNS pro existující adresu PublicIpAddress, která ještě nemá název DNS, musíte také zadat název DNS:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Azure CLI

Chcete-li přidat do existující adresu PublicIpAddress reverzních záznamů DNS:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Chcete-li přidat reverzních záznamů DNS pro existující adresu PublicIpAddress, která ještě nemá název DNS, musíte také zadat název DNS:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>Vytvoření veřejné IP adresy s reverzních záznamů DNS

Vytvoření nové PublicIpAddress s vlastnost reverzní DNS už zadali:

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

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>Zobrazení reverzních záznamů DNS pro existující adresu PublicIpAddress

Chcete-li zobrazit použije se konfigurovaná hodnota pro existující adresu PublicIpAddress:

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

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Odebrání existující veřejné IP adresy reverzních záznamů DNS

Reverzní DNS vlastnost odebrání existující adresu PublicIpAddress:

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


## <a name="configure-reverse-dns-for-cloud-services"></a>Konfigurace reverzních záznamů DNS pro Cloud Services

Tato část obsahuje podrobné pokyny, jak nakonfigurovat reverzních záznamů DNS pro cloudové služby v modelu nasazení Classic pomocí prostředí Azure PowerShell. Konfigurace reverzních záznamů DNS pro cloudové služby není podporována prostřednictvím webu Azure portal, Azure classic CLI nebo Azure CLI.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Přidat do existujících cloudových služeb reverzních záznamů DNS

Přidání zpětný záznam DNS do existující cloudové služby:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>Vytvořit Cloudovou službu s reverzních záznamů DNS

Chcete-li vytvořit novou Cloudovou službu s vlastnost reverzní DNS už zadali:

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>Zobrazení reverzních záznamů DNS pro existující cloudové služby

Chcete-li zobrazit existující Cloudovou službu vlastnost reverzní DNS:

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Odebrat reverzních záznamů DNS z existujících cloudových služeb

Reverzní DNS vlastnost odebrání existující cloudové služby:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>Nejčastější dotazy

### <a name="how-much-do-reverse-dns-records-cost"></a>Kolik reverzní DNS záznamy náklady?

Jsou zdarma!  Se neúčtují žádné další poplatky pro reverzních záznamů DNS nebo dotazy.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Vyřeší Moje reverzních záznamů DNS z Internetu?

Ano. Jakmile jednou nastavíte vlastnost reverzní DNS pro službu Azure, Azure spravuje delegování DNS a zóny DNS, které jsou potřeba k tomu, že zpětný záznam DNS překládá všem uživatelům Internetu.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Vytvořené výchozí reverzních záznamů DNS pro Moje služby Azure?

Ne. Reverzních záznamů DNS je přihlašovaná funkce. Reverzních záznamů DNS žádné výchozí se vytvoří, pokud se rozhodnete, že je nakonfigurovat.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Co je formát pro název plně kvalifikované domény (FQDN)?

Plně kvalifikované názvy domény jsou uvedeny v pořadí dopředu a musí být ukončen tečkou (například "app1.contoso.com.").

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>Co se stane, když ověření pro reverzních záznamů DNS jste zadané nezdaří?

Pokud reverzní kontroly ověřování DNS nepodaří, nakonfigurovat zpětný záznam DNS nezdaří. Opravte hodnotu reverzní DNS podle potřeby a zkuste to znovu.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Můžete nakonfigurovat reverzních záznamů DNS pro službu Azure App Service?

Ne. Reverzní DNS není podporována pro službu Azure App Service.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Můžete nakonfigurovat více reverzních záznamů DNS pro službu Azure?

Ne. Azure podporuje jeden zpětný záznam DNS pro jednotlivé cloudové služby Azure nebo PublicIpAddress.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>Můžete nakonfigurovat reverzních záznamů DNS pro adresu PublicIpAddress protokolu IPv6 prostředky?

Ne. Azure nyní podporují zpětné vyhledávání DNS pouze pro IPv4 adresu PublicIpAddress prostředky a cloudové služby.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Můžu poslat e-mailů do externích domén z mých služeb Azure Compute?

Technické možnost odesílat e-maily přímo z nasazení služby Azure závisí na typu předplatného. Bez ohledu na typ předplatného společnost Microsoft doporučuje používat služby pro přenos přes důvěryhodný e-mailu k odesílání odchozích e-mailu. Další podrobnosti najdete v tématu [rozšířené zabezpečení Azure pro odesílání e-mailem – listopad 2017 Update](https://blogs.msdn.microsoft.com/mast/2017/11/15/enhanced-azure-security-for-sending-emails-november-2017-update/).

## <a name="next-steps"></a>Další postup

Další informace o reverzních záznamů DNS najdete v tématu [zpětného vyhledávání DNS v encyklopedii Wikipedia](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Zjistěte, jak [hostování zóny zpětného vyhledávání pro váš rozsah IP přiřazené poskytovatele internetových služeb v Azure DNS](dns-reverse-dns-for-azure-services.md).

