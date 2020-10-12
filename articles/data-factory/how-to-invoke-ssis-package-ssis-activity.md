---
title: Spuštění balíčku SSIS pomocí aktivity balíčku Execute SSIS
description: Tento článek popisuje, jak spustit balíček služba SSIS (SQL Server Integration Services) (SSIS) v kanálu Azure Data Factory pomocí aktivity spustit balíček SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.date: 07/20/2020
ms.openlocfilehash: 901693c512ddfcf5d3c4dafaec71b1606b5dc5f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89077842"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Spuštění balíčku SSIS pomocí aktivity Spustit balíček SSIS ve službě Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje, jak spustit balíček služba SSIS (SQL Server Integration Services) (SSIS) v kanálu Azure Data Factory pomocí aktivity spustit balíček SSIS. 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vytvořte prostředí Azure-SSIS Integration runtime (IR), pokud ho ještě nemáte, podle podrobných pokynů v tomto [kurzu: zřizování Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Spuštění balíčku v Azure Portal
V této části použijete Data Factory uživatelské rozhraní (UI) nebo aplikace k vytvoření kanálu Data Factory s aktivitou spustit SSIS balíčku, která spouští balíček SSIS.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Vytvoření kanálu s aktivitou Execute SSIS Package
V tomto kroku použijete Data Factory uživatelské rozhraní nebo aplikaci k vytvoření kanálu. Přidáte do kanálu aktivitu spustit balíček SSIS a nakonfigurujete ji tak, aby běžela balíček SSIS. 

