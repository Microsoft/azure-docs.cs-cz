---
title: Import a export souboru zóny domény pro privátní DNS Azure – Azure CLI
titleSuffix: Azure DNS
description: Naučte se importovat a exportovat soubor zóny DNS do privátního DNS Azure pomocí Azure CLI.
services: dns
author: duongau
ms.service: dns
ms.date: 03/16/2021
ms.author: duau
ms.topic: how-to
ms.openlocfilehash: 2e5babb85dbf4aa7da707e22be5eeaf3ae89972d
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450956"
---
# <a name="import-and-export-a-private-dns-zone-file-for-azure-private-dns"></a>Import a export privátního souboru zóny DNS pro privátní DNS Azure

Tento článek vás provede postupem importu a exportu souborů zóny DNS pro Azure DNS pomocí rozhraní příkazového řádku Azure CLI.

## <a name="introduction-to-dns-zone-migration"></a>Úvod do migrace zóny DNS

Soubor zóny DNS je textový soubor, který obsahuje podrobnosti o všech záznamech DNS (Domain Name System) v zóně. Sleduje se podle standardního formátu a je vhodný pro přenos záznamů DNS mezi systémy DNS. Použití souboru zóny je rychlý, spolehlivý a pohodlný způsob přenosu zóny DNS do nebo z Azure DNS.

Privátní DNS Azure podporuje import a export souborů zón pomocí rozhraní příkazového řádku (CLI) Azure. Import souboru zóny se **v současné době nepodporuje prostřednictvím** Azure PowerShell nebo Azure Portal.

