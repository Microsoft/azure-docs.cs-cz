---
title: Sada Batch Processing Kit pro kontejnery řeči
titleSuffix: Azure Cognitive Services
description: Pro škálování požadavků na kontejner řeči použijte sadu Batch Processing Kit.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/22/2020
ms.author: aahi
ms.openlocfilehash: cc6bcef77ca1601b76468586aa6af202836f1438
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "97631988"
---
# <a name="batch-processing-kit-for-speech-containers"></a>Sada Batch Processing Kit pro kontejnery řeči

Pomocí sady Batch Processing můžete doplňovat a škálovat úlohy na kontejnerech řeči. Tento Open source nástroj, který je dostupný jako kontejner, usnadňuje dávkové přepisy pro velký počet zvukových souborů, a to napříč libovolným počtem místních a cloudových koncových bodů kontejneru řeči. 

:::image type="content" source="media/containers/general-diagram.png" alt-text="Diagram znázorňující příklad pracovního postupu kontejneru Batch-Kit.":::

Kontejner sady Batch Kit je zdarma dostupný na [GitHubu](https://github.com/microsoft/batch-processing-kit) a   [Docker Hub](https://hub.docker.com/r/batchkit/speech-batch-kit/tags). Budou se vám [účtovat](speech-container-howto.md#billing) jenom vámi používané kontejnery řeči.

| Funkce  | Popis  |
|---------|---------|
| Distribuce souboru Batch audio     | Automaticky odesílá velké počty souborů do místních nebo cloudových koncových bodů kontejneru řeči. Soubory mohou být na jakémkoli svazku kompatibilním se standardem POSIX, včetně síťových systémů souborů.       |
| Integrace sady Speech SDK | Předat společné příznaky sadě Speech SDK, včetně: n-nejlepší hypotéza, diarization, jazyk, maskování vulgárních výrazů.  |
|Režimy spuštění     | Spustit klienta Batch jednou, nepřetržitě na pozadí nebo vytvořit koncové body HTTP pro zvukové soubory.         |
| Odolnost proti chybám | Automatické opakování a pokračování v přepisu, aniž by došlo ke ztrátě průběhu, a rozlišení mezi tím, které chyby může a nelze opakovat. |
| Detekce dostupnosti koncového bodu | Pokud koncový bod přestane být k dispozici, bude klient služby Batch pokračovat zdlouhavého přepisování pomocí dalších koncových bodů kontejneru. Po opětovném zpřístupnění se klient automaticky spustí s koncovým bodem.   |
| Výměna koncového bodu za chodu | Přidání, odebrání nebo úprava koncových bodů kontejneru řeči za běhu bez přerušení průběhu dávky Aktualizace jsou okamžité. |
| Protokolování v reálném čase | Protokolování pokusů o žádosti, časová razítka a důvody selhání v reálném čase se soubory protokolu sady Speech SDK pro každý zvukový soubor. |

## <a name="get-the-container-image-with-docker-pull"></a>Získat image kontejneru pomocí `docker pull`

Použijte příkaz [Docker Pull](https://docs.docker.com/engine/reference/commandline/pull/) ke stažení nejnovějšího kontejneru sady Batch Kit.

```bash
docker pull docker.io/batchkit/speech-batch-kit:latest
```

## <a name="endpoint-configuration"></a>Konfigurace koncového bodu

Klient Batch převezme konfigurační soubor YAML, který určuje koncové body kontejneru on-Prem. Následující příklad lze zapsat do `/mnt/my_nfs/config.yaml` , který je použit v níže uvedených příkladech. 

```yaml
MyContainer1: 
  concurrency: 5 
  host: 192.168.0.100 
  port: 5000 
  rtf: 3 
MyContainer2: 
  concurrency: 5 
  host: BatchVM0.corp.redmond.microsoft.com 
  port: 5000 
  rtf: 2 
MyContainer3: 
  concurrency: 10 
  host: localhost 
  port: 6001 
  rtf: 4 
```

Tento příklad YAML určuje tři kontejnery řeči na třech hostitelích. První hostitel je určený adresou IPv4, druhý je spuštěný ve stejném virtuálním počítači jako klient služby Batch a třetí kontejner je určený názvem DNS jiného virtuálního počítače. `concurrency`Hodnota určuje maximální počet současných přepisů souborů, které lze spustit ve stejném kontejneru. `rtf`Hodnota (faktor reálného času) je volitelná a lze ji použít k vyladění výkonu.
 
Klient služby Batch může dynamicky zjistit, jestli koncový bod nebude k dispozici (třeba kvůli restartování kontejneru nebo síťovému problému), a když bude znovu dostupný. Žádosti o přepis nebudou odesílány do kontejnerů, které nejsou k dispozici, a klient bude pokračovat v používání dalších dostupných kontejnerů. Koncové body můžete kdykoli přidat, odebrat nebo upravit, aniž by došlo k přerušení průběhu dávky.


## <a name="run-the-batch-processing-container"></a>Spuštění kontejneru dávkového zpracování
  
> [!NOTE] 
> * Tento příklad používá stejný adresář ( `/my_nfs` ) pro konfigurační soubor a vstupní, výstupní a protokoly adresáře. Pro tyto složky můžete použít hostované adresáře nebo adresáře připojené k systému souborů NFS.
> * Po spuštění klienta se `–h` zobrazí seznam dostupných parametrů příkazového řádku a jejich výchozí hodnoty. 
> * Kontejner dávkového zpracování je podporován pouze v systému Linux.

`run`Ke spuštění kontejneru použijte příkaz Docker. Tím se spustí interaktivní prostředí uvnitř kontejneru.

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs --entrypoint /bin/bash /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest
```

Spuštění klienta Batch:  

```Docker
run-batch-client -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -file_log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```

Spuštění klienta Batch a kontejneru v jednom příkazu:

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest  -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -file_log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```


Klient bude spuštěn. Pokud se už zvukový soubor přepisu v předchozím běhu, klient ho automaticky přeskočí. Když dojde k přechodným chybám, odesílají se soubory s automatickým opakováním a můžete odlišit, mezi kterými chybami se má klient pokusit znovu. Při chybě přepisu bude klient pokračovat v přepisu a může to zkusit znovu, aniž by došlo ke ztrátě průběhu.  

## <a name="run-modes"></a>Režimy spuštění 

Sada Batch Processing Kit nabízí tři režimy pomocí `--run-mode` parametru.

#### <a name="oneshot"></a>[Oneshot](#tab/oneshot)

`ONESHOT` režim transcribes jednu dávku zvukových souborů (ze vstupního adresáře a volitelného seznamu souborů) do výstupní složky.

:::image type="content" source="media/containers/batch-oneshot-mode.png" alt-text="Diagram znázorňující soubory zpracování kontejneru Batch-Kit v režimu OneShot":::

1. Zadejte koncové body kontejneru řeči, které bude klient Batch používat v `config.yaml` souboru. 
2. Umístěte zvukové soubory pro přepis do vstupního adresáře.  
3. Vyvolat kontejner v adresáři, který bude začínat zpracováním souborů. Pokud se už zvukový soubor přepisu v předchozím běhu se stejným výstupním adresářem (stejný název souboru a kontrolní součet), klient tento soubor přeskočí. 
4. Soubory jsou odesílány do koncových bodů kontejneru z kroku 1.
5. Do zadaného výstupního adresáře se vrátí protokoly a výstup kontejneru řeči. 

#### <a name="daemon"></a>[Proces](#tab/daemon)

> [!TIP]
> Pokud se ke vstupnímu adresáři přidávají současně víc souborů, můžete místo toho zvýšit výkon přidáním v pravidelných intervalech.

`DAEMON` režim transcribes stávající soubory v dané složce a průběžně transcribes nové zvukové soubory při jejich přidání.          

:::image type="content" source="media/containers/batch-daemon-mode.png" alt-text="Diagram znázorňující soubory zpracování kontejneru sady Batch-Kit v režimu démona.":::

1. Zadejte koncové body kontejneru řeči, které bude klient Batch používat v `config.yaml` souboru. 
2. Vyvolá kontejner ve vstupním adresáři. Klient služby Batch spustí monitorování adresáře pro příchozí soubory. 
3. Nastavte průběžné doručování zvukového souboru do vstupního adresáře. Pokud se už zvukový soubor přepisu v předchozím běhu se stejným výstupním adresářem (stejný název souboru a kontrolní součet), klient tento soubor přeskočí. 
4. Po zjištění, že se najde zápis souboru nebo indikátor POSIX, se kontejner aktivuje.
5. Soubory jsou odesílány do koncových bodů kontejneru z kroku 1.
6. Do zadaného výstupního adresáře se vrátí protokoly a výstup kontejneru řeči. 

#### <a name="rest"></a>[REST](#tab/rest)

`REST` režim je režim serveru rozhraní API, který poskytuje základní sadu koncových bodů HTTP pro odeslání dávky zvukového souboru, kontrolu stavu a dlouhé cyklické dotazování. Také umožňuje programovou spotřebu pomocí rozšíření modulu Pythonu nebo importu jako dílčí modul.

:::image type="content" source="media/containers/batch-rest-api-mode.png" alt-text="Diagram znázorňující soubory zpracování kontejneru Batch-Kit v režimu REST":::

1. Zadejte koncové body kontejneru řeči, které bude klient Batch používat v `config.yaml` souboru. 
2. Odešle požadavek na požadavek HTTP na jeden z koncových bodů serveru API. 
        
    |Koncový bod  |Popis  |
    |---------|---------|
    |`/submit`     | Koncový bod pro vytváření nových požadavků na dávky        |
    |`/status`     | Koncový bod pro kontrolu stavu dávkového požadavku. Připojení zůstane otevřené, dokud se dávka nedokončí.       |
    |`/watch`     | Koncový bod pro použití dlouhého cyklického dotazování HTTP, dokud se dávka nedokončí        |

3. Zvukové soubory jsou odesílány ze vstupního adresáře. Pokud se už zvukový soubor přepisu v předchozím běhu se stejným výstupním adresářem (stejný název souboru a kontrolní součet), klient tento soubor přeskočí. 
4. Pokud je požadavek odeslán do `/submit` koncového bodu, soubory jsou odesílány do koncových bodů kontejneru z kroku 1.
5. Do zadaného výstupního adresáře se vrátí protokoly a výstup kontejneru řeči. 

---

## <a name="logging"></a>protokolování

> [!NOTE]
> Klient Batch může soubor *Run. log* pravidelně přepsat, pokud je příliš velký.

Klient vytvoří soubor *Run. log* v adresáři určeném `-log_folder` argumentem v příkazu Docker `run` . Protokoly jsou zachyceny ve výchozím nastavení na úrovni ladění. Stejné protokoly jsou odesílány do `stdout/stderr` a filtrovány v závislosti na `-file_log_level` `console_log_level` argumentech nebo. Tento protokol je nutný pouze pro ladění, nebo pokud potřebujete odeslat trasování pro podporu. Složka protokolování obsahuje také protokoly sady Speech SDK pro jednotlivé zvukové soubory.

Výstupní adresář určený parametrem `-output_folder` bude obsahovat *run_summary.jsv*   souboru, který se pravidelně přepíše každých 30 sekund nebo kdykoli po dokončení nových přepisů. Tento soubor můžete použít ke kontrole průběhu zpracování dávky. Bude také obsahovat konečné statistiky spuštění a konečný stav všech souborů po dokončení dávky. Dávka se dokončí, až proces skončí čistým ukončením. 

## <a name="next-steps"></a>Další kroky

* [Postup instalace a spuštění kontejnerů](speech-container-howto.md)
