---
title: Importovat a exportovat soubor zóny domény do DNS Azure pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Zjistěte, jak importovat a exportovat soubor zóny DNS do Azure DNS pomocí rozhraní příkazového řádku Azure
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.assetid: f5797782-3005-4663-a488-ac0089809010
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: victorh
ms.openlocfilehash: 5afb607f0410b428d8e67fdff043a4e376dd60a5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956349"
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>Import a export souboru zóny DNS pomocí Azure CLI 

Tento článek vás provede pro import a export zón DNS pro Azure DNS pomocí Azure CLI.

## <a name="introduction-to-dns-zone-migration"></a>Úvod do migrace zóny DNS

Soubor zóny DNS je textový soubor, který obsahuje podrobné informace o všechny záznamy systému DNS (Domain Name) v zóně. Postupuje standardního formátu, takže vhodný pro přenos mezi systémy DNS záznamy DNS. Použití souboru zóny je rychlé, spolehlivé a pohodlný způsob pro přenos zóny DNS do nebo z Azure DNS.

Azure DNS podporuje import a export zón s použitím rozhraní příkazového řádku Azure (CLI). Import souboru zóny je **není** prostřednictvím Azure Powershellu nebo na webu Azure portal aktuálně podporován.

Azure CLI je nástroj příkazového řádku napříč platformami pro správu služeb Azure. Je dostupná pro platformy Windows, Mac a Linux z [stránky pro stažení Azure](https://azure.microsoft.com/downloads/). Podporu pro různé platformy je důležité pro import a export zón, protože serverový software nejběžnější název [SVÁZAT](https://www.isc.org/downloads/bind/), obvykle běží na systému Linux.


## <a name="obtain-your-existing-dns-zone-file"></a>Získat existující soubor zóny DNS

Před importem souboru zóny DNS v Azure DNS, budete muset získat kopii souboru zóny. Zdroj tohoto souboru závisí na aktuálně hostuje zóny DNS.

* Pokud zónu DNS je hostitelem partnerské služby (například registrátora domény, vyhrazené poskytovatele hostingu DNS nebo alternativní cloud poskytovatele), tato služba by měla poskytnout možnost stažení souboru zóny DNS.
* Pokud zónu DNS je hostovaný ve službě Windows DNS, je výchozí složku pro soubory zóny **%systemroot%\system32\dns**. Úplná cesta k souboru každé zóny také ukazuje na **Obecné** kartu konzolu DNS.
* Pokud zónu DNS je hostitelem pomocí vazby, je umístění souboru zóny pro každou zónu zadané v konfiguračním souboru vazby **named.conf**.

> [!NOTE]
> Zóna soubory stahované z GoDaddy mají mírně nestandardní formát. Je potřeba to napravit, před importem těchto zón do Azure DNS.
>
> Názvy DNS v RDATA všechny záznamy DNS jsou určené jako plně kvalifikované názvy, ale nemají ukončující "." To znamená, že jsou interpretovány jiných systémů DNS jako relativní názvy. Je potřeba upravit soubor zóny pro ukončení připojení "." názvy před importem do Azure DNS.
>
> Například by měla být změněna záznam CNAME "v doméně contoso.com CNAME www 3600" k "www 3600 v doméně contoso.com CNAME."
> (s ukončující ".").

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Import souboru zóny DNS do Azure DNS

Nové zóny v Azure DNS importem souboru zóny vytvoří, pokud ještě neexistuje. Pokud zóna již existuje, je potřeba sloučit sady záznamů v souboru zóny s existující sady záznamů.

### <a name="merge-behavior"></a>Sloučit chování

* Ve výchozím nastavení jsou sloučeny stávající i nové sady záznamů. Jsou identické záznamy v rámci sady záznamů sloučené zrušení duplicitních.
* Když jsou sloučeny sady záznamů, použije se time to live (TTL) dříve existující sady záznamů.
* Začátek záznam Authority (SOA) parametry (s výjimkou `host`) vždy pocházejí ze souboru importované zóny. Podobně pro záznam názvového serveru nastavit ve vrcholu zóny, hodnota TTL je vždy odebrat soubor importovaný zóny.
* Importovaný záznam CNAME nenahrazuje existující záznam CNAME se stejným názvem.  
* Když dojde ke konfliktu mezi záznam CNAME, který a jiný záznam stejným názvem, ale jiným typem (bez ohledu na to, který je existující nebo nové), se uchovávají existující záznam. 

### <a name="additional-information-about-importing"></a>Další informace o importu

Následující poznámky k poskytují další technické podrobnosti o zóně importu.

* `$TTL` – Direktiva je volitelný a je podporované. Pokud ne `$TTL` – direktiva je zadaný, záznamy bez explicitní hodnota TTL jsou importované nastavenou na výchozí hodnota TTL 3 600 sekund. Dva záznamy v sadě záznamů stejnou specifikování různých hodnot TTL, je použít nižší hodnotu.
* `$ORIGIN` – Direktiva je volitelný a je podporované. Pokud ne `$ORIGIN` je nastavena výchozí hodnota používaná je název zóny, jak je uvedeno v příkazovém řádku (plus ukončení ".").
* `$INCLUDE` a `$GENERATE` direktivy nejsou podporovány.
* Podporovány jsou tyto typy záznamů: A, AAAA, CNAME, MX, NS, SOA, SRV a TXT.
* Záznam SOA se automaticky vytvoří Azure DNS, při vytvoření zóny. Při importu souboru zóny všechny parametry SOA pocházejí ze souboru zóny *s výjimkou* `host` parametru. Tento parametr používá hodnotu poskytovanou infrastrukturou Azure DNS. Je to proto, že tento parametr musí odkazovat na primární název serveru poskytuje Azure DNS.
* Záznam názvového serveru, nastavte ve vrcholu zóny se také automaticky vytvoří Azure DNS při vytváření zóny. Je importován pouze TTL tuto sadu záznamů. Tyto záznamy obsahují názvy názvových serverů poskytuje Azure DNS. Záznam dat není přepsán podle hodnoty obsažené v souboru importované zóny.
* Ve verzi Public Preview Azure DNS podporuje pouze jeden řetězec záznamů TXT. Nahrazován záznamy TXT jsou zřetězeny a zkrácen na 255 znaků.

### <a name="cli-format-and-values"></a>Formát rozhraní příkazového řádku a hodnoty

Formát příkazu rozhraní příkazového řádku Azure pro import zóny DNS je:

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Hodnoty:

* `<resource group>` je název skupiny prostředků pro zónu v Azure DNS.
* `<zone name>` je název zóny.
* `<zone file name>` je cesta a název souboru zóny k importu.

Pokud zóna s tímto názvem neexistuje ve skupině prostředků, vytvoří se pro vás. Pokud už existuje zóna, importované sady záznamů jsou sloučeny s existující sady záznamů. 


### <a name="step-1-import-a-zone-file"></a>Krok 1. Importovat soubor zóny

Chcete-li importovat soubor zóny pro zónu **contoso.com**.

1. Pokud je nemáte, musíte vytvořit skupinu prostředků Resource Manageru.

    ```azurecli
    az group create --group myresourcegroup -l westeurope
    ```

2. Chcete-li importovat zóny **contoso.com** ze souboru **contoso.com.txt** do nové zóny DNS ve skupině prostředků **myresourcegroup**, spustíte příkaz `az network dns zone import` .<BR>Tento příkaz načte soubor zóny a analyzuje to. Tento příkaz spustí řadu příkazů ve službě Azure DNS k vytvoření zóny a nastaví všechny záznamy v zóně. Příkaz hlásí průběh v okně konzoly spolu se žádné chyby nebo upozornění. Protože sady záznamů jsou vytvářeny v řadě, může trvat několik minut, než import souboru velké zóny.

    ```azurecli
    az network dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>Krok 2. Ověřte, zóna

Po importu souboru, ověření zóny DNS, můžete použít některou z následujících metod:

* Pomocí následujícího příkazu rozhraní příkazového řádku Azure můžete vytvořit seznam záznamů:

    ```azurecli
    az network dns record-set list -g myresourcegroup -z contoso.com
    ```

* Pomocí rutiny prostředí PowerShell můžete vytvořit seznam záznamů `Get-AzureRmDnsRecordSet`.
* Můžete použít `nslookup` k ověření překladu názvů u záznamů. Protože ještě není přidělena zóny, musíte explicitně zadat správné názvové servery Azure DNS. Následující příklad ukazuje, jak načíst názvy názvových serverů, které jsou přiřazeny k zóně. To také ukazuje, jak provádět dotazy pomocí záznamu "www" `nslookup`.

    ```azurecli
    az network dns record-set ns list -g myresourcegroup -z  --output json 
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

Až si ověříte, správně importovány zóny, budete muset aktualizovat delegování DNS tak, aby odkazoval na názvové servery Azure DNS. Další informace najdete v článku [aktualizovat delegování DNS](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Exportovat soubor zóny DNS v Azure DNS

Formát příkazu rozhraní příkazového řádku Azure pro import zóny DNS je:

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

Hodnoty:

* `<resource group>` je název skupiny prostředků pro zónu v Azure DNS.
* `<zone name>` je název zóny.
* `<zone file name>` je cesta a název export souboru zóny.

Jako importu zóny je nejprve nutné pro přihlášení, vaše předplatné a konfigurace rozhraní příkazového řádku Azure pro použití režimu Resource Manageru.

### <a name="to-export-a-zone-file"></a>Chcete-li exportovat soubor zóny

Chcete-li exportovat existující zónu Azure DNS **contoso.com** ve skupině prostředků **myresourcegroup** do souboru **contoso.com.txt** (v aktuální složce), spusťte `azure network dns zone export`. Tento příkaz volá služba Azure DNS vytvořit výčet sady záznamů v zóně a exportovat výsledky do souboru zóny vazby kompatibilní.

    ```
    az network dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```
