---
title: Simulace selhání při přístupu k redundantnímu úložišti jen pro čtení v Azure | Microsoft Docs
description: Simulace chyby při přístupu ke geograficky redundantnímu úložišti jen pro čtení
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/23/2017
ms.author: tamram
ms.openlocfilehash: 044cc30a418f3c54053a6f4878f97f5c9ea9f9e2
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52335108"
---
# <a name="tutorial-simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Kurz: Simulace selhání při přístupu k redundantnímu úložišti jen pro čtení

Tento kurz je druhá část série.  V tomto kurzu můžete simulovat selhání žádostí na primární koncový bod vašeho účtu [geograficky redundantního úložiště jen pro čtení](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) pomocí [Fiddleru](#simulate-a-failure-with-fiddler) nebo [statického směrování](#simulate-a-failure-with-an-invalid-static-route).

![Aplikace scénáře](media/storage-simulate-failure-ragrs-account-app/scenario.png)

K dokončení tohoto kurzu je nutné dokončit předchozí kurz o úložišti: [Zajištění vysoké dostupnosti dat vaší aplikace pomocí úložiště Azure][previous-tutorial].

Ve druhé části této série se naučíte:

> [!div class="checklist"]
> * Spouštět a pozastavovat aplikaci
> * Simulovat selhání pomocí [fiddleru](#simulate-a-failure-with-fiddler) nebo [neplatné statické trasy](#simulate-a-failure-with-an-invalid-static-route) 
> * Simulovat obnovení primárního koncového bodu


## <a name="prerequisites"></a>Požadavky

K simulaci selhání pomocí Fiddleru budete potřebovat: 

* Stáhnout a [nainstalovat Fiddler](https://www.telerik.com/download/fiddler)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="simulate-a-failure-with-fiddler"></a>Simulace selhání pomocí Fiddleru

Simulaci selhání pomocí Fiddleru provedete tak, že pro žádosti na primární koncový bod vašeho účtu úložiště RA-GRS vložíte neúspěšnou odpověď, a tím nasimulujete selhání.

Následujícím postupem nasimulujete selhání a obnovení primárního koncového bodu pomoc fiddleru.

### <a name="launch-fiddler"></a>Spuštění fiddleru

Otevřete Fiddler, vyberte **Rules** (Pravidla) a **Customize Rules** (Přizpůsobit pravidla).

![Přizpůsobení pravidel Fiddleru](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Spustí se Fiddler ScriptEditor zobrazující soubor **SampleRules.js**. Tento soubor slouží k přizpůsobení Fiddleru. Vložte následující vzorový kód do funkce `OnBeforeResponse`. Nový kód je zakomentovaný, aby se logika, kterou vytváří, neimplementovala okamžitě. Po dokončení uložte provedené změny tak, že vyberete **File** (Soubor) a **Save** (Uložit).

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

### <a name="start-and-pause-the-application"></a>Spuštění a pozastavení aplikace

Spusťte aplikaci ve vašem integrovaném vývojovém prostředí (IDE) nebo textovém editoru. Jakmile aplikace zahájí čtení z primárního koncového bodu, pozastavte aplikaci stisknutím **libovolné klávesy** v okně konzoly.

### <a name="simulate-failure"></a>Simulace chyby

Teď, když je aplikace pozastavena, můžete odkomentovat vlastní pravidlo, které jsme uložili ve Fiddleru v předchozím kroku. Vzorový kód vyhledá žádosti na účet úložiště RA-GRS, a pokud bude cesta obsahovat název bitové kopie, `HelloWorld`, vrátí kód odpovědi `503 - Service Unavailable`.

Přejděte do Fiddleru a vyberte **Rules** (Pravidla)  -> **Customize Rules...**  (Přizpůsobit pravidla).  Odkomentujte následující řádky, nahraďte `STORAGEACCOUNTNAME` názvem vašeho účtu úložiště. Uložte změny tak, že vyberete **File** (Soubor)  -> **Save** (Uložit). 

> [!NOTE]
> Pokud používáte ukázkovou aplikaci v systému Linux, musíte restartovat Fiddler vždy, když upravíte soubor **CustomRule.js**, aby Fiddler vlastní logiku nainstaloval. 
> 
> 


```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

Obnovte chod aplikace stisknutím **libovolné klávesy**.

Jakmile se aplikace znovu spustí, žádosti na primární koncový bod začnou být neúspěšné. Aplikace se pokusí znovu připojit k primárnímu koncovému bodu pětkrát. Po dosažení prahové hodnoty pěti pokusů aplikace zažádá o bitovou kopii ze sekundárního koncového bodu jen pro čtení. Jakmile aplikace dvacetkrát úspěšně načte bitovou kopii ze sekundárního koncového bodu, pokusí se připojit k primárnímu koncovému bodu. Pokud je primární koncový bod stále nedostupný, obnoví aplikace čtení ze sekundárního koncového bodu. Tento model představuje model [Jistič](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) popsaný v předchozím kurzu.

![Vložení vlastního pravidla](media/storage-simulate-failure-ragrs-account-app/figure3.png)

### <a name="simulate-primary-endpoint-restoration"></a>Simulovat obnovení primárního koncového bodu

Se sadou vlastních pravidel Fiddleru nastavenou v předchozím kroku žádosti na primární koncový bod selžou. K simulaci obnovení funkce primárního koncového bodu odeberete logiku, která vkládá chybu `503`.

Pozastavte aplikaci stisknutím **libovolné klávesy**.

Přejděte do Fiddleru a vyberte **Rules** (Pravidla) a **Customize Rules...** (Přizpůsobit pravidla).  Zakomentujte nebo odeberte vlastní logiku ve funkci `OnBeforeResponse` a ponechejte výchozí funkci. Uložte změny tak, že vyberete **File** (Soubor) a **Save** (Uložit).

![Odebrání vlastního pravidla](media/storage-simulate-failure-ragrs-account-app/figure5.png)

Potom obnovte chod aplikace stisknutím **libovolné klávesy**. Aplikace bude pokračovat ve čtení z primárního koncového bodu, dokud nedosáhne 999 čtení.

![Obnovení chodu aplikace](media/storage-simulate-failure-ragrs-account-app/figure4.png)


## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Simulace selhání pomocí neplatné statické trasy 
Pro všechny žádosti na primární koncový bod vašeho účtu [geograficky redundantního úložiště jen pro čtení](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) můžete vytvořit neplatnou statickou trasu. V tomto kurzu se používá místní hostitel jako brána pro směrování žádostí na účet úložiště. Použití místního hostitele jako brány způsobí, že všechny žádosti na primární koncový bod vašeho účtu úložiště se ve smyčce vrátí zpět do hostitele, což následně povede k selhání. Následujícím postupem nasimulujete selhání a obnovení primárního koncového bodu pomocí neplatné statické trasy. 

### <a name="start-and-pause-the-application"></a>Spuštění a pozastavení aplikace

Spusťte aplikaci ve vašem integrovaném vývojovém prostředí (IDE) nebo textovém editoru. Jakmile aplikace zahájí čtení z primárního koncového bodu, pozastavte aplikaci stisknutím **libovolné klávesy** v okně konzoly. 

### <a name="simulate-failure"></a>Simulace chyby

Zatímco je aplikace pozastavená, spusťte ve Windows příkazový řádek jako správce. Pokud používáte Linux, spusťte terminál jako root. Zadáním následujícího příkazu na příkazovém řádku nebo v terminálu získáte informace o doméně primárního koncového bodu účtu úložiště.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
``` 
 Nahraďte `STORAGEACCOUNTNAME` názvem vašeho účtu úložiště. Zkopírujte IP adresu vašeho účtu úložiště do textového editoru pro pozdější použití. Pokud chcete získat IP adresu místního hostitele, zadejte `ipconfig` na příkazovém řádku Windows nebo `ifconfig` na terminálu Linuxu. 

Zadáním následujícího příkazu na příkazovém řádku Windows nebo terminálu Linuxu přidáte statickou trasu pro cílového hostitele. 


# <a name="linuxtablinux"></a>[Linux](#tab/linux)

  route add <destination_ip> gw <gateway_ip>

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

  route add <destination_ip> <gateway_ip>

---
 
Nahraďte `<destination_ip>` IP adresou vašeho učtu úložiště a `<gateway_ip>` IP adresou místního hostitele. Obnovte chod aplikace stisknutím **libovolné klávesy**.

Jakmile se aplikace znovu spustí, žádosti na primární koncový bod začnou být neúspěšné. Aplikace se pokusí znovu připojit k primárnímu koncovému bodu pětkrát. Po dosažení prahové hodnoty pěti pokusů aplikace zažádá o bitovou kopii ze sekundárního koncového bodu jen pro čtení. Jakmile aplikace dvacetkrát úspěšně načte bitovou kopii ze sekundárního koncového bodu, pokusí se připojit k primárnímu koncovému bodu. Pokud je primární koncový bod stále nedostupný, obnoví aplikace čtení ze sekundárního koncového bodu. Tento model představuje model [Jistič](/azure/architecture/patterns/circuit-breaker) popsaný v předchozím kurzu.

### <a name="simulate-primary-endpoint-restoration"></a>Simulovat obnovení primárního koncového bodu

K simulaci obnovení funkce primárního koncového bodu odeberete statickou trasu primárního koncového bodu ze směrovací tabulky. To umožní směrování všech žádostí na primární koncový bod pomocí výchozí brány. 

Zadáním následujícího příkazu na příkazovém řádku Windows nebo na terminálu Linuxu odstraníte statickou trasu cílového hostitele, účtu úložiště. 
 
# <a name="linuxtablinux"></a>[Linux](#tab/linux)

route del <destination_ip> gw <gateway_ip>

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

route delete <destination_ip>

---

Obnovte chod aplikace stisknutím **libovolné klávesy**. Aplikace bude pokračovat ve čtení z primárního koncového bodu, dokud nedosáhne 999 čtení.

![Obnovení chodu aplikace](media/storage-simulate-failure-ragrs-account-app/figure4.png)


## <a name="next-steps"></a>Další postup

Ve druhé části série jste získali informace o simulaci selhání za účelem testování geograficky redundantního úložiště s přístupem pro čtení. Například jste se dozvěděli, jak:

> [!div class="checklist"]
> * Spouštět a pozastavovat aplikaci
> * Simulovat selhání pomocí [fiddleru](#simulate-a-failure-with-fiddler) nebo [neplatné statické trasy](#simulate-a-failure-with-an-invalid-static-route) 
> * Simulovat obnovení primárního koncového bodu

Další informace o tom, jak úložiště RA-GRS funguje (a o souvisejících rizicích), najdete v následujícím článku.

> [!div class="nextstepaction"]
> [Navrhování aplikací s vysokou dostupností s využitím RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md