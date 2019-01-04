---
title: 'Kurz: Simulace selhání při přístupu k redundantnímu úložišti jen pro čtení v Azure | Microsoft Docs'
description: Simulace chyby při přístupu ke geograficky redundantnímu úložišti jen pro čtení
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: tamram
ms.openlocfilehash: 2d86c0c4838fb2ae9d839e64a067824019133d8b
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54024429"
---
# <a name="tutorial-simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Kurz: Simulace selhání při přístupu k redundantnímu úložišti jen pro čtení

Tento kurz je druhá část série. V něm dozvíte o výhodách [čtení geograficky redundantní](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (čtení RA-GRS) pomocí simulace selhání.

K simulaci selhání, můžete použít buď [Fiddler](#simulate-a-failure-with-fiddler) nebo [statické směrování](#simulate-a-failure-with-an-invalid-static-route). Některé z metod umožňuje simulovat selhání žádostí na primární koncový bod vašeho [čtení geograficky redundantní](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) účet úložiště (pro čtení RA-GRS), příčinou aplikace čtení ze sekundárního koncového bodu místo toho.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Ve druhé části této série se naučíte:

> [!div class="checklist"]
> * Spouštět a pozastavovat aplikaci
> * Simulovat selhání pomocí [fiddleru](#simulate-a-failure-with-fiddler) nebo [neplatné statické trasy](#simulate-a-failure-with-an-invalid-static-route) 
> * Simulovat obnovení primárního koncového bodu

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu, dokončete předchozí kurz o službě: [Ujistěte se, vašich aplikačních dat pomocí služby Azure storage s vysokou dostupností][previous-tutorial].

K simulaci selhání pomocí Fiddleru budete potřebovat: 

* Stáhnout a [nainstalovat Fiddler](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-fiddler"></a>Simulace selhání pomocí Fiddleru

K simulaci selhání pomocí fiddleru provedete, vložíte neúspěšnou odpověď, a pro žádosti na primární koncový bod vašeho účtu úložiště RA-GRS.

V dalších částech znázornění simulace selhání a obnovení primárního koncového bodu pomocí fiddleru.

### <a name="launch-fiddler"></a>Spuštění fiddleru

Otevřete Fiddler, vyberte **Rules** (Pravidla) a **Customize Rules** (Přizpůsobit pravidla).

![Přizpůsobení pravidel Fiddleru](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Spustí se Fiddler ScriptEditor a zobrazí **SampleRules.js** souboru. Tento soubor slouží k přizpůsobení Fiddleru.

Vložte následující vzorový kód do funkce `OnBeforeResponse`. Nový kód je zakomentovaný, aby se logika, kterou vytváří, neimplementovala okamžitě.

Jakmile budete hotovi, vyberte **souboru** a **Uložit** uložte provedené změny.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error. 
        // When you're ready to stop sending back errors, comment these lines of script out again 
        //     and save the changes.

        if ((oSession.hostname == "contosoragrs.blob.core.windows.net") 
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;  
        }
    */
```

![Vložení vlastního pravidla](media/storage-simulate-failure-ragrs-account-app/figure2.png)

### <a name="interrupting-the-application"></a>Přerušení aplikace

# <a name="net-python-and-java-v7-tabdotnet-python-java-v7"></a>[Rozhraní .NET, Python a Java v7] (# kartu/dotnet-python-java-v7)

Spusťte aplikaci v integrovaném vývojovém prostředí nebo prostředí.

Jakmile aplikace zahájí čtení z primárního koncového bodu, pozastavte aplikaci stisknutím **libovolné klávesy** v okně konzoly.

![Aplikace scénáře](media/storage-simulate-failure-ragrs-account-app/scenario.png)

# <a name="java-v10-tabjava-v10"></a>[Java v10] (# kartu nebo Java – v10)

Spusťte aplikaci v integrovaném vývojovém prostředí nebo prostředí.

Protože můžete řídit vzorku, není nutné přerušit k simulaci selhání. Ujistěte se, že soubor byl odeslán do vašeho účtu úložiště spuštěním ukázky a zadáním **P**.

![Aplikace scénáře](media/storage-simulate-failure-ragrs-account-app/Java-put-list-output.png)

---

### <a name="simulate-failure"></a>Simulace chyby

Když je aplikace pozastavená, Odkomentujte vlastní pravidlo, které jsme předtím uložili ve Fiddleru.

Vzorový kód vyhledá žádosti na účet úložiště RA-GRS a, pokud cesta obsahuje název souboru `HelloWorld`, vrátí kód odpovědi `503 - Service Unavailable`.

Přejděte do Fiddleru a vyberte **Rules** (Pravidla)  -> **Customize Rules...**  (Přizpůsobit pravidla).

Odkomentujte následující řádky, nahraďte `STORAGEACCOUNTNAME` s názvem účtu úložiště. Uložte změny tak, že vyberete **File** (Soubor)  -> **Save** (Uložit). 

> [!NOTE]
> Pokud používáte ukázkovou aplikaci v systému Linux, musíte restartovat Fiddler vždy, když upravíte soubor **CustomRule.js**, aby Fiddler vlastní logiku nainstaloval.

```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

# <a name="net-python-and-java-v7-tabdotnet-python-java-v7"></a>[Rozhraní .NET, Python a Java v7] (# kartu/dotnet-python-java-v7)

Obnovte chod aplikace stisknutím **libovolné klávesy**.

Jakmile se aplikace znovu spustí, žádosti na primární koncový bod začnou být neúspěšné. Aplikace se pokusí znovu připojit k primárnímu koncovému bodu pětkrát. Po dosažení prahové hodnoty pěti pokusů aplikace zažádá o bitovou kopii ze sekundárního koncového bodu jen pro čtení. Když aplikace úspěšně načte bitovou kopii 20krát ze sekundárního koncového bodu se pokusí připojit k primárnímu koncovému bodu. Pokud je primární koncový bod stále nedostupný, obnoví aplikace čtení ze sekundárního koncového bodu.

Tento model představuje model [Jistič](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) popsaný v předchozím kurzu.

![Vložení vlastního pravidla](media/storage-simulate-failure-ragrs-account-app/figure3.png)

# <a name="java-v10-tabjava-v10"></a>[Java v10] (# kartu nebo Java – v10)

Teď, když jste naši nabídku doplnili o selhání, zadejte **G** na test chybou.

Informuje vás, že používá sekundární kanál na rozdíl od primární kanál.

---

### <a name="simulate-primary-endpoint-restoration"></a>Simulovat obnovení primárního koncového bodu

# <a name="net-python-and-java-v7-tabdotnet-python-java-v7"></a>[Rozhraní .NET, Python a Java v7] (# kartu/dotnet-python-java-v7)

Se sadou vlastních pravidel Fiddleru nastavenou v předchozím kroku žádosti na primární koncový bod selžou.

K simulaci obnovení funkce primárního koncového bodu odeberete logiku, která vkládá chybu `503`.

Pozastavte aplikaci stisknutím **libovolné klávesy**.

Přejděte do Fiddleru a vyberte **Rules** (Pravidla) a **Customize Rules...** (Přizpůsobit pravidla). 

Zakomentujte nebo odeberte vlastní logiku ve funkci `OnBeforeResponse` a ponechejte výchozí funkci.

Uložte změny tak, že vyberete **File** (Soubor) a **Save** (Uložit).

![Odebrání vlastního pravidla](media/storage-simulate-failure-ragrs-account-app/figure5.png)

Potom obnovte chod aplikace stisknutím **libovolné klávesy**. Aplikace bude pokračovat ve čtení z primárního koncového bodu, dokud nedosáhne 999 čtení.

![Obnovení chodu aplikace](media/storage-simulate-failure-ragrs-account-app/figure4.png)

# <a name="java-v10-tabjava-v10"></a>[Java v10] (# kartu nebo Java – v10)

Se sadou vlastních pravidel Fiddleru nastavenou v předchozím kroku žádosti na primární koncový bod selžou.

K simulaci obnovení funkce primárního koncového bodu odeberete logiku, která vkládá chybu `503`.

Přejděte do Fiddleru a vyberte **Rules** (Pravidla) a **Customize Rules...** (Přizpůsobit pravidla).  Zakomentujte nebo odeberte vlastní logiku ve funkci `OnBeforeResponse` a ponechejte výchozí funkci.

Uložte změny tak, že vyberete **File** (Soubor) a **Save** (Uložit).

Jakmile budete hotovi, zadejte **G** otestovat stahování. Aplikace budou hlásit, že nyní používá primární kanál znovu.

---

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Simulace selhání pomocí neplatné statické trasy

Pro všechny žádosti na primární koncový bod vašeho účtu [geograficky redundantního úložiště jen pro čtení](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) můžete vytvořit neplatnou statickou trasu. V tomto kurzu se používá místní hostitel jako brána pro směrování žádostí na účet úložiště. Použití místního hostitele jako brány způsobí, že všechny žádosti na primární koncový bod vašeho účtu úložiště se ve smyčce vrátí zpět do hostitele, což následně povede k selhání. Následujícím postupem nasimulujete selhání a obnovení primárního koncového bodu pomocí neplatné statické trasy. 

### <a name="start-and-pause-the-application"></a>Spuštění a pozastavení aplikace

# <a name="net-python-and-java-v7-tabdotnet-python-java-v7"></a>[Rozhraní .NET, Python a Java v7] (# kartu/dotnet-python-java-v7)

Spusťte aplikaci v integrovaném vývojovém prostředí nebo prostředí. Jakmile aplikace zahájí čtení z primárního koncového bodu, pozastavte aplikaci stisknutím **libovolné klávesy** v okně konzoly.

# <a name="java-v10-tabjava-v10"></a>[Java v10] (# kartu nebo Java – v10)

Protože řídíte vzorku, není nutné přerušit účely otestování selhání.

Ujistěte se, že soubor byl odeslán do vašeho účtu úložiště spuštěním ukázky a zadáním **P**.

---

### <a name="simulate-failure"></a>Simulace chyby

Zatímco je aplikace pozastavená, spusťte ve Windows příkazový řádek jako správce. Pokud používáte Linux, spusťte terminál jako root.

Získejte informace o doméně primárního koncového bodu účtu úložiště tak, že zadáte následující příkaz na příkazovém řádku nebo terminálu.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
``` 
 Nahraďte `STORAGEACCOUNTNAME` názvem vašeho účtu úložiště. Zkopírujte IP adresu vašeho účtu úložiště do textového editoru pro pozdější použití.

Pokud chcete získat IP adresu místního hostitele, zadejte `ipconfig` na příkazovém řádku Windows nebo `ifconfig` na terminálu Linuxu. 

Zadáním následujícího příkazu na příkazovém řádku Windows nebo terminálu Linuxu přidáte statickou trasu pro cílového hostitele. 

#### <a name="linux"></a>Linux

`route add <destination_ip> gw <gateway_ip>`

#### <a name="windows"></a>Windows

`route add <destination_ip> <gateway_ip>`

Nahraďte `<destination_ip>` IP adresou vašeho učtu úložiště a `<gateway_ip>` IP adresou místního hostitele.

# <a name="net-python-and-java-v7-tabdotnet-python-java-v7"></a>[Rozhraní .NET, Python a Java v7] (# kartu/dotnet-python-java-v7)

Obnovte chod aplikace stisknutím **libovolné klávesy**.

Jakmile se aplikace znovu spustí, žádosti na primární koncový bod začnou být neúspěšné. Aplikace se pokusí znovu připojit k primárnímu koncovému bodu pětkrát. Po dosažení prahové hodnoty pěti pokusů aplikace zažádá o bitovou kopii ze sekundárního koncového bodu jen pro čtení. Jakmile aplikace dvacetkrát úspěšně načte bitovou kopii ze sekundárního koncového bodu, pokusí se připojit k primárnímu koncovému bodu. Pokud je primární koncový bod stále nedostupný, obnoví aplikace čtení ze sekundárního koncového bodu. Tento model představuje model [Jistič](/azure/architecture/patterns/circuit-breaker) popsaný v předchozím kurzu.

# <a name="java-v10-tabjava-v10"></a>[Java v10] (# kartu nebo Java – v10)

Teď, když jste naši nabídku doplnili o selhání, zadejte **G** na test chybou. Informuje vás, že používá sekundární kanál na rozdíl od primární kanál.

---

### <a name="simulate-primary-endpoint-restoration"></a>Simulovat obnovení primárního koncového bodu

K simulaci obnovení funkce primárního koncového bodu odeberete statickou trasu primárního koncového bodu ze směrovací tabulky. To umožní směrování všech žádostí na primární koncový bod pomocí výchozí brány.

Zadáním následujícího příkazu na příkazovém řádku Windows nebo na terminálu Linuxu odstraníte statickou trasu cílového hostitele, účtu úložiště.

#### <a name="linux"></a>Linux

`route del <destination_ip> gw <gateway_ip>`

#### <a name="windows"></a>Windows

`route delete <destination_ip>`

# <a name="net-python-and-java-v7-tabdotnet-python-java-v7"></a>[Rozhraní .NET, Python a Java v7] (# kartu/dotnet-python-java-v7)

Obnovte chod aplikace stisknutím **libovolné klávesy**. Aplikace bude pokračovat ve čtení z primárního koncového bodu, dokud nedosáhne 999 čtení.

![Obnovení chodu aplikace](media/storage-simulate-failure-ragrs-account-app/figure4.png)


# <a name="java-v10-tabjava-v10"></a>[Java v10] (# kartu nebo Java – v10)

Zadejte **G** otestovat stahování. Aplikace budou hlásit, že nyní používá primární kanál znovu.

![Obnovení chodu aplikace](media/storage-simulate-failure-ragrs-account-app/java-get-pipeline-example-v10.png)

---

## <a name="next-steps"></a>Další postup

Ve druhé části série jste se dozvěděli o simulaci selhání za účelem testování geograficky redundantní úložiště s přístupem pro čtení.

Přečtěte si více o tom, jak úložiště RA-GRS funguje, a také související rizika, najdete v následujícím článku:

> [!div class="nextstepaction"]
> [Navrhování aplikací s vysokou dostupností s využitím RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md