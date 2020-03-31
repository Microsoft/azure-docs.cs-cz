---
title: Import a export souboru zóny domény – Azure CLI
titleSuffix: Azure DNS
description: Zjistěte, jak importovat a exportovat soubor zóny DNS do Azure DNS pomocí azure cli
services: dns
author: rohinkoul
ms.service: dns
ms.date: 4/3/2019
ms.author: rohink
ms.topic: conceptual
ms.openlocfilehash: a5c2fdde564eba2d95e7f14f4d47e4d381739d5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365164"
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>Import a export souboru zóny DNS pomocí Azure CLI

Tento článek vás provede, jak importovat a exportovat soubory zóny DNS pro Azure DNS pomocí Azure CLI.

## <a name="introduction-to-dns-zone-migration"></a>Úvod k migraci zóny DNS

Soubor zóny DNS je textový soubor, který obsahuje podrobnosti o každém záznamu DNS (Domain Name System) v zóně. Je ve standardním formátu, takže je vhodný pro přenos DNS záznamů mezi DNS systémy. Použití souboru zóny je rychlý, spolehlivý a pohodlný způsob přenosu zóny DNS do nebo z Azure DNS.

Azure DNS podporuje import a export zónových souborů pomocí rozhraní příkazového řádku Azure (CLI). Import souborů zóny **není** momentálně podporovaný přes Azure PowerShell nebo portál Azure.

