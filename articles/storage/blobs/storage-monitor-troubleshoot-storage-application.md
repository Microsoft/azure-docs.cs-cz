---
title: Monitorování a řešení potíží s aplikací cloudového úložiště v Azure | Microsoft Docs
description: Použijte diagnostické nástroje, metriky a upozornění k monitorování a řešení potíží s cloudovou aplikací.
services: storage
author: tamram
manager: twooley
ms.service: storage
ms.workload: web
ms.topic: tutorial
ms.date: 07/20/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: ad64384ff17b1666f88ba99e04ec345015e07276
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39206050"
---
# <a name="monitor-and-troubleshoot-a-cloud-storage-application"></a>Monitorování a řešení potíží s aplikací cloudového úložiště

Tento kurz je čtvrtou a poslední částí série. Zjistíte, jak monitorovat a řešit potíže s aplikací cloudového úložiště.

Ve čtvrté části této série se naučíte:

> [!div class="checklist"]
> * Zapnutí protokolování a metrik
> * Povolení upozornění na chyby autorizace
> * Spuštění testovacího provozu s nesprávnými tokeny SAS
> * Stažení a analýza protokolů

[Analýza úložiště Azure](../common/storage-analytics.md) poskytuje data metrik a protokolování pro účet úložiště. Tato data poskytují přehled o stavu účtu úložiště. Pokud chcete shromažďovat data analýz služby Azure Storage, můžete nakonfigurovat protokolování, metriky a upozornění. Tento proces zahrnuje zapnutí protokolování, konfiguraci metrik a povolení upozornění.

Protokolování a metriky účtů úložiště se zapínají na kartě **Diagnostika** na webu Azure Portal. Protokolování úložiště umožňuje zaznamenávat podrobnosti o úspěšných i neúspěšných požadavcích v účtu úložiště. V těchto protokolech můžete zobrazit podrobnosti o operacích čtení, zápisu a odstranění provedených s vašimi tabulkami, frontami a objekty blob v Azure. Můžete z nich také zjistit důvody neúspěšných požadavků, jako jsou časové limity, omezování nebo chyby autorizace.

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal

