---
title: Řešení potíží se správou Integration Runtime SSIS v Azure Data Factory | Microsoft Docs
description: Tento článek popisuje pokyny k odstraňování potíží se správou SSIS Integration Runtime (SSIS IR)
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/08/2019
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: 17fe8d9ed02156b8fe9aafd7adca946531895883
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68253020"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Řešení potíží se správou SSIS Integration Runtime v Azure Data Factory

Tento dokument popisuje Průvodce odstraňováním potíží pro problémy se správou SSIS Integration Runtime (SSIS IR).

## <a name="overview"></a>Přehled

Pokud dojde k nějakému problému při zřizování nebo rušení zřizování SSIS IR, bude na portálu ADF (ADF) k dispozici chybová zpráva nebo se vrátí z rutiny prostředí PowerShell. Chyba je vždy ve formátu jako kód chyby s podrobnou chybovou zprávou.

Znamená to, že služba obsahuje nějaké přechodné problémy, pokud je kód chyby Nenalezeno. Operaci můžete opakovat později. Pokud to nepomůže, kontaktujte tým podpory Azure Data Factory.

Existují tři hlavní vnější závislosti, které mohou způsobit chyby: Azure SQL Database Server nebo spravovaná instance, vlastní instalační skript a konfigurace Virtual Network, pokud kód chyby není Nenalezeno.

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Problémy Azure SQL Database serveru nebo spravované instance

Azure SQL Database Server nebo spravovaná instance jsou požadovány, pokud zřizování SSIS IR pomocí databáze katalogu SSIS. Server Azure SQL Database nebo spravovaná instance by měly být přístupné SSIS IR. Účet Azure SQL Databaseho serveru nebo spravované instance by měl mít oprávnění k vytvoření databáze katalogu SSIS (SSISDB). Pokud dojde k nějaké chybě, na portálu ADF se zobrazí kód chyby s podrobnostmi o výjimce SQL. Při odstraňování potíží s kódy chyb postupujte podle následujících pokynů.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Tento problém se může zobrazit, když zřizujete nový SSIS IR nebo během běhu IR.

Může to být způsobeno následujícími důvody, pokud se při zřizování infračerveného přenosu zobrazí chyba, a v chybové zprávě můžete získat podrobnou zprávu SqlException.

* Problém s připojením k síti. Ověřte, SQL Server zda je název hostitele spravované instance dostupný, a že pro přístup k serveru není k dispozici brána firewall nebo NSG bloky SSIS IR.
* Přihlášení se nezdařilo a používá se ověřování SQL. Znamená to, že zadaný účet se nemůže přihlásit k SQL Server. Ujistěte se, že je zadaný správný uživatelský účet.
* Přihlášení se nezdařilo a používá se ověřování AAD (spravovaná identita). Přidejte spravovanou identitu vaší továrny do skupiny AAD a zpřístupněte spravovanou identitu přístup k vašemu serveru databáze katalogu.
* Časový limit připojení je vždy z důvodu konfigurace související se zabezpečením. Doporučuje se vytvořit nový virtuální počítač, aby se virtuální počítač připojil ke stejné virtuální síti, pokud je IR ve virtuální síti, pak nainstaluje SSMS a zkontroluje stav serveru Azure SQL Database nebo spravované instance.

Další problémy najdete v podrobnostech chybové zprávy o výjimce SQL a odstranění problému uvedeného v chybové zprávě. Pokud stále máte problémy, obraťte se na tým podpory Azure SQL Database serveru nebo spravované instance.

Je pravděpodobnější, že se některá skupina zabezpečení sítě nebo brána firewall změní, pokud se zobrazí chyba během běhu IR, což způsobí, že uzel SSIS IR Worker nemůže získat přístup k Azure SQL Databasemu serveru nebo spravované instanci již. Odblokujte pracovní uzel SSIS IR pro přístup k serveru Azure SQL Database nebo spravované instanci.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Chybová zpráva vypadá jako "databáze ' SSISDB ' dosáhla kvóty velikosti. Můžete rozdělit nebo odstranit data, vyřadit indexy nebo si v dokumentaci vyhledat možná řešení. " Možná řešení:
* Zvyšte velikost kvóty vaší SSISDB.
* Změňte konfigurace SSISDB tak, aby se snížila velikost, jako je:
   * Zkrácení doby uchování a počtu verzí projektu.
   * Zkrácení doby uchování protokolu.
   * Změna výchozí úrovně protokolu a tak dále.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Tato chyba znamená, že server Azure SQL Database nebo spravovaná instance již má vytvořené SSISDB a používá ho jiný IR. Potřebujete buď zadat jiný server Azure SQL Database nebo spravovanou instanci, nebo odstranit existující SSISDB a restartovat nové prostředí IR.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Tato chyba může být způsobena níže uvedenými důvody:

