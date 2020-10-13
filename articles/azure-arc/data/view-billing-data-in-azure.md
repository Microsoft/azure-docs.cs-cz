---
title: Nahrání fakturačních dat do Azure a jejich zobrazení v Azure Portal
description: Nahrání fakturačních dat do Azure a jejich zobrazení v Azure Portal
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 55269b45159210eec2ec7a6dd8eaea661ff13ebd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91760302"
---
# <a name="upload-billing-data-to-azure-and-view-it-in-the-azure-portal"></a>Nahrání fakturačních dat do Azure a jejich zobrazení v Azure Portal

> [!IMPORTANT] 
>  V období Preview se za použití datových služeb s podporou Azure ARC neúčtují žádné náklady. I když fakturační systém funguje na konci, je měřič účtování nastavený na $0.  Pokud budete postupovat podle tohoto scénáře, zobrazí se položky ve vaší fakturaci za službu, která je aktuálně pojmenována do **služby Hybrid Data Services** , a pro prostředky typu s názvem **Microsoft. AzureData/ `<resource type>` **. Budete moct zobrazit záznam pro každou datovou službu – Azure ARC, který vytvoříte, ale každý záznam se bude účtovat $0.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connectivity-modes---implications-for-billing-data"></a>Režimy připojení – důsledky pro fakturační data

V budoucnu budou existovat dva režimy, ve kterých můžete spustit datové služby s podporou ARC Azure:

- **Nepřímo připojeno** – neexistuje žádné přímé připojení k Azure. Data se odesílají do Azure jenom prostřednictvím procesu exportu nebo nahrání. Všechna nasazení datových služeb Azure ARC v tomto režimu fungují ještě dnes ve verzi Preview.
- **Přímo připojeno** – v tomto režimu bude existovat závislost na službě Kubernetes s povoleným ARC Azure, která poskytuje přímé propojení mezi Azure a clusterem Kubernetes, na kterém běží datové služby s podporou ARC Azure. Tím se povolí více možností a také vám umožní používat Azure Portal a Azure CLI ke správě datových služeb s podporou ARC Azure, stejně jako při správě datových služeb v Azure PaaS.  Tento režim připojení ještě není dostupný ve verzi Preview, ale brzo se připravuje.

