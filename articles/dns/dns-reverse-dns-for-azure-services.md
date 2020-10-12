---
title: Reverzní DNS pro služby Azure – Azure DNS
description: Pomocí této cesty výukového programu začněte konfigurovat reverzní vyhledávání DNS pro služby hostované v Azure.
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 15396467e92b3e035add03d0d29888558571aa2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84711234"
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Konfigurace reverzního DNS pro služby hostované v Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tento článek vysvětluje, jak nakonfigurovat reverzní vyhledávání DNS pro služby hostované v Azure.

Služby v Azure používají IP adresy přiřazené Azure a vlastněné Microsoftem. Tyto reverzní záznamy DNS (záznamy PTR) se musí vytvořit v odpovídajících zónách zpětného vyhledávání DNS vlastněných společností Microsoft. Tento článek vysvětluje, jak to provést.

Tento scénář by neměl být zaměnitelný s možností [hostování zón reverzního vyhledávání DNS pro přiřazené rozsahy IP adres v Azure DNS](dns-reverse-dns-hosting.md). V takovém případě musí být rozsahy IP adres reprezentované zónou zpětného vyhledávání přiřazené vaší organizaci, obvykle prostřednictvím poskytovatele internetových služeb.

Před čtením tohoto článku byste měli být obeznámeni s tímto [přehledem reverzních DNS a podpory v Azure](dns-reverse-dns-overview.md).

V Azure DNS jsou k dispozici výpočetní prostředky (například virtuální počítače, sady škálování virtuálních počítačů nebo clustery Service Fabric) prostřednictvím prostředku PublicIpAddress. Reverzní vyhledávání DNS se konfigurují pomocí vlastnosti ' ReverseFqdn ' objektu PublicIpAddress.


Reverzní DNS se v současnosti pro Azure App Service nepodporuje.

## <a name="validation-of-reverse-dns-records"></a>Ověření reverzních záznamů DNS

Třetí strana by neměla mít oprávnění k vytváření reverzních záznamů DNS pro své mapování služeb Azure do domén DNS. Aby k tomu nedocházelo, Azure umožňuje vytvořit reverzní záznam DNS jenom v případě, že název domény zadaný v záznamu reverzní DNS je stejný jako název DNS nebo IP adresa PublicIpAddress nebo cloudové služby ve stejném předplatném Azure.

Toto ověření se provede, jenom když se nastaví nebo upraví reverzní záznam DNS. Periodické opakované ověření není provedeno.

Příklad: Předpokládejme, že prostředek PublicIpAddress má název DNS contosoapp1.northus.cloudapp.azure.com a IP adresa 23.96.52.53. ReverseFqdn pro PublicIpAddress lze zadat jako:
* Název DNS pro PublicIpAddress, contosoapp1.northus.cloudapp.azure.com
* Název DNS pro různé PublicIpAddress ve stejném předplatném, jako je contosoapp2.westus.cloudapp.azure.com
* Název DNS individuální, jako je například app1.contoso.com, pokud je tento název *poprvé* NAKONFIGUROVANÝ jako CNAME na ContosoApp1.northus.cloudapp.Azure.com nebo jiný PublicIpAddress ve stejném předplatném.
* Název DNS individuální, jako je například app1.contoso.com, pokud se tento název *nejprve* nakonfiguruje jako záznam a na IP adresu 23.96.52.53, nebo na IP adresu jiného PublicIpAddress ve stejném předplatném.

Stejné omezení platí pro reverzní DNS pro Cloud Services.


## <a name="reverse-dns-for-publicipaddress-resources"></a>Reverzní DNS pro prostředky PublicIpAddress

V této části najdete podrobné pokyny pro konfiguraci reverzních prostředků DNS pro prostředky PublicIpAddress v modelu nasazení Správce prostředků, a to pomocí Azure PowerShell, rozhraní příkazového řádku Azure Classic nebo rozhraní příkazového řádku Azure CLI. Konfigurace reverzního DNS pro prostředky PublicIpAddress se v tuto chvíli nepodporuje prostřednictvím Azure Portal.

Azure aktuálně podporuje reverzní DNS jenom pro prostředky IPv4 PublicIpAddress. Pro protokol IPv6 se nepodporuje.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Přidat reverzní DNS do existujícího PublicIpAddresses

#### <a name="powershell"></a>PowerShell

Postup aktualizace reverzního DNS na stávající PublicIpAddress:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

Pokud chcete přidat reverzní DNS do existující PublicIpAddress, která ještě nemá název DNS, musíte zadat taky název DNS:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Azure Classic CLI

Postup přidání reverzního DNS do existujícího PublicIpAddress:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

Pokud chcete přidat reverzní DNS do existující PublicIpAddress, která ještě nemá název DNS, musíte zadat taky název DNS:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Azure CLI

