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
ms.openlocfilehash: d16267e104d753770dc40ce99b0f56e5c749b2d0
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2019
ms.locfileid: "72752173"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Řešení potíží se správou SSIS Integration Runtime v Azure Data Factory

V tomto článku najdete pokyny k odstraňování potíží se správou v Azure-služba SSIS (SQL Server Integration Services) (SSIS) Integration Runtime (IR), označované taky jako SSIS IR.

## <a name="overview"></a>Přehled

Pokud narazíte na problém při zřizování nebo rušení zřizování SSIS IR, zobrazí se na portálu Microsoft Azure Data Factory chybová zpráva nebo v rutině PowerShellu se vrátila Chyba. Chyba se vždycky zobrazuje ve formátu kódu chyby s podrobnou chybovou zprávou.

Pokud kód chyby je Nenalezeno, služba obsahuje přechodné problémy a tuto operaci byste měli opakovat později. Pokud to nepomůže, obraťte se na tým podpory Azure Data Factory.

Jinak tři hlavní závislosti můžou způsobit chyby: Azure SQL Database Server nebo spravovaná instance, vlastní instalační skript a konfigurace virtuální sítě.

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Problémy Azure SQL Database serveru nebo spravované instance

Pokud zřizujete prostředí SSIS IR s databází katalogu SSIS, vyžaduje se spravovaná instance nebo server služby Azure SQL Database. Prostředí SSIS IR musí mít přístup ke spravované instanci nebo serveru služby Azure SQL Database. Účet spravované instance nebo serveru služby Azure SQL Database by také měl mít oprávnění k vytvoření databáze katalogu SSIS (SSISDB). Pokud dojde k chybě, na portálu služby Data Factory se zobrazí kód chyby s podrobnou zprávou o výjimce SQL. Při řešení potíží s jednotlivými kódy chyb využijte informace v následujícím seznamu.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

K tomuto problému může dojít při zřizování nového prostředí SSIS IR nebo za běhu prostředí IR. Pokud k této chybě dojde při zřizování prostředí IR, můžete z chybové zprávy získat podrobnou zprávu o výjimce SQL, která značí jeden z následujících problémů:

* Problém se síťovým připojením. Zkontrolujte, jestli je dostupný název hostitele SQL Serveru nebo spravované instance. Ověřte také, že žádná brána firewall ani skupina zabezpečení sítě (NSG) neblokuje přístup prostředí SSIS IR k serveru.
* Přihlášení selhalo během ověřování SQL. Zadaný účet se nemůže přihlásit k databázi SQL Serveru. Ujistěte se, že jste zadali správný uživatelský účet.
* Přihlášení selhalo během ověřování Microsoft Azure Active Directory (Azure AD) (spravovaná identita). Přidejte spravovanou identitu vaší továrny do skupiny AAD a ujistěte se, že spravovaná identita má přístupová oprávnění k databázovému serveru katalogu.
* Vypršení časového limitu připojení. Příčinou této chyby je vždy konfigurace související se zabezpečením. Doporučený postup:
  1. Vytvořte nový virtuální počítač.
  1. Připojte se k virtuálnímu počítači ke stejnému Microsoft Azure Virtual Network IR, pokud je IR ve virtuální síti.
  1. Nainstalujte SSMS a ověřte stav Azure SQL Database serveru nebo spravované instance.

V případě jiných problémů opravte problém uvedený v podrobné chybové zprávě o výjimce SQL. Pokud stále máte problémy, kontaktujte tým podpory pro spravovanou instanci nebo server služby Azure SQL Database.

Pokud k této chybě dojde za běhu prostředí IR, nějaké změny skupiny zabezpečení sítě nebo brány firewall pravděpodobně brání pracovnímu uzlu prostředí SSIS IR v přístupu ke spravované instanci nebo serveru služby Azure SQL Database. Odblokujte pracovní uzel prostředí SSIS IR, aby mohl získat přístup ke spravované instanci nebo serveru služby Azure SQL Database.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Tento druh chybové zprávy může vypadat takto: "databáze ' SSISDB ' dosáhla kvóty velikosti. Můžete rozdělit nebo odstranit data, vyřadit indexy nebo si v dokumentaci vyhledat možná řešení. " 

Mezi možná řešení patří:
* Zvyšte velikost kvóty vašeho SSISDB.
* Zmenšení velikosti změnou konfigurace databáze SSISDB:
   * Zkrácení doby uchování a počtu verzí projektu.
   * Zkrácení doby uchování protokolu.
   * Změna výchozí úrovně protokolu.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Tato chyba znamená, že spravovaná instance nebo server služby Azure SQL Database již mají databázi SSISDB, kterou používá jiné prostředí IR. Musíte zadat jinou spravovanou instanci nebo server služby Azure SQL Database, nebo odstranit stávající databázi SSISDB a restartovat nové prostředí IR.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

