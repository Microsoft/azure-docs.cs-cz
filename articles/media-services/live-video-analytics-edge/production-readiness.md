---
title: Připravenost na produkční prostředí a osvědčené postupy – Azure
description: Tento článek poskytuje pokyny ke konfiguraci a nasazení živé analýzy videí v modulu IoT Edge v produkčním prostředí.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 56982d84b7ffac718072683076657d56a2691d6c
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400552"
---
# <a name="production-readiness-and-best-practices"></a>Připravenost na produkční prostředí a osvědčené postupy

Tento článek poskytuje pokyny ke konfiguraci a nasazení živé analýzy videí v modulu IoT Edge v produkčním prostředí. Měli byste si také projít téma [Příprava na nasazení IoT Edge řešení v produkčním](../../iot-edge/production-checklist.md) článku o přípravě IoT Edge řešení. 

> [!NOTE]
> V aspektech týkajících se zabezpečení byste se měli obrátit na oddělení IT vaší organizace.

## <a name="running-the-module-as-a-local-user"></a>Spuštění modulu jako místní uživatel

Když nasadíte nástroj Live video Analytics v IoT Edge do hraničního zařízení, standardně se spustí se zvýšenými oprávněními. Když to uděláte, zobrazí se při kontrole protokolů v modulu ( `sudo iotedge logs {name-of-module}` ) následující:

```
!! production readiness: user accounts – Warning
       LOCAL_USER_ID and LOCAL_GROUP_ID environment variables are not set. The program will run as root!
       For optimum security, make sure to set LOCAL_USER_ID and LOCAL_GROUP_ID environment variables to a non-root user and group.
       See https://aka.ms/lva-iot-edge-prod-checklists-user-accounts for more information.
```

Následující části popisují, jak můžete vyřešit výše uvedené upozornění.

### <a name="creating-and-using-a-local-user-account"></a>Vytvoření a použití místního uživatelského účtu

V produkčním prostředí můžete pomocí účtu s co nejmenším možným počtem oprávnění spouštět živé analýzy videí v modulu IoT Edge. Následující příkazy například ukazují, jak můžete vytvořit místní uživatelský účet na virtuálním počítači se systémem Linux:

```
sudo groupadd -g 1010 localuser
sudo adduser --home /home/edgeuser --uid 1010 -gid 1010 edgeuser
```

Dále můžete v manifestu nasazení nastavit proměnné prostředí LOCAL_USER_ID a LOCAL_GROUP_ID na tento nekořenový uživatel a skupinu:

```
"lvaEdge": {
"version": "1.0",
…
"env": {
    "LOCAL_USER_ID": 
    {
        "value": "1010"
    },
    "LOCAL_GROUP_ID": {
        "value": "1010"
    }
}
},
…
```

### <a name="granting-permissions-to-device-storage"></a>Udělení oprávnění pro úložiště zařízení

Live video Analytics v modulu IoT Edge vyžaduje možnost zapisovat soubory do místního systému souborů v těchto případech:

