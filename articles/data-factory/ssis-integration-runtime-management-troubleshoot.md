---
title: Řešení potíží se správou Integration Runtime SSIS
description: Tento článek popisuje pokyny k odstraňování potíží se správou SSIS Integration Runtime (SSIS IR)
ms.service: data-factory
ms.topic: conceptual
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
ms.custom: seo-lt-2019
ms.date: 07/08/2019
ms.openlocfilehash: 917bcce641cfd5178f868e4338354edb78062ab8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100361838"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Řešení potíží se správou SSIS Integration Runtime v Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

V tomto článku najdete pokyny k odstraňování potíží se správou v Azure-služba SSIS (SQL Server Integration Services) (SSIS) Integration Runtime (IR), označované taky jako SSIS IR.

## <a name="overview"></a>Přehled

Pokud narazíte na problém při zřizování nebo rušení zřizování SSIS IR, zobrazí se na portálu Microsoft Azure Data Factory chybová zpráva nebo v rutině PowerShellu se vrátila Chyba. Chyba se vždycky zobrazuje ve formátu kódu chyby s podrobnou chybovou zprávou.

Pokud kód chyby je Nenalezeno, služba obsahuje přechodné problémy a tuto operaci byste měli opakovat později. Pokud to nepomůže, obraťte se na tým podpory Azure Data Factory.

Jinak tři hlavní závislosti můžou způsobit chyby: Azure SQL Database nebo Azure SQL Managed instance, vlastní instalační skript a konfigurace virtuální sítě.

## <a name="sql-database-or-sql-managed-instance-issues"></a>Problémy s SQL Database nebo SQL Managed instance

Pokud vytváříte SSIS IR pomocí databáze katalogu SSIS, je vyžadována SQL Database nebo spravovaná instance SQL. SSIS IR musí být schopna získat přístup k SQL Database nebo ke spravované instanci SQL. Přihlašovací účet pro SQL Database nebo spravovanou instanci SQL musí mít také oprávnění k vytvoření databáze katalogu SSIS (SSISDB). Pokud dojde k chybě, na portálu služby Data Factory se zobrazí kód chyby s podrobnou zprávou o výjimce SQL. Při řešení potíží s jednotlivými kódy chyb využijte informace v následujícím seznamu.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

K tomuto problému může dojít při zřizování nového prostředí SSIS IR nebo za běhu prostředí IR. Pokud k této chybě dojde při zřizování prostředí IR, můžete z chybové zprávy získat podrobnou zprávu o výjimce SQL, která značí jeden z následujících problémů:

* Problém se síťovým připojením. Ověřte, zda je název hostitele pro SQL Database nebo spravovanou instanci SQL přístupný. Ověřte také, že žádná brána firewall ani skupina zabezpečení sítě (NSG) neblokuje přístup prostředí SSIS IR k serveru.
* Přihlášení selhalo během ověřování SQL. Zadaný účet se nemůže přihlásit k databázi SQL Serveru. Ujistěte se, že jste zadali správný uživatelský účet.
* Přihlášení selhalo během ověřování Microsoft Azure Active Directory (Azure AD) (spravovaná identita). Přidejte spravovanou identitu vaší továrny do skupiny AAD a ujistěte se, že spravovaná identita má přístupová oprávnění k databázovému serveru katalogu.
* Vypršení časového limitu připojení. Příčinou této chyby je vždy konfigurace související se zabezpečením. Doporučený postup:
  1. Vytvořte nový virtuální počítač.
  1. Připojte se k virtuálnímu počítači ke stejnému Microsoft Azure Virtual Network IR, pokud je IR ve virtuální síti.
  1. Nainstalujte SSMS a podívejte se na stav spravované instance SQL SQL Database nebo SQL.

V případě jiných problémů opravte problém uvedený v podrobné chybové zprávě o výjimce SQL. Pokud máte potíže i nadále, obraťte se na tým podpory spravované instance SQL SQL Database nebo SQL.

Pokud se zobrazí chyba, když je spuštěný IR, Změna skupiny zabezpečení sítě nebo brány firewall pravděpodobně brání uzlu SSIS IR v přístupu k SQL Database nebo spravované instanci SQL. Odblokujte pracovní uzel SSIS IR, aby mohl přistupovat k SQL Database nebo spravované instanci SQL.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Tento druh chybové zprávy může vypadat takto: "databáze ' SSISDB ' dosáhla kvóty velikosti. Můžete rozdělit nebo odstranit data, vyřadit indexy nebo si v dokumentaci vyhledat možná řešení. " 