K této chybě může dojít z některého z následujících důvodů:

* Uživatelský účet nakonfigurovaný pro prostředí SSIS IR nemá oprávnění k vytvoření databáze. Můžete uživateli udělit oprávnění k vytvoření databáze.
* Při vytváření databáze dojde k vypršení časového limitu například spuštění nebo databázové operace. Operaci byste měli zopakovat později. Pokud opakovaný pokus nepomůže, kontaktujte tým podpory pro spravovanou instanci nebo server služby Azure SQL Database.

V případě jiných problémů zkontrolujte chybovou zprávu o výjimce SQL a opravte problém uvedený v podrobnostech o chybě. Pokud stále máte problémy, kontaktujte tým podpory pro spravovanou instanci nebo server služby Azure SQL Database.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Tento druh chybové zprávy vypadá takto: "neplatný název objektu Catalog. catalog_properties". V této situaci už buď máte databázi s názvem SSISDB, ale ta se nevytvořila v SSIS IR, nebo se databáze nachází v neplatném stavu, který způsobuje chyby při posledním zřizování SSIS IR. Můžete odstranit stávající databázi s názvem SSISDB nebo pro prostředí IR můžete nakonfigurovat novou spravovanou instanci nebo server služby Azure SQL Database.

## <a name="custom-setup-issues"></a>Problémy s vlastním nastavením

