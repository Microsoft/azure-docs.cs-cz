---
title: Správa a aktualizace mezipaměti HPC Azure
description: Jak spravovat a aktualizovat mezipaměť HPC Azure pomocí Azure Portal nebo Azure CLI
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/08/2021
ms.author: v-erkel
ms.openlocfilehash: b34beb65bb8c4136887651d8365c937b17718572
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103471901"
---
# <a name="manage-your-cache"></a>Správa mezipaměti

Stránka s přehledem mezipaměti v Azure Portal zobrazuje podrobnosti projektu, stav mezipaměti a základní statistiky pro vaši mezipaměť. Obsahuje taky ovládací prvky pro zastavení nebo spuštění mezipaměti, odstranění mezipaměti, vyprázdnění dat pro dlouhodobé ukládání a aktualizace softwaru.

Tento článek také vysvětluje, jak provádět tyto základní úlohy pomocí Azure CLI.

Chcete-li otevřít stránku Přehled, vyberte prostředek mezipaměti v Azure Portal. Načtěte například stránku **všechny prostředky** a klikněte na název mezipaměti.

![snímek stránky s přehledem instance mezipaměti HPC v Azure](media/hpc-cache-overview.png)

Tlačítka v horní části stránky vám pomůžou spravovat mezipaměť:

* **Spuštění** a [**zastavení**](#stop-the-cache) – obnoví nebo pozastaví operaci mezipaměti.
* [**Flush**](#flush-cached-data) – zapisuje změněná data do cílů úložiště
* [**Upgrade**](#upgrade-cache-software) – aktualizuje software pro mezipaměť.
* [**Shromažďovat diagnostiku**](#collect-diagnostics) – nahrání informací o ladění
* **Refresh** -znovu načte stránku Přehled.
* [**Odstranit**](#delete-the-cache) – trvale zničí mezipaměť

Přečtěte si další informace o těchto možnostech.

Klikněte na obrázek níže a sledujte [video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) , které ukazuje úlohy správy mezipaměti.

[![Miniatura videa: Azure HPC cache: Manage (kliknutím můžete navštívit stránku video)](media/video-5-manage.png)](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)

## <a name="stop-the-cache"></a>Zastavení mezipaměti

Můžete zastavit mezipaměť a snížit tak náklady během neaktivního období. Za dobu provozu se vám neúčtují při zastavení mezipaměti, ale účtují se vám poplatky za úložiště na disku přidělené mezipamětí. (Podrobnosti najdete na stránce s [cenami](https://aka.ms/hpc-cache-pricing) .)

Zastavená mezipaměť nereaguje na požadavky klientů. Před zastavením mezipaměti byste měli odpojit klienty.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Tlačítko **zastavit** pozastavuje aktivní mezipaměť. Tlačítko **zastavit** je k dispozici, když je stav mezipaměti **v pořádku** nebo je **degradován**.

![snímek obrazovky horních tlačítek se zvýrazněným stopou a automaticky otevíraná zpráva popisující akci zastavení a dotazování chcete pokračovat? s Ano (výchozí) a bez tlačítek](media/stop-cache.png)

Po kliknutí na tlačítko Ano potvrďte zastavení mezipaměti, mezipaměť automaticky vyprázdní svůj obsah do cílů úložiště. Tento proces může nějakou dobu trvat, ale zajišťuje konzistenci dat. Nakonec se stav mezipaměti změní na **Zastaveno**.

Chcete-li znovu aktivovat zastavenou mezipaměť, klikněte na tlačítko **Start** . Není nutné žádné potvrzení.

![snímek obrazovky horních tlačítek se zvýrazněnou možností Start](media/start-cache.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Nastavte Azure CLI pro mezipaměť HPC Azure](./az-cli-prerequisites.md).

Dočasně pozastavíte mezipaměť pomocí příkazu [AZ HPC-cache stop](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-stop) . Tato akce je platná jenom v případě, že stav mezipaměti je **v pořádku** nebo je **degradováný**.

Mezipaměť automaticky vyprázdní svůj obsah do cílů úložiště před jeho zastavením. Tento proces může nějakou dobu trvat, ale zajišťuje konzistenci dat.

Po dokončení akce se stav mezipaměti změní na **Zastaveno**.

Znovu aktivujte zastavenou mezipaměť pomocí [AZ HPC-cache Start](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-start).

Při vystavení příkazu spustit nebo zastavit zobrazí příkazový řádek spuštěnou stavovou zprávu, dokud se operace nedokončí.

```azurecli
$ az hpc-cache start --name doc-cache0629
 - Running ..
```

Po dokončení se zpráva aktualizuje na dokončeno a zobrazí návratové kódy a další informace.

```azurecli
$ az hpc-cache start --name doc-cache0629
{- Finished ..
  "endTime": "2020-07-01T18:46:43.6862478+00:00",
  "name": "c48d320f-f5f5-40ab-8b25-0ac065984f62",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-01T18:40:28.5468983+00:00",
  "status": "Succeeded"
}
```

---

## <a name="flush-cached-data"></a>Vyprázdnit data uložená v mezipaměti

Tlačítko **vyprázdnit** na stránce Přehled oznamuje mezipaměť, aby okamžitě zapisovala všechna změněná data, která jsou uložená v mezipaměti, do cíle úložiště back-endu. Mezipaměť rutinně ukládá data do cílů úložiště, takže není nutné ji provádět ručně, pokud nechcete zajistit, aby byl systém back-end úložiště aktuální. Například můžete použít **vyprázdnit** před pořizováním snímku úložiště nebo kontrolou velikosti sady dat.

> [!NOTE]
> V průběhu procesu vyprázdnění nemůže mezipaměť obsluhovat požadavky klienta. Po dokončení operace je přístup do mezipaměti pozastaven a pokračuje.

Když zahájíte operaci vyprázdnění mezipaměti, mezipaměť zastaví přijímání požadavků klientů a stav mezipaměti na stránce s přehledem se změní na **vyprázdnění**.

Data v mezipaměti se ukládají do příslušných cílů úložiště. V závislosti na tom, kolik dat je potřeba vyprázdnit, může tento proces trvat několik minut nebo déle než hodinu.

Po uložení všech dat do cílů úložiště mezipaměť automaticky začne přebírat požadavky klienta. Stav mezipaměti se vrátí do **stavu v pořádku**.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pokud chcete mezipaměť vyprázdnit, klikněte na tlačítko **vyprázdnit** a potom kliknutím na **Ano** tuto akci potvrďte.

![snímek obrazovky horních tlačítek s zvýrazněnou možností vyprázdnění a automaticky otevíraná okna popisující akci vyprázdnění a dotaz Chcete pokračovat? s Ano (výchozí) a bez tlačítek](media/hpc-cache-flush.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Nastavte Azure CLI pro mezipaměť HPC Azure](./az-cli-prerequisites.md).

Pomocí [AZ HPC-cache flush](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-flush) vynutíte mezipaměť zapisovat všechna změněná data do cílů úložiště.

Příklad:

```azurecli
$ az hpc-cache flush --name doc-cache0629 --resource-group doc-rg
 - Running ..
```

Po dokončení operace vyprázdnění se vrátí zpráva o úspěchu.

```azurecli
{- Finished ..
  "endTime": "2020-07-09T17:26:13.9371983+00:00",
  "name": "c22f8e12-fcf0-49e5-b897-6a6e579b6489",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-09T17:25:21.4278297+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="upgrade-cache-software"></a>Upgrade softwaru pro mezipaměť

Pokud je k dispozici nová verze softwaru, bude tlačítko pro **upgrade** aktivní. V horní části stránky by se měla zobrazit také zpráva o aktualizaci softwaru.

![snímek obrazovky s horním řádkem tlačítek s povoleným tlačítkem upgrade](media/hpc-cache-upgrade-button.png)

Klientský přístup se během upgradu softwaru nepřerušil, ale výkon mezipaměti je pomalý. Naplánujte upgrade softwaru v době nešpičky využití nebo v plánované době údržby.

Aktualizace softwaru může trvat několik hodin. Mezipaměť nakonfigurované s vyšší propustností trvá déle než mezipaměť s menšími hodnotami propustnosti ve špičce.

Pokud je k dispozici upgrade softwaru, budete mít týden, nebo ho budete muset použít ručně. Koncové datum je uvedeno ve zprávě o upgradu. Pokud během této doby upgrade neprovedete, Azure tuto aktualizaci automaticky nainstaluje do mezipaměti. Časování automatického upgradu není možné konfigurovat. Pokud máte obavy o dopad na výkon mezipaměti, měli byste software upgradovat sami ještě předtím, než vyprší časový interval.

Pokud se vaše mezipaměť zastaví po průchodu koncového data, mezipaměť při příštím spuštění automaticky upgraduje software. (Aktualizace se nemusí spustit okamžitě, ale spustí se první hodinu.)

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Kliknutím na tlačítko **upgradovat** zahájíte aktualizaci softwaru. Stav mezipaměti se změní na **upgradovat** , dokud se operace nedokončí.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Nastavte Azure CLI pro mezipaměť HPC Azure](./az-cli-prerequisites.md).

V rozhraní příkazového řádku Azure CLI jsou nové informace o softwaru součástí na konci sestavy o stavu mezipaměti. (K ověření použijte [AZ HPC-cache show](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-show) .) Ve zprávě vyhledejte řetězec "upgradeStatus".

Použijte [AZ HPC-cache upgrade-firmware](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-upgrade-firmware) pro použití aktualizace, pokud existuje.

Pokud není k dispozici žádná aktualizace, tato operace nemá žádný vliv.

Tento příklad ukazuje stav mezipaměti (žádná aktualizace není k dispozici) a výsledky příkazu upgrade-firmware.

```azurecli
$ az hpc-cache show --name doc-cache0629
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },

<...>

  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.61",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-06-29T22:18:32.004822+00:00",
    "pendingFirmwareVersion": null
  }
}
$ az hpc-cache upgrade-firmware --name doc-cache0629
$
```

---

## <a name="collect-diagnostics"></a>Shromažďování diagnostických údajů

Tlačítko **shromáždit diagnostiku** ručně spustí proces shromažďování systémových informací a nahrajte ho do služby a podpory Microsoftu pro řešení potíží. Mezipaměť automaticky shromažďuje a odesílá stejné diagnostické informace, pokud dojde k potížím s vážným problémem v mezipaměti.

Tento ovládací prvek použijte v případě, že ho služba a podpora Microsoftu požaduje.

Po kliknutí na toto tlačítko potvrďte odeslání kliknutím na **Ano** .

![snímek obrazovky s dialogovým potvrzením spuštění kolekce diagnostiky Výchozí tlačítko ' Ano ' je zvýrazněno.](media/diagnostics-confirm.png)

## <a name="delete-the-cache"></a>Odstraní mezipaměť.

Tlačítko **Odstranit** zničí mezipaměť. Když mezipaměť odstraníte, všechny její prostředky se zničí a už se neúčtují poplatky za účet.

Záložní úložné svazky používané jako cíle úložiště nejsou při odstraňování mezipaměti ovlivněny. Později je můžete přidat do budoucí mezipaměti nebo je vyřadit samostatně.

> [!NOTE]
> Mezipaměť HPC Azure nepřed odstraněním mezipaměti automaticky nezapisuje změněná data z mezipaměti do back-endové systémů úložiště.
>
> Chcete-li zajistit, aby byla všechna data v mezipaměti zapsána do dlouhodobého úložiště, [zastavte mezipaměť](#stop-the-cache) před odstraněním. Ujistěte se, že se před odstraněním zobrazuje stav **Zastaveno** .

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Po zastavení mezipaměti kliknutím na tlačítko **Odstranit** trvale odeberete mezipaměť.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Nastavte Azure CLI pro mezipaměť HPC Azure](./az-cli-prerequisites.md).

Pomocí příkazu rozhraní příkazového řádku Azure [AZ HPC-cache Delete](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-delete) trvale odeberte mezipaměť.

Příklad:
```azurecli
$ az hpc-cache delete --name doc-cache0629
 - Running ..

<...>

{- Finished ..
  "endTime": "2020-07-09T22:24:35.1605019+00:00",
  "name": "7d3cd0ba-11b3-4180-8298-d9cafc9f22c1",
  "startTime": "2020-07-09T22:13:32.0732892+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="cache-metrics-and-monitoring"></a>Metriky a monitorování mezipaměti

Stránka Přehled obsahuje grafy pro některé základní statistiky mezipaměti – propustnost mezipaměti, operace za sekundu a latenci.

![snímek obrazovky se třemi spojnicovým grafem ukazující výše uvedenou statistikou pro ukázkovou mezipaměť](media/hpc-cache-overview-stats.png)

Tyto grafy jsou součástí integrovaných nástrojů pro monitorování a analýzu v Azure. Další nástroje a výstrahy jsou k dispozici na stránkách pod hlavičkou **monitorování** na bočním panelu na portálu. Další informace najdete v části portál v [dokumentaci ke službě Azure Monitoring](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="view-warnings"></a>Zobrazit upozornění

Pokud mezipaměť přechází do stavu není v pořádku, podívejte se na stránku s **upozorněními** . Tato stránka zobrazuje oznámení ze softwaru mezipaměti, který vám může porozumět jeho stavu.

Tato oznámení se nezobrazí v protokolu aktivit, protože nejsou ovládána nástrojem Azure Portal. Jsou často spojené s vlastním nastavením, které jste udělali.

Mezi typy upozornění, která se tady můžete zobrazovat, patří:

* Mezipaměť se nemůže připojit k serveru NTP.
* Mezipaměti se nepodařilo stáhnout informace o uživatelském jménu rozšířených skupin.
* Změnila se vlastní nastavení DNS v cíli úložiště.

![snímek obrazovky se stránkou upozornění monitorování > ukazující zprávu, že nelze stáhnout uživatelská jména rozšířených skupin](media/warnings-page.png)

## <a name="next-steps"></a>Další kroky

* Další informace o [metrikách Azure a nástrojích statistiky](../azure-monitor/index.yml)
* Získejte [pomoc s mezipamětí Azure HPC](hpc-cache-support-ticket.md)
