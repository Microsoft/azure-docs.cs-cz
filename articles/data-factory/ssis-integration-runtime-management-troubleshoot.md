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
ms.openlocfilehash: 8abffdf443e26c03c38c12a3947a47a94157c9da
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68609617"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Řešení potíží se správou SSIS Integration Runtime v Azure Data Factory

V tomto článku najdete pokyny k odstraňování potíží se správou v Azure-služba SSIS (SQL Server Integration Services) (SSIS) Integration Runtime (IR), označované taky jako SSIS IR.

## <a name="overview"></a>Přehled

Pokud narazíte na problém při zřizování nebo rušení zřizování SSIS IR, zobrazí se na portálu Microsoft Azure Data Factory chybová zpráva nebo v rutině PowerShellu se vrátila Chyba. Chyba se vždycky zobrazuje ve formátu kódu chyby s podrobnou chybovou zprávou.

Pokud kód chyby je Nenalezeno, služba obsahuje přechodné problémy a tuto operaci byste měli opakovat později. Pokud to nepomůže, obraťte se na tým podpory Azure Data Factory.

Jinak tři hlavní závislosti můžou způsobit chyby: Azure SQL Database Server nebo spravovaná instance, vlastní instalační skript a konfigurace virtuální sítě.

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Problémy Azure SQL Database serveru nebo spravované instance

Azure SQL Database Server nebo spravovaná instance se vyžadují v případě, že zřizujete SSIS IR pomocí databáze katalogu SSIS. SSIS IR musí být schopný získat přístup k serveru Azure SQL Database nebo spravované instanci. Účet Azure SQL Databaseho serveru nebo spravované instance by měl mít také oprávnění k vytvoření databáze katalogu SSIS (SSISDB). Pokud dojde k chybě, zobrazí se na portálu Data Factory kód chyby s podrobnou zprávou o výjimce SQL. Pomocí informací v následujícím seznamu můžete odstraňovat potíže s kódy chyb.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Tento problém se může zobrazit, když zřizujete nový SSIS IR nebo když je spuštěný IR. Pokud dojde k této chybě při zřizování infračerveného přenosu, může se zobrazit podrobná zpráva SqlException v chybové zprávě, která indikuje jeden z následujících problémů:

* Problém s připojením k síti. Ověřte, zda je název hostitele SQL Server nebo spravované instance přístupný. Ověřte také, jestli brána firewall nebo skupina zabezpečení sítě (NSG) neblokuje SSIS infračervený přístup k serveru.
* Přihlášení se nepovedlo během ověřování SQL. Poskytnutý účet se nemůže přihlásit k databázi SQL Server. Ujistěte se, že jste zadali správný uživatelský účet.
* Přihlášení se nepovedlo během ověřování Microsoft Azure Active Directory (Azure AD) (spravovaná identita). Přidejte spravovanou identitu vaší továrny do skupiny AAD a ujistěte se, že má spravovaná identita přístupová oprávnění k serveru databáze katalogu.
* Vypršel časový limit připojení. Tato chyba je vždy způsobena konfigurací související se zabezpečením. Doporučujeme:
  1. Vytvořte nový virtuální počítač.
  1. Připojte se k virtuálnímu počítači ke stejnému Microsoft Azure Virtual Network IR, pokud je IR ve virtuální síti.
  1. Nainstalujte SSMS a ověřte stav Azure SQL Database serveru nebo spravované instance.

Pro jiné problémy opravte problém uvedený v podrobné chybové zprávě výjimky SQL. Pokud máte potíže i nadále, obraťte se na tým podpory pro Azure SQL Database Server nebo na spravovanou instanci.

Pokud se zobrazí chyba, když je spuštěný IR, může se jednat o skupinu zabezpečení sítě nebo o změny brány firewall, aby se uzel SSIS IR nepřístupoval k serveru Azure SQL Database nebo spravované instanci. Odblokujte pracovní uzel SSIS IR, aby mohl přistupovat k serveru Azure SQL Database nebo spravované instanci.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Tento druh chybové zprávy může vypadat takto: "Databáze ' SSISDB ' dosáhla kvóty velikosti. Můžete rozdělit nebo odstranit data, vyřadit indexy nebo si v dokumentaci vyhledat možná řešení. " 

Možná řešení:
* Zvyšte velikost kvóty vašeho SSISDB.
* Změňte konfiguraci SSISDB, aby se zmenšila velikost o:
   * Zkrácení doby uchování a počtu verzí projektu.
   * Zkrácení doby uchování protokolu.
   * Změna výchozí úrovně protokolu.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Tato chyba znamená, že server Azure SQL Database nebo spravovaná instance již má SSISDB a že je používána jiným IR. Musíte buď zadat jiný server Azure SQL Database nebo spravovanou instanci, nebo odstranit existující SSISDB a restartovat nové prostředí IR.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