Vlastní instalace nabízí rozhraní pro přidání vlastních pokynů k instalaci během zřizování a rekonfigurace prostředí SSIS IR. Další informace najdete v tématu [Přizpůsobení instalace prostředí Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

Ujistěte se, že váš kontejner obsahuje pouze potřebné soubory vlastní instalace. Všechny soubory v kontejneru se stáhnou do pracovního uzlu prostředí SSIS IR. Před spuštěním skriptu vlastní instalace v prostředí SSIS IR doporučujeme skript otestovat na místním počítači a opravit případné problémy s jeho spuštěním.

Vzhledem k tomu, že se prostředí SSIS IR pravidelně aktualizuje, za běhu prostředí IR se zkontroluje kontejner se skriptem vlastní instalace. Tyto aktualizace vyžadují přístup ke kontejneru, aby bylo možné stáhnout skript vlastní instalace a znovu provést instalaci. V rámci tohoto procesu se také zkontroluje, jestli je kontejner dostupný a jestli existuje soubor main.cmd.

Pro všechny chyby, které zahrnují vlastní instalaci, se zobrazí kód chyby CustomSetupScriptFailure s podklíčovým kódem, jako je CustomSetupScriptBlobContainerInaccessible nebo CustomSetupScriptNotFound.

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Tato chyba znamená, že prostředí SSIS IR nemá přístup ke kontejneru objektů blob v Azure pro vlastní instalaci. Ujistěte se, že je identifikátor URI SAS kontejneru dostupný a že jeho platnost nevypršela.

Pokud je prostředí IR spuštěné, zastavte ho, nakonfigurujte v něm nový identifikátor URI SAS kontejneru vlastní instalace a pak ho restartujte.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Tato chyba znamená, že prostředí SSIS IR ve vašem kontejneru objektů blob nemůže najít skript vlastní instalace (main.cmd). Ujistěte se, že kontejner obsahuje soubor main.cmd, který je vstupním bodem pro vlastní instalaci.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Tato chyba znamená, že selhalo spuštění skriptu vlastní instalace (main.cmd). Vyzkoušejte skript nejprve na místním počítači nebo zkontrolujte protokoly spuštění vlastní instalace v kontejneru objektů blob.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Tato chyba znamená, že došlo k vypršení časového limitu spuštění skriptu vlastní instalace. Ujistěte se, že se váš skript dá spustit Tichě a není potřeba žádný interaktivní vstup, a ujistěte se, že váš kontejner objektů BLOB obsahuje jenom nezbytné vlastní instalační soubory. Doporučuje se nejdřív otestovat skript na místním počítači. Měli byste také zkontrolovat protokoly spuštění vlastní instalace v kontejneru objektů blob. Vlastní instalace může trvat maximálně 45 minut, pak dojde k vypršení časového limitu. Tato maximální doba zahrnuje čas potřebný ke stažení všech souborů z kontejneru a jejich instalaci v prostředí SSIS IR. Pokud potřebujete tuto dobu prodloužit, vytvořte lístek podpory.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Tato chyba znamená, že došlo k selhání při pokusu o nahrání protokolů spuštění vlastní instalace do kontejneru objektů blob. K tomuto problému dochází proto, že prostředí SSIS IR nemá oprávnění k zápisu do kontejneru objektů blob, nebo kvůli problémům se sítí nebo úložištěm. Pokud vlastní instalace proběhne úspěšně, tato chyba neovlivní fungování prostředí SSIS, ale nebudou k dispozici protokoly. Pokud vlastní instalace selže s jinou chybou a protokol se nenahraje, ohlásíme nejprve tuto chybu, aby bylo možné nahrát protokol k analýze. Jakmile se tento problém vyřeší, ohlásíme také všechny další konkrétní problémy. Pokud se tento problém nevyřeší opakovaným pokusem, kontaktujte tým podpory pro Azure Data Factory.

## <a name="virtual-network-configuration"></a>Konfigurace virtuální sítě

Když připojíte prostředí SSIS IR k virtuální síti Azure, SSIS IR bude využívat virtuální síť v rámci předplatného uživatele. Další informace najdete v tématu [Připojení prostředí Azure-SSIS Integration Runtime k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Pokud dojde k problému, který souvisí s virtuální sítí, zobrazí se některá z následujících chyb.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

K této chybě může dojít z nejrůznějších důvodů. Informace o jejím řešení najdete v částech [Forbidden](#forbidden), [InvalidPropertyValue](#invalidpropertyvalue) a [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings).

### <a name="forbidden"></a>Forbidden

Tento druh chyby může vypadat přibližně takto: "SubnetId není pro aktuální účet povolené. Zprostředkovatel prostředků Microsoft. Batch není zaregistrován v rámci stejného předplatného virtuální sítě.

Tyto podrobnosti znamenají, že služba Azure Batch nemá přístup k vaší virtuální síti. Zaregistrujte poskytovatele prostředků Microsoft.Batch ve stejném předplatném jako virtuální síť.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Tento druh chyby může vypadat nějak takto: 

- Buď zadaná virtuální síť neexistuje, nebo k ní služba Batch nemá přístup. "
- "Zadaná podsíť xxx neexistuje."

Tyto chyby znamenají, že virtuální síť neexistuje, služba Azure Batch k ní nemá přístup nebo neexistuje poskytovatel podsítě. Ujistěte se, že virtuální síť i podsíť existují a že k nim má služba Azure Batch přístup.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Tento druh chybové zprávy může vypadat takto: "nepovedlo se zřídit Integration Runtime ve virtuální síti. Pokud jsou nakonfigurovaná nastavení server DNS nebo NSG, ujistěte se, že je server DNS přístupný a že je NSG správně nakonfigurovaný. "

V této situaci pravděpodobně máte přizpůsobenou konfiguraci nastavení serveru DNS nebo skupiny zabezpečení sítě, což brání překladu názvu serveru Azure, který vyžaduje prostředí SSIS IR, nebo přístupu k tomuto serveru. Další informace najdete v tématu [Konfigurace virtuální sítě SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Pokud stále máte problémy, obraťte se na tým podpory pro Azure Data Factory.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

Prostředí SSIS IR se pravidelně automaticky aktualizuje. Během upgradu se vytvoří nový fond služby Azure Batch a starý fond služby Azure Batch se odstraní. Odstraní se také prostředky související s virtuální sítí pro starý fond a ve vašem předplatném se vytvoří nové prostředky související s virtuální sítí. Tato chyba znamená, že došlo k selhání odstranění prostředků souvisejících s virtuální sítí pro starý fond kvůli zámku proti odstranění na úrovni předplatného nebo skupiny prostředků. Vzhledem k tomu, že zámek proti odstranění řídí a nastavuje zákazník, musí ho v této situaci odstranit.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Pokud selže zřizování prostředí SSIS IR, všechny vytvořené prostředky se odstraní. Pokud je však na úrovni předplatného nebo skupiny prostředků zámek proti odstranění prostředků, k očekávanému odstranění prostředků virtuální sítě nedojde. Pokud chcete tuto chybu opravit, odeberte zámek proti odstranění a restartujte prostředí IR.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

Když zastavíte prostředí SSIS IR, všechny prostředky související s virtuální sítí se odstraní. Odstranění ale může selhat, pokud je na úrovni předplatného nebo skupiny prostředků zámek proti odstranění prostředků. Také v tomto případě zámek proti odstranění řídí a nastavuje zákazník. Proto musí zámek proti odstranění odebrat a pak znovu zastavit prostředí SSIS IR.

### <a name="nodeunavailable"></a>NodeUnavailable

K této chybě dochází, když je prostředí IR spuštěné, a znamená, že prostředí IR přešlo do špatného stavu. Tato chyba je vždy způsobená změnou v konfiguraci serveru DNS nebo skupiny zabezpečení sítě, která brání prostředí SSIS IR v připojení k potřebné službě. Vzhledem k tomu, že konfiguraci serveru DNS a skupiny zabezpečení sítě řídí zákazník, musí zákazník opravit blokující problémy na své straně. Další informace najdete v tématu [Konfigurace virtuální sítě SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Pokud stále máte problémy, obraťte se na tým podpory pro Azure Data Factory.
