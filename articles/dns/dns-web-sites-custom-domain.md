---
title: Vytvořit vlastní záznamy DNS pro webovou aplikaci | Microsoft Docs
description: Jak vytvořit vlastní doménu. záznamy DNS pro webovou aplikaci pomocí Azure DNS.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.assetid: 6c16608c-4819-44e7-ab88-306cf4d6efe5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: kumud
ms.openlocfilehash: 00a56a2683e95e70bb13acd6b936e766f044e1cd
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="create-dns-records-for-a-web-app-in-a-custom-domain"></a>Vytvořit záznamy DNS pro webovou aplikaci ve vlastní domény

Azure DNS můžete použít k hostování vlastní doménu pro webové aplikace. Například při vytváření webové aplikace Azure a mají vaši uživatelé k němu přístup, a to buď pomocí contoso.com nebo www.contoso.com jako plně kvalifikovaný název domény.

Chcete-li to provést, je nutné vytvořit dva záznamy:

* Kořenový "A" záznam odkazující na contoso.com
* Záznam "CNAME" www název, který odkazuje na záznam a.

Mějte na paměti, že pokud vytváříte záznam pro webovou aplikaci v Azure, záznam A musí být ručně aktualizovat, pokud zdrojová IP adresa pro změny webové aplikace.

## <a name="before-you-begin"></a>Než začnete

Než začnete, musíte nejprve vytvořit zónu DNS v Azure DNS a delegovat zóny v registrátora do Azure DNS.

1. Pokud chcete vytvořit zónu DNS, postupujte podle kroků v [vytvořit zónu DNS](dns-getstarted-create-dnszone.md).
2. Delegování DNS do Azure DNS, postupujte podle kroků v [delegování DNS domény](dns-domain-delegation.md).

Po vytvoření zóny a delegování do Azure DNS, potom můžete vytvořit záznamy pro vaši vlastní doménu.

## <a name="1-create-an-a-record-for-your-custom-domain"></a>1. Vytvoření záznamu A pro vaši vlastní doménu.

Záznam A slouží k mapování názvů na IP adresu. V následujícím příkladu jsme se přiřadit jako záznam na adresu IPv4:

### <a name="step-1"></a>Krok 1

Vytvořte záznam a přiřaďte proměnnou $rs

```powershell
$rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600
```

### <a name="step-2"></a>Krok 2

Přidejte hodnotu IPv4 do sady dříve vytvořenou záznamu "@" použití $rs proměnné přiřazené. IPv4 hodnotu přiřazenou bude IP adresa pro vaši webovou aplikaci.

Najít IP adresu pro webovou aplikaci, postupujte podle kroků v [konfigurace vlastního názvu domény v Azure App Service](../app-service/app-service-web-tutorial-custom-domain.md).

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address "<your web app IP address>"
```

### <a name="step-3"></a>Krok 3

Potvrďte změny provedené v sadě záznamů. Použití `Set-AzureRMDnsRecordSet` odeslání změn na záznam nastavit do Azure DNS:

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="2-create-a-cname-record-for-your-custom-domain"></a>2. Vytvořte záznam CNAME pro vaši vlastní doménu.

Pokud vaše doména je již spravován nástrojem Azure DNS (v tématu [delegování DNS domény](dns-domain-delegation.md), můžete použít následující příklad vytvoří záznam CNAME pro contoso.azurewebsites.net.

### <a name="step-1"></a>Krok 1

Otevřete prostředí PowerShell a vytvořit novou sadu záznamů CNAME a proměnné $rs přiřadit. Tento příklad vytvoří sadu záznamů typu CNAME s "time to live" 600 sekund v zóně DNS s názvem "contoso.com".

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

Po vytvoření sady záznamů CNAME, budete muset vytvořit hodnotu alias, který bude odkazovat na webovou aplikaci.

Pomocí dříve přiřazenou proměnnou "$rs" můžete použít následující příkaz prostředí PowerShell pro vytvoření aliasu pro contoso.azurewebsites.net aplikace webové.

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

Potvrzení změn pomocí `Set-AzureRMDnsRecordSet` rutiny:

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

Můžete ověřit záznam byl vytvořen správně dotazováním "www.contoso.com" pomocí nástroje nslookup, jak je uvedeno níže:

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

## <a name="create-an-awverify-record-for-web-apps"></a>Vytvoření záznamů "awverify" pro webové aplikace

Pokud se rozhodnete použít záznam pro vaši webovou aplikaci, musí projít procesem ověření Ujistěte se, že vlastníte vlastní doménu. Tento krok ověření se provádí tak, že vytvoříte speciální záznam CNAME s názvem "awverify". Tato část se týká pouze záznamy.

### <a name="step-1"></a>Krok 1

Vytvořte záznam "awverify". V následujícím příkladu vytvoříme záznam "aweverify" pro doménu contoso.com ověřit vlastnictví pro vlastní doménu.

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

Po vytvoření sady záznamů "awverify" přiřadíte sadu alias záznamů CNAME. V následujícím příkladu jsme přiřadí nastavte alias na awverify.contoso.azurewebsites.net záznam CNAMe.

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

Potvrzení změn pomocí `Set-AzureRMDnsRecordSet cmdlet`, jak ukazuje následující příkaz.

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="next-steps"></a>Další postup

Postupujte podle kroků v [konfigurace vlastního názvu domény pro službu App Service](../app-service/app-service-web-tutorial-custom-domain.md) ke konfiguraci vaší webové aplikace na používat vlastní doménu.
