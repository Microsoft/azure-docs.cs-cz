---
title: Vytvoření vlastních záznamů DNS pro webovou aplikaci | Dokumentace Microsoftu
description: Jak vytvořit vlastní doménu záznamy DNS pro webovou aplikaci pomocí Azure DNS.
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
ms.assetid: 6c16608c-4819-44e7-ab88-306cf4d6efe5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: victorh
ms.openlocfilehash: f24c301cea5ef91d101206e71b69b7ceb03b0282
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39172445"
---
# <a name="create-dns-records-for-a-web-app-in-a-custom-domain"></a>Vytvoření záznamů DNS pro webovou aplikaci ve vlastní doméně

Azure DNS můžete hostovat vlastní domény pro web apps. Například při vytváření webové aplikace Azure a mají vaši uživatelé k němu přistupovat buď používá plně kvalifikovaný název domény contoso.com nebo www.contoso.com.

Chcete-li to provést, je nutné vytvořit dva záznamy:

* Záznam kořenové "A" odkazující na contoso.com
* Záznam "CNAME" www název, který odkazuje na záznam A

Mějte na paměti, že pokud vytvoříte záznam A pro webovou aplikaci v Azure, záznam A musí být ručně aktualizovat Pokud základní IP adres pro změny webové aplikace.

## <a name="before-you-begin"></a>Než začnete

Než začnete, musíte nejprve vytvořit zónu DNS v Azure DNS a delegovat zónu v Azure DNS vašeho registrátora.

1. Chcete-li vytvořit zónu DNS, postupujte podle kroků v [vytvořit zónu DNS](dns-getstarted-create-dnszone.md).
2. Delegování DNS do Azure DNS, postupujte podle kroků v [delegování DNS domény](dns-domain-delegation.md).

Po vytvoření zóny a delegování do Azure DNS, potom můžete vytvořit záznamy pro vaši vlastní doménu.

## <a name="1-create-an-a-record-for-your-custom-domain"></a>1. Vytvořit záznam A pro vaši vlastní doménu.

Záznam A slouží k mapování názvu na jeho IP adresu. V následujícím příkladu přiřadíme \@ jako záznam na adresu IPv4:

### <a name="step-1"></a>Krok 1

Vytvořit záznam a přiřaďte jej do proměnné $rs

```powershell
$rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600
```

### <a name="step-2"></a>Krok 2

Přidejte hodnotu IPv4 do dříve vytvořené sady záznamů "\@" použití $rs proměnné přiřadit. IPv4 hodnota přiřazená bude představovat IP adresu pro vaši webovou aplikaci.

Pokud chcete zjistit IP adresu pro webovou aplikaci, postupujte podle kroků v [konfigurace vlastního názvu domény ve službě Azure App Service](../app-service/app-service-web-tutorial-custom-domain.md).

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address "<your web app IP address>"
```

### <a name="step-3"></a>Krok 3

Potvrďte změny do sady záznamů. Použití `Set-AzureRMDnsRecordSet` odeslání změn do Azure DNS sada záznamů:

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="2-create-a-cname-record-for-your-custom-domain"></a>2. Vytvořte záznam CNAME pro vlastní doménu

Pokud se vaše doména je už spravovaný zprostředkovatelem Azure DNS (naleznete v tématu [delegování DNS domény](dns-domain-delegation.md), můžete použít následující příklad vytvoří záznam CNAME pro contoso.azurewebsites.net.

### <a name="step-1"></a>Krok 1

Otevřete PowerShell a vytvořit novou sadu záznamů CNAME a přiřaďte jej do proměnné $rs. Tento příklad vytvoří typ sady záznamů CNAME s "time to live" 600 sekund v zóně DNS s názvem "contoso.com".

```powershell
$rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600
```

Dalším příkladem je tato odpověď.

```
Name              : www
ZoneName          : contoso.com
ResourceGroupName : myresourcegroup
Ttl               : 600
Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
RecordType        : CNAME
Records           : {}
Tags              : {}
```

### <a name="step-2"></a>Krok 2

Po vytvoření sady záznamů CNAME, je potřeba vytvořit alias hodnotu, která bude ukazovat na webovou aplikaci.

Dříve přiřazenou proměnnou "$rs" můžete pomocí následujícího příkazu Powershellu vytvořit alias pro contoso.azurewebsites.net webové aplikace.

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"
```

Dalším příkladem je tato odpověď.

```
    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}
```

### <a name="step-3"></a>Krok 3

Potvrďte změny `Set-AzureRMDnsRecordSet` rutiny:

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

Můžete ověřit záznam byl vytvořen správně dotazováním "www.contoso.com" pomocí nástroje nslookup, jak je znázorněno níže:

```
PS C:\> nslookup
Default Server:  Default
Address:  192.168.0.1

> www.contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    <instance of web app service>.cloudapp.net
Address:  <ip of web app service>
Aliases:  www.contoso.com
contoso.azurewebsites.net
<instance of web app service>.vip.azurewebsites.windows.net
```

## <a name="create-an-awverify-record-for-web-apps"></a>Vytvoření záznamů "awverify" pro službu web apps

Pokud se rozhodnete použít záznam A pro vaši webovou aplikaci, musí projít procesem ověřování Ujistěte se, že jste vlastníkem vlastní domény. Tento krok ověření se provádí tak, že vytvoříte speciální záznam CNAME, který s názvem "awverify". Tato část se týká pouze záznamů.

### <a name="step-1"></a>Krok 1

Vytvoření záznamu "awverify". V následujícím příkladu vytvoříme "aweverify" záznam pro ověření vlastnictví pro vlastní doménu contoso.com.

```powershell
$rs = New-AzureRMDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "myresourcegroup" -Name "awverify" -RecordType "CNAME" -Ttl 600
```

Dalším příkladem je tato odpověď.

```
Name              : awverify
ZoneName          : contoso.com
ResourceGroupName : myresourcegroup
Ttl               : 600
Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
RecordType        : CNAME
Records           : {}
Tags              : {}
```

### <a name="step-2"></a>Krok 2

Po vytvoření sady záznamů "awverify" přiřadíte alias sadu záznamů CNAME. V následujícím příkladu přiřadíme nastavte alias na awverify.contoso.azurewebsites.net záznam CNAMe.

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"
```

Dalším příkladem je tato odpověď.

```
    Name              : awverify
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {awverify.contoso.azurewebsites.net}
    Tags              : {}
```

### <a name="step-3"></a>Krok 3

Potvrďte změny `Set-AzureRMDnsRecordSet cmdlet`, jak je znázorněno v následujícím příkazu.

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="next-steps"></a>Další postup

Postupujte podle kroků v [konfigurace vlastního názvu domény pro službu App Service](../app-service/app-service-web-tutorial-custom-domain.md) nakonfigurovat webovou aplikaci vlastní doménu.