Azure CLI je nástroj příkazového řádku pro různé platformy, který slouží ke správě služeb Azure. Je k dispozici pro platformy Windows, Mac a Linux ze [stránky Azure downloads](https://azure.microsoft.com/downloads/). Podpora více platforem je důležitá pro import a export souborů zóny, protože nejběžnější software názvového serveru, [BIND](https://www.isc.org/downloads/bind/), se obvykle spouští na Linux.

## <a name="obtain-your-existing-dns-zone-file"></a>Získat existující soubor zóny DNS

Před importem souboru zóny DNS do Azure DNS musíte získat kopii souboru zóny. Zdroj tohoto souboru závisí na tom, kde je zóna DNS aktuálně hostovaná.

* Pokud je vaše zóna DNS hostovaná službou partnera (například doménovým registrátorem, vyhrazeným poskytovatelem hostingu DNS nebo alternativním poskytovatelem cloudu), měla by tato služba poskytovat možnost stáhnout soubor zóny DNS.
* Pokud je vaše zóna DNS hostovaná na DNS systému Windows, bude výchozí složkou pro soubory zóny **%SystemRoot%\System32\Dns**. Úplná cesta ke každému souboru zóny se zobrazí také na kartě **Obecné** konzoly DNS.
* Pokud je vaše zóna DNS hostovaná pomocí služby BIND, umístění souboru zóny pro každou zónu je zadané v konfiguračním souboru vazby **s názvem. conf**.

## <a name="import-a-dns-zone-file-into-azure-private-dns"></a>Import souboru zóny DNS do privátního DNS Azure

Import souboru zóny vytvoří v privátním DNS Azure novou zónu, pokud ještě neexistuje. Pokud zóna již existuje, musí být sady záznamů v souboru zóny sloučeny s existujícími sadami záznamů.

### <a name="merge-behavior"></a>Chování sloučení

* Ve výchozím nastavení se sloučí existující a nové sady záznamů. Identické záznamy v sadě sloučených záznamů jsou duplicitní.
* Při sloučení sad záznamů se použije hodnota TTL (Time to Live) pro stávající sady záznamů.
* Parametry Start of Authority (SOA) (s výjimkou `host` ) jsou vždy odebírány ze souboru importované zóny. Podobně platí, že pro záznam názvového serveru nastaveného na vrcholu zóny se hodnota TTL vždy převezme ze souboru importované zóny.
* Importovaný záznam CNAME nenahrazuje stávající záznam CNAME se stejným názvem.  
* Pokud dojde ke konfliktu mezi záznamem CNAME a jiným záznamem se stejným názvem, ale jiným typem (bez ohledu na to, který je už existující nebo nový), zachová se stávající záznam. 

### <a name="additional-information-about-importing"></a>Další informace o importu

Následující poznámky poskytují další technické podrobnosti o procesu importu zóny.

* `$TTL`Direktiva je volitelná a je podporovaná. Pokud `$TTL` není zadána žádná direktiva, jsou záznamy bez explicitní hodnoty TTL importovány nastavené na výchozí hodnotu ttl 3600 sekund. Pokud dva záznamy ve stejné sadě záznamů určují různé TTLs, použije se spodní hodnota.
* `$ORIGIN`Direktiva je volitelná a je podporovaná. Pokud `$ORIGIN` není nastavená, použije se výchozí hodnota název zóny, jak je uvedeno na příkazovém řádku (plus ukončovací znak ".").
* `$INCLUDE` `$GENERATE` Direktivy a nejsou podporovány.
* Podporují se tyto typy záznamů: A, AAAA, CAA, CNAME, MX, NS, SOA, SRV a TXT.
* Záznam SOA se vytvoří automaticky Azure DNS při vytvoření zóny. Když importujete soubor zóny, všechny parametry SOA jsou pořízeny ze souboru zóny *s výjimkou* `host` parametru. Tento parametr používá hodnotu poskytnutou Azure DNS. Důvodem je, že tento parametr musí odkazovat na primární názvový server, který poskytuje Azure DNS.
* Záznam názvového serveru nastavený na vrcholu zóny je také automaticky vytvořen Azure DNS při vytvoření zóny. Naimportovala se jenom hodnota TTL této sady záznamů. Tyto záznamy obsahují název názvového serveru, který poskytuje Azure DNS. Data záznamu nejsou přepsána hodnotami obsaženými v importovaném souboru zóny.
* Během Public Preview Azure DNS podporuje pouze záznamy TXT s jedním řetězcem. Záznamy s více řetězci se budou zřetězit a zkrátit na 255 znaků.

### <a name="cli-format-and-values"></a>Hodnoty a formát CLI

Formát příkazu rozhraní příkazového řádku Azure pro import zóny DNS:

```azurecli
az network private-dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Hodnoty:

* `<resource group>` je název skupiny prostředků pro zónu v Azure DNS.
* `<zone name>` je název zóny.
* `<zone file name>` je cesta nebo název souboru zóny, který se má importovat.

Pokud ve skupině prostředků neexistuje zóna s tímto názvem, vytvoří se pro vás. Pokud již zóna existuje, importované sady záznamů budou sloučeny s existujícími sadami záznamů.

### <a name="import-a-zone-file"></a>Importovat soubor zóny

Import souboru zóny pro zónu **contoso.com**.

1. Pokud ho ještě nemáte, budete muset vytvořit Správce prostředků skupinu prostředků.

    ```azurecli
    az group create --resource-group myresourcegroup -l westeurope
    ```

2. Pokud chcete importovat zónu **contoso.com** ze souboru **contoso.com.txt** do nové zóny DNS ve skupině prostředků **myresourcegroup**, spusťte příkaz `az network private-dns zone import` .<BR>Tento příkaz načte soubor zóny a analyzuje ho. Příkaz spustí sérii příkazů ve službě Azure DNS k vytvoření zóny a všech sad záznamů v zóně. Příkaz hlásí průběh v okně konzoly spolu s případnými chybami a upozorněními. Vzhledem k tomu, že sady záznamů jsou vytvořeny v řadě, může trvat několik minut, než se naimportuje soubor velké zóny.

    ```azurecli
    az network private-dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="verify-the-zone"></a>Ověření zóny

Pokud chcete po importu souboru ověřit zónu DNS, můžete použít některou z následujících metod:

* Záznamy můžete vypsat pomocí následujícího příkazu rozhraní příkazového řádku Azure:

    ```azurecli
    az network private-dns record-set list -g myresourcegroup -z contoso.com
    ```

* Můžete použít `nslookup` k ověření překladu názvů záznamů. Protože zóna ještě není delegovaná, musíte explicitně zadat správné názvové servery Azure DNS. Následující příklad ukazuje, jak načíst názvy názvových serverů přiřazené k zóně. Také se dozvíte, jak zadat dotaz na záznam "www" pomocí `nslookup` .

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Exportovat soubor zóny DNS z Azure DNS

Formátem příkazu rozhraní příkazového řádku Azure pro export zóny DNS je:

```azurecli
az network private-dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

Hodnoty:

* `<resource group>` je název skupiny prostředků pro zónu v Azure DNS.
* `<zone name>` je název zóny.
* `<zone file name>` je cesta nebo název souboru zóny, který má být exportován.

Stejně jako u importu zóny se nejprve musíte přihlásit, zvolit předplatné a nakonfigurovat rozhraní příkazového řádku Azure CLI tak, aby používalo režim Správce prostředků.

### <a name="to-export-a-zone-file"></a>Export souboru zóny

Pokud chcete exportovat existující zónu Azure DNS **contoso.com** ve skupině prostředků **myresourcegroup** do souboru **contoso.com.txt** (v aktuální složce), spusťte příkaz `azure network private-dns zone export` . Tento příkaz volá službu Azure DNS pro zobrazení výčtu sad záznamů v zóně a export výsledků do souboru zóny kompatibilního s BIND.

```azurecli
az network private-dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
```

## <a name="next-steps"></a>Další kroky

* Naučte se [Spravovat sady záznamů a záznamy](./private-dns-getstarted-cli.md) v zóně DNS.
