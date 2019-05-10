---
title: 'Kurz: Simulace selhání při přístupu k redundantnímu úložišti jen pro čtení v Azure | Microsoft Docs'
description: Simulace chyby při přístupu ke geograficky redundantnímu úložišti jen pro čtení
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: tamram
ms.reviewer: artek
ms.openlocfilehash: 1f5c404e410ded2714be761e35060f3c07379bd3
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65508100"
---
# <a name="tutorial-simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Kurz: Simulace selhání při přístupu k redundantnímu úložišti jen pro čtení

Tento kurz je druhá část série. V něm dozvíte o výhodách [čtení geograficky redundantní](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (čtení RA-GRS) pomocí simulace selhání.

K simulaci selhání, můžete použít buď [statické směrování](#simulate-a-failure-with-an-invalid-static-route) nebo [Fiddler](#simulate-a-failure-with-fiddler). Obě metody umožňuje simulovat selhání žádostí na primární koncový bod vašeho [čtení geograficky redundantní](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) účet úložiště (pro čtení RA-GRS), příčinou aplikace čtení ze sekundárního koncového bodu místo toho.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Ve druhé části této série se naučíte:

> [!div class="checklist"]
> * Spouštět a pozastavovat aplikaci
> * Simulace selhání pomocí [neplatné statické trasy](#simulate-a-failure-with-an-invalid-static-route) nebo [Fiddleru](#simulate-a-failure-with-fiddler)
> * Simulovat obnovení primárního koncového bodu

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu, dokončete předchozí kurz o službě: [Ujistěte se, vašich aplikačních dat pomocí služby Azure storage s vysokou dostupností][previous-tutorial].

Simulace selhání se statickým směrováním, použijete příkazového řádku se zvýšenými oprávněními.

K simulaci selhání pomocí Fiddleru, stáhněte si a [nainstalovat Fiddler](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Simulace selhání pomocí neplatné statické trasy

Pro všechny žádosti na primární koncový bod vašeho účtu [geograficky redundantního úložiště jen pro čtení](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) můžete vytvořit neplatnou statickou trasu. V tomto kurzu se používá místní hostitel jako brána pro směrování žádostí na účet úložiště. Použití místního hostitele jako brány způsobí, že všechny žádosti na primární koncový bod vašeho účtu úložiště se ve smyčce vrátí zpět do hostitele, což následně povede k selhání. Následujícím postupem nasimulujete selhání a obnovení primárního koncového bodu pomocí neplatné statické trasy.

### <a name="start-and-pause-the-application"></a>Spuštění a pozastavení aplikace

Postupujte podle pokynů v [předchozí kurz o službě] [ previous-tutorial] ke spuštění ukázky a stáhněte si soubor testu, potvrzení, že pocházejí z primárního úložiště. V závislosti na vaší cílové platformy můžete ručně pozastavit ukázku nebo počkejte na řádku.

### <a name="simulate-failure"></a>Simulace chyby

Když je aplikace pozastavená, otevřete příkazový řádek na Windows jako správce nebo spusťte terminál jako root v Linuxu.

Získejte informace o doméně primárního koncového bodu účtu úložiště tak, že zadáte následující příkaz na příkazový řádek nebo terminálových nahrazuje příkaz `STORAGEACCOUNTNAME` s názvem účtu úložiště.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
```

Zkopírujte IP adresu vašeho účtu úložiště do textového editoru pro pozdější použití.

Pokud chcete získat IP adresu místního hostitele, zadejte `ipconfig` na příkazovém řádku Windows nebo `ifconfig` na terminálu Linuxu.

Přidáte statickou trasu pro cílového hostitele, zadejte následující příkaz na příkazovém řádku Windows nebo Linuxem terminálu, nahrazení `<destination_ip>` se IP adresa vašeho účtu úložiště a `<gateway_ip>` s IP adresou místního hostitele.

#### <a name="linux"></a>Linux

```
route add <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route add <destination_ip> <gateway_ip>
```

V okně s ukázkou spuštěné obnovte chod aplikace nebo stisknout klávesu odpovídající stáhnout ukázkový soubor a ověřte, zda pochází ze sekundárního úložiště. Potom můžete pozastavit ukázku znovu nebo počkejte na řádku.

### <a name="simulate-primary-endpoint-restoration"></a>Simulovat obnovení primárního koncového bodu

Pro simulaci stávají funkční znovu primární koncový bod, odstraňte ze směrovací tabulky neplatné statické trasy. To umožní směrování všech žádostí na primární koncový bod pomocí výchozí brány. Zadejte následující příkaz na příkazovém řádku Windows nebo terminálu Linuxu.

#### <a name="linux"></a>Linux

```
route del <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route delete <destination_ip>
```

Potom můžete pokračovat, aplikace nebo stiskněte klávesu odpovídající Stáhněte ukázku soubor znovu, tento čas potvrzení, že znovu pochází z primárního úložiště.

## <a name="simulate-a-failure-with-fiddler"></a>Simulace selhání pomocí Fiddleru

K simulaci selhání pomocí fiddleru provedete, vložíte neúspěšnou odpověď, a pro žádosti na primární koncový bod vašeho účtu úložiště RA-GRS.

V dalších částech znázornění simulace selhání a obnovení primárního koncového bodu pomocí fiddleru.

### <a name="launch-fiddler"></a>Spuštění fiddleru

Otevřete Fiddler, vyberte **Rules** (Pravidla) a **Customize Rules** (Přizpůsobit pravidla).

![Přizpůsobení pravidel Fiddleru](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Spustí se Fiddler ScriptEditor a zobrazí **SampleRules.js** souboru. Tento soubor slouží k přizpůsobení Fiddleru.

Vložte následující vzorový kód v `OnBeforeResponse` fungovala, nahrazení `STORAGEACCOUNTNAME` s názvem účtu úložiště. V závislosti na ukázky, budete také muset nahradit `HelloWorld` s názvem souboru testu (nebo předpona jako `sampleFile`) stahování. Nový kód je zakomentovaný, ujistěte se, že není spuštěna ihned.

Jakmile budete hotovi, vyberte **souboru** a **Uložit** uložte provedené změny. Nechte okno ScriptEditor otevřené pro použití v následujících krocích.

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

Postupujte podle pokynů v [předchozí kurz o službě] [ previous-tutorial] ke spuštění ukázky a stáhněte si soubor testu, potvrzení, že pocházejí z primárního úložiště. V závislosti na vaší cílové platformy můžete ručně pozastavit ukázku nebo počkejte na řádku.

### <a name="simulate-failure"></a>Simulace chyby

Když je aplikace pozastavená, přepněte zpět do Fiddleru a odkomentovat vlastní pravidlo, které jste si uložili v `OnBeforeResponse` funkce. Je potřeba vybrat možnost **souboru** a **Uložit** uložte provedené změny, takže pravidla se projeví. Tento kód vyhledá žádosti na účet úložiště RA-GRS a pokud cesta obsahuje název ukázkového souboru, vrátí kód odpovědi `503 - Service Unavailable`.

V okně s ukázkou spuštěné obnovte chod aplikace nebo stisknout klávesu odpovídající stáhnout ukázkový soubor a ověřte, zda pochází ze sekundárního úložiště. Potom můžete pozastavit ukázku znovu nebo počkejte na řádku.

### <a name="simulate-primary-endpoint-restoration"></a>Simulovat obnovení primárního koncového bodu

V aplikaci Fiddler odstranit nebo okomentovat vlastní pravidlo znovu. Vyberte **souboru** a **Uložit** zajistit pravidlo, již nebudou platit.

V okně s ukázkou spuštěné obnovte chod aplikace nebo stisknutím klávesy odpovídající stáhnout ukázkový soubor a potvrďte, že pocházejí z primárního úložiště ještě jednou. Potom můžete ukončit vzorku.

## <a name="next-steps"></a>Další postup

Ve druhé části série jste se dozvěděli o simulaci selhání za účelem testování geograficky redundantní úložiště s přístupem pro čtení.

Přečtěte si více o tom, jak úložiště RA-GRS funguje, a také související rizika, najdete v následujícím článku:

> [!div class="nextstepaction"]
> [Navrhování aplikací s vysokou dostupností s využitím RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
