---
title: Poradce při potížích se správou runtime integrace SSIS
description: Tento článek obsahuje pokyny pro řešení potíží se správou prostředí SSIS Integration Runtime (SSIS IR)
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/08/2019
ms.openlocfilehash: 52b1d93935e6428563c72361655893ffddf8a507
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74941847"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Poradce při potížích se správou runtime integrace SSIS v Azure Data Factory

Tento článek obsahuje pokyny pro řešení potíží se správou v modulu Runtime integrace Azure-SQL Server Integration Services (SSIS), známém také jako SSIS IR.

## <a name="overview"></a>Přehled

Pokud narazíte na jakýkoli problém při zřizování nebo zrušení zřízení služby SSIS IR, zobrazí se chybová zpráva na portálu Microsoft Azure Data Factory nebo chyba vrácená z rutiny prostředí PowerShell. Chyba se vždy zobrazí ve formátu kódu chyby s podrobnou chybovou zprávou.

Pokud je kód chyby InternalServerError, služba má přechodné problémy a operaci byste měli zopakovat později. Pokud opakování nepomůže, obraťte se na tým podpory Azure Data Factory.

V opačném případě mohou způsobit chyby tři hlavní externí závislosti: server Azure SQL Database nebo spravovaná instance, vlastní instalační skript a konfigurace virtuální sítě.

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Problémy s databázovým serverem Azure SQL nebo spravovanou instancí

Pokud zřizujete prostředí SSIS IR s databází katalogu SSIS, vyžaduje se spravovaná instance nebo server služby Azure SQL Database. Prostředí SSIS IR musí mít přístup ke spravované instanci nebo serveru služby Azure SQL Database. Účet spravované instance nebo serveru služby Azure SQL Database by také měl mít oprávnění k vytvoření databáze katalogu SSIS (SSISDB). Pokud dojde k chybě, na portálu služby Data Factory se zobrazí kód chyby s podrobnou zprávou o výjimce SQL. Při řešení potíží s jednotlivými kódy chyb využijte informace v následujícím seznamu.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

K tomuto problému může dojít při zřizování nového prostředí SSIS IR nebo za běhu prostředí IR. Pokud k této chybě dojde při zřizování prostředí IR, můžete z chybové zprávy získat podrobnou zprávu o výjimce SQL, která značí jeden z následujících problémů:

* Problém se síťovým připojením. Zkontrolujte, jestli je dostupný název hostitele SQL Serveru nebo spravované instance. Ověřte také, že žádná brána firewall ani skupina zabezpečení sítě (NSG) neblokuje přístup prostředí SSIS IR k serveru.
* Přihlášení selhalo během ověřování SQL. Zadaný účet se nemůže přihlásit k databázi SQL Serveru. Ujistěte se, že jste zadali správný uživatelský účet.
* Přihlášení selhalo během ověřování Microsoft Azure Active Directory (Azure AD) (spravovaná identita). Přidejte spravovanou identitu vaší továrny do skupiny AAD a ujistěte se, že spravovaná identita má přístupová oprávnění k databázovému serveru katalogu.
* Vypršení časového limitu připojení. Příčinou této chyby je vždy konfigurace související se zabezpečením. Doporučený postup:
  1. Vytvořte nový virtuální virtuální ms.
  1. Připojte virtuální počítač ke stejné virtuální síti Microsoft Azure infračerveného zařízení, pokud je infračervený přenos ve virtuální síti.
  1. Nainstalujte SSMS a zkontrolujte stav azure sql database serveru nebo spravované instance.

V případě jiných problémů opravte problém uvedený v podrobné chybové zprávě o výjimce SQL. Pokud stále máte problémy, kontaktujte tým podpory pro spravovanou instanci nebo server služby Azure SQL Database.

Pokud k této chybě dojde za běhu prostředí IR, nějaké změny skupiny zabezpečení sítě nebo brány firewall pravděpodobně brání pracovnímu uzlu prostředí SSIS IR v přístupu ke spravované instanci nebo serveru služby Azure SQL Database. Odblokujte pracovní uzel prostředí SSIS IR, aby mohl získat přístup ke spravované instanci nebo serveru služby Azure SQL Database.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Zde je to, co tento druh chybové zprávy může vypadat: "Databáze 'SSISDB' dosáhla své kvóty velikosti. Rozdělení nebo odstranění dat, přetažení indexů nebo prohledání možných řešení v dokumentaci." 

Mezi možná řešení patří:
* Zvětšete velikost kvóty sisisdb.
* Zmenšení velikosti změnou konfigurace databáze SSISDB:
   * Zkrácení doby uchovávání a počtu verzí projektu.
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

