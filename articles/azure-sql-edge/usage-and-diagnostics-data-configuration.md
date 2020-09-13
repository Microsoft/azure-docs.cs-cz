---
title: Konfigurace využití a dat diagnostiky Azure SQL Edge
description: Naučte se konfigurovat data o využití a diagnostiku v Azure SQL Edge.
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 08/04/2020
ms.openlocfilehash: af4353e5b0991f7ce2f6fe8ff940e916717b0579
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650489"
---
# <a name="azure-sql-edge-usage-and-diagnostics-data-configuration"></a>Konfigurace využití a dat diagnostiky Azure SQL Edge

Ve výchozím nastavení Azure SQL Edge shromažďuje informace o tom, jak jeho zákazníci používají aplikaci. Konkrétně Azure SQL Edge shromažďuje informace o zkušenostech s nasazením, využití a výkonu. Tyto informace pomáhají Microsoftu vylepšit produkt, aby lépe vyhovoval potřebám zákazníků. Microsoft například shromažďuje informace o tom, jaké druhy chybových kódů se zákazníci setkávají, abychom mohli opravit související chyby, vylepšit naši dokumentaci, jak používat Azure SQL Edge, a určit, jestli se mají do produktu přidat funkce, aby lépe poskytovali zákazníkům.

Společnost Microsoft konkrétně neposílá žádné z následujících typů informací v rámci tohoto mechanismu:

- Všechny hodnoty z tabulek uživatelů.
- Jakékoli přihlašovací údaje nebo jiné ověřovací údaje.
- Veškerá osobní nebo zákaznická data.

Následující vzorový scénář obsahuje informace o použití funkcí, které pomáhají zlepšit produkt.

Ukázkový dotaz z dotazů použitých pro shromažďování dat využití a diagnostiky je uvedený níže. Dotaz určuje počet a typy různých zdrojů dat streamování používaných ve službě Azure SQL Edge. Tato data pomáhají společnosti Microsoft určit, které zdroje dat streamování se používají, obvykle tím, že Microsoft může vylepšit výkon a uživatelské prostředí spojené s těmito zdroji dat. 

```sql
select 
count(*) as [count], sum(inputs) as inputs, sum(outputs) as outputs, sum(linked_to_job) 
as linked_to_job, data_source_type  
from ( 
select isnull(value,'unknown') as data_source_type, inputs, outputs, linked_to_job  
from 
    ( 
        select 
        convert(sysname, lower(substring(ds.location, 0, charindex('://', ds.location))), 1) as data_source_type, 
        isnull(inputs, 0) as inputs, isnull(outputs, 0) as outputs, isnull(js.stream_id/js.stream_id, 0) as linked_to_job 
        from sys.external_streams es              
        join sys.external_data_sources ds 
             on es.data_source_id = ds.data_source_id             
        left join 
            ( 
            select stream_id, max(cast(is_input as int)) inputs, max(cast(is_output as int)) outputs 
            from sys.external_job_streams group by stream_id 
            ) js                
             on js.stream_id = es.object_id 
    ) ds            
left join 
    (
        select value from string_split('edgehub,sqlserver,kafka', ',')) as known_ep on data_source_type = value 
    ) known_ds        
group by data_source_type
```

## <a name="disable-usage-and-diagnostic-data-collection"></a>Zakázat shromažďování dat o využití a diagnostiku

Shromažďování dat o využití a diagnostiku v Azure SQL Edge se dá zakázat pomocí některé z následujících metod.

### <a name="disable-usage-and-diagnostics-using-environment-variables"></a>Zakázání používání a diagnostiky pomocí proměnných prostředí

Pokud chcete zakázat shromažďování dat o využití a diagnostice na Edge SQL Azure, přidejte následující proměnnou prostředí a nastavte její hodnotu na `*False*` . Další informace o konfiguraci Edge Azure SQL pomocí proměnných prostředí najdete v tématu [Konfigurace pomocí proměnných prostředí](configure.md#configure-by-using-environment-variables).

`MSSQL_TELEMETRY_ENABLED = TRUE | FALSE`

- TRUE – povolí shromažďování dat o využití a diagnostice. Toto je výchozí konfigurace.
- FALSE – zakáže shromažďování dat o využití a diagnostiku.

### <a name="disable-usage-and-diagnostics-using-mssqlconf-file"></a>Zakázání používání a diagnostiky pomocí souboru MSSQL. conf

Pokud chcete zakázat shromažďování dat o využití a diagnostice na Edge SQL Azure, přidejte následující řádky do souboru MSSQL. conf na trvalé jednotce úložiště, která je namapovaná na složku/var/opt/MSSQL/v modulu SQL Edge. Další informace o konfiguraci Azure SQL Edge pomocí souboru MSSQL. conf najdete v tématu [Konfigurace pomocí souboru MSSQL. conf](configure.md#configure-by-using-an-mssqlconf-file).

```ini
[telemetry]
customerfeedback = false
```

## <a name="local-audit-of-usage-and-diagnostic-data-collection"></a>Místní audit shromažďování dat využití a diagnostiky

Místní součást auditu využití Azure SQL Edge a shromažďování diagnostických dat může zapisovat data shromážděná službou do určené složky, která představuje data (protokoly), která se odešlou společnosti Microsoft. Účelem místního auditu je dovolit zákazníkům Zobrazit všechna data, která Microsoft shromažďuje s touto funkcí, z důvodů dodržování předpisů, regulativního charakteru nebo ověření ochrany osobních údajů.

### <a name="enable-local-audit-of-usage-and-diagnostics-data"></a>Povolit místní audit dat využití a diagnostiky

Povolení místního auditu a dat diagnostiky na Azure SQL Edge

1. Vytvořte cílový adresář pro nové místní úložiště protokolu auditu. Tento cílový adresář může být buď na hostiteli, nebo v rámci kontejneru. V následujícím příkladu je cílový adresář vytvořen ve stejném připojeném svazku, který je namapován na/var/opt/MSSQL/cestu na serveru SQL Edge.

   ```bash
   sudo mkdir <host mount path>/audit
   ```

2. Nakonfigurujte audit dat využití a diagnostiky pomocí proměnných prostředí nebo souboru MSSQL. conf.

   - Použití proměnných prostředí – přidejte následující proměnnou prostředí do nasazení SQL Edge a určete cílový adresář pro soubory auditu.
   
     `*MSSQL_TELEMETRY_DIR = <host mount path>/audit*`
   
   - Použití souboru MSSQL. conf – přidejte následující řádky do souboru MSSQL. conf a určete cílový adresář pro soubory auditu.
       ```ini
       [telemetry]
       userrequestedlocalauditdirectory  = <host mount path>/audit
       ```  

## <a name="next-steps"></a>Další kroky

- [Připojení k Edge SQL Azure](connect.md)
- [Sestavení komplexního řešení IoT s využitím SQL Edge](tutorial-deploy-azure-resources.md)