Postup přidání reverzního DNS do existujícího PublicIpAddress:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Pokud chcete přidat reverzní DNS do existující PublicIpAddress, která ještě nemá název DNS, musíte zadat taky název DNS:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>Vytvoření veřejné IP adresy pomocí reverzního DNS

Chcete-li vytvořit novou PublicIpAddress s již zadanou vlastností reverzního DNS:

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

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>Zobrazit reverzní DNS pro existující PublicIpAddress

Zobrazení nakonfigurované hodnoty pro existující PublicIpAddress:

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

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Odebrat reverzní DNS z existující Veřejné IP adresy

Postup odebrání reverzní vlastnosti DNS z existujícího PublicIpAddress:

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


## <a name="configure-reverse-dns-for-cloud-services"></a>Konfigurace reverzního DNS pro Cloud Services

V této části najdete podrobné pokyny ke konfiguraci reverzního DNS pro Cloud Services v modelu nasazení Classic pomocí Azure PowerShell. Konfigurace reverzního DNS pro Cloud Services není podporována prostřednictvím Azure Portal, rozhraní příkazového řádku Azure Classic nebo rozhraní příkazového řádku Azure CLI.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Přidat reverzní DNS do existující Cloud Services

Postup přidání reverzního záznamu DNS do existující cloudové služby:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>Vytvoření cloudové služby pomocí reverzního DNS

Pokud chcete vytvořit novou cloudovou službu s již zadanou vlastností reverzního DNS:

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>Zobrazit reverzní DNS pro existující Cloud Services

Zobrazení reverzní vlastnosti DNS pro existující cloudovou službu:

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Odebrat reverzní DNS z existující Cloud Services

Odebrání reverzní vlastnosti DNS z existující cloudové služby:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>Časté otázky

### <a name="how-much-do-reverse-dns-records-cost"></a>Kolik stojí reverzních záznamů DNS?

Jsou zdarma.  Pro reverzní záznamy a dotazy DNS se neúčtují žádné další náklady.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Vyřeší se moje reverzní záznamy DNS z Internetu?

Ano. Po nastavení reverzní vlastnosti DNS pro vaši službu Azure spravuje Azure všechny delegování DNS a zóny DNS, které jsou potřeba k tomu, aby se zajistilo, že reverzní záznam DNS bude vyřešen pro všechny uživatele internetu.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Jsou výchozí reverzní záznamy DNS vytvořené pro moje služby Azure?

Ne. Reverzní služba DNS je funkce výslovného souhlasu. Pokud se rozhodnete nekonfigurovat, nebudou vytvořeny žádné výchozí záznamy DNS reverzní.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Jaký je formát plně kvalifikovaného názvu domény (FQDN)?

Plně kvalifikované názvy domény jsou určené v pořadí od sebe a musí se končit tečkou (například "app1.contoso.com").

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>Co se stane, když se u zadané reverzní služby DNS nepodaří ověřit ověření?

Pokud se kontrola reverzního ověřování DNS nezdařila, operace konfigurace reverzního záznamu DNS se nezdařila. Opravte hodnotu reverzní DNS podle potřeby a zkuste to znovu.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Můžu pro Azure App Service nakonfigurovat reverzní DNS?

Ne. Reverzní DNS se pro Azure App Service nepodporuje.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Můžu nakonfigurovat více reverzních záznamů DNS pro službu Azure?

Ne. Azure podporuje jeden reverzní záznam DNS pro každou cloudovou službu Azure nebo PublicIpAddress.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>Můžu nakonfigurovat reverzní DNS pro prostředky PublicIpAddress IPv6?

Ne. Azure aktuálně podporuje reverzní DNS jenom pro prostředky IPv4 PublicIpAddress a Cloud Services.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Můžu odeslat e-maily externím doménám z výpočetních služeb Azure?

Technická možnost odesílání e-mailů přímo z nasazení Azure závisí na typu předplatného. Bez ohledu na typ předplatného Microsoft doporučuje odesílat odchozí e-mailové služby pomocí důvěryhodných poštovních přenosů. Další podrobnosti najdete v tématu [Rozšířené zabezpečení Azure pro odesílání e-mailů – listopad 2017 Update](https://blogs.msdn.microsoft.com/mast/2017/11/15/enhanced-azure-security-for-sending-emails-november-2017-update/).

## <a name="next-steps"></a>Další kroky

Další informace o reverzních DNS najdete v tématu [reverzní DNS Lookup v Wikipedii](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Naučte se [hostovat zónu zpětného vyhledávání pro rozsah IP adres přiřazený poskytovateli internetových služeb v Azure DNS](dns-reverse-dns-for-azure-services.md).