Azure CLI je nástroj příkazového řádku pro různé platformy, který se používá ke správě služeb Azure. Je k dispozici pro platformy Windows, Mac a Linux na [stránce Azure ke stažení](https://azure.microsoft.com/downloads/). Podpora mezi platformami je důležitá pro import a export souborů zón, protože nejběžnější software názvového serveru [BIND](https://www.isc.org/downloads/bind/), obvykle běží na Linuxu.

## <a name="obtain-your-existing-dns-zone-file"></a>Získání existujícího souboru zóny DNS

Před importem souboru zóny DNS do služby Azure DNS je třeba získat kopii souboru zóny. Zdroj tohoto souboru závisí na tom, kde je aktuálně hostována zóna DNS.

* Pokud je vaše zóna DNS hostována partnerskou službou (například registrátorem domény, vyhrazeným poskytovatelem hostingu DNS nebo alternativním poskytovatelem cloudu), měla by tato služba poskytovat možnost stáhnout soubor zóny DNS.
* Pokud je vaše zóna DNS hostována ve službě Windows DNS, je výchozí složkou souborů zóny **%systemroot%\system32\dns**. Úplná cesta ke každému souboru zóny se také zobrazuje na kartě **Obecné** konzoly DNS.
* Pokud je vaše zóna DNS hostována pomocí funkce BIND, je v konfiguračním souboru BIND **s názvem.conf**určeno umístění souboru zóny pro každou zónu.

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Import souboru zóny DNS do Azure DNS

Import souboru zóny vytvoří novou zónu v Azure DNS, pokud ještě neexistuje. Pokud zóna již existuje, musí být sady záznamů v souboru zóny sloučeny s existujícími sadami záznamů.

### <a name="merge-behavior"></a>Chování sloučení

* Ve výchozím nastavení jsou sloučeny existující a nové sady záznamů. Identické záznamy v rámci sloučené sady záznamů jsou duplikovány.
* Při sloučení sad záznamů se používá doba, která má být aktuální (TTL) již existujících sad záznamů.
* Parametry (s výjimkou) úřadu (kromě) `host`jsou vždy převzaty z importovaného souboru zóny. Podobně pro záznam názvového serveru nastavený na vrcholu zóny je TTL vždy převzat z importovaného souboru zóny.
* Importovaný záznam CNAME nenahradí existující záznam CNAME se stejným názvem.  
* Pokud dojde ke konfliktu mezi záznamem CNAME a jiným záznamem se stejným názvem, ale jiného typu (bez ohledu na to, který je existující nebo nový), existující záznam zůstane zachován. 

### <a name="additional-information-about-importing"></a>Další informace o importu

Následující poznámky poskytují další technické podrobnosti o procesu importu zóny.

* Směrnice `$TTL` je nepovinná a je podporována. Pokud `$TTL` není uvedena žádná směrnice, záznamy bez explicitní TTL jsou importovány nastavit výchozí TTL 3600 sekund. Pokud dva záznamy ve stejné sadě záznamů určují různé hodnoty TTL, použije se nižší hodnota.
* Směrnice `$ORIGIN` je nepovinná a je podporována. Pokud `$ORIGIN` není nastavena žádná, výchozí hodnotou je název zóny, jak je zadán na příkazovém řádku (plus ukončující ".").
* A `$INCLUDE` `$GENERATE` směrnice nejsou podporovány.
* Tyto typy záznamů jsou podporovány: A, AAAA, CAA, CNAME, MX, NS, SOA, SRV a TXT.
* Záznam SOA se vytvoří automaticky službou Azure DNS při vytvoření zóny. Při importu souboru zóny jsou ze souboru zóny převzaty všechny parametry SOA *s výjimkou* parametru. `host` Tento parametr používá hodnotu poskytovanou službou Azure DNS. Důvodem je, že tento parametr musí odkazovat na primární název serveru poskytované Azure DNS.
* Záznam názvového serveru nastavený na vrcholu zóny je také vytvořen automaticky službou Azure DNS při vytvoření zóny. Importuje se pouze ttl této sady záznamů. Tyto záznamy obsahují názvy názvových serverů poskytované službou Azure DNS. Data záznamu nejsou přepsána hodnotami obsaženými v importovaném souboru zóny.
* Během public preview azure DNS podporuje pouze jednořetězcové TXT záznamy. Víceřetězcové txt záznamy jsou zřetězené a zkráceny na 255 znaků.

### <a name="cli-format-and-values"></a>Formát a hodnoty příkazového příkazového příkazu

Formát příkazu Azure CLI pro import zóny DNS je:

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Hodnoty:

* `<resource group>`je název skupiny prostředků pro zónu v Azure DNS.
* `<zone name>`je název zóny.
* `<zone file name>`je cesta/název souboru zóny, který má být importován.

Pokud zóna s tímto názvem ve skupině prostředků neexistuje, je vytvořena za vás. Pokud zóna již existuje, importované sady záznamů se sloučí s existujícími sadami záznamů. 

### <a name="step-1-import-a-zone-file"></a>Krok 1. Import souboru zóny

Import souboru zóny pro zónu **contoso.com**.

1. Pokud ještě nemáte, je třeba vytvořit skupinu prostředků Správce prostředků.

    ```azurecli
    az group create --group myresourcegroup -l westeurope
    ```

2. Chcete-li importovat **zónu contoso.com** ze souboru **contoso.com.txt** do nové zóny DNS `az network dns zone import`ve skupině prostředků **myresourcegroup**, spustíte příkaz .<BR>Tento příkaz načte soubor zóny a analyzuje jej. Příkaz provede řadu příkazů ve službě Azure DNS k vytvoření zóny a všech sad záznamů v zóně. Příkaz hlásí průběh v okně konzoly spolu s případnými chybami nebo upozorněními. Vzhledem k tomu, že sady záznamů jsou vytvářeny v řadě, může trvat několik minut importovat soubor velké zóny.

    ```azurecli
    az network dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>Krok 2. Ověření zóny

Chcete-li ověřit zónu DNS po importu souboru, můžete použít některou z následujících metod:

* Záznamy můžete uvést pomocí následujícího příkazu Azure CLI:

    ```azurecli
    az network dns record-set list -g myresourcegroup -z contoso.com
    ```

* Záznamy můžete uvést pomocí příkazu `az network dns record-set ns list`Azure CLI .
* Můžete použít `nslookup` k ověření překladu názvů pro záznamy. Vzhledem k tomu, že zóna ještě není delegována, je třeba explicitně zadat správné názvové servery Azure DNS. Následující ukázka ukazuje, jak načíst názvy názvového serveru přiřazené zóně. To také ukazuje, jak dotaz "www" záznam pomocí `nslookup`.

    ```azurecli
    az network dns record-set ns list -g myresourcegroup -z contoso.com  --output json 
    ```

    ```json
    [
      {
       .......
       "name": "@",
        "nsRecords": [
          {
            "additionalProperties": {},
            "nsdname": "ns1-03.azure-dns.com."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns2-03.azure-dns.net."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns3-03.azure-dns.org."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns4-03.azure-dns.info."
          }
        ],
        "resourceGroup": "myresourcegroup",
        "ttl": 86400,
        "type": "Microsoft.Network/dnszones/NS"
      }
    ]
    ```

    ```cmd
    nslookup www.contoso.com ns1-03.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221
    ```

### <a name="step-3-update-dns-delegation"></a>Krok 3. Aktualizovat delegování DNS

Po ověření, že zóna byla importována správně, je třeba aktualizovat delegování DNS přejděte na názvové servery Azure DNS. Další informace naleznete v článku [Aktualizace delegace DNS](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Export souboru zóny DNS z Azure DNS

Formát příkazu Azure CLI pro export zóny DNS je:

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

Hodnoty:

* `<resource group>`je název skupiny prostředků pro zónu v Azure DNS.
* `<zone name>`je název zóny.
* `<zone file name>`je cesta/název souboru zóny, který má být exportován.

Stejně jako u importu zóny se musíte nejprve přihlásit, zvolit předplatné a nakonfigurovat rozhraní příkazového příkazového nastavení Azure tak, aby používalo režim Správce prostředků.

### <a name="to-export-a-zone-file"></a>Export souboru zóny

Chcete-li exportovat existující **zónu** Azure DNS contoso.com ve skupině prostředků **myresourcegroup** do souboru `azure network dns zone export` **contoso.com.txt** (v aktuální složce), spusťte . Tento příkaz volá službu Azure DNS k vytvoření výčtu sad záznamů v zóně a exportu výsledků do souboru zóny kompatibilního s BIND.

```azurecli
az network dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
```

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [spravovat sady záznamů a záznamy](dns-getstarted-create-recordset-cli.md) v zóně DNS.

* Přečtěte si, jak [delegovat doménu na Azure DNS](dns-domain-delegation.md).