K této chybě může dojít z některého z následujících důvodů:

* Uživatelský účet nakonfigurovaný pro SSIS IR nemá oprávnění k vytvoření databáze. Můžete uživateli udělit oprávnění k vytvoření databáze.
* Při vytváření databáze dojde k vypršení časového limitu, jako je například časový limit spuštění nebo časový limit operace databáze. Zkuste operaci zopakovat později. Pokud to nepomůže, obraťte se na tým podpory Azure SQL Databaseho serveru nebo spravované instance.

V případě jiných problémů si Projděte chybovou zprávu o výjimce SQL a opravte problém uvedený v podrobnostech o chybě. Pokud máte potíže i nadále, obraťte se na tým podpory pro Azure SQL Database Server nebo na spravovanou instanci.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Tento druh chybové zprávy vypadá nějak takto: "Neplatný název objektu Catalog. catalog_properties". V této situaci už buď máte databázi s názvem SSISDB, ale ta se nevytvořila v SSIS IR, nebo se databáze nachází v neplatném stavu, který způsobuje chyby při posledním zřizování SSIS IR. Existující databázi můžete odstranit s názvem SSISDB, nebo můžete nakonfigurovat nový server Azure SQL Database nebo spravovanou instanci pro prostředí IR.

## <a name="custom-setup-issues"></a>Problémy s vlastním nastavením