Mezi možná řešení patří:
* Zvyšte velikost kvóty vašeho SSISDB.
* Zmenšení velikosti změnou konfigurace databáze SSISDB:
   * Zkrácení doby uchování a počtu verzí projektu.
   * Zkrácení doby uchování protokolu.
   * Změna výchozí úrovně protokolu.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Tato chyba znamená, SQL Database nebo spravovaná instance SQL již má SSISDB a že je používána jiným IR. Je nutné zadat jinou SQL Database nebo spravovanou instanci SQL nebo jinak odstranit existující SSISDB a restartovat nové prostředí IR.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

K této chybě může dojít z některého z následujících důvodů:

* Uživatelský účet nakonfigurovaný pro prostředí SSIS IR nemá oprávnění k vytvoření databáze. Můžete uživateli udělit oprávnění k vytvoření databáze.
* Při vytváření databáze dojde k vypršení časového limitu například spuštění nebo databázové operace. Operaci byste měli zopakovat později. Pokud to nepomůže, obraťte se na tým podpory spravované instance SQL SQL Database nebo SQL.

V případě jiných problémů zkontrolujte chybovou zprávu o výjimce SQL a opravte problém uvedený v podrobnostech o chybě. Pokud máte potíže i nadále, obraťte se na tým podpory spravované instance SQL SQL Database nebo SQL.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Tento druh chybové zprávy vypadá takto: "neplatný název objektu catalog.catalog_properties." V této situaci už buď máte databázi s názvem SSISDB, ale ta se nevytvořila v SSIS IR, nebo se databáze nachází v neplatném stavu, který způsobuje chyby při posledním zřizování SSIS IR. Existující databázi můžete odstranit s názvem SSISDB nebo můžete nakonfigurovat novou SQL Database nebo spravovanou instanci SQL pro prostředí IR.

## <a name="custom-setup-issues"></a>Problémy s vlastním nastavením

Vlastní instalace nabízí rozhraní pro přidání vlastních pokynů k instalaci během zřizování a rekonfigurace prostředí SSIS IR. Další informace najdete v tématu [Přizpůsobení instalace prostředí Azure-SSIS Integration Runtime](./how-to-configure-azure-ssis-ir-custom-setup.md).

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

Tato chyba znamená, že došlo k vypršení časového limitu spuštění skriptu vlastní instalace. Ujistěte se, že je možné váš skript spustit bez upozornění a bez interaktivního vstupu, a také se ujistěte, že váš kontejner objektů blob obsahuje pouze potřebné soubory vlastní instalace. Doporučujeme nejdříve skript otestovat na místním počítači. Měli byste také zkontrolovat protokoly spuštění vlastní instalace v kontejneru objektů blob. Vlastní instalace může trvat maximálně 45 minut, pak dojde k vypršení časového limitu. Tato maximální doba zahrnuje čas potřebný ke stažení všech souborů z kontejneru a jejich instalaci v prostředí SSIS IR. Pokud potřebujete tuto dobu prodloužit, vytvořte lístek podpory.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Tato chyba znamená, že došlo k selhání při pokusu o nahrání protokolů spuštění vlastní instalace do kontejneru objektů blob. K tomuto problému dochází proto, že prostředí SSIS IR nemá oprávnění k zápisu do kontejneru objektů blob, nebo kvůli problémům se sítí nebo úložištěm. Pokud vlastní instalace proběhne úspěšně, tato chyba neovlivní fungování prostředí SSIS, ale nebudou k dispozici protokoly. Pokud vlastní instalace selže s jinou chybou a protokol se nenahraje, ohlásíme nejprve tuto chybu, aby bylo možné nahrát protokol k analýze. Jakmile se tento problém vyřeší, ohlásíme také všechny další konkrétní problémy. Pokud se tento problém nevyřeší opakovaným pokusem, kontaktujte tým podpory pro Azure Data Factory.

## <a name="virtual-network-configuration"></a>Konfigurace virtuální sítě

Když připojíte prostředí SSIS IR k virtuální síti Azure, SSIS IR bude využívat virtuální síť v rámci předplatného uživatele. Další informace najdete v tématu [Připojení prostředí Azure-SSIS Integration Runtime k virtuální síti](./join-azure-ssis-integration-runtime-virtual-network.md).
Po úspěšném spuštění SSIS IR dojde k problémům se síťovým připojením a můžete se pokusit diagnostikovat problém pomocí [Nástroje pro diagnostiku připojení](ssis-integration-runtime-diagnose-connectivity-faq.md) .
Pokud dojde k problému, který souvisí s virtuální sítí, zobrazí se některá z následujících chyb.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