Můžete si přečíst další informace o rozdílech mezi [režimy připojení](https://docs.microsoft.com/azure/azure-arc/data/connectivity).

V nepřímém připojeném režimu se fakturovaná data pravidelně exportují z řadiče dat ARC Azure na zabezpečený soubor a pak se nahrály do Azure a zpracovaly.  V nadcházejícím připojeném režimu se fakturační data automaticky odešlou do Azure přibližně 1 hodina, aby se vám v reálném čase zobrazila cena za vaše služby. Proces exportu a nahrávání dat v nepřímém připojeném režimu je také možné automatizovat pomocí skriptů nebo můžeme vytvořit službu, která to provede za vás.

## <a name="upload-billing-data-to-azure"></a>Nahrání fakturačních dat do Azure

Chcete-li odeslat fakturační data do Azure, je třeba nejprve provést následující:

1. Vytvořte datovou službu s povoleným ARC Azure, pokud ji ještě nemáte. Můžete například vytvořit jednu z následujících možností:
   - [Vytvoření spravované instance Azure SQL v Arc Azure](create-sql-managed-instance.md)
   - [Vytvoření skupiny serverů PostgreSQL Hyperscale s podporou služby Azure Arc](create-postgresql-hyperscale-server-group.md)
1. Pokud jste to ještě neudělali [, nahrajte inventář prostředků, data o využití, metriky a protokoly Azure monitor](upload-metrics-and-logs-to-azure-monitor.md) .
1. Počkejte alespoň 2 hodiny od vytvoření datové služby, aby proces kolekce telemetrie fakturace mohl shromažďovat některá fakturační data.

Chcete-li exportovat fakturační údaje, spusťte následující příkaz:

```console
azdata arc dc export -t usage -p usage.json
```

V současné době není soubor zašifrovaný, takže můžete zobrazit jeho obsah. Otevřete v textovém editoru a podívejte se, jak obsah vypadá.

Zjistíte, že existují dvě sady dat: `resources` a `data` . `resources`Jsou to řadič dat, PostgreSQL skupiny serverů s škálovatelným škálováním a spravované instance SQL. `resources`Záznamy v datech zachytí příslušné události v historii prostředku – při jejím vytvoření, při aktualizaci a při jejím odstranění. `data`Záznamy zachytí, kolik jader bylo k dispozici pro danou instanci pro každou hodinu.

Příklad `resource` položky:

```console
    {
        "customObjectName": "<resource type>-2020-29-5-23-13-17-164711",
        "uid": "4bc3dc6b-9148-4c7a-b7dc-01afc1ef5373",
        "instanceName": "sqlInstance001",
        "instanceNamespace": "arc",
        "instanceType": "<resource>",
        "location": "eastus",
        "resourceGroupName": "production-resources",
        "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
        "isDeleted": false,
        "externalEndpoint": "32.191.39.83:1433",
        "vCores": "2",
        "createTimestamp": "05/29/2020 23:13:17",
        "updateTimestamp": "05/29/2020 23:13:17"
    }
```

Příklad `data` položky:

```console
        {
          "requestType": "usageUpload",
          "clusterId": "4b0917dd-e003-480e-ae74-1a8bb5e36b5d",
          "name": "DataControllerTestName",
          "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
          "resourceGroup": "production-resources",
          "location": "eastus",
          "uploadRequest": {
            "exportType": "usages",
            "dataTimestamp": "2020-06-17T22:32:24Z",
            "data": "[{\"name\":\"sqlInstance001\",
                       \"namespace\":\"arc\",
                       \"type\":\"<resource type>\",
                       \"eventSequence\":1, 
                       \"eventId\":\"50DF90E8-FC2C-4BBF-B245-CB20DC97FF24\",
                       \"startTime\":\"2020-06-17T19:11:47.7533333\",
                       \"endTime\":\"2020-06-17T19:59:00\",
                       \"quantity\":1,
                       \"id\":\"4BC3DC6B-9148-4C7A-B7DC-01AFC1EF5373\"}]",
           "signature":"MIIE7gYJKoZIhvcNAQ...2xXqkK"
          }
        }
```

Spuštěním následujícího příkazu nahrajte usage.jsdo souboru do Azure:

```console
azdata arc dc upload -p usage.json
```

## <a name="view-billing-data-in-azure-portal"></a>Zobrazit fakturační data v Azure Portal

Pomocí těchto kroků můžete zobrazit fakturační údaje v Azure Portal:

1. Otevřete Azure Portal pomocí speciální adresy URL:  [https://aka.ms/arcdata](https://aka.ms/arcdata) .
1. Do pole Hledat v horní části obrazovky zadejte **cost management** a klikněte na službu cost management.
1. Na levé straně klikněte na kartu **Analýza nákladů** .
1. Klikněte na tlačítko **náklady podle prostředku** v horní části zobrazení.
1. Ujistěte se, že je váš obor nastavený na předplatné, ve kterém se vytvořily prostředky datové služby.
1. Vyberte **náklady podle prostředku** v rozevíracím seznamu zobrazení vedle volič oboru v horní části zobrazení.
1. Ujistěte se, že je filtr data nastavený na **Tento měsíc** nebo nějaký jiný časový rozsah, který dává smysl při vytváření prostředků datové služby.
1. Klikněte na **Přidat filtr** a přidejte filtr podle **typu prostředku** ,  =  `microsoft.azuredata/<data service type>` Pokud chcete filtrovat dolů pouze jeden typ datové služby s podporou ARC Azure.
1. Teď se zobrazí seznam všech prostředků, které se vytvořily a nahrály do Azure. Vzhledem k tomu, že je měřič pro účtování $0, vidíte, že náklady budou vždy $0.

## <a name="download-billing-data"></a>Stažení fakturačních údajů

Souhrnná data fakturace si můžete stáhnout přímo z Azure Portal.

1. Ve stejné **analýze nákladů – > zobrazení podle typu prostředku** , ke kterému jste se dorazili podle pokynů uvedených výše, klikněte na tlačítko Stáhnout v horní části.
1. Vyberte typ souboru ke stažení – Excel nebo CSV a klikněte na tlačítko **stáhnout data** .
1. Otevřete soubor ve vhodném editoru, který daný typ souboru vybral.

## <a name="export-billing-data"></a>Exportovat fakturační data

Můžete také pravidelně automaticky exportovat **podrobné** údaje o využití a fakturaci do kontejneru Azure Storage, a to vytvořením úlohy exportu fakturace. To je užitečné, pokud chcete zobrazit podrobnosti o fakturaci, jako je například počet hodin, po které se daná instance účtuje ve fakturačním období.

Pomocí těchto kroků nastavte úlohu exportu fakturace:

1. Na levé straně klikněte na Exportovat.
1. Klikněte na Přidat.
1. Zadejte název a četnost exportu a klikněte na další.
1. Zvolte buď vytvoření nového účtu úložiště, nebo vytvořte nový, a vyplňte formulář a zadejte účet úložiště, kontejner a cestu k adresáři, do kterého chcete exportovat soubory s fakturačními daty, a klikněte na další.
1. Klikněte na Vytvořit.

Soubory exportu fakturačních dat budou k dispozici přibližně po 4 hodinách a budou exportovány podle plánu, který jste zadali při vytváření úlohy exportu fakturace.

Pomocí těchto kroků zobrazíte exportované soubory s fakturačními daty:

Můžete ověřit soubory s fakturačními daty v Azure Portal. 

> [!IMPORTANT]
> Po vytvoření úlohy exportu fakturace počkejte 4 hodiny, než budete pokračovat v následujících krocích.

1. Do vyhledávacího pole v horní části portálu zadejte **účty úložiště** a klikněte na **účty úložiště**.
3. Klikněte na účet úložiště, který jste zadali při vytváření úlohy exportu fakturace výše.
4. Klikněte na kontejnery vlevo.
5. Klikněte na kontejner, který jste zadali při vytváření úlohy exportu fakturace výše.
6. Klikněte na složku, kterou jste zadali při vytváření úlohy exportu fakturace výše.
7. Přejděte k podrobnostem o vygenerovaných složkách a souborech a klikněte na jeden ze vygenerovaných souborů. csv.
8. Klikněte na tlačítko Stáhnout, které uloží soubor do místní složky Stažené soubory.
9. Otevřete soubor pomocí prohlížeče souborů. csv, jako je Excel.
10. Vyfiltruje výsledky tak, aby se zobrazily pouze řádky s **typem prostředku**  =  `Microsoft.AzureData/<data service resource type` .
11. Zobrazí se počet hodin, po které se instance použila v aktuální 24hodinové období ve sloupci UsageQuantity.