Vlastní instalační program poskytuje rozhraní pro přidání vlastních kroků instalace během zřizování nebo opětovné konfigurace SSIS IR. Další informace najdete v tématu [přizpůsobení nastavení pro Azure-SSIS Integration runtime](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

Ujistěte se, že váš kontejner obsahuje jenom nezbytné vlastní instalační soubory. všechny soubory v kontejneru se stáhnou do uzlu SSIS IR Worker. Doporučujeme, abyste před spuštěním skriptu v SSIS IR otestovali skript vlastní instalace na místním počítači, abyste opravili problémy s spuštěním skriptu.

Kontejner skriptu vlastní instalace se kontroluje, zatímco je spuštěný IR, protože se pravidelně aktualizuje SSIS IR. Tato aktualizace vyžaduje přístup ke kontejneru ke stažení skriptu vlastní instalace a jeho opětovné instalaci. Proces také kontroluje, zda je kontejner přístupný a zda existuje hlavní soubor. cmd.

Pro všechny chyby, které zahrnují vlastní instalaci, se zobrazí kód chyby CustomSetupScriptFailure s podklíčovým kódem, jako je CustomSetupScriptBlobContainerInaccessible nebo CustomSetupScriptNotFound.

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Tato chyba znamená, že SSIS IR nemůže získat přístup k vašemu kontejneru Azure Blob pro vlastní instalaci. Ujistěte se, že identifikátor URI SAS kontejneru je dosažitelný a že nevypršela jeho platnost.

Pokud je prostředí IR spuštěné, zastavte ho znovu s novým nastavením identifikátoru URI SAS kontejneru vlastní instalace a pak restartujte technologii IR.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Tato chyba znamená, že SSIS IR nemůže najít skript vlastního nastavení (Main. cmd) ve vašem kontejneru objektů BLOB. Zajistěte, aby v kontejneru existoval hlavní. cmd, což je vstupní bod pro instalaci vlastní instalace.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Tato chyba znamená, že spuštění skriptu vlastní instalace (Main. cmd) se nezdařilo. Vyzkoušejte nejprve skript na vašem místním počítači nebo ve vašem kontejneru objektů BLOB ověřte protokoly spuštění vlastního nastavení.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Tato chyba označuje časový limit spuštění vlastního instalačního skriptu. Ujistěte se, že váš kontejner objektů BLOB obsahuje jenom nezbytné vlastní instalační soubory. V kontejneru objektů BLOB byste také měli kontrolovat protokoly spuštění vlastního nastavení. Maximální doba pro vlastní nastavení je 45 minut před vypršením časového limitu a maximální doba zahrnuje dobu stahování všech souborů z vašeho kontejneru a jejich instalaci na SSIS IR. Pokud potřebujete delší období, vyvolejte lístek podpory.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Tato chyba znamená, že pokus o nahrání vlastních protokolů spuštění instalačního programu do kontejneru objektů BLOB se nezdařil. K tomuto problému dochází buď proto, že SSIS IR nemá oprávnění k zápisu do kontejneru objektů BLOB nebo kvůli problémům s úložištěm nebo sítí. Pokud je vlastní instalace úspěšná, tato chyba nebude mít vliv na žádnou funkci SSIS, ale protokoly budou chybět. Pokud se vlastní instalace nezdaří s jinou chybou a protokol se nahraje, pošle se tato chyba jako první, aby se protokol mohl nahrát na analýzu. Po vyřešení tohoto problému budeme nahlásit jakékoli konkrétnější problémy. Pokud se tento problém po opakování nevyřeší, obraťte se na tým podpory Azure Data Factory.

## <a name="virtual-network-configuration"></a>Konfigurace virtuální sítě

Když připojíte SSIS IR k Azure Virtual Network, SSIS IR používá virtuální síť, která je v rámci předplatného uživatele. Další informace najdete v tématu [připojení Integration runtime Azure-SSIS k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Když se vyskytne problém související s Virtual Network, zobrazí se jedna z následujících chyb.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

K této chybě může dojít z nejrůznějších důvodů. Řešení potíží najdete v částech [zakázané](#forbidden), [InvalidPropertyValue](#invalidpropertyvalue)a [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings) .

### <a name="forbidden"></a>Zakázáno

Tento druh chyby může vypadat přibližně takto: "SubnetId není pro aktuální účet povolený. Zprostředkovatel prostředků Microsoft. Batch není zaregistrován v rámci stejného předplatného virtuální sítě.

Tyto podrobnosti znamenají, že Azure Batch nemá přístup k vaší virtuální síti. Zaregistrujte poskytovatele prostředků Microsoft. Batch v rámci stejného předplatného jako Virtual Network.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Tento druh chyby může být podobný jednomu z následujících: 

- Buď zadaná virtuální síť neexistuje, nebo k ní služba Batch nemá přístup. "
- "Zadaná podsíť xxx neexistuje."

Tyto chyby znamenají, že virtuální síť neexistuje, služba Azure Batch k ní nemá přístup, nebo zadaná podsíť neexistuje. Ujistěte se, že virtuální síť a podsíť existují a že Azure Batch k nim mají přístup.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Tento druh chybové zprávy může vypadat takto: "Nepovedlo se zřídit Integration Runtime ve virtuální síti. Pokud jsou nakonfigurovaná nastavení server DNS nebo NSG, ujistěte se, že je server DNS přístupný a že je NSG správně nakonfigurovaný. "

V této situaci máte pravděpodobně přizpůsobenou konfiguraci serveru DNS nebo nastavení NSG, což brání tomu, aby se přeložilo nebo nezískal název serveru Azure, který vyžaduje SSIS IR. Další informace najdete v tématu [Konfigurace SSIS IR Virtual Network](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Pokud máte potíže i nadále, obraťte se na tým podpory Azure Data Factory.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

V pravidelných intervalech se SSIS IR automaticky aktualizuje. Během upgradu se vytvoří nový fond Azure Batch a starý Azure Batch fond se odstraní. Také se odstraní prostředky související s Virtual Network pro starý fond a nové prostředky související s Virtual Network se vytvoří v rámci vašeho předplatného. Tato chyba znamená, že při odstraňování prostředků souvisejících s Virtual Network pro starý fond došlo k chybě z důvodu zámku odstranění na úrovni předplatného nebo skupiny prostředků. Vzhledem k tomu, že zákazník řídí a nastavuje zámek pro odstranění, musí v této situaci odebrat zámek proti odstranění.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Pokud se zřizování SSIS IR nepovede, odstraní se všechny prostředky, které byly vytvořeny. Pokud ale na úrovni předplatného nebo skupiny prostředků existuje zámek pro odstranění prostředku, Virtual Network prostředky se neodstraní podle očekávání. Pokud chcete tuto chybu opravit, odeberte zámek proti odstranění a restartujte IR.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

Když zastavíte SSIS IR, odstraní se všechny prostředky související s Virtual Network. Odstranění ale může selhat, pokud existuje zámek odstranění prostředku na úrovni předplatného nebo skupiny prostředků. Tady je také řízení zákazníka a nastavuje zámek proti odstranění. Proto musí odebrat zámek proti odstranění a pak znovu zastavit SSIS IR.

### <a name="nodeunavailable"></a>NodeUnavailable

K této chybě dochází, když je spuštěný IR a to znamená, že se IR stala špatným. Tato chyba je vždy způsobena změnou v konfiguraci serveru DNS nebo NSG, která blokuje SSIS IR v připojení k potřebné službě. Vzhledem k tomu, že konfigurace serveru DNS a NSG je řízena zákazníkem, musí zákazník opravit problémy blokující na konci. Další informace najdete v tématu [Konfigurace SSIS IR Virtual Network](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Pokud máte potíže i nadále, obraťte se na tým podpory Azure Data Factory.
