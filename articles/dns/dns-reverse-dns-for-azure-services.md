---
title: Reverse DNS pro služby Azure | Microsoft Docs
description: Naučte se konfigurovat zpětné vyhledávání DNS pro služby hostované v Azure
services: dns
documentationcenter: na
author: KumudD
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: kumud
ms.openlocfilehash: bba2d75ed760a6e4eef2caacb0bb5924680b1f4b
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Konfigurace zpětné DNS pro služby hostované v Azure

Tento článek vysvětluje postup konfigurace zpětného vyhledávání DNS pro služby hostované v Azure.

Služby v Azure pomocí IP adresy přiřazené službou Azure a vlastnictví společnosti Microsoft. Tyto zpětné záznamy DNS (záznam PTR) musí být vytvořen v odpovídající ve vlastnictví společnosti Microsoft zpětného vyhledávání zóny DNS. Tento článek vysvětluje, jak to udělat.

Tento scénář Nezaměňovat s schopnost [hostitele zpětné vyhledávání zóny DNS pro vaše přiřazené rozsahy IP v Azure DNS](dns-reverse-dns-hosting.md). V takovém případě rozsahy IP reprezentována zóny zpětného vyhledávání musí být přiřadila pro vaši organizaci, obvykle vašeho poskytovatele internetových služeb.

Před přečtení tohoto článku, měli byste se seznámit s tím [přehled zpětné DNS a podpory v Azure](dns-reverse-dns-overview.md).

V Azure DNS výpočetní prostředky (třeba virtuální počítače sady škálování virtuálního počítače nebo clusterů Service Fabric) jsou zveřejňovány prostřednictvím PublicIpAddress prostředků. Zpětné vyhledávání DNS se konfiguruje pomocí vlastnosti 'ReverseFqdn' PublicIpAddress.


Zpětné DNS není aktuálně podporován pro službu Azure App Service.

## <a name="validation-of-reverse-dns-records"></a>Ověření záznamy zpětného vyhledávání DNS

Třetí strany neměli mít možnost vytvářet zpětné záznamy DNS pro jejich mapování služby Azure na vašich domén DNS. Chcete-li tomu zabránit, Azure jenom umožňuje vytvoření zpětné záznam DNS, kde název domény zadaný v zpětné záznam DNS je stejný jako nebo přeloží na název DNS nebo IP adresu PublicIpAddress nebo cloudové služby ve stejném předplatném Azure.

Toto ověření se provádí pouze při nastavit nebo změnit zpětné záznamu DNS. Pravidelně opakované ověření není provedena.

Příklad: Předpokládejme, že má prostředek PublicIpAddress contosoapp1.northus.cloudapp.azure.com název DNS a IP adresu 23.96.52.53. ReverseFqdn pro PublicIpAddress lze zadat jako:
* Název DNS pro PublicIpAddress, contosoapp1.northus.cloudapp.azure.com
* Název DNS pro různé PublicIpAddress v rámci stejného předplatného, jako je například contosoapp2.westus.cloudapp.azure.com
* Jednoduché DNS název, jako je například app1.contoso.com, tak dlouho, dokud tento název se *první* nakonfigurovaný jako záznam CNAME contosoapp1.northus.cloudapp.azure.com nebo na jinou PublicIpAddress ve stejném předplatném.
* Jednoduché DNS název, jako je například app1.contoso.com, tak dlouho, dokud tento název se *první* nakonfigurovaný jako záznam na IP adresu 23.96.52.53, nebo na IP adresu na jinou PublicIpAddress ve stejném předplatném.

Stejné omezení se vztahují na zpětnou DNS pro cloudové služby.


## <a name="reverse-dns-for-publicipaddress-resources"></a>Reverse DNS pro prostředky PublicIpAddress

Tato část obsahuje podrobné pokyny pro konfiguraci zpětné DNS pro prostředky PublicIpAddress v modelu nasazení Resource Manager pomocí prostředí Azure PowerShell, Azure CLI 1.0 nebo 2.0 rozhraní příkazového řádku Azure. Konfigurace zpětné DNS pro prostředky PublicIpAddress není podporována aktuálně prostřednictvím portálu Azure.

Azure aktuálně podporuje reverse DNS pouze pro prostředky IPv4 PublicIpAddress. Není podporováno pro protokol IPv6.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Přidejte do existující publicipaddresses, na které zpětné DNS

#### <a name="powershell"></a>PowerShell

Přidání zpětné DNS do existující PublicIpAddress:

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

Pokud chcete přidat zpětné DNS do existující PublicIpAddress, který ještě nemá název DNS, musíte zadat také název DNS:

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

Přidání zpětné DNS do existující PublicIpAddress:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

Pokud chcete přidat zpětné DNS do existující PublicIpAddress, který ještě nemá název DNS, musíte zadat také název DNS:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

