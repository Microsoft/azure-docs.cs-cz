---
title: Kurz – Vytvoření vlastních záznamů Azure DNS pro webovou aplikaci
description: V tomto kurzu vytvoříte pomocí Azure DNS vlastní záznamy domény DNS pro webovou aplikaci.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 7/20/2018
ms.author: victorh
ms.openlocfilehash: b39c2c672869bb446e58134a85130d10491fe047
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621109"
---
# <a name="tutorial-create-dns-records-in-a-custom-domain-for-a-web-app"></a>Kurz: Vytvoření vlastních záznamů DNS ve vlastní doméně pro webovou aplikaci 

Azure DNS můžete nakonfigurovat na hostování vlastní domény pro vaše webové aplikace. Můžete třeba vytvořit webovou aplikaci Azure a umožnit uživatelům přístup k ní prostřednictvím plně kvalifikovaného názvu domény (FQDN) www.contoso.com nebo contoso.com.

> [!NOTE]
> V tomto kurzu se jako příklad používá doména contoso.com. Doménu contoso.com nahraďte vlastním názvem domény.

K tomu je potřeba vytvořit tři záznamy:

* Kořenový záznam „A“ odkazující na doménu contoso.com
* Kořenový záznam „TXT“ pro ověření
* Záznam „CNAME“ pro název webu, který odkazuje na záznam A

Mějte na paměti, že pokud vytvoříte záznam A pro webovou aplikaci v Azure, v případě změny základy IP adresy webové aplikace je potřeba záznam A ručně aktualizovat.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření záznamů A a TXT pro vlastní doménu
> * Vytvoření záznamu CNAME pro vlastní doménu
> * Testování nových záznamů
> * Přidání vlastních názvů hostitele do webové aplikace
> * Testování vlastních názvů hostitele


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="prerequisites"></a>Požadavky

- [Vytvořit plán služby App Service](../app-service/app-service-web-get-started-html.md) nebo použít aplikaci, kterou jste vytvořili pro účely jiného kurzu.

- Vytvořte v Azure DNS zónu DNS a prostřednictvím svého registrátora ji delegujte na Azure DNS.

   1. Při vytváření zóny DNS postupujte podle kroků v článku [Vytvoření zóny DNS](dns-getstarted-create-dnszone.md).
   2. Pokud chcete delegovat svoji zónu do Azure DNS, postupujte podle kroků v článku [Delegování domény DNS](dns-domain-delegation.md).

Po vytvoření zóny a jejím delegování do Azure DNS můžete vytvořit záznamy pro vlastní doménu.

## <a name="create-an-a-record-and-txt-record"></a>Vytvoření záznamů A a TXT

Záznam A slouží k mapování názvu na příslušnou IP adresu. V následujícím příkladu přiřaďte „\@“ jako záznam A a použijte při tom adresu IPv4 vaší webové aplikace. \@ většinou představuje kořenovou doménu.

### <a name="get-the-ipv4-address"></a>Získání adresy IPv4

Na levém navigačním panelu stránky App Services na webu Azure Portal vyberte **Vlastní domény**. 

![Nabídka Vlastní domény](../app-service/./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na stránce **Vlastní domény** zkopírujte adresu IPv4 aplikace:

![Přechod do aplikace Azure na portálu](../app-service/./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="create-the-a-record"></a>Vytvoření záznamu A

```powershell
New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" `
 -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 `
 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "<your web app IP address>")
```

### <a name="create-the-txt-record"></a>Vytvoření záznamu TXT

Služba App Services používá tento záznam jenom při konfiguraci, když potřebuje ověřit, že vám vlastní doména opravdu patří. Po ověření a konfiguraci vlastní domény ve službě App Service můžete tento záznam TXT odstranit.

```powershell
New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup `
 -Name `"@" -RecordType "txt" -Ttl 600 `
 -DnsRecords (New-AzureRmDnsRecordConfig -Value  "contoso.azurewebsites.net")
```

## <a name="create-the-cname-record"></a>Vytvoření záznamu CNAME

Pokud už vaši doménu spravuje Azure DNS (další informace najdete v článku [Delegování domény DNS](dns-domain-delegation.md)), můžete na základě následujícího příkladu vytvořit záznam CNAME pro doménu contoso.azurewebsites.net.

Otevřete Azure PowerShell a vytvořte nový záznam CNAME. Tento příklad vytvoří typ sady záznamů CNAME s hodnotou TTL (Time to Live) 600 sekund v zóně DNS s názvem „contoso.com“ s aliasem webové aplikace contoso.azurewebsites.net.

### <a name="create-the-record"></a>Vytvoření záznamu

```powershell
New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName "MyAzureResourceGroup" `
 -Name "www" -RecordType "CNAME" -Ttl 600 `
 -DnsRecords (New-AzureRmDnsRecordConfig -cname "contoso.azurewebsites.net")
```

Dalším příkladem je tato odpověď:

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

## <a name="test-the-new-records"></a>Testování nových záznamů

Správné vytvoření záznamů můžete ověřit zadáním dotazu na www.contoso.com a contoso.com pomocí nástroje nslookup, jak vidíte tady:

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

> contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    contoso.com
Address:  <ip of web app service>

> set type=txt
> contoso.com

Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
contoso.com text =

        "contoso.azurewebsites.net"
```
## <a name="add-custom-host-names"></a>Přidání vlastních názvů hostitele

Teď můžete do webové aplikace přidat vlastní názvy hostitele:

```powershell
set-AzureRmWebApp `
 -Name contoso `
 -ResourceGroupName MyAzureResourceGroup `
 -HostNames @("contoso.com","www.contoso.com","contoso.azurewebsites.net")
```
## <a name="test-the-custom-host-names"></a>Testování vlastních názvů hostitele

Otevřete prohlížeč a přejděte na `http://www.<your domainname>` a `http://<you domain name>` .

> [!NOTE]
> Nezapomeňte použít předponu `http://` , jinak se váš prohlížeč může pokusit o automatické doplnění adresy URL!

Pro obě adresy URL by se vám měla zobrazit stejná stránka. Příklad:

![Služba aplikace Contoso](media/dns-web-sites-custom-domain/contoso-app-svc.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už prostředky vytvořené v tomto kurzu nepotřebujete, můžete odstranit skupinu prostředků **myresourcegroup**.

## <a name="next-steps"></a>Další kroky

Naučte se vytvářet privátní zóny Azure DNS.

> [!div class="nextstepaction"]
> [Začínáme s privátními zónami Azure DNS v prostředí PowerShell](private-dns-getstarted-powershell.md)
