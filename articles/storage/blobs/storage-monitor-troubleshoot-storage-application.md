---
title: Monitorování a řešení potíží s aplikací cloudového úložiště v Azure | Microsoft Docs
description: Použijte diagnostické nástroje, metriky a upozornění k monitorování a řešení potíží s cloudovou aplikací.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: web
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: eb58104309802125a8424cbbf8a1bef3d1c5e79c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31418182"
---
# <a name="monitor-and-troubleshoot-a-cloud-storage-application"></a>Monitorování a řešení potíží s aplikací cloudového úložiště

Tento kurz je čtvrtou a poslední částí série. Zjistíte, jak monitorovat a řešit potíže s aplikací cloudového úložiště.

Ve čtvrté části této série se naučíte:

> [!div class="checklist"]
> * Zapnutí protokolování a metrik
> * Povolení upozornění na chyby autorizace
> * Spuštění testovacího provozu s nesprávnými tokeny SAS
> * Stažení a analýza protokolů

[Analýza úložiště Azure](../common/storage-analytics.md) poskytuje data metrik a protokolování pro účet úložiště. Tato data poskytují přehled o stavu účtu úložiště. Abyste získali přehled o svém účtu úložiště, musíte nastavit shromažďování dat. Tento proces zahrnuje zapnutí protokolování, konfiguraci metrik a povolení upozornění.

Protokolování a metriky účtů úložiště se zapínají na kartě **Diagnostika** na webu Azure Portal. Existují dva typy metrik. Metriky **agregace** shromažďují informace o příchozím a výchozím přenosu dat, dostupnosti, latenci a procentuální úspěšnosti. Tyto metriky se agregují pro služby objektů blob, front, tabulek a souborů. Metriky **jednotlivých rozhraní API** shromažďují stejnou sadu metrik pro každou operaci úložiště v rozhraní API služby Azure Storage. Protokolování úložiště umožňuje zaznamenávat podrobnosti o úspěšných i neúspěšných požadavcích v účtu úložiště. V těchto protokolech můžete zobrazit podrobnosti o operacích čtení, zápisu a odstranění provedených s vašimi tabulkami, frontami a objekty blob v Azure. Můžete z nich také zjistit důvody neúspěšných požadavků, jako jsou časové limity, omezování nebo chyby autorizace.

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal

Přihlaste se k portálu [Azure Portal](https://portal.azure.com).

## <a name="turn-on-logging-and-metrics"></a>Zapnutí protokolování a metrik

V nabídce vlevo vyberte **Skupiny prostředků**, pak **myResourceGroup** a v seznamu prostředků pak vyberte svůj účet úložiště.

V části **Diagnostika** nastavte **Stav** na hodnotu **Zapnuto**. Přesvědčte se, že všechny možnosti v části **Vlastnosti objektu blob** jsou povolené.

Jakmile budete hotovi, klikněte na **Uložit**.

![Podokno Diagnostika](media/storage-monitor-troubleshoot-storage-application/contoso.png)

## <a name="enable-alerts"></a>Povolení upozornění

Upozornění představují způsob, jak na základě prahové hodnoty nebo porušení metriky odeslat e-mail správcům nebo aktivovat webhook. V tomto příkladě povolíte upozornění pro metriku `SASClientOtherError`.

### <a name="navigate-to-the-storage-account-in-the-azure-portal"></a>Přechod k účtu úložiště na webu Azure Portal

V nabídce vlevo vyberte **Skupiny prostředků**, pak **myResourceGroup** a v seznamu prostředků pak vyberte svůj účet úložiště.

V části **Monitorování** vyberte **Pravidla upozornění**.

Vyberte **+ Přidat upozornění** a v části **Přidat pravidlo upozornění** vyplňte požadované informace. Z rozevírací nabídky **Metrika** zvolte `SASClientOtherError`.

![Podokno Diagnostika](media/storage-monitor-troubleshoot-storage-application/figure2.png)

## <a name="simulate-an-error"></a>Simulace chyby

Pokud chcete simulovat platné upozornění, můžete z účtu úložiště zkusit vyžádat neexistující objekt blob. Provedete to tak, že nahradíte hodnotu `<incorrect-blob-name>` neexistující hodnotou. Několikrát spusťte následující vzorový kód a simulujte neúspěšné požadavky na objekt blob.

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <storage-account-name> \
    --account-key <storage-account-key> \
    --container-name <container> \
    --name <incorrect-blob-name> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d` \
    --output tsv)

curl https://<storage-account-name>.blob.core.windows.net/<container>/<incorrect-blob-name>?$sasToken
```

Následující obrázek představuje příklad upozornění založeného na simulované chybě z předchozího příkladu.

 ![Příklad upozornění](media/storage-monitor-troubleshoot-storage-application/alert.png)

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