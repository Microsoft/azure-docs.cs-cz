---
title: Kurz – Vytvoření vlastních záznamů Azure DNS pro webovou aplikaci
description: V tomto kurzu vytvoříte pomocí Azure DNS vlastní záznamy domény DNS pro webovou aplikaci.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: rohink
ms.openlocfilehash: 151d5f083fe0e945b330d65095c908be93e3330f
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92316399"
---
# <a name="tutorial-create-dns-records-in-a-custom-domain-for-a-web-app"></a>Kurz: Vytvoření vlastních záznamů DNS ve vlastní doméně pro webovou aplikaci 

Azure DNS můžete nakonfigurovat na hostování vlastní domény pro vaše webové aplikace. Můžete například vytvořit webovou aplikaci Azure, která uživatelům přistupují pomocí webové \. contoso.com nebo contoso.com jako plně kvalifikovaného názvu domény (FQDN).

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
> * Přidání vlastních názvů hostitelů do vaší webové aplikace
> * Testování vlastních názvů hostitele

## <a name="prerequisites"></a>Předpoklady

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Musíte mít k dispozici název domény pro testování, který můžete hostovat v Azure DNS. Musíte mít úplnou kontrolu nad touto doménou. Úplná kontrola zahrnuje možnost nastavit pro doménu záznamy názvového serveru (NS).
* [Vytvořit plán služby App Service](../app-service/quickstart-html.md) nebo použít aplikaci, kterou jste vytvořili pro účely jiného kurzu.

* Vytvořte v Azure DNS zónu DNS a prostřednictvím svého registrátora ji delegujte na Azure DNS.

   1. Při vytváření zóny DNS postupujte podle kroků v článku [Vytvoření zóny DNS](dns-getstarted-create-dnszone.md).
   2. Pokud chcete delegovat svoji zónu do Azure DNS, postupujte podle kroků v článku [Delegování domény DNS](dns-delegate-domain-azure-dns.md).

Po vytvoření zóny a jejím delegování do Azure DNS můžete vytvořit záznamy pro vlastní doménu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-a-record-and-txt-record"></a>Vytvoření záznamů A a TXT

Záznam A slouží k mapování názvu na příslušnou IP adresu. V následujícím příkladu přiřaďte „\@“ jako záznam A a použijte při tom adresu IPv4 vaší webové aplikace. \@ většinou představuje kořenovou doménu.

### <a name="get-the-ipv4-address"></a>Získání adresy IPv4

Na levém navigačním panelu stránky App Services na webu Azure Portal vyberte **Vlastní domény**. 

![Nabídka Vlastní domény](../app-service/./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na stránce **Vlastní domény** zkopírujte adresu IPv4 aplikace:

![Přechod do aplikace Azure na portálu](../app-service/./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="create-the-a-record"></a>Vytvoření záznamu A

```azurepowershell
New-AzDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" `
 -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "<your web app IP address>")
```

### <a name="create-the-txt-record"></a>Vytvoření záznamu TXT

Služba App Services používá tento záznam jenom při konfiguraci, když potřebuje ověřit, že vám vlastní doména opravdu patří. Po ověření a konfiguraci vlastní domény ve službě App Service můžete tento záznam TXT odstranit.

> [!NOTE]
> Pokud chcete ověřit název domény, ale nechcete směrovat provozní provoz do webové aplikace, stačí zadat záznam TXT pro krok ověření.  Ověření kromě záznamu TXT nevyžaduje i záznam typu A ani CNAME.

```azurepowershell
New-AzDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup `
 -Name "@" -RecordType "txt" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -Value  "contoso.azurewebsites.net")
```

## <a name="create-the-cname-record"></a>Vytvoření záznamu CNAME

Pokud už vaši doménu spravuje Azure DNS (další informace najdete v článku [Delegování domény DNS](dns-domain-delegation.md)), můžete na základě následujícího příkladu vytvořit záznam CNAME pro doménu contoso.azurewebsites.net.

Otevřete Azure PowerShell a vytvořte nový záznam CNAME. Tento příklad vytvoří typ sady záznamů CNAME s hodnotou TTL (Time to Live) 600 sekund v zóně DNS s názvem „contoso.com“ s aliasem webové aplikace contoso.azurewebsites.net.

### <a name="create-the-record"></a>Vytvoření záznamu

```azurepowershell
New-AzDnsRecordSet -ZoneName contoso.com -ResourceGroupName "MyAzureResourceGroup" `
 -Name "www" -RecordType "CNAME" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -cname "contoso.azurewebsites.net")
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

```azurepowershell
set-AzWebApp `
 -Name contoso `
 -ResourceGroupName MyAzureResourceGroup `
 -HostNames @("contoso.com","www.contoso.com","contoso.azurewebsites.net")
```
## <a name="test-the-custom-host-names"></a>Testování vlastních názvů hostitele

Otevřete prohlížeč a přejděte na `http://www.<your domainname>` a `http://<you domain name>` .

> [!NOTE]
> Ujistěte se, že jste `http://` předponu zahrnuli, jinak se může váš prohlížeč pokusit předpovědět adresu URL.

Pro obě adresy URL by se vám měla zobrazit stejná stránka. Příklad:

![Služba aplikace Contoso](media/dns-web-sites-custom-domain/contoso-app-svc.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už prostředky vytvořené v tomto kurzu nepotřebujete, můžete odstranit skupinu prostředků **myresourcegroup**.

## <a name="next-steps"></a>Další kroky

Naučte se vytvářet privátní zóny Azure DNS.

> [!div class="nextstepaction"]
> [Začínáme s privátními zónami DNS Azure pomocí PowerShellu](private-dns-getstarted-powershell.md)
