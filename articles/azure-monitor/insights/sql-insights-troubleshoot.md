---
title: Řešení potíží s SQL Insights (Preview)
description: Řešení potíží s SQL Insights v Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2021
ms.openlocfilehash: 85a3505dd347b96036c28c85c089afa04e3e3bd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609376"
---
# <a name="troubleshooting-sql-insights-preview"></a>Řešení potíží s SQL Insights (Preview)
Pokud chcete řešit problémy se shromažďováním dat v SQL Insights, Projděte si stav monitorovacího počítače na kartě **Spravovat profil** . Tato akce bude mít jeden z následujících stavů:

- Sběr 
- Neshromažďovat 
- Shromažďování s chybami 
 
Kliknutím na **stav** přejdete k podrobnostem a zobrazíte protokoly a další podrobnosti, které vám můžou problém vyřešit. 

:::image type="content" source="media/sql-insights-enable/monitoring-machine-status.png" alt-text="Monitorování stavu počítače":::

## <a name="not-collecting-state"></a>Stav není shromažďování 
Monitorovací počítač má stav *není shromážděn* , pokud v *InsightsMetrics* pro SQL neexistují žádná data za posledních 10 minut. 

SQL Insights k načtení těchto informací používá následující dotaz:

```
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(10m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

Ověřte, jestli neexistují žádné protokoly z telegraf, které pomáhají identifikovat hlavní příčinu problému. Pokud jsou k dispozici položky protokolu, můžete kliknout na *Neshromažďovat* a ověřit informace o protokolech a řešení problémů pro běžné problémy. 


Pokud neexistují žádné protokoly, je nutné ověřit protokoly na virtuálním počítači monitorování pro následující služby nainstalované dvěma rozšířeními virtuálního počítače:

- Microsoft. Azure. monitor. AzureMonitorLinuxAgent 
  - Služba: MDSD 
- Microsoft. Azure. monitor. rezátěžes. reWLILinuxExtension 
  - Služba: WLI 
  - Služba: MS-telegraf 
  - Služba: TD-agent-bit-WLI 
  - Protokol rozšíření pro kontrolu selhání instalace:/var/log/azure/Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension/wlilogs.log 



### <a name="wli-service-logs"></a>protokoly služby WLI 

Protokoly služby: `/var/log/wli.log`

Zobrazení posledních protokolů: `tail -n 100 -f /var/log/wli.log`
 

Pokud se zobrazí následující protokol chyb, znamená to, že se jedná o problém se službou **MDSD** .

```
2021-01-27T06:09:28Z [Error] Failed to get config data. Error message: dial unix /var/run/mdsd/default_fluent.socket: connect: no such file or directory 
```


### <a name="telegraf-service-logs"></a>Protokoly služby telegraf 

Protokoly služby: `/var/log/ms-telegraf/telegraf.log`

Chcete-li zobrazit nedávné protokoly: `tail -n 100 -f /var/log/ms-telegraf/telegraf.log` Chcete-li zobrazit nedávné protokoly chyb a upozornění: `tail -n 1000 /var/log/ms-telegraf/telegraf.log | grep "E\!\|W!"`

 Konfigurace, kterou používá telegraf, je generována službou WLI a umístěna v: `/etc/ms-telegraf/telegraf.d/wli`
 
Pokud je vygenerovaná Chybná konfigurace, služba MS-telegraf se nemusí spustit, ověřte, jestli je služba MS-telegraf spuštěná s příkazem: `service ms-telegraf status`

Chcete-li zobrazit chybové zprávy ze služby telegraf, spusťte ji ručně pomocí následujícího příkazu: 

```
/usr/bin/ms-telegraf --config /etc/ms-telegraf/telegraf.conf --config-directory /etc/ms-telegraf/telegraf.d/wli --test 
```

### <a name="mdsd-service-logs"></a>protokoly služby MDSD 

Ověřte [aktuální omezení](../agents/azure-monitor-agent-overview.md#current-limitations) pro agenta Azure monitor. 


Protokoly služby:  
- `/var/log/mdsd.err`
- `/var/log/mdsd.warn`
- `/var/log/mdsd.info`

Chcete-li zobrazit nedávné chyby: `tail -n 100 -f /var/log/mdsd.err`

 Pokud potřebujete kontaktovat podporu, shromážděte následující informace: 

- Přihlášení `/var/log/azure/Microsoft.Azure.Monitor.AzureMonitorLinuxAgent/` 
- Přihlásit se `/var/log/waagent.log` 
- Přihlášení `/var/log/mdsd*`
- Soubory v `/etc/mdsd.d/`
- Souborů `/etc/default/mdsd`

### <a name="invalid-monitoring-virtual-machine-configuration"></a>Neplatná konfigurace monitorování virtuálního počítače

Jednou z příčin stavu *neshromáždění* je, že máte neplatnou konfiguraci virtuálního počítače s monitorováním.  Toto je výchozí konfigurace:

```json
{
    "version": 1,
    "secrets": {
        "telegrafPassword": {
            "keyvault": "https://mykeyvault.vault.azure.net/",
            "name": "sqlPassword"
        }
    },
    "parameters": {
        "sqlAzureConnections": [
            "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=telegraf;Password=$telegrafPassword;"
        ],
        "sqlVmConnections": [
        ],
        "sqlManagedInstanceConnections": [
        ]
    }
}
```

Tato konfigurace určuje náhradní tokeny, které se použijí v konfiguraci profilu na virtuálním počítači monitorování. Umožňuje také odkazovat na tajné kódy z Azure Key Vault, takže nemusíte uchovávat tajné hodnoty v žádné konfiguraci, což se důrazně doporučuje.

#### <a name="secrets"></a>Tajné kódy
Tajné kódy jsou tokeny, jejichž hodnoty jsou načteny v době běhu z Azure Key Vault. Tajný kód je definovaný dvojicí Key Vault odkazu a tajného názvu. To umožňuje Azure Monitor získat dynamickou hodnotu tajného kódu a použít ho jako odkazy na konfiguraci pro příjem dat.

V konfiguraci můžete podle potřeby definovat tolik tajných klíčů, včetně tajných klíčů uložených v samostatných trezorech klíčů.

```json
   "secrets": {
        "<secret-token-name-1>": {
            "keyvault": "<key-vault-uri>",
            "name": "<key-vault-secret-name>"
        },
        "<secret-token-name-2>": {
            "keyvault": "<key-vault-uri-2>",
            "name": "<key-vault-secret-name-2>"
        }
    }