Tento druh chybové zprávy vypadá takto: "Neplatný název objektu 'catalog.catalog_properties'." V takovém případě buď již máte databázi s názvem SSISDB, ale nebyla vytvořena pomocí služby SSIS IR, nebo je databáze v neplatném stavu, který je způsoben chybami v posledním zřizování infračerveného systému SSIS. Můžete odstranit stávající databázi s názvem SSISDB nebo pro prostředí IR můžete nakonfigurovat novou spravovanou instanci nebo server služby Azure SQL Database.

## <a name="custom-setup-issues"></a>Problémy s vlastním nastavením

Vlastní instalace nabízí rozhraní pro přidání vlastních pokynů k instalaci během zřizování a rekonfigurace prostředí SSIS IR. Další informace najdete v tématu [Přizpůsobení instalace prostředí Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

Ujistěte se, že váš kontejner obsahuje pouze potřebné soubory vlastní instalace. Všechny soubory v kontejneru se stáhnou do pracovního uzlu prostředí SSIS IR. Před spuštěním skriptu vlastní instalace v prostředí SSIS IR doporučujeme skript otestovat na místním počítači a opravit případné problémy s jeho spuštěním.

Vzhledem k tomu, že se prostředí SSIS IR pravidelně aktualizuje, za běhu prostředí IR se zkontroluje kontejner se skriptem vlastní instalace. Tyto aktualizace vyžadují přístup ke kontejneru, aby bylo možné stáhnout skript vlastní instalace a znovu provést instalaci. V rámci tohoto procesu se také zkontroluje, jestli je kontejner dostupný a jestli existuje soubor main.cmd.

U každé chyby, která zahrnuje vlastní nastavení, se zobrazí kód chyby CustomSetupScriptFailure s dílčím kódem, jako je CustomSetupScriptBlobContainerInaccessible nebo CustomSetupScriptNotFound.

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Tato chyba znamená, že prostředí SSIS IR nemá přístup ke kontejneru objektů blob v Azure pro vlastní instalaci. Ujistěte se, že je identifikátor URI SAS kontejneru dostupný a že jeho platnost nevypršela.

Pokud je prostředí IR spuštěné, zastavte ho, nakonfigurujte v něm nový identifikátor URI SAS kontejneru vlastní instalace a pak ho restartujte.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Tato chyba znamená, že prostředí SSIS IR ve vašem kontejneru objektů blob nemůže najít skript vlastní instalace (main.cmd). Ujistěte se, že kontejner obsahuje soubor main.cmd, který je vstupním bodem pro vlastní instalaci.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Tato chyba znamená, že selhalo spuštění skriptu vlastní instalace (main.cmd). Vyzkoušejte skript nejprve na místním počítači nebo zkontrolujte protokoly spuštění vlastní instalace v kontejneru objektů blob.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Tato chyba znamená, že došlo k vypršení časového limitu spuštění skriptu vlastní instalace. Ujistěte se, že je možné váš skript spustit bez upozornění a bez interaktivního vstupu, a také se ujistěte, že váš kontejner objektů blob obsahuje pouze potřebné soubory vlastní instalace. Doporučujeme nejdříve skript otestovat na místním počítači. Měli byste také zkontrolovat protokoly spuštění vlastní instalace v kontejneru objektů blob. Vlastní instalace může trvat maximálně 45 minut, pak dojde k vypršení časového limitu. Tato maximální doba zahrnuje čas potřebný ke stažení všech souborů z kontejneru a jejich instalaci v prostředí SSIS IR. Pokud potřebujete tuto dobu prodloužit, vytvořte lístek podpory.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Tato chyba znamená, že došlo k selhání při pokusu o nahrání protokolů spuštění vlastní instalace do kontejneru objektů blob. K tomuto problému dochází proto, že prostředí SSIS IR nemá oprávnění k zápisu do kontejneru objektů blob, nebo kvůli problémům se sítí nebo úložištěm. Pokud vlastní instalace proběhne úspěšně, tato chyba neovlivní fungování prostředí SSIS, ale nebudou k dispozici protokoly. Pokud vlastní instalace selže s jinou chybou a protokol se nenahraje, ohlásíme nejprve tuto chybu, aby bylo možné nahrát protokol k analýze. Jakmile se tento problém vyřeší, ohlásíme také všechny další konkrétní problémy. Pokud se tento problém nevyřeší opakovaným pokusem, kontaktujte tým podpory pro Azure Data Factory.

## <a name="virtual-network-configuration"></a>Konfigurace virtuální sítě

Když připojíte prostředí SSIS IR k virtuální síti Azure, SSIS IR bude využívat virtuální síť v rámci předplatného uživatele. Další informace najdete v tématu [Připojení prostředí Azure-SSIS Integration Runtime k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Pokud dojde k problému, který souvisí s virtuální sítí, zobrazí se některá z následujících chyb.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

K této chybě může dojít z nejrůznějších důvodů. Informace o jejím řešení najdete v částech [Forbidden](#forbidden), [InvalidPropertyValue](#invalidpropertyvalue) a [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings).

### <a name="forbidden"></a>Forbidden

Tento druh chyby může vypadat takto: "Podsouborní číslo Není povoleno pro běžný účet. Poskytovatel prostředků Microsoft.Batch není registrovaný pod stejným předplatným virtuální sítě."

Tyto podrobnosti znamenají, že služba Azure Batch nemá přístup k vaší virtuální síti. Zaregistrujte poskytovatele prostředků Microsoft.Batch ve stejném předplatném jako virtuální síť.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Tento druh chyby může vypadat nějak takto: 

- "Zadaná virtuální síť neexistuje nebo k ní služba Batch nemá přístup."
- "Zadaná podsíť xxx neexistuje."

Tyto chyby znamenají, že virtuální síť neexistuje, služba Azure Batch k ní nemá přístup nebo neexistuje poskytovatel podsítě. Ujistěte se, že virtuální síť i podsíť existují a že k nim má služba Azure Batch přístup.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Tento druh chybové zprávy může vypadat takto: "Nepodařilo se zřídit integrační runtime ve virtuální síti. Pokud jsou nakonfigurována nastavení serveru DNS nebo souboru zabezpečení sítě, zkontrolujte, zda je server DNS přístupný a že je správně nakonfigurován soubor zabezpečení sítě."

V této situaci pravděpodobně máte přizpůsobenou konfiguraci nastavení serveru DNS nebo skupiny zabezpečení sítě, což brání překladu názvu serveru Azure, který vyžaduje prostředí SSIS IR, nebo přístupu k tomuto serveru. Další informace najdete v tématu [Konfigurace virtuální sítě SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Pokud stále máte problémy, obraťte se na tým podpory pro Azure Data Factory.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

Prostředí SSIS IR se pravidelně automaticky aktualizuje. Během upgradu se vytvoří nový fond služby Azure Batch a starý fond služby Azure Batch se odstraní. Odstraní se také prostředky související s virtuální sítí pro starý fond a ve vašem předplatném se vytvoří nové prostředky související s virtuální sítí. Tato chyba znamená, že došlo k selhání odstranění prostředků souvisejících s virtuální sítí pro starý fond kvůli zámku proti odstranění na úrovni předplatného nebo skupiny prostředků. Vzhledem k tomu, že zámek proti odstranění řídí a nastavuje zákazník, musí ho v této situaci odstranit.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Pokud selže zřizování prostředí SSIS IR, všechny vytvořené prostředky se odstraní. Pokud je však na úrovni předplatného nebo skupiny prostředků zámek proti odstranění prostředků, k očekávanému odstranění prostředků virtuální sítě nedojde. Pokud chcete tuto chybu opravit, odeberte zámek proti odstranění a restartujte prostředí IR.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

Když zastavíte prostředí SSIS IR, všechny prostředky související s virtuální sítí se odstraní. Odstranění ale může selhat, pokud je na úrovni předplatného nebo skupiny prostředků zámek proti odstranění prostředků. Také v tomto případě zámek proti odstranění řídí a nastavuje zákazník. Proto musí zámek proti odstranění odebrat a pak znovu zastavit prostředí SSIS IR.

### <a name="nodeunavailable"></a>NodeUnavailable

K této chybě dochází, když je prostředí IR spuštěné, a znamená, že prostředí IR přešlo do špatného stavu. Tato chyba je vždy způsobená změnou v konfiguraci serveru DNS nebo skupiny zabezpečení sítě, která brání prostředí SSIS IR v připojení k potřebné službě. Vzhledem k tomu, že konfiguraci serveru DNS a skupiny zabezpečení sítě řídí zákazník, musí zákazník opravit blokující problémy na své straně. Další informace najdete v tématu [Konfigurace virtuální sítě SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Pokud stále máte problémy, obraťte se na tým podpory pro Azure Data Factory.

## <a name="static-public-ip-addresses-configuration"></a>Konfigurace statických veřejných IP adres

Když se připojíte k zařízení Azure-SSIS IR do virtuální sítě Azure, můžete také přenést vlastní statické veřejné IP adresy pro infračervený přenos, aby infračervený přenos měl přístup ke zdrojům dat, které omezují přístup k určitým IP adresám. Další informace najdete v tématu [Připojení prostředí Azure-SSIS Integration Runtime k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Kromě výše uvedených problémů s virtuální sítí se můžete setkat také se statickými problémy souvisejícími s veřejnými IP adresami. V nápovědě naleznete následující chyby.

### <a name="invalidpublicipspecified"></a><a name="InvalidPublicIPSpecified"></a>Neplatný publicIPspecifikován

K této chybě může dojít z různých důvodů při spuštění azure-SSIS IR:

| Chybová zpráva | Řešení|
|:--- |:--- |
| Zadaný statický veřejný IP adresa se již používá, uveďte dvě nepoužívané pro váš Azure-SSIS Integration Runtime. | Měli byste vybrat dvě nepoužívané statické veřejné IP adresy nebo odebrat aktuální odkazy na zadanou veřejnou IP adresu a potom restartovat azure-SSIS IR. |
| Poskytnutá statická veřejná IP adresa nemá žádný název DNS, zadejte dva z nich s názvem DNS pro prostředí Azure-SSIS Integration Runtime. | Název DNS veřejné IP adresy můžete nastavit na webu Azure Portal, jak ukazuje obrázek níže. Konkrétní kroky jsou následující: (1) Otevřete portál Azure a přejděte na stránku prostředků této veřejné IP adresy; (2) Vyberte oddíl **Konfigurace** a nastavte název DNS a klepněte na tlačítko **Uložit.** (3) Restartujte infračervený přenos Zařízení Azure-SSIS. |
| Zadaný virtuální síť a statické veřejné IP adresy pro váš Azure-SSIS Integration Runtime musí být ve stejném umístění. | Podle požadavků sítě Azure by měla být statická veřejná IP adresa a virtuální síť ve stejném umístění a předplatné. Zadejte dvě platné statické veřejné IP adresy a restartujte infračervený přenos Azure-SSIS. |
| Poskytnutá statická veřejná IP adresa je základní, poskytněte dvě standardní pro prostředí Azure-SSIS Integration Runtime. | Nápovědu naleznete v [části SKU veřejné IP adresy.](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku) |

![Prostředí Azure-SSIS IR](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

### <a name="publicipresourcegrouplockedduringstart"></a>PublicIPResourceGroupLockedDuringStart

Pokud azure-SSIS Ir zřizování selže, všechny prostředky, které byly vytvořeny jsou odstraněny. Pokud je však prostředek odstranit zámek na úrovni předplatného nebo skupiny prostředků (která obsahuje statickou veřejnou IP adresu), síťové prostředky nejsou odstraněny podle očekávání. Chcete-li chybu opravit, odeberte zámek odstranění a restartujte infračervený přenos.

### <a name="publicipresourcegrouplockedduringstop"></a>PublicIPResourceGroupLockedDuringStop

Když zastavíte Azure-SSIS IR, budou odstraněny všechny síťové prostředky vytvořené ve skupině prostředků obsahující vaši veřejnou IP adresu. Odstranění však může selhat, pokud je zámek odstranění prostředku na úrovni předplatného nebo skupiny prostředků (která obsahuje statickou veřejnou IP adresu). Odstraňte zámek odstranění a restartujte infračervený přenos.

### <a name="publicipresourcegrouplockedduringupgrade"></a>PublicIPResourceGroupLockedDuringUpgrade

Azure-SSIS IR se pravidelně aktualizuje automaticky. Nové infračervené uzly jsou vytvořeny během upgradu a staré uzly budou odstraněny. Vytvořené síťové prostředky (např. správce zatížení a skupina zabezpečení sítě) pro staré uzly jsou také odstraněny a nové síťové prostředky jsou vytvořeny v rámci vašeho předplatného. Tato chyba znamená, že odstranění síťových prostředků pro staré uzly se nezdařilo z důvodu odstranění zámku na úrovni předplatného nebo prostředků (která obsahuje statickou veřejnou IP adresu). Odeberte zámek odstranění, abychom mohli vyčistit staré uzly a uvolnit statickou veřejnou IP adresu pro staré uzly. V opačném případě nelze uvolnit statickou veřejnou IP adresu a nebudeme moci infračervený přenos dále upgradovat.

### <a name="publicipnotusableduringupgrade"></a>PublicIPNotUsableduringUpgrade

Pokud chcete přenést vlastní statické veřejné IP adresy, měly by být k dispozici dvě veřejné IP adresy. Jeden z nich bude použit k okamžitému vytvoření infračervených uzlů a další bude použit při upgradu infračerveného uzliny. K této chybě může dojít, pokud je během inovace nepoužitelná jiná veřejná adresa IP. Možné příčiny naleznete [v poli InvalidPublicIPSpecified.](#InvalidPublicIPSpecified)