* Uživatelský účet nakonfigurovaný pro SSIS IR nemá oprávnění k vytvoření databáze. Uživateli můžete udělit oprávnění k vytvoření databáze.
* Vytvořte časový limit databáze, třeba časový limit spuštění, časový limit operace databáze a tak dále. Můžete to zkusit znovu později a zjistit, jestli se problém vyřeší. Pokud to nepomůže, obraťte se na tým podpory Azure SQL Database serveru nebo spravované instance.

V případě jiných problémů se podívejte na chybovou zprávu výjimky SQL a opravte problém uvedený v chybové zprávě. Pokud stále máte problémy, obraťte se na tým podpory Azure SQL Database serveru nebo spravované instance.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Chybová zpráva vypadá jako "neplatný název objektu Catalog. catalog_properties". znamená to, že už máte databázi s názvem SSISDB, ale není vytvořená SSIS IR, nebo se databáze nachází v neplatném stavu, což je způsobeno chybami při posledním zřizování SSIS pro infračervený přenos. Můžete odstranit stávající databázi s názvem SSISDB nebo nakonfigurovat nový server Azure SQL Database nebo spravovanou instanci pro prostředí IR.

## <a name="custom-setup"></a>Vlastní instalace

Vlastní instalační program poskytuje rozhraní pro přidání vlastních kroků instalace během zřizování nebo opětovné konfigurace SSIS IR. Další informace najdete v tématu [přizpůsobení nastavení pro prostředí Azure-SSIS Integration runtime](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

Zajistěte, aby váš kontejner obsahoval pouze nezbytné vlastní instalační soubory, protože všechny soubory v kontejneru budou staženy do uzlu SSIS IR Worker. Před spuštěním skriptu v SSIS IR se doporučuje otestovat skript vlastního nastavení na místním počítači, aby se opravily problémy s spuštěním skriptu.

Kontejner skriptu pro vlastní instalaci se kontroluje během běhu IR, protože je pravidelně aktualizovaný SSIS IR, který pro stažení vlastního instalačního skriptu potřebuje znovu získat přístup ke kontejneru a znovu nainstalovat. Tato kontrolu zahrnuje, zda je kontejner přístupný a zda existuje hlavní soubor. cmd.

Jakákoli chyba s vlastním nastavením, zobrazí se chyba s kódem CustomSetupScriptFailure, podívejte se na chybovou zprávu s kódem podřízené chyby.  Při odstraňování potíží s kódy sub Error postupujte podle následujících pokynů.  

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

To znamená, že SSIS IR nemůže získat přístup k vašemu kontejneru objektů blob Azure, aby se vlastní instalace. Ověřte, že identifikátor URI SAS kontejneru je dosažitelný a že nevypršela jeho platnost.

Pokud je prostředí IR v běžícím stavu, zastavte infračervený signál, překonfigurujte IR pomocí nového vlastního instalačního identifikátoru URI kontejneru SAS a pak znovu spusťte IR.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

To znamená, že SSIS IR nemůže najít skript vlastního nastavení (Main. cmd) ve vašem kontejneru objektů BLOB. Zajistěte, aby v kontejneru existoval hlavní. cmd, což je vstupní bod pro instalaci vlastní instalace.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Znamená to, že se nepovedlo spustit skript pro vlastní instalaci (Main. cmd), můžete vyzkoušet nejdřív skript na svém místním počítači nebo ve svém kontejneru objektů BLOB ověřit protokoly spuštění vlastního nastavení.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Znamená to, že spustí časový limit skriptu pro vlastní instalaci. Ujistěte se, že váš kontejner objektů BLOB obsahuje jenom nezbytné vlastní instalační soubory. V kontejneru objektů blob můžete také kontrolovat vlastní protokoly spuštění instalačního programu. Maximální doba pro vlastní instalaci je nastavená na 45 minut před vypršením časového limitu a maximální doba zahrnuje dobu stahování všech souborů z vašeho kontejneru a jejich instalaci na SSIS IR. Pokud je potřeba delší období, vyvolejte lístek podpory.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Znamená to, že nahrání vlastních protokolů spuštění instalačního programu do kontejneru objektů BLOB se nepovedlo, protože SSIS IR nemá oprávnění k zápisu do kontejneru objektů BLOB nebo nějaké problémy s úložištěm nebo sítí. Pokud je vlastní instalace úspěšná, tato chyba nemá vliv na žádnou funkci SSIS, ale protokoly chybí. Pokud se vlastní instalace nepovedla s jinou chybou a nepovedlo se nám nahrát protokol, nahlásíme tuto chybu jako první, takže protokol se dá nahrát k analýze a po vyřešení tohoto problému budeme nahlásit víc zadaných potíží. Pokud se tento problém po opakování nevyřeší, obraťte se na tým podpory Azure Data Factory.

## <a name="virtual-network-configuration"></a>Konfigurace virtuální sítě

Při spojování SSIS IR do Virtual Network (VNet) se používá virtuální síť v rámci předplatného uživatele. Další informace najdete v tématu [připojení prostředí Azure-SSIS Integration runtime k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Pokud dojde k problému souvisejícímu se sítí VNet, zobrazí se chyba níže.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Příčinou může být varianta z variant. Při odstraňování potíží s kódy sub Error postupujte podle následujících pokynů.

### <a name="forbidden"></a>Zakázáno

Chybová zpráva vypadá jako "subnetId není pro aktuální účet povolený. Zprostředkovatel prostředků Microsoft. Batch není zaregistrován v rámci stejného předplatného virtuální sítě.

To znamená, že Azure Batch nemá přístup k vaší virtuální síti. Zaregistrujte poskytovatele prostředků Microsoft. Batch v rámci stejného předplatného virtuální sítě.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Chybová zpráva vypadá takto: buď zadaná virtuální síť neexistuje, nebo k ní služba Batch nemá přístup, nebo zadaná podsíť xxx neexistuje.

Znamená to, že virtuální síť neexistuje nebo služba Azure Batch k ní nemá přístup, nebo zadaná podsíť neexistuje. Ujistěte se, že virtuální síť a podsíť existují a Azure Batch k nim mají přístup.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Zpráva je jako "nepovedlo se zřídit Integration Runtime ve virtuální síti. Pokud jsou nakonfigurovaná nastavení server DNS nebo NSG, ujistěte se, že je server DNS přístupný a že je NSG správně nakonfigurovaný. "

Je možné, že máte nějakou přizpůsobenou konfiguraci serveru DNS nebo nastavení NSG, což způsobí, že název serveru Azure vyžadovaný nástrojem SSIS IR nejde přeložit nebo k němu nejde dostat. Další informace najdete v dokumentu [Konfigurace virtuální sítě SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) . Pokud máte problémy stále, obraťte se na tým podpory Azure Data Factory.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

SSIS IR se automaticky aktualizuje v pravidelných intervalech a vytvoří se nový fond Azure Batch během upgradu a starý Azure Batch fond se odstraní, odstraní se prostředek související s virtuální sítí pro starý fond a vytvoří se nový prostředek související s virtuální sítí. formě. Tato chyba znamená, že při odstraňování prostředku souvisejícího s virtuální sítí pro starý fond došlo k chybě kvůli zámku odstranit na úrovni předplatného nebo skupiny prostředků. Pomocí této klávesy odeberte zámek proti odstranění.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Zřizování SSIS IR z důvodů příčiny selže, a pokud dojde k selhání, odstraní se všechny vytvořené prostředky. Nepovedlo se ale odstranit prostředky virtuální sítě, protože je na úrovni předplatného nebo skupiny prostředků k dispozici zámek proti odstranění prostředku. Odeberte zámek proti odstranění a restartujte IR.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

Při zastavování SSIS IR se odstraní veškerý prostředek týkající se virtuální sítě, ale odstranění se nepovedlo, protože na úrovni předplatného nebo skupiny prostředků se odstraní zámek pro odstranění prostředku. Pomocí této klávesy odeberte zámek proti odstranění a zkuste operaci zastavit znovu.

### <a name="nodeunavailable"></a>NodeUnavailable

K této chybě dojde během běhu IR, znamená to, že je infračervený stav předtím, než je v pořádku, je vždycky z důvodu změny konfigurace serveru DNS nebo NSG a způsobilo, že se SSIS IR nemůže připojit k závislé službě, což vám umožní opravit problémy s konfigurací serveru DNS nebo NSG. Další informace najdete v tématu [Konfigurace virtuální sítě SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Pokud máte problémy stále, obraťte se na tým podpory Azure Data Factory.