```

Oprávnění pro přístup k Key Vault jsou poskytována Identita spravované služby na virtuálním počítači monitorování. Azure Monitor očekává, že Key Vault poskytne přístup k virtuálnímu počítači aspoň tajným klíčům. Můžete ji povolit z šablony Azure Portal, PowerShellu, CLI nebo Správce prostředků.

#### <a name="parameters"></a>Parametry
Parametry jsou tokeny, na které se dá odkazovat v konfiguraci profilu prostřednictvím JSON šablonování. Parametry mají název a hodnotu. Hodnoty můžou být libovolný typ JSON, včetně objektů a polí. V konfiguraci profilu je odkazováno na parametr pomocí jeho názvu v této úmluvě `.Parameters.<name>` .

Parametry mohou odkazovat na tajné kódy v Key Vault pomocí stejné konvence. Například odkazuje na `sqlAzureConnections` tajný kód `telegrafPassword` pomocí konvence `$telegrafPassword` .

V době spuštění budou všechny parametry a tajné klíče vyřešeny a sloučeny s konfigurací profilu, aby bylo možné vytvořit skutečnou konfiguraci, která se má na počítači použít.

> [!NOTE]
> Názvy parametrů `sqlAzureConnections` , `sqlVmConnections` a `sqlManagedInstanceConnections` jsou vyžadované v konfiguraci i v případě, že nemáte připojovací řetězce, které poskytnete pro některé z nich.


## <a name="collecting-with-errors-state"></a>Shromažďování s chybovým stavem
Monitorovací počítač bude *shromážděn s chybami* , pokud existuje alespoň jeden protokol *InsightsMetrics* , ale v tabulce *operací* jsou také chyby.

SQL Insights pomocí následujících dotazů načte tyto informace:

```
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(240m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

```
Operation 
 | where OperationCategory == "WorkloadInsights" 
 | summarize Errors = countif(OperationStatus == 'Error') 
```

Ve většině případů poskytujeme informace pro řešení potíží v našich zobrazeních protokolů: 

:::image type="content" source="media/sql-insights-enable/troubleshooting-logs-view.png" alt-text="Zobrazení protokolů pro řešení potíží.":::



## <a name="next-steps"></a>Další kroky

- Získejte podrobnosti o [povolování SQL Insights](sql-insights-enable.md).