Přidání zpětné DNS do existující PublicIpAddress:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Pokud chcete přidat zpětné DNS do existující PublicIpAddress, který ještě nemá název DNS, musíte zadat také název DNS:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>Vytvoření veřejné IP adresy s zpětné DNS

Vytvoření nové PublicIpAddress s vlastností zpětné DNS už zadali:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network public-ip create -n PublicIp -g MyResourceGroup -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
az network public-ip create --name PublicIp --resource-group MyResourceGroup --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>Zobrazení zpětného DNS pro existující PublicIpAddress

Chcete-li zobrazit konfigurovaná hodnota pro existující PublicIpAddress:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network public-ip show -n PublicIp -g MyResourceGroup
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
az network public-ip show --name PublicIp --resource-group MyResourceGroup
```

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Odeberte zpětné DNS z existující veřejné IP adresy

Odebrání stávající PublicIpAddress zpětné vlastnosti DNS:

#### <a name="powershell"></a>PowerShell

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup –f ""
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn ""
```


## <a name="configure-reverse-dns-for-cloud-services"></a>Konfigurace zpětné DNS pro cloudové služby

Tato část obsahuje podrobné pokyny pro konfiguraci DNS zpětného pro cloudové služby v modelu nasazení Classic pomocí Azure PowerShell. Konfigurace zpětné DNS pro cloudové služby není podporována prostřednictvím portálu Azure, Azure CLI 1.0 nebo 2.0 rozhraní příkazového řádku Azure.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Přidání zpětné DNS do existující cloudové služby

Chcete-li přidat zpětné záznam DNS do existující cloudové služby:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>Vytvoření cloudové služby se zpětné DNS

Chcete-li vytvořit novou Cloudovou službu s vlastností zpětné DNS již zadán:

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>Zobrazení zpětného DNS pro existující cloudové služby

Pokud chcete zobrazit zpětné vlastnosti DNS pro stávající Cloudovou službu:

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Odeberte zpětné DNS z existující cloudové služby

Pokud chcete odebrat zpětné DNS z existující cloudové služby:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>Nejčastější dotazy

### <a name="how-much-do-reverse-dns-records-cost"></a>Kolik reverse náklady na záznamy DNS?

Jsou zdarma!  Není k dispozici bez dalších nákladů pro zpětné záznamy DNS nebo dotazy.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Odstraní zpětné záznamy DNS z Internetu?

Ano. Jakmile jednou nastavíte vlastnost zpětné DNS služby Azure, Azure spravuje všechny delegování DNS a vyžaduje se pro zajištění toho, zda řeší zpětné záznam DNS pro všechny uživatele Internetu zóny DNS.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Vytvářejí výchozí záznamy zpětného vyhledávání DNS pro Moje služby Azure?

Ne. Zpětné DNS je funkce přihlášení. Žádné záznamy výchozí zpětného vyhledávání DNS se vytvoří, pokud se rozhodnete je nakonfigurovat.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Co je formát plně kvalifikovaný název domény (FQDN)?

Plně kvalifikované názvy domény jsou zadány popořadě a musí být ukončen tečkou (například "app1.contoso.com.").

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>Co se stane, když ověření pravosti pro zpětné DNS I jste určili nezdaří?

Kde zpětné DNS kontrolu ověření nezdaří, operace nakonfigurovat zpětné záznam DNS se nezdaří. Opravte hodnotu zpětné DNS podle potřeby a opakujte.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Můžete nakonfigurovat zpětné DNS pro službu Azure App Service?

Ne. Zpětné DNS není podporována pro službu Azure App Service.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Můžete nakonfigurovat více zpětné záznamy DNS pro Moje služba Azure?

Ne. Azure podporuje jeden zpětné záznam DNS pro každé cloudové služby Azure nebo PublicIpAddress.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>Můžete nakonfigurovat zpětné DNS pro prostředky IPv6 PublicIpAddress?

Ne. Azure aktuálně podporuje reverse DNS pouze pro prostředky IPv4 PublicIpAddress a cloudové služby.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Můžete odeslat e-mailů k externími doménami z mé Azure výpočetní služby?

Technické schopnost posílání e-mailu přímo z nasazení služby Azure, závisí na typu předplatného. Bez ohledu na typ předplatného společnost Microsoft doporučuje používat služby předávání přes důvěryhodné e-mailu k odesílání odchozí pošty. Další podrobnosti najdete v tématu [rozšířené zabezpečení Azure pro odesílání E-maily – listopad 2017 aktualizace](https://blogs.msdn.microsoft.com/mast/2017/11/15/enhanced-azure-security-for-sending-emails-november-2017-update/).

## <a name="next-steps"></a>Další postup

Další informace o zpětné DNS najdete v tématu [zpětného vyhledávání DNS na webu Wikipedia](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Zjistěte, jak [hostitel zóny zpětného vyhledávání pro váš rozsah poskytovatele internetových služeb přiřadit IP v Azure DNS](dns-reverse-dns-for-azure-services.md).