K této chybě může dojít z nejrůznějších důvodů. Informace o jejím řešení najdete v částech [Forbidden](#forbidden), [InvalidPropertyValue](#invalidpropertyvalue) a [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings).

### <a name="forbidden"></a>Forbidden

Tento druh chyby může vypadat přibližně takto: "SubnetId není pro aktuální účet povolené. Poskytovatel prostředků Microsoft.Batch není zaregistrován v rámci stejného předplatného virtuální sítě. "

Tyto podrobnosti znamenají, že služba Azure Batch nemá přístup k vaší virtuální síti. Zaregistrujte poskytovatele prostředků Microsoft.Batch ve stejném předplatném jako virtuální síť.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Tento druh chyby může vypadat nějak takto: 

- Buď zadaná virtuální síť neexistuje, nebo k ní služba Batch nemá přístup. "
- "Zadaná podsíť xxx neexistuje."

Tyto chyby znamenají, že virtuální síť neexistuje, služba Azure Batch k ní nemá přístup nebo neexistuje poskytovatel podsítě. Ujistěte se, že virtuální síť i podsíť existují a že k nim má služba Azure Batch přístup.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Tento druh chybové zprávy může vypadat takto: "nepovedlo se zřídit Integration Runtime ve virtuální síti. Pokud jsou nakonfigurovaná nastavení server DNS nebo NSG, ujistěte se, že je server DNS přístupný a že je NSG správně nakonfigurovaný. "

V této situaci pravděpodobně máte přizpůsobenou konfiguraci nastavení serveru DNS nebo skupiny zabezpečení sítě, což brání překladu názvu serveru Azure, který vyžaduje prostředí SSIS IR, nebo přístupu k tomuto serveru. Další informace najdete v tématu [Konfigurace virtuální sítě SSIS IR](./join-azure-ssis-integration-runtime-virtual-network.md). Pokud stále máte problémy, obraťte se na tým podpory pro Azure Data Factory.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

Prostředí SSIS IR se pravidelně automaticky aktualizuje. Během upgradu se vytvoří nový fond služby Azure Batch a starý fond služby Azure Batch se odstraní. Odstraní se také prostředky související s virtuální sítí pro starý fond a ve vašem předplatném se vytvoří nové prostředky související s virtuální sítí. Tato chyba znamená, že došlo k selhání odstranění prostředků souvisejících s virtuální sítí pro starý fond kvůli zámku proti odstranění na úrovni předplatného nebo skupiny prostředků. Vzhledem k tomu, že zámek proti odstranění řídí a nastavuje zákazník, musí ho v této situaci odstranit.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Pokud selže zřizování prostředí SSIS IR, všechny vytvořené prostředky se odstraní. Pokud je však na úrovni předplatného nebo skupiny prostředků zámek proti odstranění prostředků, k očekávanému odstranění prostředků virtuální sítě nedojde. Pokud chcete tuto chybu opravit, odeberte zámek proti odstranění a restartujte prostředí IR.

### <a name="vnetresourcegrouplockedduringstopvnetdeletelock"></a>VNetResourceGroupLockedDuringStop/VNetDeleteLock

Když zastavíte prostředí SSIS IR, všechny prostředky související s virtuální sítí se odstraní. Odstranění ale může selhat, pokud je na úrovni předplatného nebo skupiny prostředků zámek proti odstranění prostředků. Také v tomto případě zámek proti odstranění řídí a nastavuje zákazník. Proto musí zámek proti odstranění odebrat a pak znovu zastavit prostředí SSIS IR.

### <a name="nodeunavailable"></a>NodeUnavailable

K této chybě dochází, když je prostředí IR spuštěné, a znamená, že prostředí IR přešlo do špatného stavu. Tato chyba je vždy způsobená změnou v konfiguraci serveru DNS nebo skupiny zabezpečení sítě, která brání prostředí SSIS IR v připojení k potřebné službě. Vzhledem k tomu, že konfiguraci serveru DNS a skupiny zabezpečení sítě řídí zákazník, musí zákazník opravit blokující problémy na své straně. Další informace najdete v tématu [Konfigurace virtuální sítě SSIS IR](./join-azure-ssis-integration-runtime-virtual-network.md). Pokud stále máte problémy, obraťte se na tým podpory pro Azure Data Factory.

## <a name="static-public-ip-addresses-configuration"></a>Konfigurace statických veřejných IP adres

Když se připojíte k Azure-SSIS IR ke službě Azure Virtual Network, můžete také využít vlastní statické veřejné IP adresy pro infračervený přenos, aby IR mohl přistupovat ke zdrojům dat, které omezují přístup ke konkrétním IP adresám. Další informace najdete v tématu [Připojení prostředí Azure-SSIS Integration Runtime k virtuální síti](./join-azure-ssis-integration-runtime-virtual-network.md).

Kromě výše uvedených potíží s virtuální sítí můžete také splnit problémy související se statickými veřejnými IP adresami. Nápovědu najdete v následujících chybách.

### <a name="invalidpublicipspecified"></a><a name="InvalidPublicIPSpecified"></a>InvalidPublicIPSpecified

K této chybě může dojít z nejrůznějších důvodů při spuštění Azure-SSIS IR:

| Chybová zpráva | Řešení|
|:--- |:--- |
| Zadaná statická veřejná IP adresa se už používá. Zadejte prosím pro svůj Azure-SSIS Integration Runtime dvě nepoužité. | Měli byste vybrat dvě nepoužívané statické veřejné IP adresy nebo odebrat aktuální odkazy na zadanou veřejnou IP adresu a pak restartovat Azure-SSIS IR. |
| Zadaná statická veřejná IP adresa nemá žádný název DNS, zadejte prosím pro Azure-SSIS Integration Runtime dva názvy DNS. | Název DNS veřejné IP adresy můžete nastavit v Azure Portal, jak ukazuje následující obrázek. Konkrétní kroky jsou následující: (1) otevřít Azure Portal a přejít na stránku prostředku této veřejné IP adresy; (2) vyberte **konfigurační** oddíl a nastavte název DNS a pak klikněte na tlačítko **Uložit** . (3) restartujte Azure-SSIS IR. |
| Zadaná virtuální síť VNet a statické veřejné IP adresy pro váš Azure-SSIS Integration Runtime musí být ve stejném umístění. | V souladu s požadavky sítě Azure by statická veřejná IP adresa a virtuální síť měla být ve stejném umístění a předplatném. Poskytněte prosím dvě platné statické veřejné IP adresy a restartujte Azure-SSIS IR. |
| Poskytnutá statická veřejná IP adresa je základní, zadejte prosím pro svůj Azure-SSIS Integration Runtime dvě standardní. | Nápovědu najdete v [části SKU veřejné IP adresy](../virtual-network/public-ip-addresses.md#sku) . |

![Prostředí Azure-SSIS IR](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

### <a name="publicipresourcegrouplockedduringstart"></a>PublicIPResourceGroupLockedDuringStart

Pokud se Azure-SSIS IR zřizování nepovede, odstraní se všechny prostředky, které byly vytvořeny. Pokud ale dojde k uzamčení prostředku v předplatném nebo ve skupině prostředků (která obsahuje vaši statickou veřejnou IP adresu), síťové prostředky se neodstraní podle očekávání. Chcete-li chybu opravit, odeberte zámek proti odstranění a restartujte technologii IR.

### <a name="publicipresourcegrouplockedduringstop"></a>PublicIPResourceGroupLockedDuringStop

Když zastavíte Azure-SSIS IR, odstraní se všechny síťové prostředky vytvořené ve skupině prostředků, která obsahuje vaši veřejnou IP adresu. Odstranění ale může selhat, pokud existuje zámek odstranění prostředku v předplatném nebo ve skupině prostředků (která obsahuje vaši statickou veřejnou IP adresu). Odeberte prosím zámek proti odstranění a restartujte IR.

### <a name="publicipresourcegrouplockedduringupgrade"></a>PublicIPResourceGroupLockedDuringUpgrade

Azure-SSIS IR se pravidelně aktualizuje v pravidelných intervalech. Během upgradu se vytvoří nové uzly IR a staré uzly se odstraní. Také se odstraní vytvořené síťové prostředky (například nástroj pro vyrovnávání zatížení a skupina zabezpečení sítě) pro staré uzly a nové síťové prostředky se vytvoří v rámci vašeho předplatného. Tato chyba znamená, že odstranění síťových prostředků pro staré uzly selhalo kvůli zámku odstranění v předplatném nebo ve skupině prostředků (která obsahuje vaši statickou veřejnou IP adresu). Odeberte prosím zámek proti odstranění, aby bylo možné vyčistit staré uzly a uvolnit statickou veřejnou IP adresu pro staré uzly. V opačném případě se statická veřejná IP adresa nedá uvolnit a nebudeme moct dál upgradovat IR.

### <a name="publicipnotusableduringupgrade"></a>PublicIPNotUsableDuringUpgrade

Pokud chcete použít vlastní statické veřejné IP adresy, je třeba poskytnout dvě veřejné IP adresy. Jedna z nich se použije k okamžitému vytvoření uzlů IR a druhá se použije při upgradu IR. K této chybě může dojít, pokud není během upgradu možné použít jinou veřejnou IP adresu. Možné příčiny najdete v  [InvalidPublicIPSpecified](#InvalidPublicIPSpecified) .