1. Na Data Factory přehled nebo na domovské stránce v Azure Portal na samostatné kartě spusťte Data Factory uživatelské rozhraní nebo aplikaci na dlaždici **autora &ho monitoru** . 

   ![Data Factory domovskou stránku](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Na stránce **Začínáme** vyberte **Vytvořit kanál**. 

   ![Stránka Začínáme](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. Na panelu nástrojů **aktivity** rozbalte **Obecné**. Poté přetáhněte aktivitu **spustit balíček SSIS** na plochu návrháře kanálu. 

   ![Přetáhněte aktivitu spustit balíček SSIS na plochu návrháře](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

   Vyberte objekt aktivity balíčku Execute SSIS a nakonfigurujte jeho **Obecné**, **Nastavení**, **parametry SSIS**, **Správce připojení**a záložky **vlastností Overrides** .

#### <a name="general-tab"></a>Karta Obecné

Na kartě **Obecné** v aktivitě SSIS balíčku proveďte následující kroky.

![Nastavení vlastností na kartě Obecné](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

   1. Do pole **název**zadejte název aktivity vykonání balíčku SSIS.

   1. Jako **Popis**zadejte popis aktivity SSIS balíčku Execute.

   1. V poli **časový limit**zadejte maximální dobu, po kterou může být spuštěná aktivita SSIS balíčku pro spuštění. Výchozí hodnota je 7 dní, formát je D. HH: MM: SS.

   1. Pro **opakování**zadejte maximální počet opakovaných pokusů o spuštění aktivity balíčku SSIS.

   1. V poli **interval opakování**zadejte počet sekund mezi jednotlivými pokusy o spuštění aktivity balíčku SSIS. Výchozí hodnota je 30 sekund.

   1. Zaškrtněte políčko **zabezpečený výstup** a zvolte, jestli chcete z protokolování vyloučit výstup aktivity SSIS balíčku Execute.

   1. Zaškrtnutím políčka **zabezpečený vstup** vyberte, zda chcete z protokolování vyloučit zadání aktivity balíčku Execute SSIS.

#### <a name="settings-tab"></a>Karta nastavení

Na kartě **Nastavení** aktivity spustit balíček SSIS proveďte následující kroky.

![Nastavení vlastností na kartě nastavení – automaticky](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   1. V **Azure-SSIS IR**vyberte určenou Azure-SSIS IR pro spuštění aktivity balíčku Execute SSIS.

   1. Jako **Popis**zadejte popis aktivity SSIS balíčku Execute.

   1. Zaškrtněte políčko **ověřování systému Windows** , chcete-li zvolit, zda chcete použít ověřování systému Windows pro přístup k úložištím dat, jako jsou například servery SQL nebo sdílené složky souborů v místním počítači nebo soubory Azure.
   
      Pokud zaškrtnete toto políčko, zadejte hodnoty přihlašovacích údajů pro spuštění balíčku do polí **doména**, **uživatelské jméno**a **heslo** . Například pro přístup k souborům Azure je doména `Azure` , uživatelské jméno `<storage account name>` a heslo `<storage account key>` .

      Alternativně můžete jako své hodnoty použít tajné kódy uložené v Azure Key Vault. Provedete to tak, že zaškrtnete políčko **Trezor klíčů Azure** vedle sebe. Vyberte nebo upravte existující propojenou službu trezoru klíčů nebo vytvořte novou. Pak vyberte název a verzi tajného klíče pro vaši hodnotu. Když vytváříte nebo upravujete propojenou službu trezoru klíčů, můžete vybrat nebo upravit existující Trezor klíčů nebo vytvořit nový. Pokud jste to ještě neudělali, ujistěte se, že jste vašemu trezoru klíčů udělili Data Factory spravovaný přístup k identitám. Svůj tajný klíč můžete zadat také přímo v následujícím formátu: `<key vault linked service name>/<secret name>/<secret version>` .
      
   1. Zaškrtnutím políčka **32-bit runtime** zvolte, jestli váš balíček potřebuje ke spuštění 32 běhový modul runtime.

   1. V případě **umístění balíčku**vyberte **SSISDB**, **systém souborů (balíček)**, **systém souborů (projekt)**, **vložený balíček**nebo **úložiště balíčků**. 

##### <a name="package-location-ssisdb"></a>Umístění balíčku: SSISDB

Pokud je vaše Azure-SSIS IR vytvořená pomocí katalogu SSIS (SSISDB) hostovaného serverem Azure SQL Database Server/spravovanou instancí, **SSISDB** při automatickém výběru umístění balíčku, nebo ho můžete vybrat sami. Pokud je vybraná, proveďte následující kroky.

   1. Pokud je spuštěný Azure-SSIS IR a políčko **Ruční Ruční zadání** není zaškrtnuto, vyhledejte a vyberte existující složky, projekty, balíčky a prostředí z SSISDB. Vyberte **aktualizovat** a načtěte vaše nově přidané složky, projekty, balíčky nebo prostředí z SSISDB, aby byly dostupné pro procházení a výběr. Chcete-li procházet a vybírat prostředí pro provádění balíčků, je nutné nakonfigurovat vaše projekty předem, aby byla tato prostředí přidána jako odkazy ze stejných složek v SSISDB. Další informace najdete v tématu [Vytvoření a mapování SSIS prostředí](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages).

   1. V části **úroveň protokolování**vyberte předdefinovaný rozsah protokolování pro spuštění balíčku. Pokud chcete místo toho zadat vlastní název protokolování, zaškrtněte políčko **vlastní** . 

   1. Pokud vaše Azure-SSIS IR není spuštěná nebo je zaškrtnuté políčko **Ruční** zadání, zadejte cestu k balíčku a prostředí z SSISDB přímo v následujících formátech: `<folder name>/<project name>/<package name>.dtsx` a `<folder name>/<environment name>` .

      ![Nastavení vlastností na kartě Nastavení – ruční](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

##### <a name="package-location-file-system-package"></a>Umístění balíčku: systém souborů (balíček)

**Systém souborů (balíček)** se automaticky vybere, pokud se váš Azure-SSIS IR ZŘÍDIL bez SSISDB, nebo ho můžete vybrat sami. Pokud je vybraná, proveďte následující kroky.

![Nastavení vlastností na kartě nastavení – systém souborů (balíček)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)
   
   1. Určete balíček, který se má spustit, zadáním cesty UNC (Universal Naming Convention) k souboru balíčku (s `.dtsx` ) v poli **cesta k balíčku** . Balíček můžete procházet a vybrat výběrem možnosti **Procházet soubor úložiště** nebo zadat jeho cestu ručně. Pokud například uložíte balíček do souborů Azure, jeho cesta je `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx` . 
   
   1. Pokud nakonfigurujete balíček v samostatném souboru, budete také muset zadat cestu UNC ke konfiguračnímu souboru (s `.dtsConfig` ) v poli **cesta konfigurace** . Můžete procházet a vybírat konfiguraci výběrem možnosti **Procházet soubor úložiště** nebo zadat jeho cestu ručně. Pokud například uložíte konfiguraci do souborů Azure, je její cesta `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` .

   1. Zadejte přihlašovací údaje pro přístup k balíčku a konfigurační soubory. Pokud jste dříve zadali hodnoty přihlašovacích údajů pro spuštění balíčku (pro **ověřování systému Windows**), můžete je znovu použít zaškrtnutím políčka **stejné jako přihlašovací údaje pro spuštění balíčku** . V opačném případě zadejte hodnoty přihlašovacích údajů k balíčku do polí **doména**, **uživatelské jméno**a **heslo** . Pokud například uložíte balíček a konfiguraci do souborů Azure, doména je `Azure` , uživatelské jméno `<storage account name>` a heslo `<storage account key>` . 

      Alternativně můžete jako své hodnoty použít tajné kódy uložené v Azure Key Vault. Provedete to tak, že zaškrtnete políčko **Trezor klíčů Azure** vedle sebe. Vyberte nebo upravte existující propojenou službu trezoru klíčů nebo vytvořte novou. Pak vyberte název a verzi tajného klíče pro vaši hodnotu. Když vytváříte nebo upravujete propojenou službu trezoru klíčů, můžete vybrat nebo upravit existující Trezor klíčů nebo vytvořit nový. Pokud jste to ještě neudělali, ujistěte se, že jste vašemu trezoru klíčů udělili Data Factory spravovaný přístup k identitám. Svůj tajný klíč můžete zadat také přímo v následujícím formátu: `<key vault linked service name>/<secret name>/<secret version>` . 

      Tyto přihlašovací údaje se také používají pro přístup k podřízeným balíčkům v úloze spustit balíček, na které odkazuje vlastní cesta a další konfigurace, které jsou určeny v balíčcích. 

   1. Pokud jste při vytváření balíčku pomocí nástroje SQL Server Data Tools (SSDT) použili úroveň ochrany **EncryptAllWithPassword** nebo **EncryptSensitiveWithPassword** , zadejte v poli **Šifrovací heslo** hodnotu pro heslo. Alternativně můžete použít tajný kód uložený v Azure Key Vault jako jeho hodnotu (viz výše).
      
      Pokud jste použili úroveň ochrany **EncryptSensitiveWithUserKey** , znovu zadejte své citlivé hodnoty do konfiguračních souborů nebo na karty **SSIS**, **Správce připojení**nebo **přepisy vlastností** (viz níže).
      
      Pokud jste použili úroveň ochrany **EncryptAllWithUserKey** , není podporována. Musíte znovu nakonfigurovat balíček tak, aby používal jinou úroveň ochrany prostřednictvím SSDT nebo `dtutil` nástroje příkazového řádku. 

   1. V části **úroveň protokolování**vyberte předdefinovaný rozsah protokolování pro spuštění balíčku. Pokud chcete místo toho zadat vlastní název protokolování, zaškrtněte políčko **vlastní** . 
   
   1. Pokud chcete protokolovat provádění balíčků mimo použití standardních zprostředkovatelů protokolů, které lze zadat v balíčku, zadejte svou složku protokolu zadáním cesty UNC do pole **cesta protokolování** . Složku protokolu můžete procházet a vybrat tak, že vyberete **Procházet soubor úložiště** nebo zadat jeho cestu ručně. Pokud například uložíte protokoly do souborů Azure, vaše cesta protokolování je `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . V této cestě se vytvoří podsložka pro každý spuštěný balíček, který se pojmenuje po spuštění ID běhu aktivity balíčku SSIS a kdy se soubory protokolů generují každých pět minut. 
   
   1. Zadejte přihlašovací údaje pro přístup do složky protokolu. Pokud jste dříve zadali hodnoty přihlašovacích údajů pro přístup k balíčku (viz výše), můžete je znovu použít tak, že vyberete **stejné jako Přístupová pověření k balíčku** . V opačném případě zadejte hodnoty přihlašovacích údajů pro přístup k přihlášení do polí **doména**, **uživatelské jméno**a **heslo** . Pokud například uložíte protokoly do souborů Azure, doména je `Azure` , uživatelské jméno `<storage account name>` a heslo `<storage account key>` . Alternativně můžete použít tajné klíče uložené v Azure Key Vault jako jejich hodnoty (viz výše).
   
U všech výše zmíněných cest UNC musí mít plně kvalifikovaný název souboru méně než 260 znaků. Název adresáře musí být kratší než 248 znaků.

##### <a name="package-location-file-system-project"></a>Umístění balíčku: systém souborů (projekt)

Pokud jako umístění balíčku vyberete **systém souborů (projekt)** , proveďte následující kroky.

![Nastavení vlastností na kartě nastavení – systém souborů (projekt)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   1. Určete balíček, který se má spustit, zadáním cesty UNC k souboru projektu (s `.ispac` ) v poli **cesta k projektu** a souborem balíčku (s `.dtsx` ) z projektu v poli **název balíčku** . Můžete procházet a vybírat projekt výběrem možnosti **Procházet soubor úložiště** nebo zadat jeho cestu ručně. Pokud například uložíte projekt do souborů Azure, jeho cesta je `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac` .

   1. Zadejte přihlašovací údaje pro přístup k souborům projektu a balíčku. Pokud jste dříve zadali hodnoty přihlašovacích údajů pro spuštění balíčku (pro **ověřování systému Windows**), můžete je znovu použít zaškrtnutím políčka **stejné jako přihlašovací údaje pro spuštění balíčku** . V opačném případě zadejte hodnoty přihlašovacích údajů k balíčku do polí **doména**, **uživatelské jméno**a **heslo** . Pokud například uložíte projekt a balíček do souborů Azure, doména je `Azure` , uživatelské jméno `<storage account name>` a heslo `<storage account key>` . 

      Alternativně můžete jako své hodnoty použít tajné kódy uložené v Azure Key Vault. Provedete to tak, že zaškrtnete políčko **Trezor klíčů Azure** vedle sebe. Vyberte nebo upravte existující propojenou službu trezoru klíčů nebo vytvořte novou. Pak vyberte název a verzi tajného klíče pro vaši hodnotu. Když vytváříte nebo upravujete propojenou službu trezoru klíčů, můžete vybrat nebo upravit existující Trezor klíčů nebo vytvořit nový. Pokud jste to ještě neudělali, ujistěte se, že jste vašemu trezoru klíčů udělili Data Factory spravovaný přístup k identitám. Svůj tajný klíč můžete zadat také přímo v následujícím formátu: `<key vault linked service name>/<secret name>/<secret version>` . 

      Tyto přihlašovací údaje se také používají pro přístup k podřízeným balíčkům v úloze spustit balíček, na které se odkazuje ze stejného projektu. 

   1. Pokud jste při vytváření balíčku prostřednictvím SSDT použili úroveň ochrany **EncryptAllWithPassword** nebo **EncryptSensitiveWithPassword** , zadejte v poli **Šifrovací heslo** hodnotu pro heslo. Alternativně můžete použít tajný kód uložený v Azure Key Vault jako jeho hodnotu (viz výše).
      
      Pokud jste použili úroveň ochrany **EncryptSensitiveWithUserKey** , znovu zadejte citlivé hodnoty na kartách **SSIS**, **Connection**Manager nebo **přepisy vlastností** (viz níže).
      
      Pokud jste použili úroveň ochrany **EncryptAllWithUserKey** , není podporována. Musíte znovu nakonfigurovat balíček tak, aby používal jinou úroveň ochrany prostřednictvím SSDT nebo `dtutil` nástroje příkazového řádku. 

   1. V části **úroveň protokolování**vyberte předdefinovaný rozsah protokolování pro spuštění balíčku. Pokud chcete místo toho zadat vlastní název protokolování, zaškrtněte políčko **vlastní** . 
   
   1. Pokud chcete protokolovat provádění balíčků mimo použití standardních zprostředkovatelů protokolů, které lze zadat v balíčku, zadejte svou složku protokolu zadáním cesty UNC do pole **cesta protokolování** . Složku protokolu můžete procházet a vybrat tak, že vyberete **Procházet soubor úložiště** nebo zadat jeho cestu ručně. Pokud například uložíte protokoly do souborů Azure, vaše cesta protokolování je `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . V této cestě se vytvoří podsložka pro každý spuštěný balíček, který se pojmenuje po spuštění ID běhu aktivity balíčku SSIS a kdy se soubory protokolů generují každých pět minut. 
   
   1. Zadejte přihlašovací údaje pro přístup do složky protokolu. Pokud jste dříve zadali hodnoty přihlašovacích údajů pro přístup k balíčku (viz výše), můžete je znovu použít tak, že vyberete **stejné jako Přístupová pověření k balíčku** . V opačném případě zadejte hodnoty přihlašovacích údajů pro přístup k přihlášení do polí **doména**, **uživatelské jméno**a **heslo** . Pokud například uložíte protokoly do souborů Azure, doména je `Azure` , uživatelské jméno `<storage account name>` a heslo `<storage account key>` . Alternativně můžete použít tajné klíče uložené v Azure Key Vault jako jejich hodnoty (viz výše).
   
U všech výše zmíněných cest UNC musí mít plně kvalifikovaný název souboru méně než 260 znaků. Název adresáře musí být kratší než 248 znaků.

##### <a name="package-location-embedded-package"></a>Umístění balíčku: vložený balíček

Pokud jako umístění balíčku vyberete **vložený balíček** , proveďte následující kroky.

![Nastavení vlastností na kartě nastavení – balíček Embedded](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)

   1. Přetáhněte soubor balíčku (with `.dtsx` ) nebo ho **nahrajte** ze složky souborů do zadaného pole. Balíček se automaticky komprimuje a vloží do datové části aktivity. Po vložení si můžete balíček později **Stáhnout** a upravit. Vložený balíček můžete také **parametrizovat** tak, že ho přiřadíte k parametru kanálu, který se dá použít ve více aktivitách, takže se optimalizuje velikost datové části kanálu. Vkládání souborů projektu (s `.ispac` ) se momentálně nepodporuje, takže nemůžete použít parametry SSIS/Správce připojení s oborem na úrovni projektu ve vložených balíčcích.
   
   1. Pokud vložený balíček není zašifrovaný a zjistili jsme, že se v něm používá úloha spustit balíček (EPT), bude se automaticky vybrat zaškrtávací políčko **spustit balíček** a automaticky se přidají vaše podřízené balíčky, na které se odkazuje pomocí cesty k systému souborů. můžete je taky vložit.
   
      Pokud nedokážeme zjistit použití EPT, musíte ručně zaškrtnout políčko **spustit balíček** a přidat na něj podřízené balíčky, na které se odkazuje pomocí cesty systému souborů One po jednom, takže je můžete vložit i. Pokud jsou vaše podřízené balíčky uložené v SQL Server Database (MSDB), nemůžete je vkládat, takže je potřeba zajistit, aby Azure-SSIS IR přístup k databázi MSDB, aby je mohl načíst pomocí jejich SQL Server odkazů. Vkládání souborů projektu (s `.ispac` ) je aktuálně nepodporované, takže nemůžete použít odkazy založené na projektu pro vaše podřízené balíčky.
   
   1. Pokud jste při vytváření balíčku prostřednictvím SSDT použili úroveň ochrany **EncryptAllWithPassword** nebo **EncryptSensitiveWithPassword** , zadejte v poli **Šifrovací heslo** hodnotu pro heslo. 
   
      Alternativně můžete použít tajný kód uložený v Azure Key Vault jako jeho hodnotu. Pokud to chcete udělat, zaškrtněte políčko **Trezor klíčů Azure** vedle něho. Vyberte nebo upravte existující propojenou službu trezoru klíčů nebo vytvořte novou. Pak vyberte název a verzi tajného klíče pro vaši hodnotu. Když vytváříte nebo upravujete propojenou službu trezoru klíčů, můžete vybrat nebo upravit existující Trezor klíčů nebo vytvořit nový. Pokud jste to ještě neudělali, ujistěte se, že jste vašemu trezoru klíčů udělili Data Factory spravovaný přístup k identitám. Svůj tajný klíč můžete zadat také přímo v následujícím formátu: `<key vault linked service name>/<secret name>/<secret version>` .
      
      Pokud jste použili úroveň ochrany **EncryptSensitiveWithUserKey** , znovu zadejte své citlivé hodnoty do konfiguračních souborů nebo na karty **SSIS**, **Správce připojení**nebo **přepisy vlastností** (viz níže).
      
      Pokud jste použili úroveň ochrany **EncryptAllWithUserKey** , není podporována. Musíte znovu nakonfigurovat balíček tak, aby používal jinou úroveň ochrany prostřednictvím SSDT nebo `dtutil` nástroje příkazového řádku.

   1. V části **úroveň protokolování**vyberte předdefinovaný rozsah protokolování pro spuštění balíčku. Pokud chcete místo toho zadat vlastní název protokolování, zaškrtněte políčko **vlastní** . 
   
   1. Pokud chcete protokolovat provádění balíčků mimo použití standardních zprostředkovatelů protokolů, které lze zadat v balíčku, zadejte svou složku protokolu zadáním cesty UNC do pole **cesta protokolování** . Složku protokolu můžete procházet a vybrat tak, že vyberete **Procházet soubor úložiště** nebo zadat jeho cestu ručně. Pokud například uložíte protokoly do souborů Azure, vaše cesta protokolování je `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . V této cestě se vytvoří podsložka pro každý spuštěný balíček, který se pojmenuje po spuštění ID běhu aktivity balíčku SSIS a kdy se soubory protokolů generují každých pět minut. 
   
   1. Zadejte přihlašovací údaje pro přístup ke složce protokolu zadáním jejich hodnot do polí **doména**, **uživatelské jméno**a **heslo** . Pokud například uložíte protokoly do souborů Azure, doména je `Azure` , uživatelské jméno `<storage account name>` a heslo `<storage account key>` . Alternativně můžete použít tajné klíče uložené v Azure Key Vault jako jejich hodnoty (viz výše).
   
U všech výše zmíněných cest UNC musí mít plně kvalifikovaný název souboru méně než 260 znaků. Název adresáře musí být kratší než 248 znaků.

##### <a name="package-location-package-store"></a>Umístění balíčku: úložiště balíčků

Pokud jako umístění balíčku vyberete možnost **obchod s balíčky** , proveďte následující kroky.

![Nastavení vlastností na kartě nastavení – úložiště balíčků](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings6.png)
   
   1. V případě **názvu úložiště balíčků**vyberte existující úložiště balíčků, které je připojené k vašemu Azure-SSIS IR.

   1. Určete balíček, který se má spustit, zadáním jeho cesty (bez `.dtsx` ) z vybraného úložiště balíčků v poli **cesta k balíčku** . Pokud je vybrané úložiště balíčků nad systémem souborů/soubory Azure, můžete procházet a vybírat balíček výběrem možnosti **Procházet soubor úložiště**, jinak můžete zadat jeho cestu ve formátu `<folder name>\<package name>` . Nové balíčky můžete také importovat do vybraného úložiště balíčků prostřednictvím SQL Server Management Studio (SSMS) podobně jako u [starší verze úložiště balíčků SSIS](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017). Další informace najdete v tématu [Správa balíčků SSIS pomocí Azure-SSIS IRch balíčků](https://docs.microsoft.com/azure/data-factory/azure-ssis-integration-runtime-package-store).

   1. Pokud nakonfigurujete balíček v samostatném souboru, musíte zadat cestu UNC ke konfiguračnímu souboru (s `.dtsConfig` ) v poli **cesta konfigurace** . Můžete procházet a vybírat konfiguraci výběrem možnosti **Procházet soubor úložiště** nebo zadat jeho cestu ručně. Pokud například uložíte konfiguraci do souborů Azure, je její cesta `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` .

   1. Zaškrtnutím políčka **přihlašovací údaje pro přístup k konfiguraci** vyberte, jestli chcete zadat přihlašovací údaje pro přístup ke konfiguračnímu souboru samostatně. To je nutné v případě, že je vybrané úložiště balíčků nad SQL Server Database (MSDB), jejímž hostitelem je vaše spravovaná instance Azure SQL, nebo neukládá i konfigurační soubor.
   
      Pokud jste dříve zadali hodnoty přihlašovacích údajů pro spuštění balíčku (pro **ověřování systému Windows**), můžete je znovu použít zaškrtnutím políčka **stejné jako přihlašovací údaje pro spuštění balíčku** . V opačném případě zadejte hodnoty přihlašovacích údajů pro přístup k konfiguraci do polí **doména**, **uživatelské jméno**a **heslo** . Pokud například uložíte konfiguraci do souborů Azure, doména je `Azure` , uživatelské jméno `<storage account name>` a heslo `<storage account key>` . 

      Alternativně můžete jako své hodnoty použít tajné kódy uložené v Azure Key Vault. Provedete to tak, že zaškrtnete políčko **Trezor klíčů Azure** vedle sebe. Vyberte nebo upravte existující propojenou službu trezoru klíčů nebo vytvořte novou. Pak vyberte název a verzi tajného klíče pro vaši hodnotu. Když vytváříte nebo upravujete propojenou službu trezoru klíčů, můžete vybrat nebo upravit existující Trezor klíčů nebo vytvořit nový. Pokud jste to ještě neudělali, ujistěte se, že jste vašemu trezoru klíčů udělili Data Factory spravovaný přístup k identitám. Svůj tajný klíč můžete zadat také přímo v následujícím formátu: `<key vault linked service name>/<secret name>/<secret version>` .

   1. Pokud jste při vytváření balíčku prostřednictvím SSDT použili úroveň ochrany **EncryptAllWithPassword** nebo **EncryptSensitiveWithPassword** , zadejte v poli **Šifrovací heslo** hodnotu pro heslo. Alternativně můžete použít tajný kód uložený v Azure Key Vault jako jeho hodnotu (viz výše).
      
      Pokud jste použili úroveň ochrany **EncryptSensitiveWithUserKey** , znovu zadejte své citlivé hodnoty do konfiguračních souborů nebo na karty **SSIS**, **Správce připojení**nebo **přepisy vlastností** (viz níže).
      
      Pokud jste použili úroveň ochrany **EncryptAllWithUserKey** , není podporována. Musíte znovu nakonfigurovat balíček tak, aby používal jinou úroveň ochrany prostřednictvím SSDT nebo `dtutil` nástroje příkazového řádku. 

   1. V části **úroveň protokolování**vyberte předdefinovaný rozsah protokolování pro spuštění balíčku. Pokud chcete místo toho zadat vlastní název protokolování, zaškrtněte políčko **vlastní** . 
   
   1. Pokud chcete protokolovat provádění balíčků mimo použití standardních zprostředkovatelů protokolů, které lze zadat v balíčku, zadejte svou složku protokolu zadáním cesty UNC do pole **cesta protokolování** . Složku protokolu můžete procházet a vybrat tak, že vyberete **Procházet soubor úložiště** nebo zadat jeho cestu ručně. Pokud například uložíte protokoly do souborů Azure, vaše cesta protokolování je `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . V této cestě se vytvoří podsložka pro každý spuštěný balíček, který se pojmenuje po spuštění ID běhu aktivity balíčku SSIS a kdy se soubory protokolů generují každých pět minut. 
   
   1. Zadejte přihlašovací údaje pro přístup ke složce protokolu zadáním jejich hodnot do polí **doména**, **uživatelské jméno**a **heslo** . Pokud například uložíte protokoly do souborů Azure, doména je `Azure` , uživatelské jméno `<storage account name>` a heslo `<storage account key>` . Alternativně můžete použít tajné klíče uložené v Azure Key Vault jako jejich hodnoty (viz výše).
   
U všech výše zmíněných cest UNC musí mít plně kvalifikovaný název souboru méně než 260 znaků. Název adresáře musí být kratší než 248 znaků.

#### <a name="ssis-parameters-tab"></a>Karta parametry SSIS

Na kartě **parametry SSIS** aktivity spustit balíček SSIS proveďte následující kroky.

![Nastavení vlastností na kartě Parametry SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

   1. Pokud je spuštěný Azure-SSIS IR, jako umístění balíčku je vybraný **SSISDB** a zaškrtávací políčko Ruční položky na kartě **Nastavení** se neodstraní. pro přiřazení hodnot k těmto **položkám** se zobrazí existující parametry SSIS ve vybraném projektu a balíčku z SSISDB. V opačném případě je můžete zadat jednu po jedné a přiřadit k nim hodnoty ručně. Ujistěte se, že existují a jsou správně zadány pro spuštění balíčku, aby bylo úspěšné. 
   
   1. Pokud jste při vytváření balíčku použili úroveň ochrany **EncryptSensitiveWithUserKey** prostřednictvím SSDT a **systému souborů (balíček)**, **systému souborů (projektu)**, **vloženého balíčku**nebo **úložiště balíčků** , jako umístění balíčku je také potřeba znovu zadat citlivé parametry a přiřadit jim hodnoty na této kartě. 
   
Při přiřazování hodnot k parametrům můžete přidat dynamický obsah pomocí výrazů, funkcí, Data Factory systémových proměnných a Data Factory parametrů kanálu nebo proměnných.

Alternativně můžete jako své hodnoty použít tajné kódy uložené v Azure Key Vault. Provedete to tak, že zaškrtnete políčko **Trezor klíčů Azure** vedle sebe. Vyberte nebo upravte existující propojenou službu trezoru klíčů nebo vytvořte novou. Pak vyberte název a verzi tajného klíče pro vaši hodnotu. Když vytváříte nebo upravujete propojenou službu trezoru klíčů, můžete vybrat nebo upravit existující Trezor klíčů nebo vytvořit nový. Pokud jste to ještě neudělali, ujistěte se, že jste vašemu trezoru klíčů udělili Data Factory spravovaný přístup k identitám. Svůj tajný klíč můžete zadat také přímo v následujícím formátu: `<key vault linked service name>/<secret name>/<secret version>` . 

#### <a name="connection-managers-tab"></a>Karta Správci připojení

Na kartě **Správci připojení** aktivity spustit balíček SSIS proveďte následující kroky.

![Nastavení vlastností na kartě Správci připojení](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

   1. Pokud je spuštěný Azure-SSIS IR, jako umístění balíčku se vybere **SSISDB** a na kartě **Nastavení** se zruší zaškrtávací políčko **Ruční zadání** . pro přiřazení hodnot ke svým vlastnostem se zobrazí stávající Správci připojení ve vybraném projektu a balíčku z SSISDB. V opačném případě je můžete zadat jednu po jedné a přiřadit hodnoty jejich vlastnostem ručně. Ujistěte se, že existují a jsou správně zadány pro spuštění balíčku, aby bylo úspěšné. 
   
      Správný **obor**, **název**a názvy **vlastností** pro libovolného Správce připojení můžete získat tak, že otevřete balíček, který ho obsahuje v SSDT. Po otevření balíčku vyberte odpovídajícího Správce připojení a zobrazte názvy a hodnoty všech jejích vlastností v okně **vlastnosti** SSDT. Pomocí těchto informací můžete v době běhu přepsat hodnoty všech vlastností Správce připojení. 

      ![Získání vlastností Správce připojení z SSDT](media/how-to-invoke-ssis-package-ssis-activity/ssdt-connection-manager-properties.png)

      Například bez úprav původního balíčku na SSDT můžete převést své místní toky dat běžící na SQL Server do toků dat v místním prostředí, které běží na SSIS IR v ADF, a to přepsáním hodnot vlastností **ConnectByProxy**, **ConnectionString**a **ConnectUsingManagedIdentity** v existujících správcích připojení v době běhu.
      
      Tyto přepisy za běhu můžou povolit Self-Hosted IR (SHIR) jako proxy pro SSIS IR při přístupu k datům místně, najdete v tématu [Konfigurace SHIR jako proxy serveru pro SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)a připojení Azure SQL Database/spravované instance pomocí nejnovějšího ovladače MSOLEDBSQL, který umožňuje ověřování Azure Active Directory (AAD) pomocí spravované identity ADF, v tématu [Konfigurace ověřování AAD pomocí spravované identity ADF pro připojení OLEDB](https://docs.microsoft.com/sql/integration-services/connection-manager/ole-db-connection-manager?view=sql-server-ver15#managed-identities-for-azure-resources-authentication).

      ![Nastavení vlastností z SSDT na kartě Správci připojení](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers2.png)
   
   1. Pokud jste při vytváření balíčku použili úroveň ochrany **EncryptSensitiveWithUserKey** prostřednictvím SSDT a **systému souborů (balíček)**, **systému souborů (projektu)**, **vloženého balíčku**nebo **úložiště balíčků** , jako umístění balíčku je třeba znovu zadat vlastnosti citlivého Správce připojení a přiřadit jim hodnoty na této kartě. 

Když přiřazujete hodnoty k vlastnostem Správce připojení, můžete přidat dynamický obsah pomocí výrazů, funkcí, Data Factory systémových proměnných a Data Factory parametrů kanálu nebo proměnných. 

Alternativně můžete jako své hodnoty použít tajné kódy uložené v Azure Key Vault. Provedete to tak, že zaškrtnete políčko **Trezor klíčů Azure** vedle sebe. Vyberte nebo upravte existující propojenou službu trezoru klíčů nebo vytvořte novou. Pak vyberte název a verzi tajného klíče pro vaši hodnotu. Když vytváříte nebo upravujete propojenou službu trezoru klíčů, můžete vybrat nebo upravit existující Trezor klíčů nebo vytvořit nový. Pokud jste to ještě neudělali, ujistěte se, že jste vašemu trezoru klíčů udělili Data Factory spravovaný přístup k identitám. Svůj tajný klíč můžete zadat také přímo v následujícím formátu: `<key vault linked service name>/<secret name>/<secret version>` . 

#### <a name="property-overrides-tab"></a>Karta přepsání vlastností

Na kartě **přepsání vlastností** aktivity spustit balíček SSIS proveďte následující kroky.

![Nastavte vlastnosti na kartě přepsání vlastností.](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   1. Zadejte cesty k existujícím vlastnostem ve vybraném balíčku jeden po jednom, aby byly hodnoty přiřazeny ručně. Ujistěte se, že existují a jsou správně zadány pro spuštění balíčku, aby bylo úspěšné. Chcete-li například přepsat hodnotu proměnné uživatele, zadejte cestu v následujícím formátu: `\Package.Variables[User::<variable name>].Value` . 

      Správnou **cestu vlastnosti** pro libovolnou vlastnost balíčku můžete získat otevřením balíčku, který ho obsahuje v SSDT. Po otevření balíčku vyberte jeho vlastnost tok a **Konfigurace** v okně **vlastnosti** SSDT. V dalším kroku vyberte tlačítko se třemi tečkami (**...**) vedle vlastnosti **Konfigurace** a otevřete tak **organizátor konfigurací balíčku** , který se obvykle používá k [Vytvoření konfigurací balíčku v modelu nasazení balíčku](https://docs.microsoft.com/sql/integration-services/packages/legacy-package-deployment-ssis#create-package-configurations). 

      ![Získat vlastnosti balíčku z SSDT – vlastnost Configurations](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties.png)

      V **Organizátoru konfigurace balíčku**zaškrtněte políčko **Povolit konfigurace balíčků** a klikněte na tlačítko **Přidat...** a otevřete **Průvodce konfigurací balíčku**. 
      
      V **Průvodci konfigurací balíčku**vyberte položku **konfigurační soubor XML** v rozevírací nabídce **typ konfigurace** a klikněte na tlačítko **zadat nastavení konfigurace přímo** , zadejte název konfiguračního souboru a vyberte tlačítko **Další >** . 

      ![Získat vlastnosti balíčku z SSDT – organizátor konfigurací](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties2.png)

      Nakonec vyberte vlastnosti balíčku, jejichž cestu chcete a tlačítko **další >** .  Nyní můžete zobrazit, kopírovat & cesty k vlastnostem balíčku, které chcete uložit, a uložit je do konfiguračního souboru. Pomocí těchto informací můžete v době běhu přepsat hodnoty všech vlastností balíčku. 

      ![Získat vlastnosti balíčku z SSDT – Průvodce konfigurací](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties3.png)
   
   1. Pokud jste při vytváření balíčku použili úroveň ochrany **EncryptSensitiveWithUserKey** prostřednictvím SSDT a **systému souborů (balíček)**, **systému souborů (projektu)**, **vloženého balíčku**nebo **úložiště balíčků** , je také nutné znovu zadat vlastnosti citlivého balíčku a přiřadit k nim hodnoty na této kartě. 
   
Když přiřazujete hodnoty k vlastnostem balíčku, můžete přidat dynamický obsah pomocí výrazů, funkcí, Data Factory systémových proměnných a Data Factory parametrů kanálu nebo proměnných.

Hodnoty přiřazené v konfiguračních souborech a na kartě **parametry SSIS** lze přepsat pomocí karet **Správce připojení** nebo **přepsání vlastností** . Hodnoty přiřazené na kartě **Správci připojení** lze také přepsat pomocí karty **vlastnosti Overrides** .

Pokud chcete ověřit konfiguraci kanálu, vyberte **ověřit** na panelu nástrojů. Pokud chcete **sestavu ověření kanálu**zavřít, vyberte **>>** .

Pokud chcete publikovat kanál do Data Factory, vyberte **publikovat vše**. 

### <a name="run-the-pipeline"></a>Spuštění kanálu
V tomto kroku aktivujete spuštění kanálu. 

1. Pokud chcete aktivovat spuštění kanálu, vyberte **aktivační událost** na panelu nástrojů a vyberte **aktivovat nyní**. 

   ![Aktivovat](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. V okně **Spuštění kanálu** vyberte **Dokončit**. 

### <a name="monitor-the-pipeline"></a>Monitorování kanálu

1. Vlevo přepněte na kartu **Monitorování**. Zobrazí se spuštění kanálu a jeho stav spolu s dalšími informacemi, jako je například čas **spuštění** . Jestliže chcete zobrazení aktualizovat, vyberte **Aktualizovat**.

   ![Spuštění kanálu](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Klikněte na odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. Zobrazí se pouze jedno spuštění aktivity, protože kanál má pouze jednu aktivitu. Je to aktivita SSIS balíčku Execute.

   ![Spuštění aktivit](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Spusťte následující dotaz pro databázi SSISDB na serveru SQL Server a ověřte, zda byl balíček spuštěn. 

   ```sql
   select * from catalog.executions
   ```

   ![Ověření provedení balíčku](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. ID spuštění SSISDB můžete také získat z výstupu aktivity kanálu a pomocí tohoto ID ověřit komplexnější protokoly spouštění a chybové zprávy v SQL Server Management Studio.

   ![Získá ID spuštění.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Naplánování kanálu pomocí triggeru

Můžete také vytvořit plánovanou aktivační událost pro svůj kanál, aby kanál běžel podle plánu, například každou hodinu nebo každý den. Příklad najdete v tématu [Vytvoření datové továrny – Data Factory uživatelského rozhraní](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Spuštění balíčku pomocí prostředí PowerShell
V této části použijete Azure PowerShell k vytvoření kanálu Data Factory s aktivitou spustit SSIS balíčku, která spouští balíček SSIS. 

Pomocí podrobných pokynů, [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/install-az-ps), nainstalujte nejnovější Azure PowerShell moduly.

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Vytvoření datové továrny pomocí Azure-SSIS IR
Můžete buď použít stávající objekt pro vytváření dat, který už Azure-SSIS IR zřízený, nebo vytvořit novou datovou továrnu s Azure-SSIS IR. Postupujte podle podrobných pokynů v tomto [kurzu: nasazení balíčků SSIS do Azure prostřednictvím PowerShellu](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Vytvoření kanálu s aktivitou Execute SSIS Package 
V tomto kroku vytvoříte kanál s aktivitou spustit SSIS s balíčkem. Aktivita spustí balíček SSIS. 

1. Ve složce vytvořte soubor JSON `RunSSISPackagePipeline.json` `C:\ADF\RunSSISPackage` s podobným obsahem jako v následujícím příkladu.

   > [!IMPORTANT]
   > Před uložením souboru nahraďte názvy objektů, popisy a cesty, vlastnosti nebo hodnoty parametrů, hesla a další hodnoty proměnných. 
    
   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "type": "SSISDB",
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

   Pokud chcete spouštět balíčky uložené v souborech systému souborů nebo Azure, zadejte následující hodnoty vlastností balíčku a umístění protokolu:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "type": "File",
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Pokud chcete spouštět balíčky v projektech uložených v souborech systému souborů nebo Azure, zadejte následující hodnoty pro vlastnosti umístění balíčku:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Pokud chcete spustit vložené balíčky, zadejte hodnoty pro vlastnosti umístění balíčku následujícím způsobem:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "InlinePackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "packageName": "MyPackage.dtsx",
                           "packageContent":"My compressed/uncompressed package content",
                           "packageLastModifiedDate": "YYYY-MM-DDTHH:MM:SSZ UTC-/+HH:MM"
                       }
                   }
               }
           }
       }
   }
   ```

   Pokud chcete spouštět balíčky uložené v úložištích balíčků, zadejte hodnoty pro vlastnosti balíčku a umístění konfigurace následujícím způsobem:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "PackageStore",
                       "packagePath": "myPackageStore/MyFolder/MyPackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           },
                           "configurationPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyConfiguration.dtsConfig",
                           "configurationAccessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

2. V Azure PowerShell přepněte do `C:\ADF\RunSSISPackage` složky.

3. Pokud chcete vytvořit kanál **RunSSISPackagePipeline**, spusťte rutinu **set-AzDataFactoryV2Pipeline** .

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Zde je ukázkový výstup:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>Spuštění kanálu
Ke spuštění kanálu použijte rutinu **Invoke-AzDataFactoryV2Pipeline** . Tato rutina vrací ID spuštění kanálu pro budoucí monitorování.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>Monitorování kanálu

Spusťte následující skript PowerShellu, který bude nepřetržitě kontrolovat stav spuštění kanálu, dokud nedokončí kopírování dat. Zkopírujte nebo vložte následující skript v okně PowerShellu a vyberte Enter. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

Kanál můžete také monitorovat pomocí Azure Portal. Podrobné pokyny najdete v tématu [monitorování kanálu](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Naplánování kanálu pomocí triggeru
V předchozím kroku jste kanál spustili na vyžádání. Můžete také vytvořit aktivační proceduru Schedule pro spuštění kanálu podle plánu, například každou hodinu nebo každý den.

1. Ve složce vytvořte soubor JSON `MyTrigger.json` `C:\ADF\RunSSISPackage` s následujícím obsahem: 
        
   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
    
1. V Azure PowerShell přepněte do `C:\ADF\RunSSISPackage` složky.
1. Spusťte rutinu **set-AzDataFactoryV2Trigger** , která vytvoří Trigger. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. Ve výchozím nastavení je aktivační událost v zastaveném stavu. Spusťte Trigger spuštěním rutiny **Start-AzDataFactoryV2Trigger** . 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. Spuštěním rutiny **Get-AzDataFactoryV2Trigger** ověřte, že je aktivační událost spuštěná. 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. Po další hodiny spusťte následující příkaz. Pokud je například aktuální čas 3:25. UTC, spusťte příkaz ve 4. UTC. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Spusťte následující dotaz pro databázi SSISDB na serveru SQL Server a ověřte, zda byl balíček spuštěn. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Další kroky
Podívejte se na tento příspěvek na blogu:
- [Modernizovat a rozšíří pracovní postupy ETL/ELT s aktivitami SSIS v kanálu Azure Data Factory.](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