Přihlaste se k portálu [Azure Portal](https://portal.azure.com).

## <a name="turn-on-logging-and-metrics"></a>Zapnutí protokolování a metrik

V nabídce vlevo vyberte **Skupiny prostředků**, pak **myResourceGroup** a v seznamu prostředků pak vyberte svůj účet úložiště.

V části **Nastavení diagnostiky (klasické)** u položky **Stav** vyberte **Zapnuto**. Přesvědčte se, že všechny možnosti v části **Vlastnosti objektu blob** jsou povolené.

Jakmile budete hotovi, klikněte na **Uložit**.

![Podokno Diagnostika](media/storage-monitor-troubleshoot-storage-application/enable-diagnostics.png)

## <a name="enable-alerts"></a>Povolení upozornění

Upozornění představují způsob, jak na základě prahové hodnoty nebo porušení metriky odeslat e-mail správcům nebo aktivovat webhook. V tomto příkladě povolíte upozornění pro metriku `SASClientOtherError`.

### <a name="navigate-to-the-storage-account-in-the-azure-portal"></a>Přechod k účtu úložiště na webu Azure Portal

V části **Monitorování** vyberte **Upozornění (klasická)**.

Vyberte **Přidat upozornění metriky (klasické)** a zadejte požadované údaje do formuláře **Přidat pravidlo**. V rozevíracím seznamu **Metrika** vyberte `SASClientOtherError`. Pokud chcete, aby se upozornění aktivovalo při první chybě, v rozevíracím seznamu **Podmínka** vyberte **Větší než nebo rovno**.

![Podokno Diagnostika](media/storage-monitor-troubleshoot-storage-application/add-alert-rule.png)

## <a name="simulate-an-error"></a>Simulace chyby

Pokud chcete simulovat platné upozornění, můžete z účtu úložiště zkusit vyžádat neexistující objekt blob. Následující příkaz vyžaduje název kontejneru úložiště. Můžete buď použít název existujícího kontejneru, nebo vytvořit nový kontejner pro účely tohoto příkladu.

Nahraďte zástupný text skutečnými hodnotami (ujistěte se, že je položka `<INCORRECT_BLOB_NAME>` nastavená na hodnotu, která neexistuje) a spusťte příkaz.

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <STORAGE_ACCOUNT_NAME> \
    --account-key <STORAGE_ACCOUNT_KEY> \
    --container-name <CONTAINER_NAME> \
    --name <INCORRECT_BLOB_NAME> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d`)

curl https://<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/<CONTAINER_NAME>/<INCORRECT_BLOB_NAME>?$sasToken
```

Následující obrázek představuje příklad upozornění založeného na simulované chybě z předchozího příkladu.

 ![Příklad upozornění](media/storage-monitor-troubleshoot-storage-application/email-alert.png)

## <a name="download-and-view-logs"></a>Stažení a zobrazení protokolů

Protokoly úložiště ukládají data ve vašem účtu úložiště v sadě objektů blob v kontejneru objektů blob s názvem **$logs**. Tento kontejner se nezobrazí při výpisu všech kontejnerů objektů blob ve vašem účtu, ale pokud k němu přistoupíte přímo, můžete zobrazit jeho obsah.

V tomto scénáři použijete k práci se svým účtem úložiště Azure aplikaci [Microsoft Message Analyzer](http://technet.microsoft.com/library/jj649776.aspx).

### <a name="download-microsoft-message-analyzer"></a>Stažení aplikace Microsoft Message Analyzer

Stáhněte a nainstalujte aplikaci [Microsoft Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226).

Spusťte aplikaci a zvolte **File** (Soubor) > **Open** (Otevřít) > **From Other File Sources** (Z jiných zdrojů souborů).

V dialogovém okně **File Selector** (Selektor souborů) vyberte **+ Add Azure Connection** (+ Přidat připojení k Azure). Zadejte **název účtu úložiště** a **klíč účtu** a pak klikněte na **OK**.

![Microsoft Message Analyzer – Dialogové okno Add Azure Storage Connection (Přidat připojení k účtu úložiště Azure)](media/storage-monitor-troubleshoot-storage-application/figure3.png)

Po připojení rozbalte kontejnery ve stromovém zobrazení úložiště a zobrazte objekty blob protokolů. Vyberte nejnovější protokol a klikněte na **OK**.

![Microsoft Message Analyzer – Dialogové okno Add Azure Storage Connection (Přidat připojení k účtu úložiště Azure)](media/storage-monitor-troubleshoot-storage-application/figure4.png)

V dialogovém okně **New Session** (Nová relace) kliknutím na **Start** (Spustit) zobrazte protokol.

Po otevření protokolu můžete zobrazit události úložiště. Jak je vidět z následujícího obrázku, v účtu úložiště se aktivovala událost `SASClientOtherError`. Další informace o protokolování úložiště najdete v tématu [Analýza úložiště](../common/storage-analytics.md).

![Microsoft Message Analyzer – Zobrazení událostí](media/storage-monitor-troubleshoot-storage-application/figure5.png)

Dalším nástrojem, který můžete použít k práci se svým účtem úložiště včetně kontejneru **$logs** a protokolů, které obsahuje, je [Průzkumník služby Storage](https://azure.microsoft.com/features/storage-explorer/).

## <a name="next-steps"></a>Další kroky

Ve čtvrté a poslední části série jste zjistili, jak monitorovat a řešit potíže se svým účtem úložiště, a naučili jste se například:

> [!div class="checklist"]
> * Zapnutí protokolování a metrik
> * Povolení upozornění na chyby autorizace
> * Spuštění testovacího provozu s nesprávnými tokeny SAS
> * Stažení a analýza protokolů

Pod tímto odkazem najdete ukázky předdefinovaných úložišť.

> [!div class="nextstepaction"]
> [Ukázky skriptů úložiště Azure](storage-samples-blobs-cli.md)