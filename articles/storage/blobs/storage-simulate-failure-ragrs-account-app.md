---
title: Kurz – simulace selhání při čtení dat z primární oblasti
titleSuffix: Azure Storage
description: Simulovat chybu při čtení dat z primární oblasti při čtení přístup geograficky redundantní úložiště (RA-GRS) je povolena pro účet úložiště.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: artek
ms.openlocfilehash: 3693ae8c9c425c35ab1a4ebf88b6cd3dfb58f92c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061296"
---
# <a name="tutorial-simulate-a-failure-in-reading-data-from-the-primary-region"></a>Kurz: Simulace selhání při čtení dat z primární oblasti

Tento kurz je druhá část série. V něm se dozvíte o výhodách [geograficky redundantního úložiště pro čtení](../common/storage-redundancy.md) (RA-GRS) simulací selhání.

Chcete-li simulovat selhání, můžete použít [statické směrování](#simulate-a-failure-with-an-invalid-static-route) nebo [šumař](#simulate-a-failure-with-fiddler). Obě metody vám umožní simulovat selhání požadavků na primární koncový bod vašeho účtu úložiště [pro přístup ke čtení](../common/storage-redundancy.md) (RA-GRS), což způsobí, že aplikace bude číst ze sekundárního koncového bodu.

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

Ve druhé části této série se naučíte:

> [!div class="checklist"]
> * Spouštět a pozastavovat aplikaci
> * Simulace selhání [neplatnou statickou trasou](#simulate-a-failure-with-an-invalid-static-route) nebo [Šumařem](#simulate-a-failure-with-fiddler)
> * Simulovat obnovení primárního koncového bodu

## <a name="prerequisites"></a>Požadavky

Než začnete tento kurz, dokončete předchozí kurz: [Zpřístupněte data aplikace vysoce dostupná pomocí úložiště Azure][previous-tutorial].

Chcete-li simulovat selhání se statickým směrováním, použijete příkazový řádek se zvýšenými oprávněními.

Chcete-li simulovat selhání pomocí Fiddler, stáhněte a [nainstalujte Šumař](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Simulace selhání pomocí neplatné statické trasy

Pro všechny žádosti na primární koncový bod vašeho účtu [geograficky redundantního úložiště jen pro čtení](../common/storage-redundancy.md) (RA-GRS) můžete vytvořit neplatnou statickou trasu. V tomto kurzu se používá místní hostitel jako brána pro směrování žádostí na účet úložiště. Použití místního hostitele jako brány způsobí, že všechny žádosti na primární koncový bod vašeho účtu úložiště se ve smyčce vrátí zpět do hostitele, což následně povede k selhání. Následujícím postupem nasimulujete selhání a obnovení primárního koncového bodu pomocí neplatné statické trasy.

### <a name="start-and-pause-the-application"></a>Spuštění a pozastavení aplikace

Pomocí pokynů v [předchozím kurzu][previous-tutorial] spusťte ukázku a stáhněte testovací soubor s potvrzením, že pochází z primárního úložiště. V závislosti na cílové platformě pak můžete vzorek ručně pozastavit nebo počkat na výzvu.

### <a name="simulate-failure"></a>Simulace chyby

Když je aplikace pozastavena, otevřete příkazový řádek v systému Windows jako správce nebo spusťte terminál jako root na Linuxu.

Informace o primární doméně koncového bodu účtu úložiště získáte zadáním následujícího příkazu na příkazovém řádku nebo terminálu a nahradíte `STORAGEACCOUNTNAME` tak název účtu úložiště.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
```

Zkopírujte IP adresu vašeho účtu úložiště do textového editoru pro pozdější použití.

Pokud chcete získat IP adresu místního hostitele, zadejte `ipconfig` na příkazovém řádku Windows nebo `ifconfig` na terminálu Linuxu.

Chcete-li přidat statickou trasu pro cílového hostitele, zadejte na `<destination_ip>` příkazovém řádku systému Windows nebo terminálu Systému Windows následující příkaz, který nahradí IP adresu účtu úložiště a `<gateway_ip>` adresu IP místního hostitele.

#### <a name="linux"></a>Linux

```
route add <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route add <destination_ip> <gateway_ip>
```

V okně s spuštěnou ukázkou pokračujte v aplikaci nebo stisknutím příslušné klávesy stáhněte ukázkový soubor a ověřte, zda pochází ze sekundárního úložiště. Potom můžete vzorek znovu pozastavit nebo počkat na výzvu.

### <a name="simulate-primary-endpoint-restoration"></a>Simulovat obnovení primárního koncového bodu

Chcete-li simulovat, že se primární koncový bod opět stane funkčním, odstraňte neplatnou statickou trasu ze směrovací tabulky. To umožní směrování všech žádostí na primární koncový bod pomocí výchozí brány. Na příkazovém řádku systému Windows nebo terminálu Linuxu zadejte následující příkaz.

#### <a name="linux"></a>Linux

```
route del <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route delete <destination_ip>
```

Potom můžete pokračovat v aplikaci nebo stisknutím příslušné klávesy stáhnout ukázkový soubor znovu, tentokrát potvrzuje, že opět pochází z primárního úložiště.

## <a name="simulate-a-failure-with-fiddler"></a>Simulace selhání pomocí Fiddleru

Chcete-li simulovat selhání s Šumař, vložíte neúspěšnou odpověď pro požadavky na primární koncový bod účtu úložiště RA-GRS.

Následující části popisují, jak simulovat selhání a obnovení primárního koncového bodu pomocí houslista.

### <a name="launch-fiddler"></a>Spuštění fiddleru

Otevřete Fiddler, vyberte **Rules** (Pravidla) a **Customize Rules** (Přizpůsobit pravidla).

![Přizpůsobení pravidel Fiddleru](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Fiddler ScriptEditor spustí a zobrazí **samplerules.js** soubor. Tento soubor slouží k přizpůsobení Fiddleru.

Vložte do funkce `OnBeforeResponse` následující ukázku `STORAGEACCOUNTNAME` kódu a nahraďte ji názvem účtu úložiště. V závislosti na vzorku může být `HelloWorld` také nutné nahradit název testovacího souboru `sampleFile`(nebo předponu, například ) stažené. Nový kód je zakomentován, aby bylo zajištěno, že nebude spuštěn okamžitě.

Po dokončení vyberte **Soubor** a **Uložit,** chcete-li uložit změny. Ponechte okno ScriptEditor otevřené pro použití v následujících krocích.

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

![Vložení vlastního pravidla](media/storage-simulate-failure-ragrs-account-app/figure2.png)

### <a name="start-and-pause-the-application"></a>Spuštění a pozastavení aplikace

Pomocí pokynů v [předchozím kurzu][previous-tutorial] spusťte ukázku a stáhněte testovací soubor s potvrzením, že pochází z primárního úložiště. V závislosti na cílové platformě pak můžete vzorek ručně pozastavit nebo počkat na výzvu.

### <a name="simulate-failure"></a>Simulace chyby

Když je aplikace pozastavena, přepněte zpět na Šumař a `OnBeforeResponse` odkomentujte vlastní pravidlo, které jste uložili do funkce. Nezapomeňte vybrat **Soubor** a **Uložit,** abyste změny uložili, aby se pravidlo projevilo. Tento kód vyhledá požadavky na účet úložiště RA-GRS a pokud cesta obsahuje název ukázkového `503 - Service Unavailable`souboru, vrátí kód odpovědi aplikace .

V okně s spuštěnou ukázkou pokračujte v aplikaci nebo stisknutím příslušné klávesy stáhněte ukázkový soubor a ověřte, zda pochází ze sekundárního úložiště. Potom můžete vzorek znovu pozastavit nebo počkat na výzvu.

### <a name="simulate-primary-endpoint-restoration"></a>Simulovat obnovení primárního koncového bodu

V Fiddler, odstranit nebo zakomentovat vlastní pravidlo znovu. Vyberte **Soubor** a **Uložit,** abyste zajistili, že pravidlo již nebude platné.

V okně s spuštěnou ukázkou pokračujte v aplikaci nebo stisknutím příslušné klávesy stáhněte ukázkový soubor a znovu potvrďte, že pochází z primárního úložiště. Potom můžete ukončit ukázku.

## <a name="next-steps"></a>Další kroky

Ve druhé části série jste se dozvěděli o simulaci selhání testování přístupu ke čtení geograficky redundantní úložiště.

Další informace o tom, jak funguje úložiště RA-GRS, a souvisejících rizicích naleznete v následujícím článku:

> [!div class="nextstepaction"]
> [Navrhování aplikací s vysokou dostupností s využitím RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