* Pomocí vlastnosti modulu s dvojitou vlastností [`applicationDataDirectory`](module-twin-configuration-schema.md#module-twin-properties) , kde byste měli zadat adresář v místním systému souborů pro ukládání konfiguračních dat.
* Pomocí Media graphu zaznamenáte video do cloudu, modul vyžaduje použití adresáře na hraničním zařízení jako mezipaměti (Další informace najdete v článku [průběžné nahrávání videa](continuous-video-recording-concept.md) ).
* [Záznam do místního souboru](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources), kde byste měli zadat cestu k souboru pro zaznamenané video.

Pokud máte v úmyslu využít některou z výše uvedených součástí, měli byste zajistit, aby výše uvedený uživatelský účet měl přístup k příslušnému adresáři. Zvažte applicationDataDirectory například. Můžete vytvořit adresář na hraničním zařízení a propojit úložiště zařízení s modulem úložiště modulů. 

```
sudo mkdir /var/local/mediaservices
sudo chown -R edgeuser /var/local/mediaservices
```

Dále můžete v části Vytvoření pro modul Edge v manifestu nasazení přidat nastavení vazby mapování adresáře (var/local/MediaServices/) výše do adresáře v modulu (například "/var/lib/azuremediaservices/"). A použijete druhý adresář jako hodnotu pro applicationDataDirectory.

```
"lvaEdge": {
    "version": "1.0",
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
        "image": "mcr.microsoft.com/media/live-video-analytics:1.0",
        "createOptions": "{\"HostConfig\":{\"LogConfig\":{\"Type\":\"\",\"Config\":{\"max-size\":\"10m\",\"max-file\":\"10\"}},\"Binds\":[\"/var/local/mediaservices/:/var/lib/azuremediaservices/\"]}}"
    },
    "env": {
        "LOCAL_USER_ID": 
        {
            "value": "1010"
        },
        "LOCAL_GROUP_ID": {
            "value": "1010"
        }
    }
    },
    …
    
    "lvaEdge": {
    "properties.desired": {
    "applicationDataDirectory": "/var/lib/azuremediaservices",
    …
    }
}
```

Pokud se podíváte na ukázkové mediální grafy pro rychlý Start a kurzy, jako je například [nepřetržité nahrávání videa](continuous-video-recording-tutorial.md), Upozorňujeme, že adresář localMediaCachePath (Media cache) používá podadresář pod applicationDataDirectory. Toto je doporučený přístup, protože mezipaměť obsahuje přechodná data.

### <a name="naming-video-assets-or-files"></a>Pojmenování prostředků videí nebo videosouborů

Grafy médií umožňují vytvářet prostředky v cloudu nebo souborech MP4 na hraničních zařízeních. Prostředky médií je možné vygenerovat [nepřetržitým záznamem videa](continuous-video-recording-tutorial.md) nebo [nahráváním videa založeného na událostech](event-based-video-recording-tutorial.md). I když se tyto prostředky a soubory můžou pojmenovat tak, jak chcete, doporučuje se u nepřetržitého mediálního záznamu založeného na záznamech, která je " &lt; anytext &gt; -$ {System. GraphTopologyName}-$ {System. GraphInstanceName}", doporučená struktura pojmenování.   

Vzor nahrazení je definovaný znakem $ následovaným složenými závorkami: **$ {Variable}**.  

Jako příklad můžete nastavit assetNamePattern na jímku assetu následujícím způsobem:

```
"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}
```

Pro prostředky vygenerované záznamem videa založeného na událostech je doporučený vzor pojmenování " &lt; anytext &gt; -$ {System. DateTime}". Systémová proměnná zajišťuje, že se prostředky nepřepisují, pokud dojde k událostem ve stejnou dobu. Jako příklad můžete nastavit assetNamePattern na jímku Assetu následujícím způsobem:

```
"assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}"
```

Pokud spouštíte více instancí stejného grafu, můžete použít název a název instance topologie grafu pro rozlišení. Jako příklad můžete nastavit assetNamePattern na jímku assetu následujícím způsobem:

```
"assetNamePattern": "sampleAssetFromEVR-${System.GraphTopologyName}-${System.GraphInstanceName}-${System.DateTime}"
```

U video na hranách vygenerovaných datovými klipy MP4 založenými na událostech by měl být doporučený vzor pro pojmenování typu DateTime a pro více instancí stejného grafu doporučujeme použít systémové proměnné GraphTopologyName a GraphInstanceName. Jako příklad můžete nastavit filePathPattern pro jímky souborů následujícím způsobem: 

```
"fileNamePattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}-${System.DateTime}"
```

Nebo 

```
"fileNamePattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}--${System.GraphTopologyName}-${System.GraphInstanceName} ${System.DateTime}"
```
>[!NOTE]
> V předchozím příkladu je proměnná **fileSinkOutputName** názvem ukázkové proměnné, kterou definujete v topologii grafu. Toto není **Systémová** proměnná. 

#### <a name="system-variables"></a>Systémové proměnné
Některé proměnné definované systémem, které lze použít, jsou:

|Systémová proměnná|Popis|Příklad|
|-----------|-----------|-----------|
|System. DateTime|Datum a čas UTC ve formátu kompatibilním se soubory ISO8601 (základní reprezentace YYYYMMDDThhmmss).|20200222T173200Z|
|System. PreciseDateTime|Datum a čas UTC ve formátu kompatibilním se soubory ISO8601 s milisekundami (základní reprezentace YYYYMMDDThhmmss. zabezpečené úložiště).|20200222T 173200.123 Z|
|System. GraphTopologyName|Uživatel zadal název zpracovávané topologie grafu.|IngestAndRecord|
|System. GraphInstanceName|Uživatelem zadaný název instance spouštěného grafu|camera001|

>[!TIP]
> System. PreciseDateTime nelze použít při pojmenování prostředků z důvodu "." v názvu
### <a name="keeping-your-vm-clean"></a>Údržba virtuálního počítače

Virtuální počítač se systémem Linux, který používáte jako hraniční zařízení, může přestat reagovat, pokud není spravován v pravidelných intervalech. Je nutné uchovat vyčištění mezipaměti, eliminovat nepotřebné balíčky a odebírat také nepoužívané kontejnery z virtuálního počítače. Pokud to chcete udělat, je to sada doporučených příkazů, které můžete použít na hraničním VIRTUÁLNÍm počítači.

1. `sudo apt-get clean`

    Příkaz apt-get Cleanup vymaže místní úložiště načtených souborů balíčku, které jsou v/var/cache.. Adresáře, které čistí, jsou/var/cache/apt/archives/a/var/cache/apt/archives/Partial/.. Jedinými soubory, které opustí v/var/cache/apt/archives, jsou soubor zámku a částečný podadresář. Příkaz apt-get Cleanup se obecně používá k vymazání místa na disku podle potřeby, obecně v rámci pravidelné plánované údržby. Další informace najdete v tématu [Vyčištění pomocí apt-get](https://www.networkworld.com/article/3453032/cleaning-up-with-apt-get.html).
1. `sudo apt-get autoclean`

    Možnost apt-get autoclean, jako je apt-get clean, vymaže místní úložiště načtených souborů balíčku, ale odebere pouze soubory, které již nelze stáhnout a které nejsou užitečné. Pomáhá udržet větší rostoucí objem paměti.
1. `sudo apt-get autoremove1`

    Možnost automaticky odebrat odstraní balíčky, které byly automaticky nainstalovány, protože je vyžaduje jiný balíček, ale s odebranými balíčky již není potřeba.
1. `sudo docker image ls` – Poskytuje seznam imagí Docker na hraničním systému
1. `sudo docker system prune`

    Docker využívá konzervativní přístup k vymazání nepoužívaných objektů (často označovaného jako "uvolňování paměti"), jako jsou obrázky, kontejnery, svazky a sítě: tyto objekty se většinou neodstraňují, pokud k tomu nechcete explicitně požádat Docker. Může to způsobit, že Docker použije dodatečné místo na disku. Pro každý typ objektu nabízí Docker příkaz k vyřazení. Kromě toho můžete použít vyřazení Docker System k vyčištění více typů objektů najednou. Další informace najdete v tématu [vyřazení nepoužívaných objektů Docker](https://docs.docker.com/config/pruning/).
1. `sudo docker rmi REPOSITORY:TAG`

    Vzhledem k tomu, že se v modulu Edge vyskytují aktualizace, může mít dokovací modul stále starší verze modulu Edge. V takovém případě je vhodné použít příkaz Docker RMI k odebrání konkrétních imagí identifikovaných pomocí značky verze image.

## <a name="next-steps"></a>Další kroky

[Rychlý Start: Začínáme – Live video Analytics na IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
