---
title: Kurz – simulace selhání při čtení dat z primární oblasti
titleSuffix: Azure Storage
description: Simuluje chybu při čtení dat z primární oblasti, když je účet úložiště nakonfigurovaný pro čtení geograficky redundantního úložiště s přístupem pro čtení (RA-GZRS).
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 04/16/2020
ms.author: tamram
ms.reviewer: artek
ms.custom: devx-track-js
ms.openlocfilehash: 5a0bef4fbfd4ee9aa720dab430a33bbfcd0b918b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91280347"
---
# <a name="tutorial-simulate-a-failure-in-reading-data-from-the-primary-region"></a>Kurz: simulace selhání při čtení dat z primární oblasti

Tento kurz je druhá část série. V takovém případě se dozvíte o výhodách [geograficky redundantního úložiště s přístupem pro čtení](../common/storage-redundancy.md) (RA-GZRS) pro simulaci selhání.

Aby se mohla simulovat chyba, můžete použít buď [statické směrování](#simulate-a-failure-with-an-invalid-static-route) , nebo [Fiddler](#simulate-a-failure-with-fiddler). Obě metody umožní simulovat selhání požadavků na primární koncový bod vašeho [geograficky redundantního účtu úložiště s přístupem pro čtení](../common/storage-redundancy.md) (RA-GZRS), takže aplikace bude číst ze sekundárního koncového bodu.

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

Ve druhé části této série se naučíte:

> [!div class="checklist"]
> * Spouštět a pozastavovat aplikaci
> * Simulace selhání s [neplatnou statickou trasou](#simulate-a-failure-with-an-invalid-static-route) nebo [Fiddler](#simulate-a-failure-with-fiddler)
> * Simulovat obnovení primárního koncového bodu

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, dokončete předchozí kurz: [zajištění vysoké dostupnosti dat aplikace v Azure Storage][previous-tutorial].

Chcete-li simulovat selhání se statickým směrováním, použijte příkazový řádek se zvýšenými oprávněními.

Postup při simulaci selhání pomocí Fiddler, stažení a [instalace Fiddler](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Simulace selhání pomocí neplatné statické trasy

Můžete vytvořit neplatnou statickou trasu pro všechny požadavky na primární koncový bod vašeho [geograficky redundantního účtu úložiště s přístupem pro čtení](../common/storage-redundancy.md) (RA-GZRS). V tomto kurzu se používá místní hostitel jako brána pro směrování žádostí na účet úložiště. Použití místního hostitele jako brány způsobí, že všechny žádosti na primární koncový bod vašeho účtu úložiště se ve smyčce vrátí zpět do hostitele, což následně povede k selhání. Následujícím postupem nasimulujete selhání a obnovení primárního koncového bodu pomocí neplatné statické trasy.

### <a name="start-and-pause-the-application"></a>Spuštění a pozastavení aplikace

Pomocí pokynů v [předchozím kurzu][previous-tutorial] spusťte ukázku a Stáhněte soubor testu a potvrďte, že pochází z primárního úložiště. V závislosti na cílové platformě můžete tuto ukázku ručně pozastavit nebo počkat na příkazovém řádku.

### <a name="simulate-failure"></a>Simulace chyby

Když je aplikace pozastavena, otevřete příkazový řádek ve Windows jako správce nebo spusťte terminál jako kořenový adresář v systému Linux.

Zadáním následujícího příkazu na příkazovém řádku nebo terminálu Získejte informace o primární doméně koncového bodu účtu úložiště a nahraďte ho `STORAGEACCOUNTNAME` názvem vašeho účtu úložiště.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
```

Zkopírujte IP adresu vašeho účtu úložiště do textového editoru pro pozdější použití.

Pokud chcete získat IP adresu místního hostitele, zadejte `ipconfig` na příkazovém řádku Windows nebo `ifconfig` na terminálu Linuxu.

Pokud chcete přidat statickou trasu pro cílového hostitele, zadejte na příkazovém řádku Windows nebo terminálu pro Linux následující příkaz a nahraďte IP `<destination_ip>` adresou vašeho účtu úložiště a `<gateway_ip>` IP adresou místního hostitele.

#### <a name="linux"></a>Linux

```
route add <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route add <destination_ip> <gateway_ip>
```

V okně se spuštěnou ukázkou obnovte aplikaci nebo stiskněte odpovídající klíč ke stažení ukázkového souboru a potvrďte, že pochází ze sekundárního úložiště. Pak můžete ukázku znovu pozastavit nebo počkat na příkazovém řádku.

### <a name="simulate-primary-endpoint-restoration"></a>Simulovat obnovení primárního koncového bodu

Pro simulaci opětovného fungování primárního koncového bodu odstraňte ze směrovací tabulky neplatnou statickou trasu. To umožní směrování všech žádostí na primární koncový bod pomocí výchozí brány. Do příkazového řádku Windows nebo terminálu pro Linux zadejte následující příkaz.

#### <a name="linux"></a>Linux

```bash
route del <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```console
route delete <destination_ip>
```

Pak můžete aplikaci obnovit nebo stisknout odpovídající klíč pro opětovné stažení ukázkového souboru, tentokrát s potvrzením, že znovu pochází z primárního úložiště.

## <a name="simulate-a-failure-with-fiddler"></a>Simulace selhání pomocí Fiddleru

Pokud chcete simulovat selhání pomocí Fiddler, vložíte do primárního koncového bodu vašeho účtu úložiště RA-GZRS neúspěšnou odpověď.

Následující části popisují, jak simulovat selhání a obnovení primárního koncového bodu pomocí Fiddler.

### <a name="launch-fiddler"></a>Spuštění fiddleru

Otevřete Fiddler, vyberte **Rules** (Pravidla) a **Customize Rules** (Přizpůsobit pravidla).

![Přizpůsobení pravidel Fiddleru](media/simulate-primary-region-failure/figure1.png)

Fiddler ScriptEditor spustí a zobrazí soubor **SampleRules.js** . Tento soubor slouží k přizpůsobení Fiddleru.

Do funkce vložte následující ukázku kódu `OnBeforeResponse` a nahraďte `STORAGEACCOUNTNAME` názvem svého účtu úložiště. V závislosti na ukázce může být také nutné nahradit `HelloWorld` názvem testovacího souboru (nebo předpony, jako je například), kterou chcete `sampleFile` Stáhnout. Nový kód je komentovaný, aby se zajistilo, že se nespustí okamžitě.

Až budete hotovi, vyberte **soubor** a **Uložit** a uložte provedené změny. Ponechte okno ScriptEditor otevřené pro použití v následujících krocích.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error.
        // When you're ready to stop sending back errors, comment these lines of script out again
        //     and save the changes.

        if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;
        }
    */
```

![Vložení vlastního pravidla](media/simulate-primary-region-failure/figure2.png)

### <a name="start-and-pause-the-application"></a>Spuštění a pozastavení aplikace

Pomocí pokynů v [předchozím kurzu][previous-tutorial] spusťte ukázku a Stáhněte soubor testu a potvrďte, že pochází z primárního úložiště. V závislosti na cílové platformě můžete tuto ukázku ručně pozastavit nebo počkat na příkazovém řádku.

### <a name="simulate-failure"></a>Simulace chyby

Když je aplikace pozastavená, přepněte zpátky na Fiddler a odkomentujte vlastní pravidlo, které jste uložili do `OnBeforeResponse` funkce. Nezapomeňte vybrat **soubor** a **Uložit** změny, aby se pravidlo projevilo. Tento kód vyhledá požadavky na účet úložiště RA-GZRS a pokud cesta obsahuje název ukázkového souboru, vrátí kód odpovědi `503 - Service Unavailable` .

V okně se spuštěnou ukázkou obnovte aplikaci nebo stiskněte odpovídající klíč ke stažení ukázkového souboru a potvrďte, že pochází ze sekundárního úložiště. Pak můžete ukázku znovu pozastavit nebo počkat na příkazovém řádku.

### <a name="simulate-primary-endpoint-restoration"></a>Simulovat obnovení primárního koncového bodu

V Fiddler odeberte vlastní pravidlo nebo ho Odkomentujte. Vyberte **soubor** a **uložte** se, abyste se ujistili, že pravidlo již nebude platit.

V okně se spuštěnou ukázkou obnovte aplikaci nebo stiskněte odpovídající klíč ke stažení ukázkového souboru a potvrďte, že je znovu nacházet z primárního úložiště. Pak můžete ukázku ukončit.

## <a name="next-steps"></a>Další kroky

Ve třetí části série jste se dozvěděli o simulaci selhání při testování geograficky redundantního úložiště s přístupem pro čtení.

Pokud chcete získat další informace o tom, jak funguje úložiště RA-GZRS, a také jeho přidružená rizika, přečtěte si následující článek:

> [!div class="nextstepaction"]
> [Návrh aplikací s vysokou dostupností pomocí RA-GZRS](../common/geo-redundant-design.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
