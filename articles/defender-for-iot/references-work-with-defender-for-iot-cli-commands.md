---
title: Práce s příkazy CLI Defenderu for IoT
description: Tento článek popisuje Defender pro příkazy IoT CLI pro senzory a místní konzoly pro správu.
ms.date: 12/12/2020
ms.topic: article
ms.openlocfilehash: 9cd3f4325db2bc45ddcd6cc011dd4993e385a43c
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778674"
---
# <a name="work-with-defender-for-iot-cli-commands"></a>Práce s příkazy CLI Defenderu for IoT

Tento článek popisuje příkazy rozhraní příkazového řádku pro senzory a místní konzoly pro správu. Příkazy jsou k dispozici pro následující uživatele:

- Správce
- CyberX 
- Podpora

Pokud chcete začít pracovat v rozhraní příkazového řádku, připojte se pomocí terminálu. Například název terminálu `Putty` a `Support` uživatel. 

## <a name="create-local-alert-exclusion-rules"></a>Vytvořit pravidla vyloučení místních výstrah

Zadáním následujícího příkazu do rozhraní příkazového řádku můžete vytvořit pravidlo vyloučení místních výstrah:

```azurecli-interactive
alerts exclusion-rule-create [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

S pravidly vyloučení výstrah lze použít následující atributy:

| Atribut | Popis |
|--|--|
| [-h] | Vytiskne informace o nápovědě k příkazu. |
| -n název | Název vytvářeného pravidla. |
| [– časy TS] | Časový rozsah, pro který je pravidlo aktivní. Tento parametr by měl být zadaný jako:<br />`xx:yy-xx:yy`<br />Můžete definovat více než jedno časové období pomocí čárky mezi nimi. Příklad: `xx:yy-xx:yy, xx:yy-xx:yy`. |
| [-Dir – směr] | Směr použití pravidla Tento parametr by měl být zadaný jako:<br />`both | src | dst` |
| [-vývojová zařízení] | Adresa IP a typ adresy zařízení, které mají být vyloučeny z pravidla, které je zadáno jako:<br />`ip-x.x.x.x`<br />`mac-xx:xx:xx:xx:xx:xx`<br />`subnet: x.x.x.x/x` |
| [-a upozornění] | Název výstrahy, která bude vyloučena z pravidla:<br />`0x00000`<br />`0x000001` |

## <a name="append-local-alert-exclusion-rules"></a>Připojit pravidla vyloučení místních výstrah

Můžete připojit pravidla vyloučení místních výstrah zadáním následujícího příkazu v rozhraní příkazového řádku:

```azurecli-interactive
alerts exclusion-rule-append [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

Zde používané atributy jsou stejné jako atributy vysvětlené v části Vytvoření pravidel vyloučení místních výstrah. Rozdíl v použití je, že zde jsou atributy aplikovány na stávající pravidla.

## <a name="show-local-alert-exclusion-rules"></a>Zobrazit pravidla vyloučení místních výstrah

Zadejte následující příkaz pro zobrazení existujícího seznamu pravidel vyloučení:

```azurecli-interactive
alerts exclusion-rule-list [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

## <a name="delete-local-alert-exclusion-rules"></a>Odstranit pravidla vyloučení místních výstrah

Stávající pravidlo vyloučení výstrahy můžete odstranit zadáním následujícího příkazu:

```azurecli-interactive
alerts exclusion-rule-remove [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

Pomocí pravidel pro vyloučení výstrah lze použít následující atribut:

| Atribut | Popis|
| --------- | ---------------------------------- |
| -n název | Název pravidla, které se má odstranit |

## <a name="sync-time-from-the-ntp-server"></a>Čas synchronizace ze serveru NTP

Můžete povolit nebo zakázat synchronizaci času z určeného serveru NTP.

### <a name="enable-ntp-sync"></a>Povolit synchronizaci NTP

Zadejte následující příkaz, který bude pravidelně získávat čas ze zadaného serveru NTP:

```azurecli-interactive
ntp enable IP
```

Atribut, který můžete definovat v rámci příkazu, je IP adresa serveru NTP.

### <a name="disable-ntp-sync"></a>Zakázat synchronizaci NTP

Zadejte následující příkaz, který zakazuje synchronizaci času se zadaným serverem NTP:

```azurecli-interactive
ntp disable IP
```

Atribut, který můžete definovat v rámci příkazu, je IP adresa serveru NTP.

## <a name="network-configuration"></a>Konfigurace sítě

Následující tabulka popisuje příkazy, které jsou k dispozici pro konfiguraci možností sítě pro Azure Defender pro IoT:

|Name|Příkaz|Popis|
|-----------|-------|-----------|
|Ping|`ping IP`| Otestujete adresu a adresu mimo Defender pro platformu IoT.|
|Blink|`network blink`| Vyhledejte připojení tím, že se indikátory rozhraní zablikají. |
|Překonfigurujte síť. |`network edit-settings`| Povolí změnu v parametrech konfigurace sítě. |
|Zobrazit nastavení sítě |`network list`|Zobrazí parametry síťového adaptéru. |
|Ověření konfigurace sítě |`network validate` |Zobrazí výstupní nastavení sítě. <br /> <br />Například: <br /> <br />Aktuální nastavení sítě: <br /> rozhraní: eth0 <br /> IP adresa: 10.100.100.1 <br />podsíť: 255.255.255.0 <br />Výchozí brána: 10.100.100.254 <br />DNS: 10.100.100.254 <br />rozhraní monitorování: eth1|
|Import certifikátu |`certificate import FILE` |Importuje certifikát HTTPS. Je nutné zadat úplnou cestu, která vede k \* souboru. CRT. |
|Zobrazit datum |`date` |Vrátí aktuální datum na hostiteli ve formátu GMT. |

## <a name="filter-network-configurations"></a>Filtrovat síťové konfigurace

`network capture-filter`Příkaz umožňuje správcům eliminovat síťový provoz, který není nutné analyzovat. Provoz můžete filtrovat pomocí seznamu zahrnutí nebo seznamu vyloučení.

```azurecli-interactive
network capture-filter
```

Po zadání příkazu se zobrazí následující otázka:

>`Would you like to supply devices and subnet masks you wish to include in the capture filter? [Y/N]:`

Výběrem `Y` otevřete soubor nano, do kterého můžete přidat zařízení, kanál, port a podmnožinu podle následující syntaxe:

| Atribut | Popis |
|--|--|
| 1.1.1.1 | Zahrnuje veškerý provoz tohoto zařízení. |
| 1.1.1.1, 2.2.2.2 | Zahrnuje veškerý provoz tohoto kanálu. |
| 1.1.1, 2.2.2 | Zahrnuje veškerý provoz pro tuto podsíť. |

Oddělte argumenty vyřazením řádku.

Když zahrnete zařízení, kanál nebo podsíť, zpracuje senzor veškerý platný provoz tohoto argumentu, včetně portů a přenosů, které se obvykle nezpracovávají.

Pak se zobrazí výzva k zadání následující otázky:

>`Would you like to supply devices and subnet masks you wish to exclude from the capture filter? [Y/N]:`

Výběrem `Y` otevřete soubor nano, kde můžete přidat zařízení, kanál, port a podmnožiny podle následující syntaxe:

| Atribut | Popis |
|--|--|
| 1.1.1.1 | Vyloučí veškerý provoz tohoto zařízení. |
| 1.1.1.1, 2.2.2.2 | Vyloučí veškerý provoz tohoto kanálu, což znamená veškerý provoz mezi dvěma zařízeními. |
| 1.1.1.1, 2.2.2.2, 443 | Vyloučí veškerý provoz tohoto kanálu podle portu. |
| 1.1.1 | Vyloučí veškerý provoz v této podsíti. |
| 1.1.1, 2.2.2 | Vyloučí veškerý provoz mezi podsítěmi. |

Oddělte argumenty vyřazením řádku.

Když vyloučíte zařízení, kanál nebo podsíť, vyloučí se senzor všech platných přenosů pro tento argument.

### <a name="ports"></a>Porty

Zahrňte nebo vylučte porty UDP a TCP pro veškerý provoz.

>`502`: jeden port

>`502,443`: oba porty

>`Enter tcp ports to include (delimited by comma or Enter to skip):`

>`Enter udp ports to include (delimited by comma or Enter to skip):`

>`Enter tcp ports to exclude (delimited by comma or Enter to skip):`

>`Enter udp ports to exclude (delimited by comma or Enter to skip):`

### <a name="components"></a>Komponenty

Budete požádáni o následující otázku:

>`In which component do you wish to apply this capture filter?`

Máte tyto možnosti:  `all` , `dissector` , `collector` , `statistics-collector` , `rpc-parser` , nebo `smb-parser` .

Ve většině případů použití vyberte `all` .

### <a name="custom-base-capture-filter"></a>Vlastní základní filtr zachycení

Základní filtr zachytávání je základem pro součásti. Filtr například určuje, které porty jsou k dispozici pro komponentu.

Vyberte `Y` pro všechny následující možnosti. Po nastavení změn jsou všechny filtry přidány do směrného plánu. Pokud provedete změnu, přepíše se stávající standardní hodnoty.

>`Would you like to supply a custom base capture filter for the dissector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the statistics-collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the rpc-parser component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the smb-parser component? [Y/N]:`

>`Type Y for "internal" otherwise N for "all-connected" (custom operation mode enabled) [Y/N]:`

Pokud se rozhodnete vyloučit podsíť, například 1.1.1:

- `internal` vyřadí pouze tuto podsíť.

- `all-connected` vyloučí tuto podsíť a veškerý provoz do a z této podsítě.

Doporučujeme, abyste vybrali `internal` .

> [!NOTE]
> Vaše volby se použijí pro všechny filtry v nástroji a nejsou závislé na relaci. Jinými slovy, nemůžete někdy zvolit `internal`   pro některé filtry a  `all-connected`   pro ostatní.

### <a name="comments"></a>Komentáře

Filtry můžete zobrazit v nástroji  ```/var/cyberx/properties/cybershark.properties``` :

- **Statistika – kolektor**:  `bpf_filter property` v ```/var/cyberx/properties/net.stats.collector.properties```

- **inodvětvová**: `override.capture_filter`   vlastnost v ```/var/cyberx/properties/cybershark.properties```

- **RPC-parser**: `override.capture_filter` vlastnost v ```/var/cyberx/properties/rpc-parser.properties```

- **SMB-parser**:  `override.capture_filter`   vlastnost v ```/var/cyberx/properties/smb-parser.properties```

- **kolektor**: `general.bpf_filter`   vlastnost v ```/var/cyberx/properties/collector.properties```

Výchozí konfiguraci můžete obnovit zadáním následujícího kódu pro uživatele CyberX:

```azurecli-interactive
sudo cyberx-xsense-capture-filter -p all -m all-connected
```

## <a name="define-client-and-server-hosts"></a>Definování hostitelů klienta a serveru

Pokud program Defender pro IoT nedetekuje klienta automaticky a hostitele serveru, zadejte následující příkaz pro nastavení hostitelů klienta a serveru:

```azurecli-interactive
directions [-h] [--identifier IDENTIFIER] [--port PORT] [--remove] [--add]  
[--tcp] [--udp]
```

Pomocí příkazu můžete použít následující atributy `directions` :

| Atribut | Popis |
|--|--|
| [-h] | Vytiskne informace o nápovědě k příkazu. |
| [identifikátor identifikátoru] | Identifikátor serveru. |
| [--port portu] | Port serveru. |
| [--remove] | Odebere klienta nebo hostitele serveru ze seznamu. |
| [--Add] | Přidá do seznamu hostitele klienta nebo serveru. |
| [--TCP] | Při komunikaci s tímto hostitelem použijte protokol TCP. |
| [--UDP] | Při komunikaci s tímto hostitelem použijte protokol UDP. |

## <a name="system-actions"></a>Systémové akce
Následující tabulka popisuje příkazy, které jsou k dispozici pro provádění různých systémových akcí v programu Defender pro IoT:

|Name|Kód|Popis|
|----|----|-----------|
|Zobrazit datum|`date`|Vrátí aktuální datum na hostiteli ve formátu GMT.|
|Restartujte hostitele.|`system reboot`|Restartuje hostitelské zařízení.|
|Vypnout hostitele|`system shutdown`|Ukončí hostitele.|
|Zálohování systému|`system backup`|Iniciuje okamžitou zálohu (Neplánovaná záloha).|
|Obnovení systému ze zálohy|`system restore`|Obnoví z poslední zálohy.|
|Výpis záložních souborů|`system backup-list`|Zobrazí seznam dostupných záložních souborů.|
|Zobrazit stav všech Defenderů pro služby IoT Platform|`system sanity`|Vyhledá výkon systému zobrazením aktuálního stavu všech Defenderů pro služby platformy IoT.|
|Zobrazit verzi softwaru|`system version`|Zobrazuje verzi softwaru aktuálně spuštěného v systému.|

## <a name="deploy-ssl-and-tls-certificates-to-appliances"></a>Nasazení certifikátů SSL a TLS na zařízeních

Zadejte následující příkaz pro import certifikátů SSL a TLS Enterprise do rozhraní příkazového řádku:

```azurecli-interactive
cyberx-xsense-certificate-import
```
Chcete-li použít nástroj, je třeba odeslat soubory certifikátů do zařízení. Můžete to provést prostřednictvím nástrojů, jako je WinSCP nebo wget. 

Příkaz podporuje následující vstupní příznaky:

| Příznak | Popis |
|--|--|
| -h | Zobrazuje syntaxi Help příkazového řádku. |
| --CRT | Cesta k souboru certifikátu (přípona. CRT). |
| --klíč | \*Soubor. Key. Délka klíče by měla být minimálně 2 048 bitů. |
| --řetěz | Cesta k souboru řetězu certifikátů (volitelné). |
| --Pass | Heslo použité k šifrování certifikátu (volitelné). |
| --heslo-sada | Výchozí hodnota je **false**, **nepoužívá** se. <br />Nastavte na **hodnotu true** , pokud chcete použít předchozí přístupové heslo, které jste zadali v předchozím certifikátu (volitelné). | 

Při použití nástroje:

- Ověřte, jestli jsou na zařízení čitelné soubory certifikátů. 

- Ověřte s tím, jak se doména zařízení (jak se zobrazuje v certifikátu) s vaším serverem DNS a odpovídající IP adresou. 
    
## <a name="see-also"></a>Viz také

[Defender pro rozhraní API pro senzory rozhraní IoT a konzolu pro správu](references-work-with-defender-for-iot-apis.md)
